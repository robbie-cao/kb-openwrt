# UBUS on OpenWrt

## Introduction

The ubus is an interface that allows users to access and use services from the same place. Some services are built-in to OpenWRT and other services are executable files that we have created ourselves.

![ubus structure](http://i.imgur.com/NStEmL5.png)

> https://wiki.onion.io/Tutorials/OpenWRT%20Tutorials/UBUS_Tutorial/Part1_Ubus_Intro

> https://wiki.onion.io/Tutorials/OpenWRT%20Tutorials/UBUS_Tutorial/Part3_RPCD

The heart of ubus is `ubusd` daemon. It provides interface for other daemons to register themselves as well as sending messages. For those curious, this interface is implemented using Unix socket and it uses TLV (type-length-value) messages.

To simplify development of software using ubus (connecting to it) a library called libubus has been created.

Every daemon registers set of own paths under specific namespace. Every path can provide multiple procedures with various amount of arguments. Procedures can reply with a message.

> https://wiki.openwrt.org/doc/techref/ubus

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

## Unix/Linux IPC

The Linux kernel provides the following IPC mechanisms: Signals, Anonymous Pipes, Named Pipes or FIFOs, SysV Message Queues, POSIX Message Queues, SysV Shared memory, POSIX Shared memory, SysV semaphores, POSIX semaphores, FUTEX locks, File-backed and anonymous shared memory using mmap, UNIX Domain Sockets, Netlink Sockets, Network Sockets, Inotify mechanisms, FUSE subsystem, D-Bus subsystem.

Here are the big seven:

1. Pipe

   Useful only among processes related as parent/child. Call `pipe(2)` and `fork(2)`. Unidirectional.

2. FIFO, or named pipe

   Two unrelated processes can use FIFO unlike plain pipe. Call `mkfifo(3)`. Unidirectional.

3. Socket and Unix Domain Socket

   Bidirectional. Meant for network communication, but can be used locally too. Can be used for different protocol. There's no message boundary for TCP. Call `socket(2)`.

4. Message Queue

   OS maintains discrete message. See `sys/msg.h`.

5. Signal

   Signal sends an integer to another process. Doesn't mesh well with multi-threads. Call `kill(2)`.

6. Semaphore

   A synchronization mechanism for multi processes or threads, similar to a queue of people waiting for bathroom. See `sys/sem.h`.

7. Shared memory

   Do your own concurrency control. Call `shmget(2)`.

> http://stackoverflow.com/questions/404604/comparing-unix-linux-ipc

> http://beej.us/guide/bgipc/output/html/multipage/index.html

> https://en.wikipedia.org/wiki/Inter-process_communication

Selection of IPC technique depends on application which you are trying to implement. Below is a good comparison base on performance:

```
IPC name      Latency     Throughput   Description
-----------------------------------------------------------------------------------------
Signal        Low          n/a         Can be used only for notification, traditionally-
                                       to push process to change its state

Socket        Moderate     Moderate    Only one mechanism which works for remote nodes,
                                       not very fast but universal

D-Bus         High         High        A high-level protocol that increases latency and
                                       reduces throughput compared to when using base
                                       sockets, gains in increased ease of use

Shared        n/a          High        Data saved in-between process runs(due to swapping
memory                                 access time) can have non-constant access latency

Mapped files  n/a          High        Data can be saved in-between device boots

Message       Low          Moderate    Data saved in-between process runs. The message
queue                                  size is limited but there is less overhead
                                       to handle messages
```

> http://stackoverflow.com/questions/14985999/linux-ipc-selection?noredirect=1&lq=1

## Reference

- https://wiki.openwrt.org/doc/techref/ubus
