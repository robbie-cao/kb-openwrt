# MT7688 Pin

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
Group ephy - [ephy] gpio
Group wled - [wled] gpio

root@mua:~# ls -l /sys/kernel/debug/pinctrl/
drwxr-xr-x    2 root     root             0 Jan  1  1970 pinctrl
-r--r--r--    1 root     root             0 Jan  1  1970 pinctrl-devices
-r--r--r--    1 root     root             0 Jan  1  1970 pinctrl-handles
-r--r--r--    1 root     root             0 Jan  1  1970 pinctrl-maps

root@mua:~# ls -l /sys/kernel/debug/pinctrl/pinctrl/
-r--r--r--    1 root     root             0 Jan  1  1970 gpio-ranges
-r--r--r--    1 root     root             0 Jan  1  1970 pingroups
-r--r--r--    1 root     root             0 Jan  1  1970 pinmux-functions
-r--r--r--    1 root     root             0 Jan  1  1970 pinmux-pins
-r--r--r--    1 root     root             0 Jan  1  1970 pins


root@mua:~# cat /sys/kernel/debug/pinctrl/pinctrl-devices
name [pinmux] [pinconf]
rt2880-pinmux yes no

root@mua:~# cat /sys/kernel/debug/pinctrl/pinctrl-handles
Requested pin control handlers their pinmux maps:
device: pinctrl current state: default
  state: default
    type: MUX_GROUP controller rt2880-pinmux group: gpio (14) function: gpio (0)
    type: MUX_GROUP controller rt2880-pinmux group: perst (6) function: gpio (0)
    type: MUX_GROUP controller rt2880-pinmux group: refclk (5) function: gpio (0)
    type: MUX_GROUP controller rt2880-pinmux group: i2s (11) function: gpio (0)
    type: MUX_GROUP controller rt2880-pinmux group: spis (13) function: gpio (0)
    type: MUX_GROUP controller rt2880-pinmux group: wled_an (15) function: wled_an (52)
    type: MUX_GROUP controller rt2880-pinmux group: wdt (7) function: gpio (0)
device: 10005000.pwm current state: default
  state: default
    type: MUX_GROUP controller rt2880-pinmux group: pwm0 (1) function: pwm0 (8)
    type: MUX_GROUP controller rt2880-pinmux group: pwm1 (0) function: pwm1 (4)
device: 10000c00.uartlite current state: default
  state: default
    type: MUX_GROUP controller rt2880-pinmux group: uart0 (10) function: uart0 (32)
device: 10000d00.uart1 current state: default
  state: default
    type: MUX_GROUP controller rt2880-pinmux group: uart1 (3) function: uart1 (16)
device: 10000e00.uart2 current state: default
  state: default
    type: MUX_GROUP controller rt2880-pinmux group: uart2 (2) function: uart2 (12)
device: 10000b00.spi current state: default
  state: default
    type: MUX_GROUP controller rt2880-pinmux group: spi (8) function: spi (24)
    type: MUX_GROUP controller rt2880-pinmux group: spi cs1 (12) function: spi cs1 (40)
device: 10130000.sdhci current state: default
  state: default
    type: MUX_GROUP controller rt2880-pinmux group: sdmode (9) function: sdxc (28)
device: 10000900.i2c current state: default
  state: default
    type: MUX_GROUP controller rt2880-pinmux group: i2c (4) function: i2c (20)


root@mua:~# cat /sys/kernel/debug/pinctrl/pinctrl-maps
Pinctrl maps:
device pinctrl
state default
type MUX_GROUP (2)
controlling device pinctrl
group gpio
function gpio

device pinctrl
state default
type MUX_GROUP (2)
controlling device pinctrl
group perst
function gpio

device pinctrl
state default
type MUX_GROUP (2)
controlling device pinctrl
group refclk
function gpio

device pinctrl
state default
type MUX_GROUP (2)
controlling device pinctrl
group i2s
function gpio

device pinctrl
state default
type MUX_GROUP (2)
controlling device pinctrl
group spis
function gpio

device pinctrl
state default
type MUX_GROUP (2)
controlling device pinctrl
group wled_kn
function gpio

device pinctrl
state default
type MUX_GROUP (2)
controlling device pinctrl
group wled_an
function wled_an

device pinctrl
state default
type MUX_GROUP (2)
controlling device pinctrl
group wdt
function gpio

device 10005000.pwm
state default
type MUX_GROUP (2)
controlling device pinctrl
group pwm0
function pwm0

device 10005000.pwm
state default
type MUX_GROUP (2)
controlling device pinctrl
group pwm1
function pwm1

device 10000c00.uartlite
state default
type MUX_GROUP (2)
controlling device pinctrl
group uart0
function uart0

device 10000d00.uart1
state default
type MUX_GROUP (2)
controlling device pinctrl
group uart1
function uart1

device 10000e00.uart2
state default
type MUX_GROUP (2)
controlling device pinctrl
group uart2
function uart2

device 10000b00.spi
state default
type MUX_GROUP (2)
controlling device pinctrl
group spi
function spi

device 10000b00.spi
state default
type MUX_GROUP (2)
controlling device pinctrl
group spi cs1
function spi cs1

device 10130000.sdhci
state default
type MUX_GROUP (2)
controlling device pinctrl
group sdmode
function sdxc

device 10000900.i2c
state default
type MUX_GROUP (2)
controlling device pinctrl
group i2c
function i2c


root@mua:~# cat /sys/kernel/debug/pinctrl/pinctrl/pins
registered pins: 47
pin 0 (io0) ralink pio
pin 1 (io1) ralink pio
pin 2 (io2) ralink pio
pin 3 (io3) ralink pio
pin 4 (io4) ralink pio
pin 5 (io5) ralink pio
pin 6 (io6) ralink pio
pin 7 (io7) ralink pio
pin 8 (io8) ralink pio
pin 9 (io9) ralink pio
pin 10 (io10) ralink pio
pin 11 (io11) ralink pio
pin 12 (io12) ralink pio
pin 13 (io13) ralink pio
pin 14 (io14) ralink pio
pin 15 (io15) ralink pio
pin 16 (io16) ralink pio
pin 17 (io17) ralink pio
pin 18 (io18) ralink pio
pin 19 (io19) ralink pio
pin 20 (io20) ralink pio
pin 21 (io21) ralink pio
pin 22 (io22) ralink pio
pin 23 (io23) ralink pio
pin 24 (io24) ralink pio
pin 25 (io25) ralink pio
pin 26 (io26) ralink pio
pin 27 (io27) ralink pio
pin 28 (io28) ralink pio
pin 29 (io29) ralink pio
pin 30 (io30) ralink pio
pin 31 (io31) ralink pio
pin 32 (io32) ralink pio
pin 33 (io33) ralink pio
pin 34 (io34) ralink pio
pin 35 (io35) ralink pio
pin 36 (io36) ralink pio
pin 37 (io37) ralink pio
pin 38 (io38) ralink pio
pin 39 (io39) ralink pio
pin 40 (io40) ralink pio
pin 41 (io41) ralink pio
pin 42 (io42) ralink pio
pin 43 (io43) ralink pio
pin 44 (io44) ralink pio
pin 45 (io45) ralink pio
pin 46 (io46) ralink pio


root@mua:~# cat /sys/kernel/debug/pinctrl/pinctrl/pingroups
registered pin groups:
group: pwm1
pin 19 (io19)

group: pwm0
pin 18 (io18)

group: uart2
pin 20 (io20)
pin 21 (io21)

group: uart1
pin 45 (io45)
pin 46 (io46)

group: i2c
pin 4 (io4)
pin 5 (io5)

group: refclk
pin 37 (io37)

group: perst
pin 36 (io36)

group: wdt
pin 38 (io38)

group: spi
pin 7 (io7)
pin 8 (io8)
pin 9 (io9)
pin 10 (io10)

group: sdmode
pin 22 (io22)
pin 23 (io23)
pin 24 (io24)
pin 25 (io25)
pin 26 (io26)
pin 27 (io27)
pin 28 (io28)
pin 29 (io29)

group: uart0
pin 12 (io12)
pin 13 (io13)

group: i2s
pin 0 (io0)
pin 1 (io1)
pin 2 (io2)
pin 3 (io3)

group: spi cs1
pin 6 (io6)

group: spis
pin 14 (io14)
pin 15 (io15)
pin 16 (io16)
pin 17 (io17)

group: gpio
pin 11 (io11)

group: wled_an
pin 44 (io44)

group: ephy_p1
pin 42 (io42)

group: ephy_p2
pin 41 (io41)

group: ephy_p3
pin 40 (io40)

group: ephy_p4
pin 39 (io39)


root@mua:~# cat /sys/kernel/debug/pinctrl/pinctrl/pinmux-functions
function: gpio, groups = [ pwm1 pwm0 uart2 uart1 i2c refclk perst wdt spi sdmode uart0 i2s spi cs1 spis gpio wled_an ephy_p1 ephy_p2 ephy_p3 ephy_p4 ]
function: sdxc d6, groups = [ pwm1 ]
function: utif, groups = [ pwm1 ]
function: gpio, groups = [ pwm1 ]
function: pwm1, groups = [ pwm1 ]
function: sdxc d7, groups = [ pwm0 ]
function: utif, groups = [ pwm0 ]
function: gpio, groups = [ pwm0 ]
function: pwm0, groups = [ pwm0 ]
function: sdxc d5 d4, groups = [ uart2 ]
function: pwm, groups = [ uart2 ]
function: gpio, groups = [ uart2 ]
function: uart2, groups = [ uart2 ]
function: sw_r, groups = [ uart1 ]
function: pwm, groups = [ uart1 ]
function: gpio, groups = [ uart1 ]
function: uart1, groups = [ uart1 ]
function: -, groups = [ i2c ]
function: debug, groups = [ i2c ]
function: gpio, groups = [ i2c ]
function: i2c, groups = [ i2c ]
function: reclk, groups = [ refclk ]
function: perst, groups = [ perst ]
function: wdt, groups = [ wdt ]
function: spi, groups = [ spi ]
function: jtag, groups = [ sdmode ]
function: utif, groups = [ sdmode ]
function: gpio, groups = [ sdmode ]
function: sdxc, groups = [ sdmode ]
function: -, groups = [ uart0 ]
function: -, groups = [ uart0 ]
function: gpio, groups = [ uart0 ]
function: uart0, groups = [ uart0 ]
function: antenna, groups = [ i2s ]
function: pcm, groups = [ i2s ]
function: gpio, groups = [ i2s ]
function: i2s, groups = [ i2s ]
function: -, groups = [ spi cs1 ]
function: refclk, groups = [ spi cs1 ]
function: gpio, groups = [ spi cs1 ]
function: spi cs1, groups = [ spi cs1 ]
function: pwm, groups = [ spis ]
function: util, groups = [ spis ]
function: gpio, groups = [ spis ]
function: spis, groups = [ spis ]
function: pcie, groups = [ gpio ]
function: refclk, groups = [ gpio ]
function: gpio, groups = [ gpio ]
function: gpio, groups = [ gpio ]
function: rsvd, groups = [ wled_an ]
function: rsvd, groups = [ wled_an ]
function: gpio, groups = [ wled_an ]
function: wled_an, groups = [ wled_an ]
function: rsvd, groups = [ ephy_p1 ]
function: rsvd, groups = [ ephy_p1 ]
function: gpio, groups = [ ephy_p1 ]
function: ephy, groups = [ ephy_p1 ]
function: rsvd, groups = [ ephy_p2 ]
function: rsvd, groups = [ ephy_p2 ]
function: gpio, groups = [ ephy_p2 ]
function: ephy, groups = [ ephy_p2 ]
function: rsvd, groups = [ ephy_p3 ]
function: rsvd, groups = [ ephy_p3 ]
function: gpio, groups = [ ephy_p3 ]
function: ephy, groups = [ ephy_p3 ]
function: rsvd, groups = [ ephy_p4 ]
function: rsvd, groups = [ ephy_p4 ]
function: gpio, groups = [ ephy_p4 ]
function: ephy, groups = [ ephy_p4 ]


root@mua:/# cat /sys/kernel/debug/pinctrl/pinctrl/pinmux-pins
Pinmux settings per pin
Format: pin (name): mux_owner gpio_owner hog?
pin 0 (io0): pinctrl (GPIO UNCLAIMED) function gpio group i2s
pin 1 (io1): pinctrl (GPIO UNCLAIMED) function gpio group i2s
pin 2 (io2): pinctrl (GPIO UNCLAIMED) function gpio group i2s
pin 3 (io3): pinctrl (GPIO UNCLAIMED) function gpio group i2s
pin 4 (io4): 10000900.i2c (GPIO UNCLAIMED) function i2c group i2c
pin 5 (io5): 10000900.i2c (GPIO UNCLAIMED) function i2c group i2c
pin 6 (io6): 10000b00.spi (GPIO UNCLAIMED) function spi cs1 group spi cs1
pin 7 (io7): 10000b00.spi (GPIO UNCLAIMED) function spi group spi
pin 8 (io8): 10000b00.spi (GPIO UNCLAIMED) function spi group spi
pin 9 (io9): 10000b00.spi (GPIO UNCLAIMED) function spi group spi
pin 10 (io10): 10000b00.spi (GPIO UNCLAIMED) function spi group spi
pin 11 (io11): pinctrl (GPIO UNCLAIMED) function gpio group gpio
pin 12 (io12): 10000c00.uartlite (GPIO UNCLAIMED) function uart0 group uart0
pin 13 (io13): 10000c00.uartlite (GPIO UNCLAIMED) function uart0 group uart0
pin 14 (io14): pinctrl (GPIO UNCLAIMED) function gpio group spis
pin 15 (io15): pinctrl (GPIO UNCLAIMED) function gpio group spis
pin 16 (io16): pinctrl (GPIO UNCLAIMED) function gpio group spis
pin 17 (io17): pinctrl (GPIO UNCLAIMED) function gpio group spis
pin 18 (io18): 10005000.pwm (GPIO UNCLAIMED) function pwm0 group pwm0
pin 19 (io19): 10005000.pwm (GPIO UNCLAIMED) function pwm1 group pwm1
pin 20 (io20): 10000e00.uart2 (GPIO UNCLAIMED) function uart2 group uart2
pin 21 (io21): 10000e00.uart2 (GPIO UNCLAIMED) function uart2 group uart2
pin 22 (io22): 10130000.sdhci (GPIO UNCLAIMED) function sdxc group sdmode
pin 23 (io23): 10130000.sdhci (GPIO UNCLAIMED) function sdxc group sdmode
pin 24 (io24): 10130000.sdhci (GPIO UNCLAIMED) function sdxc group sdmode
pin 25 (io25): 10130000.sdhci (GPIO UNCLAIMED) function sdxc group sdmode
pin 26 (io26): 10130000.sdhci (GPIO UNCLAIMED) function sdxc group sdmode
pin 27 (io27): 10130000.sdhci (GPIO UNCLAIMED) function sdxc group sdmode
pin 28 (io28): 10130000.sdhci (GPIO UNCLAIMED) function sdxc group sdmode
pin 29 (io29): 10130000.sdhci (GPIO UNCLAIMED) function sdxc group sdmode
pin 30 (io30): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 31 (io31): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 32 (io32): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 33 (io33): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 34 (io34): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 35 (io35): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 36 (io36): pinctrl (GPIO UNCLAIMED) function gpio group perst
pin 37 (io37): pinctrl (GPIO UNCLAIMED) function gpio group refclk
pin 38 (io38): pinctrl (GPIO UNCLAIMED) function gpio group wdt
pin 39 (io39): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 40 (io40): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 41 (io41): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 42 (io42): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 43 (io43): (MUX UNCLAIMED) (GPIO UNCLAIMED)
pin 44 (io44): pinctrl (GPIO UNCLAIMED) function wled_an group wled_an
pin 45 (io45): 10000d00.uart1 (GPIO UNCLAIMED) function uart1 group uart1
pin 46 (io46): 10000d00.uart1 (GPIO UNCLAIMED) function uart1 group uart1

```

