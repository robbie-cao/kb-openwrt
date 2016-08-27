# UBUS on OpenWrt

## Introduction

The ubus is an interface that allows users to access and use services from the same place. Some services are built-in to OpenWRT and other services are executable files that we have created ourselves.

![ubus structure](http://i.imgur.com/NStEmL5.png)

> https://wiki.onion.io/Tutorials/OpenWRT%20Tutorials/UBUS_Tutorial/Part1_Ubus_Intro

> https://wiki.onion.io/Tutorials/OpenWRT%20Tutorials/UBUS_Tutorial/Part3_RPCD

## ubus vs dbus

dbus is bloated, its C API is very annoying to use and requires writing large amounts of boilerplate code. In fact, the pure C API is so annoying that its own API documentation states: "If you use this low-level API directly, you're signing up for some pain."

ubus is tiny and has the advantage of being easy to use from regular C code, as well as automatically making all exported API functionality also available to shell scripts with no extra effort.

> https://wiki.openwrt.org/doc/techref/netifd#what_s_the_difference_between_ubus_and_dbus

## Arch

![ubs arch](http://img.voidcn.com/vcimg/000/003/339/024_b25_d3e.jpg)

> http://www.voidcn.com/blog/zhoyixing/article/p-4658734.html

> http://wenku.baidu.com/link?url=ESfsT9jXUG6CtGcHJD6VmqiXUvqNMFKKh4d0zIEcc4z96yTPuZjvA-yRq0sptwZdicYkyfvUcDMkClnxEaRwCLiTfn_MiFLfJMjkM-0Fr1C

![ncd-ubus](http://dev.qmp.cat/attachments/download/149/NCD_architecture_diagram_v0.2.png)

## Use

> http://blog.csdn.net/jasonchen_gbd/article/details/45627967

## Reference

- https://wiki.openwrt.org/doc/techref/ubus
