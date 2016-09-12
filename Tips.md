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


## Reference

- https://wiki.openwrt.org/doc/packages
- https://wiki.openwrt.org/doc/techref/opkg
