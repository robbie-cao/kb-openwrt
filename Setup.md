# OpenWrt Develop Environment Setup

## OS

OpenWrt build system is the buildsystem for the OpenWrt Linux distribution. OpenWrt build system works on Linux, BSD or MacOSX operating system. A case-sensitive filesystem is required.

It is recommended that you use a **Linux** distribution (Debian or Ubuntu), either a standalone installation or one running in a virtual environment (VirtualBox or VMware).

## Toolchain

  ```
  sudo apt-get update
  sudo apt-get install git-core build-essential libssl-dev libncurses5-dev unzip gawk
  sudo apt-get install subversion mercurial
  ```

> https://wiki.openwrt.org/doc/howto/buildroot.exigence

## Download Source Code

1. Init source

   Clone openwrt source from GitHub

   ```
   git clone git@github.com:openwrt/openwrt.git
   ```

   Or from https://git.openwrt.org

   ```
   git clone https://git.openwrt.org/openwrt.git
   ```

   Sometime GitHub may be very slow :-(.

   To speed up code downloading, you can copy the source repo from share drive at `\\mua\文档\嵌入式\openwrt\openwrt.tar.bz2`.

   ```
   $ mkdir openwrt
   $ tar jxvf openwrt.tar.bz2 -C openwrt
   $ cd openwrt
   $ git reset --hard
   ```

2. Modify `feeds.conf.default` to the following settings:

   ```
   src-git packages https://github.com/openwrt/packages.git;for-15.05
   src-git luci https://github.com/openwrt/luci.git;for-15.05
   src-git routing https://github.com/openwrt-routing/packages.git;for-15.05
   src-git telephony https://github.com/openwrt/telephony.git;for-15.05
   src-git management https://github.com/openwrt-management/packages.git;for-15.05
   #src-git targets https://github.com/openwrt/targets.git
   #src-git oldpackages http://git.openwrt.org/packages.git
   #src-svn xwrt http://x-wrt.googlecode.com/svn/trunk/package
   #src-svn phone svn://svn.openwrt.org/openwrt/feeds/phone
   #src-svn efl svn://svn.openwrt.org/openwrt/feeds/efl
   #src-svn xorg svn://svn.openwrt.org/openwrt/feeds/xorg
   #src-svn desktop svn://svn.openwrt.org/openwrt/feeds/desktop
   #src-svn xfce svn://svn.openwrt.org/openwrt/feeds/xfce
   #src-svn lxde svn://svn.openwrt.org/openwrt/feeds/lxde
   #src-link custom /usr/src/openwrt/custom-feed
   ```

   Download [here](https://github.com/robbie-cao/kb-openwrt/raw/master/feeds.conf.default).

3. Copy `\\mua\文档\嵌入式\openwrt\dl` to openwrt source root (optional, **strongly recommended to speed up building**)

4. Now you can start build openwrt

   ```
   $ ./scripts/feeds update -a
   $ ./scripts/feeds install -a
   $ $ make menuconfig
   Select the target:
   Target System (Ralink RT288x/RT3xxx) --->
   Subtarget (MT7688 based boards) --->
   Target Profile(Default Profile) --->

   $ make
   ```

   **NOTE: Target System, Subtarget and Target Profile may be not same as above, select the proper one to match your target board.**

## Reference

