# Tips

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


## Reference

- https://wiki.openwrt.org/doc/packages
- https://wiki.openwrt.org/doc/techref/opkg
