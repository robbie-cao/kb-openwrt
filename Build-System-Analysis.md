# OpenWrt Build System Analysis

## Package Install

In package Makefile:

```
define Package/libmraa/install
        $(INSTALL_DIR) $(1)/usr/lib/{node/mraa,python2.7/site-packages} $(1)/usr/bin
        $(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/lib/libmraa.so* $(1)/usr/lib/
        $(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/lib/node_modules/mraa/* $(1)/usr/lib/node/mraa/
        $(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/lib/python2.7/site-packages/* $(1)/usr/lib/python2.7/site-packages/
endef
```

> https://github.com/openwrt/packages/blob/master/libs/libmraa/Makefile

In `include/package-ipk.mk`:

```
    $(PKG_INFO_DIR)/$(1).provides: $$(IPKG_$(1))
    $$(IPKG_$(1)) : export CONTROL=$$(Package/$(1)/CONTROL)
    $$(IPKG_$(1)) : export DESCRIPTION=$$(Package/$(1)/description)
    $$(IPKG_$(1)): $(STAMP_BUILT) $(INCLUDE_DIR)/package-ipkg.mk
        @rm -rf $$(PDIR_$(1))/$(1)_* $$(IDIR_$(1))
        mkdir -p $(PACKAGE_DIR) $$(IDIR_$(1))/CONTROL $(PKG_INFO_DIR)
        $(call Package/$(1)/install,$$(IDIR_$(1)))
        -find $$(IDIR_$(1)) -name 'CVS' -o -name '.svn' -o -name '.#*' -o -name '*~'| $(XARGS) rm -rf
        @( \
                find $$(IDIR_$(1)) -name lib\*.so\* -or -name \*.ko | awk -F/ '{ print $$$$NF }'; \
                for file in $$(patsubst %,$(PKG_INFO_DIR)/%.provides,$$(IDEPEND_$(1))); do \
                        if [ -f "$$$$file" ]; then \
                                cat $$$$file; \
                        fi; \
                done; $(Package/$(1)/extra_provides) \
        ) | sort -u > $(PKG_INFO_DIR)/$(1).provides
        $(if $(PROVIDES),@for pkg in $(PROVIDES); do cp $(PKG_INFO_DIR)/$(1).provides $(PKG_INFO_DIR)/$$$$pkg.provides; done)
        $(CheckDependencies)

        $(RSTRIP) $$(IDIR_$(1))
        (cd $$(IDIR_$(1))/CONTROL; \
                ( \
                        echo "$$$$CONTROL"; \
                        printf "Description: "; echo "$$$$DESCRIPTION" | sed -e 's,^[[:space:]]*, ,g'; \
                ) > control; \
                chmod 644 control; \
                ( \
                        echo "#!/bin/sh"; \
                        echo "[ \"\$$$${IPKG_NO_SCRIPT}\" = \"1\" ] && exit 0"; \
                        echo ". \$$$${IPKG_INSTROOT}/lib/functions.sh"; \
                        echo "default_postinst \$$$$0 \$$$$@"; \
                ) > postinst; \
                ( \
                        echo "#!/bin/sh"; \
                        echo ". \$$$${IPKG_INSTROOT}/lib/functions.sh"; \
                        echo "default_prerm \$$$$0 \$$$$@"; \
                ) > prerm; \
                chmod 0755 postinst prerm; \
                $($(1)_COMMANDS) \
        )

        ...
		...
		...

        $(INSTALL_DIR) $$(PDIR_$(1))
        $(IPKG_BUILD) $$(IDIR_$(1)) $$(PDIR_$(1))
        @[ -f $$(IPKG_$(1)) ]

		...
		...
```

CheckDependencies:

```
  define CheckDependencies
        @( \
                rm -f $(PKG_INFO_DIR)/$(1).missing; \
                ( \
                        export \
                                READELF=$(TARGET_CROSS)readelf \
                                OBJCOPY=$(TARGET_CROSS)objcopy \
                                XARGS="$(XARGS)"; \
                        $(SCRIPT_DIR)/gen-dependencies.sh "$$(IDIR_$(1))"; \
                ) | while read FILE; do \
                        grep -qxF "$$$$FILE" $(PKG_INFO_DIR)/$(1).provides || \
                                echo "$$$$FILE" >> $(PKG_INFO_DIR)/$(1).missing; \
                done; \
                if [ -f "$(PKG_INFO_DIR)/$(1).missing" ]; then \
                        echo "Package $(1) is missing dependencies for the following libraries:" >&2; \
                        cat "$(PKG_INFO_DIR)/$(1).missing" >&2; \
                        false; \
                fi; \
        )
  endef
```

Real case:

```
find /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa -name 'CVS' -o -name '.svn' -o -name '.#*' -o -name '*~'| xargs -r rm -rf

# generate xxx.provides in staging_dir/target-<...>/pkginfo/<package>.provides
( find /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa -name lib\*.so\* -or -name \*.ko | awk -F/ '{ print $NF }'; for file in /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libc.provides /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/python.provides /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libstdcpp.provides; do if [ -f "$file" ]; then cat $file; fi; done;  ) | sort -u > /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.provides

# check missing dependencies
( rm -f /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.missing; ( export READELF=mipsel-openwrt-linux-uclibc-readelf OBJCOPY=mipsel-openwrt-linux-uclibc-objcopy XARGS="xargs -r"; /home/robbie/github/o2/scripts/gen-dependencies.sh "/home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa"; ) | while read FILE; do grep -qxF "$FILE" /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.provides || echo "$FILE" >> /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.missing; done; if [ -f "/home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.missing" ]; then echo "Package libmraa is missing dependencies for the following libraries:" >&2; cat "/home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.missing" >&2; false; fi; )

# ...
export CROSS="mipsel-openwrt-linux-uclibc-" NO_RENAME=1 ; NM="mipsel-openwrt-linux-uclibc-nm" STRIP="/home/robbie/github/o2/staging_dir/host/bin/sstrip" STRIP_KMOD="/home/robbie/github/o2/scripts/strip-kmod.sh" PATCHELF="/home/robbie/github/o2/staging_dir/host/bin/patchelf" /home/robbie/github/o2/scripts/rstrip.sh /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa
rstrip.sh: /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa/usr/lib/python2.7/site-packages/_mraa.so: shared object
rstrip.sh: /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa/usr/lib/node/mraa/mraa.node: shared object
rstrip.sh: /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa/usr/lib/libmraa.so: shared object
rstrip.sh: /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa/usr/lib/libmraa.so.0: shared object
rstrip.sh: /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa/usr/lib/libmraa.so.0.8.0: shared object
(cd /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa/CONTROL; ( echo "$CONTROL"; printf "Description: "; echo "$DESCRIPTION" | sed -e 's,^[[:space:]]*, ,g'; ) > control; chmod 644 control; ( echo "#!/bin/sh"; echo "[ \"\${IPKG_NO_SCRIPT}\" = \"1\" ] && exit 0"; echo ". \${IPKG_INSTROOT}/lib/functions.sh"; echo "default_postinst \$0 \$@"; ) > postinst; ( echo "#!/bin/sh"; echo ". \${IPKG_INSTROOT}/lib/functions.sh"; echo "default_prerm \$0 \$@"; ) > prerm; chmod 0755 postinst prerm;  )

# install package
install -d -m0755 /home/robbie/github/o2/bin/ramips/packages/packages
/home/robbie/github/o2/scripts/ipkg-build -c -o 0 -g 0 /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa /home/robbie/github/o2/bin/ramips/packages/packages
Packaged contents of /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-ramips_24kec/libmraa into /home/robbie/github/o2/bin/ramips/packages/packages/libmraa_0.8.0-70600dece4138b0c0dbaff42f57828f1559cd840_ramips_24kec.ipk
rm -rf /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/tmp-libmraa
mkdir -p /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/stamp /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/tmp-libmraa
install -d -m0755 /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/tmp-libmraa/usr/lib/{node/mraa,python2.7/site-packages} /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/tmp-libmraa/usr/bin
install -m0755 /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-install/usr/lib/libmraa.so* /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/tmp-libmraa/usr/lib/
install -m0755 /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-install/usr/lib/node_modules/mraa/* /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/tmp-libmraa/usr/lib/node/mraa/
install -m0755 /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-install/usr/lib/python2.7/site-packages/* /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/tmp-libmraa/usr/lib/python2.7/site-packages/
SHELL= /home/robbie/github/o2/staging_dir/host/bin/flock /home/robbie/github/o2/tmp/.root-copy.flock -c 'cp -fpR /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/tmp-libmraa/. /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/'
rm -rf /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/tmp-libmraa

# post install
touch /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/root-ramips/stamp/.libmraa_installed
if [ -f /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.default.install.clean ]; then rm -f /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.default.install /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.default.install.clean; fi; echo "libmraa" >> /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/pkginfo/libmraa.default.install
rm -rf /home/robbie/github/o2/tmp/stage-libmraa
mkdir -p /home/robbie/github/o2/tmp/stage-libmraa/host /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/packages /home/robbie/github/o2/staging_dir/host/packages
install -d -m0755 /home/robbie/github/o2/tmp/stage-libmraa
cp -fpR /home/robbie/github/o2/build_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/libmraa-0.8.0/ipkg-install/* /home/robbie/github/o2/tmp/stage-libmraa/
find /home/robbie/github/o2/tmp/stage-libmraa -name '*.la' | xargs -r rm -f; 
if [ -f /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/packages/libmraa.list ]; then /home/robbie/github/o2/scripts/clean-package.sh "/home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/packages/libmraa.list" "/home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2"; fi
if [ -d /home/robbie/github/o2/tmp/stage-libmraa ]; then (cd /home/robbie/github/o2/tmp/stage-libmraa; find ./ > /home/robbie/github/o2/tmp/stage-libmraa.files);       SHELL= /home/robbie/github/o2/staging_dir/host/bin/flock /home/robbie/github/o2/tmp/.staging-dir.flock -c ' mv /home/robbie/github/o2/tmp/stage-libmraa.files /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/packages/libmraa.list && cp -fpR /home/robbie/github/o2/tmp/stage-libmraa/* /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/; '; fi
rm -rf /home/robbie/github/o2/tmp/stage-libmraa
touch /home/robbie/github/o2/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/stamp/.libmraa_installed
```

Generate dependencies (`scripts/gen-dependencies.sh`):

```
#!/usr/bin/env bash
#
# Copyright (C) 2012 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
SELF=${0##*/}

READELF="${READELF:-readelf}"
OBJCOPY="${OBJCOPY:-objcopy}"
TARGETS=$*
XARGS="${XARGS:-xargs -r}"

[ -z "$TARGETS" ] && {
  echo "$SELF: no directories / files specified"
  echo "usage: $SELF [PATH...]"
  exit 1
}

find $TARGETS -type f -a -exec file {} \; | \
  sed -n -e 's/^\(.*\):.*ELF.*\(executable\|shared object\).*,.* stripped/\1/p' | \
  $XARGS -n1 $READELF -d | \
  awk '$2 ~ /NEEDED/ && $NF !~ /interpreter/ && $NF ~ /^\[?lib.*\.so/ { gsub(/[\[\]]/, "", $NF); print $NF }' | \
  sort -u

tmp=`mktemp $TMP_DIR/dep.XXXXXXXX`
for kmod in `find $TARGETS -type f -name \*.ko`; do
        $OBJCOPY -O binary -j .modinfo $kmod $tmp
        sed -e 's,\x00,\n,g' $tmp | \
                sed -ne '/^depends=.\+/ { s/^depends=//; s/,/.ko\n/g; s/$/.ko/p; q }'
done | sort -u
rm -f $tmp
```

->

```
find . -type f -a -exec file {} \; | sed -n -e 's/^\(.*\):.*ELF.*\(executable\|shared object\).*,.* stripped/\1/p' | xargs -n1 readelf -d | awk '$2 ~ /NEEDED/ && $NF !~ /interpreter/ && $NF ~ /^\[?lib.*\.so/ { gsub(/[\[\]]/, "", $NF); print $NF }' | sort -u
```

