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

## Backup Factory Data

**In Target**

```
-> check mtd partitions
root@Widora:/# cat /proc/mtd
dev:    size   erasesize  name
mtd0: 00030000 00010000 "u-boot"
mtd1: 00010000 00010000 "u-boot-env"
mtd2: 00010000 00010000 "factory"
mtd3: 00fb0000 00010000 "firmware"
mtd4: 00119d29 00010000 "kernel"
mtd5: 00e962d7 00010000 "rootfs"

-> backup factory partition
root@Widora:/# dd if=/dev/mtd2 of=/tmp/factory.backup.bin
128+0 records in
128+0 records out

-> set network (connect board to router/switch with cable)
root@Widora:/# ifconfig br-lan 192.168.31.224
root@Widora:/# ping 192.168.31.1
PING 192.168.31.1 (192.168.31.1): 56 data bytes
64 bytes from 192.168.31.1: seq=0 ttl=64 time=1.009 ms
64 bytes from 192.168.31.1: seq=1 ttl=64 time=0.481 ms
^C
--- 192.168.31.1 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.481/0.745/1.009 ms

-> set root password
root@Widora:/# passwd root
Changing password for root
New password:				(-> enter password, can be simple leave as blank)
Bad password: too short
Retype password:
Password for root changed by root


# host side
scp root@192.168.31.xxx:/tmp/factory.backup.bin .
```


**Host Side**

```
$ scp root@192.168.31.xxx:/tmp/factory.backup.bin .
root@192.168.31.224's password: 
factory.backup.bin                     100%   64KB  64.0KB/s   00:00  
$ cp factory.backup.bin /to/safe/storage
```

