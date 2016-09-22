---
layout: post
title: Device Tree
categories: linux
---

## Introduction

The **device tree** is a data structure for describing hardware, which originated from [Open Firmware](https://en.wikipedia.org/wiki/Open_Firmware). The data structure can hold any kind of data as internally it is a tree of named nodes and properties. Nodes contain properties and child nodes, while properties are name-value pairs.

More specifically, it is a description of hardware that is readable by an operating system so that the operating system doesn't need to hard code details of the machine.

### High Level View

The most important thing to understand is that the DT is simply a data
structure that describes the hardware.  There is nothing magical about
it, and it doesn't magically make all hardware configuration problems
go away.  What it does do is provide a language for decoupling the
hardware configuration from the board and device driver support in the
Linux kernel (or any other operating system for that matter).  Using
it allows board and device support to become data driven; to make
setup decisions based on data passed into the kernel instead of on
per-machine hard coded selections.

Ideally, data driven platform setup should result in less code
duplication and make it easier to support a wide range of hardware
with a single kernel image.

Linux uses DT data for three major purposes:

1. platform identification,
2. runtime configuration, and
3. device population.

> https://www.kernel.org/doc/Documentation/devicetree/usage-model.txt

### It is ...

- An framework to easilysupport multiple SoC devices with a single kernel image
- Standard interface between bootloader and OS
- Device tree is hardware description, not configuration
- It should describe the hardware layout, and how it works

![Device Tree](http://trac.gateworks.com/raw-attachment/wiki/linux/devicetree/devicetree.png)

### It's not ...

- It should not describe which particular hardware configuration you're insterested in
- Doesn't replace board-specific code
- Doesn't add features to your platform
- Doesn't boot your platform fast

### Workflow

![Device Tree Workflow](https://raw.githubusercontent.com/robbie-cao/repo-diagrams/master/linux/Linux-Device-Tree-Workflow.png)

## Accessing Device Tree from Linux

You can access the devicetree via `/proc/device-tree`. This can be useful to obtain information about the board that the bootloader configured, such as board model and serial number.

Examples:

- Show board model:

  ```
  echo $(cat /proc/device-tree/board)
  ```

- Show board serialnumber

  ```
  echo $(cat /proc/device-tree/system-serial)
  ```

- Show devicetree compatible node (this describes which device-tree was used as there is one per base-board design):

  ```
  echo $(cat /proc/device-tree/compatible)
  ```

- Show chosen bootargs (the bootargs passed in by the bootloader, same as /proc/cmdline):

  ```
  echo $(cat /proc/device-tree/chosen/bootargs)
  ```

- Print the whole device tree:

  ```
  find /proc/device-tree/
  ```

> http://trac.gateworks.com/wiki/linux/devicetree

## Why DTS

![Why DTS](http://img1.ph.126.net/V3mS5StDH6Kpdx-FXGGY7g==/6599333661097749001.png)

## Device Tree Structure

![Device Tree Structure](http://ece-research.unm.edu/jimp/codesign/ZED/Summary_of_the_Device_Tree.png)

## Misc

```
# uboot

bootm kernel_addr initrd_addr dtb_addr

# linux
- kernel entry change
  * old: kernel_entry(0, mach_id, atag_addr)
  * new: kernel_entry(0, mach_id, dtb_addr)
- config
  * Boot options -> Flattened Device Tree support
  * Device Drivers -> Device Tree and Open Firmware support -> Support for device tree in /proc
- device tree script
  * arch/arm/boot/dts
- /proc/device-tree
```

Device Tree Compiler

```
scripts/dtc
```

`arch/arm/boot/dts/Makefile` lists which DTB (Device Tree Blob) should be generated at build time.

## Reference

- https://git.kernel.org/cgit/utils/dtc/dtc.git
- http://elinux.org/Device_Tree
- http://elinux.org/Device_Tree_Usage
- http://elinux.org/Device_Tree_Reference
- https://events.linuxfoundation.org/sites/events/files/slides/petazzoni-device-tree-dummies.pdf
- http://xillybus.com/tutorials/device-tree-zynq-1
- https://en.wikipedia.org/wiki/Device_tree
- https://en.wikipedia.org/wiki/Open_Firmware
- https://www.kernel.org/doc/Documentation/devicetree/
- http://www.slideshare.net/softpapa/device-tree-support-on-arm-linux-8930303
- http://free-electrons.com/pub/conferences/2013/elce/petazzoni-device-tree-dummies/petazzoni-device-tree-dummies.pdf
- http://fossies.org/linux/qemu/dtc/Documentation/manual.txt
- https://github.com/vagrantc/device-tree-compiler/blob/master/Documentation/dts-format.txt
