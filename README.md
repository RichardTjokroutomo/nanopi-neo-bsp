# Nanopi Neo BSP

This repo contains a BSP for Nanopi Neo H3 that I re-created for fun.

---

## Partitioning the disk

1. Create two primary MBR partitions using `Fdisk`, first one has `1MB` offset & of size `16MB`. The second one is the rest of the disk.
2. Create the filesystem using `mkfs`:

```bash
mkfs.vfat /dev/<first-partition>
mkfs.ext4 /dev/<second-partition>
```

I mainly use [this docs](https://linux-sunxi.org/H3_Manual_build_howto#Installing_boot0) as reference, although I used `Fdisk` since I'm more comfortable with it.

---

## Mounting the partitions

Identify the partitions:

```bash
lsblk -o UUID,name
```

Then mount them to your mountpoint. For example, if the disk is `/dev/sda`:

```bash
mount /dev/sda1 /<mountpoint>/boot
mount /dev/sda2 /<mountpoint>
```

---

## Installing the bootloader

Run the following:

```bash
dd if=u-boot-sunxi-with-spl.bin of=<disk> bs=1024 seek=8
sync
```

Note that <disk> is the disk (e.g. /dev/sda), not the partition itself.

It is expected that nothing is seen when you try to `ls` the disk. Instead, to verify if the bootloader has been written:

```bash
hexdump -C /dev/<disk> | head
```

If successful, then hexdump shouldn't return all zero before `EFI`.

---

## Installing the kernel, device tree & extlinux.conf

zImage & device tree binary can be compiled as follows:

```bash
make sunxi_defconfig
make zImage dtbs ARCH=arm CROSS_COMPILE=<cross-compiler-tuple>- 
```

Where the device tree (`.dts`) is placed under `arch/arm/boot/dts/allwinner`. You can either write your own `.dts` for `Nanopi Neo`, or you can just copy the `.dts` from `friendlyElec's u-boot` fork ([relevant link](https://wiki.friendlyelec.com/wiki/index.php/Building_U-boot_and_Linux_for_H5/H3/H2%2B#Compile_U-boot)). When compilation completes, the `zImage` & `.dtb` can be obtained in `arch/arm/boot` & `arch/arm/boot/dts/allwinner` respectively.

`extlinux.conf` tells `u-boot` where the kernel & device tree is. It is sort of like the script for `u-boot`. I mainly use this [link](https://docs.u-boot.org/en/stable/develop/distro.html) as reference. Put this under `/boot/extlinux` directory (create a directory `extlinux` in the first partition, AKA the boot partition).

---

## Installing root

TODO

