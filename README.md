# Learning OpenWrt

## What's OpenWrt

OpenWrt is a highly extensible GNU/Linux distribution for embedded devices (typically wireless routers).
Unlike many other distributions for these routers, OpenWrt is built from the ground up to be a full-featured,
easily modifiable operating system for your router.

Instead of trying to create a single, static firmware, OpenWrt provides a fully writable filesystem with
optional package management. This frees you from the restrictions of the application selection and
configuration provided by the vendor and allows you to use packages to customize an embedded device
to suit any application. For developers, OpenWrt provides a framework to build an application without
having to create a complete firmware image and distribution around it. For users, this means the freedom
of full customization, allowing the use of an embedded device in ways the vendor never envisioned.

OpenWrt is not intended to be a distribution you can load onto an embedded device and expect to do
everything you want out of the box. Instead, the OpenWrt framework allows you to tailor your embedded
operating system to your own particular needs. At the very least, you should add features you require
to the bare OpenWrt installation by installing software packages, such as a graphical web interface that
provides easy access for beginners.

OpenWrt's objectives:
- OpenWrt will never be a product, but something which makes it easy to build a product.
- OpenWrt will never be finished, never be complete, but will always be tracking the progress of technology.
- OpenWrt will never be specific, it will always remain generic.
- OpenWrt will never be the cathedral, it will simply supply the building blocks to construct one.

> https://wiki.openwrt.org/about/start

## Download

## Branch

Code Name           | Translation | Version    | Status
------------------- | ----------- | ---------- | --------------
Designated Driver   | 代驾人      | developing | trunk
Chaos Calmer        | 平乱人      | 15.05.1    | current stable
Chaos Calmer        |             | 15.05      |
Barrier Breaker     | 破障人      | 14.07      |
Attitude Adjustment | 态度调整    | 12.09      |

> https://wiki.openwrt.org/about/history
>
> https://en.wikipedia.org/wiki/OpenWrt#History

## Build

### Quick Start

```
$ ./scripts/feeds update -a
$ ./scripts/feeds install -a
$ make menuconfig           # config target options for most cases
$ make [-jN] [V=scw]        # -jN optional, N is the number of cpu cores, V=s/c/w for warning, errors and tracing

The parameter V=x specifies level of messages in the process of the build.
    V=99 and V=1 are now deprecated in favor of a new verbosity class system,
    though the old flags are still supported.
    You can set the V variable on the command line (or OPENWRT_VERBOSE in the
    environment) to one or more of the following characters:

    - s: stdout+stderr (equal to the old V=99)
    - c: commands (for build systems that suppress commands by default, e.g. kbuild, cmake)
    - w: warnings/errors only (equal to the old V=1)
```

The OpenWrt build system is a set of Makefiles and patches that allows users to easily
generate both a cross-compilation toolchain and a root filesystem for embedded systems.

### Build System Features
- Makes it easy to port software
- Uses kconfig (Linux Kernel menuconfig) for configuration of features
- Provides integrated cross-compiler toolchain (gcc, ld, ...)
- Provides abstraction for autotools (automake, autoconf), cmake, scons
- Handles standard download, patch, configure, compile and packaging workflow
- Provides a number of common fixups for badly behaving packages

### Make Targets
- Offers a number of high level make targets for standard package workflows
- Targets always in the format "component/name/action", e.g. "toolchain/gdb/compile" or "package/mtd/install"
- Prepare a package source tree: package/foo/prepare
- Compile a package: package/foo/compile
- Clean a package: package/foo/clean

```
  make[2] toolchain/install
  make[3] -C toolchain install
  make[2] target/compile
  make[3] -C target compile
  make[4] -C target/utils prepare
```

> https://wiki.openwrt.org/about/toolchain

### Build Sequence
```
tools - automake, autoconf, sed, cmake
toolchain/binutils - as, ld, ...
toolchain/gcc - gcc, g++, cpp, ...
target/linux - kernel modules
package - core and feed packages
target/linux - kernel image
target/linux/image - firmware image file generation
```

### Make Sequence

Top command make world calls the following sequence of the commands:
```
make target/compile
make package/cleanup
make package/compile
make package/install
make package/preconfig
make target/install
make package/index
```

You may run each command independency. For example, if the process of compilation of packages stops on error, you may fix the problem and next continue without cleanup:
```
make package/compile
make package/install
make package/preconfig
make target/install
make package/index
```

> https://wiki.openwrt.org/doc/techref/buildroot

### Notes About Buildroot / SDK

The OpenWrt project provides **two** main ways to get your software compiled for and running on an OpenWrt device.
- The first option is to download and build the complete toolchain (buildroot)
- The second is to download and/or build the SDK, a stripped down version of the toolchain intended to only build packages, and not firmware images.

> https://github.com/MagnusS/p2p-dprd/wiki/BuildingOpenWrtPackages

> https://forum.openwrt.org/viewtopic.php?pid=31794#p31794

### Usage Example

#### Updating Feeds

```
./scripts/feeds update -a
./scripts/feeds install -a
./scripts/feeds install <PACKAGENAME>
```

#### Config

```
make menuconfig
-or-
make defconfig
```

```
./scripts/diffconfig.sh > diffconfig    # write the changes to diffconfig
cp diffconfig .config                   # write changes to .config
make defconfig                          # expand to full config
```

```
make kernel_menuconfig CONFIG_TARGET=subtarget  # -> kernel config, optional
```

#### Build Images

```
make
-or-
make world
```

#### Clean Up

**Clean**
```
make clean
```

deletes contents of the directories `/bin` and `/build_dir`. `make clean` does not remove the `toolchain`, it also avoids cleaning architectures/targets other than the one you have selected in your `.config`.

**Dirclean**
```
make dirclean
```

deletes contents of the directories `/bin` and `/build_dir` and additionally `/staging_dir` and `/toolchain` (=the cross-compile tools) and `/logs`. 'dirclean' is your basic "full clean" operation.

**Distclean**
```
make distclean
```

nukes everything you have compiled or configured and also deletes all downloaded feeds contents and package sources.

CAUTION: In addition to all else, this will erase your build configuration (<buildroot_dir>/.config), your toolchain and all other sources. Use with care!

**Clean Small Part**

```
make target/linux/clean
make package/base-files/clean
make package/luci/clean
```

#### Make Tips

**Building Single Packages**

```
make package/cups/compile V=s
make package/cups/{clean,compile,install} V=s   # for a rebuild
```

**Spotting Build Errors**

```
make V=s 2>&1 | tee build.log | grep -i error
```

**Getting Beep Notification**

```
make V=s ; echo -e '\a'
```

**Skipping Failed Packages**

```
IGNORE_ERRORS=1 make <make options>
```


> https://wiki.openwrt.org/doc/howto/build

> https://forum.openwrt.org/viewtopic.php?id=28267

## Buildroot


### Overview

![OpenWrt Buildroot Source Tree](images/openwrt_buildroot_source_tree.png)

> The folders in the second lines are generated during compilation.

- **tools** - contains all the build instructions to fetch the image building tools
- **toolchain** - contains all the build instructions to fetch the kernel headers, the C library, the bin-utils, the compiler itself and the debugger. If you add a completely new architecture, you would add a configuration for the C library here.
- **target** - build instruction for firmware image generating process and for the kernel building process; compiles kernel and firmware image utilities, builds firmware image, generate Image Generator (former called Image Builder)
- **package** -  the OpenWrt Makefiles and patches for all the main packages. The OpenWrt Makefile has its own syntax, different from the conventional Makefile of Linux make tool. The OpenWrt Make file defines the meta information of the package, where to download the package, how to compile, where to installed the compiled binaries, etc. See How to Build OpenWrt Application Package for more detail.
- **include** -
- **scripts** -  perl scripts that does the OpenWrt package management
- **dl** - Where the user-space package tarballs will be downloaded
- **build_dir** - where all user-space tools will be cross-compiled
- **staging_dir** -  where the cross-compilation tools will be installed
- **feeds** -
- **bin** -  where the firmware image will be generated and all the .ipk package files will be generated


Simply speaking, once the OpenWrt buildroot has been properly configured, e.g. 
the target platform and architecture is specified, user-space packages selected, etc., the OpenWrt
Buildroot will do the image building through the following steps (once the configuration is done):
1. Download the cross-compilation tools, kernel headers, etc. and
2. Set up the staging directory (staging_dir /). This is where the cross-compilation toolchain will be installed. If you want to use the same cross-compilation toolchain for other purposes, such as compiling third-party applications, you can find the cross-compiler tools in this directory, and then use arch-linux-gcc to compile your application.
3. Create the download directory (dl/ by default). This is where the tarballs will be downloaded.
4. Create the build directory (build_dir/). This is where all user-space tools while be compiled.
5. Create the target directory (build_dir/target-arch/root by default) and the target filesystem skeleton. This directory will contain the final root filesystem.
6. Install the user-space packages to the root file system and compress the whole root file system with proper format. The result firmware image is generated in bin/

> http://www.ccs.neu.edu/home/noubir/Courses/CS6710/S12/material/OpenWrt_Dev_Tutorial.pdf

### How Buildroot Works

Buildroot is basically a set of Makefiles that **download**, **configure**, and **compile** software with the correct options. It also includes patches for various software packages.

Each directory contains at least 2 files:
- something.mk is the Makefile that downloads, configures, compiles and installs the package something.
- Config.in is a part of the configuration tool description file. It describes the options related to the package.

The main Makefile performs the following steps (once the configuration is done):
- Create all the output directories: staging, target, build, etc. in the output directory (output/ by default, another value can be specified using O=)
- Generate the toolchain target.When an internal toolchain is used, this means generating the cross-compilation toolchain. When an external toolchain is used, this means checking the features of the external toolchain and importing it into the Buildroot environment.
- Generate all the targets listed in the TARGETS variable. This variable is filled by all the individual components' Makefiles. Generating these targets will trigger the compilation of the userspace packages (libraries, programs), the kernel, the bootloader and the generation of the root filesystem images, depending on the configuration.

> https://buildroot.org/downloads/manual/manual.html

### Package Infrastructures

![Package Infrastructures](images/buildroot_package_infrastructures.png)

- Each software component to be built by Buildroot comes with its own build system.
- Buildroot does not re-invent the build system of each component, it simply uses it.
- Numerous build systems available: hand-written Makefiles or shell scripts, autotools, CMake and also some specific to languages: Python, Perl, Lua, Erlang, etc.
- In order to avoid duplicating code, Buildroot has package infrastructures for well-known build systems.
- And a generic package infrastructure for software components with non-standard build systems.

![Generic Package Infrastructures](images/buildroot_generic_package_steps.png)

- To be used for software components having non-standard build systems.
- Implements a default behavior for the downloading, extracting and patching steps of the package build process.
- Implements init script installation, legal information collection, etc.
- Leaves to the package developer the responsibility of describing what should be done for the configuration, building and installation steps.

### Principle

![Embedded Linux Build Systrem Principle](images/buildroot_principle.png)

### Package Build Example

```
>>> zlib 1.2.8 Downloading
... here it wgets the tarball ...
>>> zlib 1.2.8 Extracting
xzcat /home/thomas/dl/zlib-1.2.8.tar.xz | tar ...
>>> zlib 1.2.8 Patching
>>> zlib 1.2.8 Configuring
(cd /home/thomas/projets/buildroot/output/build/zlib-1.2.8;
	...
	./configure --shared --prefix=/usr)
>>> zlib 1.2.8 Building
/usr/bin/make -j1 -C /home/thomas/projets/buildroot/output/build/zlib-1.2.8
>>> zlib 1.2.8 Installing to staging directory
/usr/bin/make -j1 -C /home/thomas/projets/buildroot/output/build/zlib-1.2.8
	DESTDIR=/home/thomas/projets/buildroot/output/host/usr/arm-buildroot-linux-uclibcgnueabi/sysroot
	LDCONFIG=true install
>>> zlib 1.2.8 Installing to target
/usr/bin/make -j1 -C /home/thomas/projets/buildroot/output/build/zlib-1.2.8
	DESTDIR=/home/thomas/projets/buildroot/output/target
	LDCONFIG=true install
```

![Package Build Example](images/buildroot_package_sequence.png)

### When You Run Make...

![When You Run Make Example](images/buildroot_make_example.png)

> http://free-electrons.com/doc/training/buildroot/buildroot-slides.pdf

## Concept

## Source Analysis

### feeds
### scripts
### tools
### toolchain
### config
### package

## Development

## Tools

## Community

## Misc

## Resources

- [OpenWrt Wiki](https://wiki.openwrt.org)
- [OpenWrt Developer](https://wiki.openwrt.org/doc/playground/developer)
- [OpenWrt Development Center](https://dev.openwrt.org)
- [OpenWrt HOWTOs](https://wiki.openwrt.org/doc/howto/start)
- [OpenWrt Git Repository](https://git.openwrt.org)
- [OpenWrt GitHub](https://github.com/openwrt)
- [OpenWrt Forum](https://forum.openwrt.org)

