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

## Reference

- https://wiki.openwrt.org/doc/packages
- https://wiki.openwrt.org/doc/techref/opkg
