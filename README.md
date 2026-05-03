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

TODO

---

## Installing root

TODO

