# Mua OpenWrt

## Project List

- git@git.muabaobao.com:depot/mua-openwrt.git
- git@git.muabaobao.com:depot/mua-openwrt-feed.git
- git@git.muabaobao.com:depot/mua-mtk-lks7688-feed.git
- git@git.muabaobao.com:depot/mua-uboot-7688.git
- git@git.muabaobao.com:depot/linux-3.18.29.git

## Setup

### openwrt

```
$ git clone git@git.muabaobao.com:depot/mua-openwrt.git
```

### uboot

```
$ git clone git@git.muabaobao.com:depot/mua-uboot-7688.git
```

### kernel

```
$ git git@git.muabaobao.com:depot/linux-3.18.29.git
```

## Build

```
$ cd openwrt

# checkout a branch for developing board
$ git checkout -b board/widora --track origin/board/widora

# use mua feeds mirror
$ cp -f feeds.config.mua feeds.config

# update and install feeds
$ ./scripts/feeds update -a
$ ./scripts/feeds install -a

# config
# there's a default .config already
# use the following command to change to other config
$ cp -f config.default .config
-or-
$ cp -f config.dev.kernel .config
-or-
$ make menuconfig

# copy download src/tool/packages tarball
$ cp -fr /home/mua/dl .

# start make
$ make -j2 V=s
```

