# OpenWrt Boot Process

## Boot Process in a Nutshell

1. bootloader configures enough low level hardware to load kernel and jump to it with a kernel cmdline
2. kernel inits hardware for everything built 'static' in the kernel
3. kernel mounts the root filesystem (via the `root=` `rootfstype=` etc parameters in the kernel cmdline)
4. kernel executes the 'init' process (PID 1).
   - this is done in the kernel's init_post function in `init/main.c` and typically will execute whichever is found first: `/sbin/init`, `/etc/init`, `/bin/init`, `/bin/sh`
   - for OpenWrt the init process is an OpenWrt specific project called **`procd`**
5. `procd` processes `/etc/inittab` and executes all scripts from `/etc/rc.d/S*`
   - files here are typically symlinks to scripts in `/etc/init.d` and have a number preceding the name to dictate a priority affects order of execution
   - `/etc/init.d/` scripts include `/etc/rc.common` which implements enable/disable functions which create the symlinks. The start/stop priority numbers are defined in the init scripts

> http://trac.gateworks.com/wiki/OpenWrt/init

## Process Trinity

### Bootloader

1. the bootloader on the flash gets executed
2. the bootloader performs the POST, which is a low-level hardware initialization
3. the bootloader decompresses the Kernel image from its (known!) location on the flash storage into main memory (=RAM)
4. the bootloader executes the Kernel with init=... option (default is `/etc/preinit`)

### Kernel

1. the Kernel further bootstraps itself
2. issues the command/op-code `start_kernel`
3. kernel scans the mtd partition rootfs for a valid superblock and mounts the SquashFS partition (which contains `/etc`) once found
4. `/etc/preinit` does pre-initialization setups (create directories, mount fs, `/proc`, `/sys`, ...)
5. the Kernel mounts any other partition (e.g. jffs2 partition) under rootfs
6. if "INITRAMFS" is not defined, calls `/sbin/init` - the mother of all processes (**it will be replaced by `/sbin/procd` afterwards**)
7. finally some kernel thread becomes the userspace `init` process

### Init

The user space starts when kernel mounts *rootfs* and the very first program to run is (by default) `/sbin/init`.

1. init reads `/etc/inittab` for the "sysinit" entry (default is "::sysinit:/etc/init.d/rcS S boot")
2. init calls `/etc/init.d/rcS S boot`
3. rcS executes the symlinks to the actual startup scripts located in `/etc/rc.d/S##xxxxxx` with option "start"
4. after rcS finishes, system should be up and running

> https://wiki.openwrt.org/doc/techref/process.boot

## Init Scripts

### Preinit

Preinit brings the system from raw kernel to ready for multiuser. To do so it performs the following tasks:

1. Sources `/etc/functions.sh` and `/lib/functions/boot.sh` for common functions for boot/mount
2. Mounts essential kernel filesystems like `procfs`
3. Initializes device tree (`/dev`)
4. Initializes console (serial console if present, otherwise dummy so that the script interpreter works properly)
5. Presents opportunity for the user to enter a special operating mode called **failsafe** (Failsafe mode is presented in a separate section. Once failsafe mode is entered it doesn't exit. A reboot is necessary to enter normal operating mode)
6. Mounts the root filesystem
7. If it's the first time booting after flashing the firmware, and a previous configuration was saved during the flashing process, that configuration is restored
8. Becomes (though exec) `init` which goes to multiuser mode

> https://wiki.openwrt.org/doc/techref/preinit_mount

### rcS Scripts

Script       | Function
-------      | --------
S05defconfig | create config files with default values for platform (if config file is not exist), really does this on first start after OpenWRT installed (copy unexisted files from `/etc/defconfig/$board/` to `/etc/config/`)
S10boot      | starts hotplug-script, mounts filesystesm, starts .., starts `syslogd`, ...
S39usb       | `mount -t usbfs none /proc/bus/usb`
S40network   | start a network subsystem (run `/sbin/netifd`, up interfaces and wifi
S45firewall  | create and implement firewall rules from `/etc/config/firewall`
S50cron      | starts `crond`, see `/etc/crontabs/root` for configuration
S50dropbear  | starts `dropbear`, see `/etc/config/dropbear` for configuration
S50telnet    | checks for root password, if non is set, `/usr/sbin/telnetd` gets started
S60dnsmasq   | starts `dnsmasq`, see `/etc/config/dhcp` for configuration
S95done      | executes `/etc/rc.local`
S96led       | load a LED configuration from `/etc/config/system` and set up LEDs (write values to `/sys/class/leds/*/*`)
S97watchdog  | start the watchdog daemon (`/sbin/watchdog`)
S99sysctl    | interprets `/etc/sysctl.conf`

```
root@mylinkit:/# ls -l /etc/rc.d/
lrwxrwxrwx    1 root     root            23 Aug 27 05:15 K10mjpg-streamer -> ../init.d/mjpg-streamer
lrwxrwxrwx    1 root     root            18 Aug 27 05:15 K50dropbear -> ../init.d/dropbear
lrwxrwxrwx    1 root     root            16 Aug 27 05:15 K85odhcpd -> ../init.d/odhcpd
lrwxrwxrwx    1 root     root            13 Aug 27 05:15 K89log -> ../init.d/log
lrwxrwxrwx    1 root     root            17 Aug 27 05:15 K90network -> ../init.d/network
lrwxrwxrwx    1 root     root            14 Aug 27 05:15 K98boot -> ../init.d/boot
lrwxrwxrwx    1 root     root            16 Aug 27 05:15 K99umount -> ../init.d/umount
lrwxrwxrwx    1 root     root            20 Aug 27 05:15 S00sysfixtime -> ../init.d/sysfixtime
lrwxrwxrwx    1 root     root            14 Aug 27 05:15 S10boot -> ../init.d/boot
lrwxrwxrwx    1 root     root            16 Aug 27 05:15 S10linkit -> ../init.d/linkit
lrwxrwxrwx    1 root     root            16 Aug 27 05:15 S10system -> ../init.d/system
lrwxrwxrwx    1 root     root            16 Aug 27 05:15 S11sysctl -> ../init.d/sysctl
lrwxrwxrwx    1 root     root            13 Aug 27 05:15 S12log -> ../init.d/log
lrwxrwxrwx    1 root     root            14 Aug 27 05:15 S12rpcd -> ../init.d/rpcd
lrwxrwxrwx    1 root     root            18 Aug 27 05:15 S19firewall -> ../init.d/firewall
lrwxrwxrwx    1 root     root            17 Aug 27 05:15 S20network -> ../init.d/network
lrwxrwxrwx    1 root     root            16 Aug 27 05:15 S35odhcpd -> ../init.d/odhcpd
lrwxrwxrwx    1 root     root            14 Aug 27 05:15 S50cron -> ../init.d/cron
lrwxrwxrwx    1 root     root            18 Aug 27 05:15 S50dropbear -> ../init.d/dropbear
lrwxrwxrwx    1 root     root            16 Aug 27 05:15 S50telnet -> ../init.d/telnet
lrwxrwxrwx    1 root     root            16 Aug 27 05:15 S50uhttpd -> ../init.d/uhttpd
lrwxrwxrwx    1 root     root            14 Aug 27 05:15 S60dbus -> ../init.d/dbus
lrwxrwxrwx    1 root     root            17 Aug 27 05:15 S60dnsmasq -> ../init.d/dnsmasq
lrwxrwxrwx    1 root     root            15 Aug 27 05:15 S60samba -> ../init.d/samba
lrwxrwxrwx    1 root     root            22 Aug 27 05:15 S61avahi-daemon -> ../init.d/avahi-daemon
lrwxrwxrwx    1 root     root            19 Aug 27 05:15 S80mosquitto -> ../init.d/mosquitto
lrwxrwxrwx    1 root     root            16 Aug 27 05:15 S80mountd -> ../init.d/mountd
lrwxrwxrwx    1 root     root            23 Aug 27 05:15 S90mjpg-streamer -> ../init.d/mjpg-streamer
lrwxrwxrwx    1 root     root            13 Aug 27 05:15 S93mpd -> ../init.d/mpd
lrwxrwxrwx    1 root     root            14 Aug 27 05:15 S95done -> ../init.d/done
lrwxrwxrwx    1 root     root            18 Aug 27 05:15 S95upmpdcli -> ../init.d/upmpdcli
lrwxrwxrwx    1 root     root            13 Aug 27 05:15 S96led -> ../init.d/led
lrwxrwxrwx    1 root     root            17 Aug 27 05:15 S98sysntpd -> ../init.d/sysntpd
lrwxrwxrwx    1 root     root            14 Sep  7 10:27 S99muad -> ../init.d/muad
```

### `/etc/rc.d/rcS` Script At Startup

The rcS script is pretty simplistic in it's function - it's sole purpose is to execute all of the scripts in the `/etc/rc.d` directory with the appropriate options. if you paid attention to the sysinit entry, the rcS script was called with the "S" and "boot" options. Since we called rcS with 2 options ("S" and "boot"), the rcS script will substitute $1 with "S" and $2 with "boot". The relevant lines in rcS are:

  ```
  for i in /etc/rc.d/$1* ; do
      [ -x $i ] && $i $2
  done
  ```

The basic breakdown is:

1. Execute the following line once for every entry (file/link) in the `/etc/rc.d` directory that begins with "S"
2. If the file is executable, execute the file with the option "boot"
3. Repeat at step 1, replacing $i with the next filename until there are no more files to check

**NOTE:**

**In OpenWrt Chaos Calmer, rcS is no longer needed, `procd` handle it now. Change at https://dev.openwrt.org/changeset/37002**

### `procd`

`procd` is the new OpenWrt process management daemon written in C. It keeps track of processes started from init scripts (via ubus calls), and can suppress redundant service start/restart requests when the config/environment has not changed.

At boot, Linux kernel starts `/sbin/init` as the first user process. In Chaos Calmer, `/sbin/init` does the preinit/failsafe steps, those that depend only on the read-only partition in flashed image, then execs (that is: **is replaced by**) `/sbin/procd` to continue boot as specified by the configuration in writable flash partition. Procd started as pid 1 assumes several roles: service manager, hotplug events handler; this as of February 2016, when this research was done.
More details refer to https://wiki.openwrt.org/doc/techref/init.detail.cc

`procd` project git at http://git.openwrt.org/?p=project/procd.git;a=summary

> https://wiki.openwrt.org/doc/techref/procd

## Reference

- https://wiki.openwrt.org/doc/techref/process.boot
- https://wiki.openwrt.org/doc/techref/initscripts
- https://wiki.openwrt.org/doc/techref/preinit_mount
- https://wiki.openwrt.org/doc/techref/procd
- https://wiki.openwrt.org/doc/techref/init.detail.cc
- https://wiki.openwrt.org/doc/techref/requirements.boot.process
- http://trac.gateworks.com/wiki/OpenWrt/init

