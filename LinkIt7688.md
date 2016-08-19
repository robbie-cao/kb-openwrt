# MediaTek LinkIt Smart 7688 with OpenWrt

## Flash Layout

![Flash Layout](images/openwrt_flash_layout_7688.png)

```
[    0.290000] spi-mt7621 10000b00.spi: sys_freq: 193333333
[    0.300000] m25p80 spi32766.0: w25q128 (16384 Kbytes)
[    0.300000] m25p80 spi32766.0: using chunked io
[    0.310000] 4 ofpart partitions found on MTD device spi32766.0
[    0.310000] Creating 4 MTD partitions on "spi32766.0":
[    0.320000] 0x000000000000-0x000000030000 : "u-boot"
[    0.320000] 0x000000030000-0x000000040000 : "u-boot-env"
[    0.330000] 0x000000040000-0x000000050000 : "factory"
[    0.340000] 0x000000050000-0x000001000000 : "firmware"
[    0.370000] 2 uimage-fw partitions found on MTD device firmware
[    0.380000] 0x000000050000-0x000000169d29 : "kernel"
[    0.390000] 0x000000169d29-0x000001000000 : "rootfs"
[    0.390000] mtd: device 5 (rootfs) set to be root filesystem
[    0.400000] 1 squashfs-split partitions found on MTD device rootfs
[    0.400000] 0x000000560000-0x000001000000 : "rootfs_data"
[    0.420000] ralink_soc_eth 10100000.ethernet eth0: ralink at 0xb0100000, irq 5
```


```
root@Widora:/# cat /proc/mtd
dev:    size   erasesize  name
mtd0: 00030000 00010000 "u-boot"
mtd1: 00010000 00010000 "u-boot-env"
mtd2: 00010000 00010000 "factory"
mtd3: 00fb0000 00010000 "firmware"
mtd4: 00119d29 00010000 "kernel"
mtd5: 00e962d7 00010000 "rootfs"
mtd6: 00aa0000 00010000 "rootfs_data"
```


```
root@Widora:/# mount
rootfs on / type rootfs (rw)
/dev/root on /rom type squashfs (ro,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,noatime)
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,noatime)
tmpfs on /tmp type tmpfs (rw,nosuid,nodev,noatime)
/dev/mtdblock6 on /overlay type jffs2 (rw,noatime)
overlayfs:/overlay on / type overlay (rw,noatime,lowerdir=/,upperdir=/overlay/upper,workdir=/overlay/work)
tmpfs on /dev type tmpfs (rw,nosuid,relatime,size=512k,mode=755)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,mode=600)
debugfs on /sys/kernel/debug type debugfs (rw,noatime)
mountd(pid1036) on /tmp/run/mountd type autofs (rw,relatime,fd=5,pgrp=1033,timeout=60,minproto=5,maxproto=5,indirect)
```

> https://wiki.openwrt.org/doc/techref/flash.layout

