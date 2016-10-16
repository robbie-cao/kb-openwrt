# Enable libnfc on OpenWRT

## Source

https://github.com/nfc-tools/libnfc

## Patch

https://github.com/openwrt/packages/tree/for-15.05/

```
diff --git a/libs/libnfc/Makefile b/libs/libnfc/Makefile
index 9a144d2..c5369cc 100644
--- a/libs/libnfc/Makefile
+++ b/libs/libnfc/Makefile
@@ -15,7 +15,7 @@ PKG_FIXUP:=autoreconf
 
 PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
 PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
-PKG_SOURCE_URL:=https://code.google.com/p/libnfc/
+PKG_SOURCE_URL:=https://github.com/nfc-tools/libnfc
 PKG_SOURCE_PROTO:=git
 PKG_SOURCE_VERSION:=$(PKG_NAME)-$(PKG_VERSION)
```

> Switch git repo from google code to github as google code is shutdown from 2016.

## Compile

```
$ make menuconfig
  -> Choose `libnfc` (Libraries --> libnfc)
  -> Choose `nfc-utils` (Utilities --> nfc-utils)
```

```
diff --git a/.config b/.config
index d11c628..515f3fa 100644
--- a/.config
+++ b/.config
@@ -2252,7 +2252,7 @@ CONFIG_PACKAGE_alsa-lib=y
 # CONFIG_PACKAGE_bind-libs is not set
 # CONFIG_PACKAGE_bluez-libs is not set
 # CONFIG_PACKAGE_boost is not set
-# CONFIG_PACKAGE_ccid is not set
+CONFIG_PACKAGE_ccid=y
 # CONFIG_PACKAGE_check is not set
 # CONFIG_PACKAGE_classpath is not set
 # CONFIG_PACKAGE_classpath-tools is not set
@@ -2418,7 +2418,7 @@ CONFIG_PACKAGE_libncurses=y
 # CONFIG_PACKAGE_libnetfilter-queue is not set
 # CONFIG_PACKAGE_libnetsnmp is not set
 # CONFIG_PACKAGE_libnettle is not set
-# CONFIG_PACKAGE_libnfc is not set
+CONFIG_PACKAGE_libnfc=y
 # CONFIG_PACKAGE_libnfnetlink is not set
 # CONFIG_PACKAGE_libnftnl is not set
 # CONFIG_PACKAGE_libnl is not set
@@ -2443,7 +2443,7 @@ CONFIG_PACKAGE_libpcap=y
 # CONFIG_PACKAGE_libpcre is not set
 # CONFIG_PACKAGE_libpcre16 is not set
 # CONFIG_PACKAGE_libpcrecpp is not set
-# CONFIG_PACKAGE_libpcsclite is not set
+CONFIG_PACKAGE_libpcsclite=y
 # CONFIG_PACKAGE_libpkcs11-spy is not set
 # CONFIG_PACKAGE_libplist is not set
 # CONFIG_PACKAGE_libplistcxx is not set
@@ -3826,7 +3826,7 @@ CONFIG_PACKAGE_libjson-script=y
 CONFIG_PACKAGE_mountd=y
 # CONFIG_PACKAGE_mpack is not set
 # CONFIG_PACKAGE_namei is not set
-# CONFIG_PACKAGE_nfc-utils is not set
+CONFIG_PACKAGE_nfc-utils=y
 # CONFIG_PACKAGE_ocf-crypto-headers is not set
 # CONFIG_PACKAGE_open-plc-utils is not set
 # CONFIG_PACKAGE_openldap-utils is not set
@@ -3838,7 +3838,7 @@ CONFIG_PACKAGE_mountd=y
 # CONFIG_PACKAGE_opus-tools is not set
 # CONFIG_PACKAGE_owipcalc is not set
 # CONFIG_PACKAGE_pciutils is not set
-# CONFIG_PACKAGE_pcscd is not set
+CONFIG_PACKAGE_pcscd=y
 # CONFIG_PACKAGE_pps-tools is not set
 # CONFIG_PACKAGE_procps is not set
 # CONFIG_PACKAGE_pv is not set
```

## Install and Test

### PN532

To use PN532 with OpenWrt, a configure file need to be configured.

```
$ make -p /etc/nfc/devices.d
$ vi /etc/nfc/libnfc.conf
# editing...
# replace /dev/ttyUSB0 with the right device of yours
allow_autoscan = true
allow_intrusive_scan = false
log_level = 1
device.name = "PN532 Module"
device.connstring = "pn532_uart:/dev/ttyUSB0"
```

Test

Read tag:

```
nfc-list
```
