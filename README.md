# Custom Kernel L4T for Nivida Jetson Orin AGX Devkit

This repo contains informations on how to build a custom Kernel for Nvidia Jetson Orin AGX Devkit.
The steps described here are for Nvidia Jetson Orin AGX Devkit 64GB

- Jetpack 6
- Ubuntu 22.04

1. Clone this repo

```bash
git clone https://github.com/muellerbernd/l4t.git
cd ./l4t
```

2. Download Jetpack 6 Kernel Sources and unpack

```bash
wget https://developer.nvidia.com/downloads/embedded/l4t/r36_release_v3.0/sources/public_sources.tbz2
tar -xvf public_sources.tbz2
cd Linux_for_Tegra/source
tar -xvf kernel_src.tbz2
```

3. Copy necessary files from this repo

```bash
cd l4t
cp ./Makefile Linux_for_Tegra/source/kernel
```

4. backup your default kernel as fallback

- `/boot/extlinux/extlinux.conf`

```
TIMEOUT 30
DEFAULT primary

MENU TITLE L4T boot options

LABEL primary
      MENU LABEL primary kernel
      LINUX /boot/Image
      INITRD /boot/initrd
      APPEND ${cbootargs} root=/dev/nvme0n1p1 rw rootwait rootfstype=ext4 mminit_loglevel=4 console=ttyTCU0,115200 console=ttyAMA0,115200 firmware_class.path=/etc/firmware fbcon=map:0 net.ifnames=0 nospectre_bhb video=efifb:off console=tty0 nv-auto-config

# When testing a custom kernel, it is recommended that you create a backup of
# the original kernel and add a new entry to this file so that the device can
# fallback to the original kernel. To do this:
#
# 1, Make a backup of the original kernel
#      sudo cp /boot/Image /boot/Image.backup
#
# 2, Copy your custom kernel into /boot/Image
#
# 3, Uncomment below menu setting lines for the original kernel
#
# 4, Reboot

LABEL backup
   MENU LABEL backup kernel
   LINUX /boot/Image.backup
   INITRD /boot/initrd
   APPEND ${cbootargs} root=/dev/nvme0n1p1 rw rootwait rootfstype=ext4 mminit_loglevel=4 console=ttyTCU0,115200 console=ttyAMA0,115200 firmware_class.path=/etc/firmware fbcon=map:0 net.ifnames=0 nospectre_bhb video=efifb:off console=tty0 nv-auto-config
```

5. build and install kernel

```bash
cd l4t/Linux_for_Tegra/source/kernel
make config # copy custom kernel defconfig
make kernel # build kernel
make install # install custom kernel
```
