# Tips

## Redirect Log to Console

```
-> In script
echo "Messages you want to output" > /dev/console

-> Log trace (by printf / fprintf(stdout/stderr, ...) of program running in background
program 2>&1 > /dev/console
```

## MT7688 Pin Mux

```
root@mua:/# mt7688_pinmux get
Group i2c - [i2c] gpio
Group uart0 - [uart] gpio
Group uart1 - [uart] gpio
Group uart2 - [uart] gpio pwm
Group pwm0 - [pwm] gpio
Group pwm1 - [pwm] gpio
Group refclk - refclk [gpio]
Group spi_s - spi_s [gpio]
Group spi_cs1 - [spi_cs1] gpio refclk
Group i2s - i2s [gpio] pcm
Group ephy - [ephy] gpio    --> set ephy pin as gpio
Group wled - [wled] gpio


root@mua:~# mt7688_pinmux set ephy gpio
set pinmux ephy -> gpio


root@mua:~# mt7688_pinmux get
Group i2c - [i2c] gpio
Group uart0 - [uart] gpio
Group uart1 - [uart] gpio
Group uart2 - [uart] gpio pwm
Group pwm0 - [pwm] gpio
Group pwm1 - [pwm] gpio
Group refclk - refclk [gpio]
Group spi_s - spi_s [gpio]
Group spi_cs1 - [spi_cs1] gpio refclk
Group i2s - i2s [gpio] pcm
Group ephy - ephy [gpio]    --> ephy pin work as gpio
Group wled - [wled] gpio
```

## WiFi Config STA -> AP

```
# uci set wireless.sta.disabled=1
# uci show wireless.sta.disabled
wireless.sta.disabled='1'
# uci commit
# wifi
```

> https://wiki.openwrt.org/doc/howto/hardware.button

> https://wiki.openwrt.org/doc/uci

## Control GPIO/LED in Command Line

The following script export IO16 as *out* and toggle LED on it.

```
#!/bin/ash

echo 16 > /sys/class/gpio/export
echo out > /sys/class/gpio/gpio16/direction

while true;
do
        echo 1 > /sys/class/gpio/gpio16/value;
        sleep 1;
        echo 0 > /sys/class/gpio/gpio16/value;
done
```

## Set Headphone / Speaker Volume

```
# amixer -c 0 sset Headphone,0 120
Simple mixer control 'Headphone',0
Capabilities: pvolume
Playback channels: Front Left - Front Right
Limits: Playback 0 - 127
Mono:
Front Left: Playback 120 [94%] [-1.00dB]
Front Right: Playback 120 [94%] [-1.00dB]

-or-

# amixer sset 'Headphone',0 80%
Simple mixer control 'Headphone',0
Capabilities: pvolume
Playback channels: Front Left - Front Right
Limits: Playback 0 - 127
Mono:
Front Left: Playback 102 [80%] [-19.00dB]
Front Right: Playback 102 [80%] [-19.00dB]
```

Replace **'Headphone'** with **Speaker** to set speaker volume.

More details refer to https://github.com/robbie-cao/kb-audio/blob/master/amixer.md#steps

## Timer with `uloop`

```
#include "uloop.h"

truct uloop_timeout something_timeout;


static void something_timeout_handler(struct uloop_timeout *t)
{
    // ...
    // operations
    // ...

    // Reset timeout to be a repeat timer
    // Not needed for oneshot timeout handler
    uloop_timeout_set(t, LED_FLASH_INTERVAL);
}

void some_func()
{
    // ...
    something_timeout.cb = something_flash_handler;
    uloop_timeout_set(&something_timeout, LED_FLASH_INTERVAL);
    // ...
}
```

## WPS Button on MT7688

**Long press 5s to reset WiFi back to STA mode**

```
# wifi reset (back to ap mode)
elif [ "$SEEN" -gt 5 ]
then
        sta_disabled="$(uci get wireless.sta.disabled)"
        if [ ! "$sta_disabled" = 1 ]; then
                echo none > /sys/class/leds/mediatek\:orange\:wifi/trigger
                echo 1 > /sys/class/leds/mediatek\:orange\:wifi/brightness
                sleep 1
                echo "START WIFI AP" > /dev/console
                uci set wireless.sta.disabled=1
                uci commit wireless
                wifi
        fi
```

**Long press 20s for factory reset**

```
# factory reset
if [ "$SEEN" -gt 20 ]
then
        [ -f /tmp/.factory_reset ] && return
        echo timer > /sys/class/leds/mediatek\:orange\:wifi/trigger
        echo 100 > /sys/class/leds/mediatek\:orange\:wifi/delay_on
        echo 100 > /sys/class/leds/mediatek\:orange\:wifi/delay_off
        touch /tmp/.factory_reset
        echo "FACTORY RESET" > /dev/console
        jffs2reset -y
        sync
        reboot
```

Script full source located at `/etc/rc.button/wps`, refer to: https://github.com/robbie-cao/mua-mtk-lks7688-feed/blob/master/mtk-linkit/files/etc/rc.button/wps

> https://wiki.openwrt.org/doc/howto/hardware.button

> http://www.cnblogs.com/sammei/p/4119659.html

## 用户空间监听 uevent

```
export DBGLVL=10; procd -h /etc/hotplug.json
```

## 按键 Button 的检测

OpenWrt 中, `procd` 作为 init 进程会处理许多事情, 其中包括了 hotplug, 按键的检测就是通过 hotplug 来实现的。

它首先写了一个内核模块: `gpio_button_hotplug`, 用于监听按键, 有中断和 poll 两种方式. 然后在发出事件的同时, 将记录并计算得出的两次按键时间差也作为 uevent 变量发出来.

这样在用户空间收到这个 uevent 事件时就知道该次按键按下了多长时间.

hotplug.json 中有描述, 如果 uevent 中含有 BUTTON 字符串, 而且 SUBSYSTEM 为 "button", 则执行 /etc/rc.button/ 下的 %BUTTON% 脚本来处理.

```
        [ "if",
                [ "and",
                        [ "has", "BUTTON" ],
                        [ "eq", "SUBSYSTEM", "button" ],
                ],
                [ "exec", "/etc/rc.button/%BUTTON%" ]
        ],
```

## Hotplug

由内核发出 event 事件

1. `kobject_uevent()` 产生 uevent 事件(`lib/kobject_uevent.c` 中), 产生的 uevent 先由 `netlink_broadcast_filtered()` 发出, 最后调用 `uevent_helper[]` 所指定的程序来处理.
2. `uevent_helper[]` 里默认指定 `/sbin/hotplug`, 但可以通过 `/sys/kernel/uevent_helper` (`kernel/ksysfs.c`) 或 `/proc/kernel/uevent_helper` (`kernel/sysctl.c`) 来修改成指定的程序.
3. **在 OpenWrt 并不使用 `user_helper[]` 指定程序来处理 uevent (`/sbin/hotplug` 不存在), 而是使用 `PF_NETLINK` 来获取来自内核的 uevent.**

用户空间监听 uevent

`procd/plug/hotplug.c` 中, 创建一个 `PF_NETLINK` 套接字来监听内核 `netlink_broadcast_filtered()` 发出的 uevent.

收到 uevent 之后, 再根据 `/etc/hotplug.json` 里的描述来处理.

通常情况下, `/etc/hotplug.json` 会调用 `/sbin/hotplug-call` 来处理, 它根据 uevent 的 `$SUBSYSTEM` 变量来分别调用 `/etc/hotplug.d/` 下不同目录中的脚本.

> http://www.cnblogs.com/sammei/p/4119659.html

## GPIO

### Init Script for Configuring GPIO

```
/etc/init.d/gpio

#!/bin/sh /etc/rc.common
# Copyright (C) 2006 OpenWrt.org

START=20
boot() {
    [ -d /sys/class/gpio ] && {
        # Custom GPIO Configuration:
        # GW2382 - steer USB from FP to PCIe:
        echo 0 > /sys/class/gpio/gpio10/value
    }
}
```

### 'gpio-button-hotplug` Driver

OpenWrt based BSP's use `procd` as PID1. One of `procd`'s features is to catch messages from the kernel and act upon them, much in the way a conventional linux system uses a hotplug helper and udev.
The `/etc/hotplug.json` file configures how various events are handled:

- using makedev to create device nodes in /dev on 'add' events and removing the nodes on 'remove' events
- facilitate firmware loading
- calling `/sbin/hotplug-call` on platform subsystem events
- calling `/etc/rc.button/$BUTTON` on button events
- calling `/sbin/hotplug-call` on various subsystem events (such as net, input, usb, block, atm, tty, button)

Button events:

- `/sbin/hotplug-call` will have the following defined:
  - `BUTTON` - button name
  - `ACTION` - pressed|released
  - `SEQNUM` - a numeric value that increments with the event message
  - `SEEN`   - number of seconds since last button event from driver

    this can be used to determine how long a button has been 'held' for 'released' events. If a button is released and `SEEN=8` it has been held for 8 seconds

The `gpio-button-hotplug` driver is the Linux kernel driver that creates hotplug events for GPIO based buttons.
The `gpio-button-hotplug` out-of-tree driver is an OpenWrt custom driver that takes the place of the in-kernel `gpio-keys` (`KEYBOARD_GPIO`) and `gpio_keys_polled` (`KEYBOARD_GPIO_POLLED`) drivers and instead of emiting linux input events it emits uevent messages to the button subsystem which tie into the OpenWrt hotplug daemon.

## LED

## Reference

- https://wiki.openwrt.org/doc/packages
- https://wiki.openwrt.org/doc/techref/opkg
