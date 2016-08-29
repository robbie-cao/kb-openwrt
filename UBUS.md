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

> http://blog.csdn.net/jasonchen_gbd/article/details/45627967

## Architecture

![ubs arch](http://img.voidcn.com/vcimg/000/003/339/024_b25_d3e.jpg)

> http://www.voidcn.com/blog/zhoyixing/article/p-4658734.html

> http://wenku.baidu.com/link?url=ESfsT9jXUG6CtGcHJD6VmqiXUvqNMFKKh4d0zIEcc4z96yTPuZjvA-yRq0sptwZdicYkyfvUcDMkClnxEaRwCLiTfn_MiFLfJMjkM-0Fr1C

![ncd-ubus](http://dev.qmp.cat/attachments/download/149/NCD_architecture_diagram_v0.2.png)

## Use

### Objects and Object Paths

The Object paths are bindings can name object instances, and allow applications to refer to them.

In OpenWRT, the object path is namespace like `network.interface.lan`.

Example:

```
# ubus list
network
network.device
network.interface.lan
network.interface.loopback
network.interface.wan
```

### Methods and Notifications

Methods are operations that can be invoked on an object, with optional input parameters and output.

Notifications are broadcasts from the object to any interested observers of the object. The notifications may contain a data payload.

Example:

```
# ubus -v list network.interface.lan
'network.interface.lan' @099f0c8b
    "up": {  }
    "down": {  }
    "status": {  }
    "prepare": {  }
    "add_device": { "name": "String" }
    "remove_device": { "name": "String" }
    "notify_proto": {  }
    "remove": {  }
    "set_data": {  }
```

### Calling a Method

A method call in ubus consists of two messages;  A call messages from process A to process B and the reply messages from process B to process A.

The send message and reply messages are both routed through the ubus daemon.

The call message contains the method arguments.

The reply messages may be error messages, or may contain method returned data.

**Call Process**

1. The call method messages contains the ubus connection context, the destination object id, the method name, the method arguments.
2. The method call message is send to the ubus daemon.
3. The ubus daemon lookup the destination object id, if a process owns the object instance, then the daemon will forward the method call to the find process. Otherwise the ubus daemon creates an error messages and sends the error message back to the message call as reply.
4. The receiving process will parse the ubus object messages, and find the call method and arguments belong to the method. Then match the object methods in object instance, if find matched method, will invoke the method and then send the reply messages.
5. Ubus daemon receive the reply message and forward the reply message to the process that made the method call.
6. The reply messages is transferred as ubus blob messages structure which is TLV (Type-Length-Value) based binary messages type.
7. The process received the reply message should parse the message  and format  to human-nice message type as JSON or XML.

Example:

```
# ubus call network.interface.wan status
{
    "up": true,
    "pending": false,
    "available": true,
    "autostart": true,
    "uptime": 86017,
    "l3_device": "eth1",
    "device": "eth1",
    "address": [
        {
            "address": "178.25.65.236",
            "mask": 21
        }
    ],
    "route": [
        {
            "target": "0.0.0.0",
            "mask": 0,
            "nexthop": "178.25.71.254"
        }
    ],
    "data": {

    }
}
```

### Notify Notifications

A notification in ubus consists of a single messages, send by one process to any number of other processes, which means the notification is a unidirectional broadcast, no need expected reply message.

The notification sender do not know the notifications recipients, it just send the  notification onto bus  The interest recipients should subscribe the sender object with the bus daemon.

**Notification Process**

1. Add notification object onto ubus daemon.
2. The notification message contains ubus connection context, the notification sender object ID, the notification type and optional arguments with the type.
3. Any process on the ubus can subscribe the notification object. The bus may has a list  of subscribers, which will match the observers when daemon handle the notification message.
4. The ubus daemon check the notification and determines which processes are  interested in it. Then send the notification to all of the interested processes.
5. Each subscriber process receiving the notification decides what to do with the  notification message.

```
root@mylinkit:/# ubus list
dhcp
iwinfo
log
network
network.device
network.interface
network.interface.lan
network.interface.loopback
network.interface.wan
network.wireless
rpc-sys
service
session
system
uci
root@mylinkit:/# ubus -v list network.wireless
'network.wireless' @5b7f5ca6
        "up":{}
        "down":{}
        "status":{}
        "notify":{}
        "get_validate":{}
root@mylinkit:/# ubus listen &
root@mylinkit:/# ubus call network.wireless down
root@mylinkit:/# [ 5143.150000] br-lan: port 2(ra0) entered disabled state
{ "network.interface": {"action":"ifdown","interface":"wan"} }
{ "network.interface": {"action":"ifdown","interface":"wan"} }
{ "network.interface": {"action":"ifdown","interface":"wan"} }
[ 5143.840000] device ra0 left promiscuous mode
[ 5143.840000] br-lan: port 2(ra0) entered disabled state
[ 5149.510000] efuse_probe: efuse = 10000012
[ 5149.680000] tssi_0_target_pwr_g_band = 32
[ 5149.690000] tssi_1_target_pwr_g_band = 35
[ 5154.810000] <==== rt28xx_init, Status=0
root@mylinkit:/# ubus call network.wireless up
root@mylinkit:/#
[ 5196.330000] device ra0 entered promiscuous mode
[ 5196.340000] br-lan: port 2(ra0) entered forwarding state
[ 5196.340000] br-lan: port 2(ra0) entered forwarding state
[ 5198.340000] br-lan: port 2(ra0) entered forwarding state
{ "network.interface": {"action":"ifup","interface":"wan"} }
{ "network.interface": {"action":"ifup","interface":"wan"} }
{ "network.interface": {"action":"ifup","interface":"wan"} }
```

> http://wenku.baidu.com/link?url=ESfsT9jXUG6CtGcHJD6VmqiXUvqNMFKKh4d0zIEcc4z96yTPuZjvA-yRq0sptwZdicYkyfvUcDMkClnxEaRwCLiTfn_MiFLfJMjkM-0Fr1C

## `ubus` cli

```
# ubus
Usage: ubus [<options>] <command> [arguments...]
Options:
 -s <socket>:           Set the unix domain socket to connect to
 -t <timeout>:          Set the timeout (in seconds) for a command to complete
 -S:                    Use simplified output (for scripts)
 -v:                    More verbose output

Commands:
 - list [<path>]                        List objects
 - call <path> <method> [<message>]     Call an object method
 - listen [<path>...]                   Listen for events
 - send <type> [<message>]              Send an event
 - wait_for <object> [<object>...]      Wait for multiple objects to appear on ubus

```


## More

### ubus vs dbus

dbus is bloated, its C API is very annoying to use and requires writing large amounts of boilerplate code. In fact, the pure C API is so annoying that its own API documentation states: "If you use this low-level API directly, you're signing up for some pain."

ubus is tiny and has the advantage of being easy to use from regular C code, as well as automatically making all exported API functionality also available to shell scripts with no extra effort.

> https://wiki.openwrt.org/doc/techref/netifd#what_s_the_difference_between_ubus_and_dbus

### Unix/Linux IPC

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
