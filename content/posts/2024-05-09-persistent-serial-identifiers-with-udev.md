---
author: splatch
category:
  - software
cover:
  alt: Orange USB cables
  image: https://images.unsplash.com/photo-1572721546624-05bf65ad7679?auto=format&fit=crop&fm=jpg&ixlib=rb-4.1.0&q=80&w=1600
date: "2024-05-09T09:33:41+00:00"
guid: http://dywicki.pl/?p=1044
summary: It is not a secret that Linux can juggle USB serial ports between restarts. This is something which doesn't happen on linux, but if you ever looked at internet forums on how to fix issue, you for sure found advice to use custom udev rules.
tag:
  - linux
  - udev
  - usb
title: Persistent serial identifiers with udev
url: /2024/05/persistent-serial-identifiers-with-udev/

---
It is not a secret that Linux can juggle USB serial ports between restarts. This is something which doesn't happen on linux, but if you ever looked at internet forums on how to fix issue, you for sure found advice to use custom udev rules.

## Problem statement

[![Listing USB ports.. in openHAB webui](/wp-content/uploads/2024/05/openhab_usb_port_list.png)](/wp-content/uploads/2024/05/openhab_usb_port_list.png)

Most of articles advice to use attributes found on USB device such as `idVendor` and `idProduct`. These are part of USB device descriptor and allow operating system to identify what was plugged. Eventually, people who run into issues with these can stick with `serial`. Problem is, I don't want to create a dedicate rule for every single thing I plug into my device, after all it is **universal** serial bus.

If we are on attributes - some vendors keep using the same value for vendor, product and serial for all devices they manufacture. In such situations some advice to rely on udev `KERNEL` S value (which we will look closer soon). For sure some manufacturers violate the specification and denies whole point of us having device descriptor, but .. that's life. Is there any other information we can follow?

## Accessing USB hub information

The `idVendor` and `idProduct` attributes come from device side. On computer end we usually have USB hub or hubs which handle ports and do all the low level work. If we look at list of attributes available at this place we can find quite many unique elements:

```plain
  looking at parent device '/devices/pci0000:00/0000:00:15.0/usb1/1-1':
    KERNELS=="1-1"
    SUBSYSTEMS=="usb"
    DRIVERS=="usb"
    ATTRS{authorized}=="1"
    ATTRS{avoid_reset_quirk}=="0"
    ATTRS{bConfigurationValue}=="1"
    ATTRS{bDeviceClass}=="00"
    ATTRS{bDeviceProtocol}=="00"
    ATTRS{bDeviceSubClass}=="00"
...
    ATTRS{bmAttributes}=="80"
    ATTRS{busnum}=="1"
    ATTRS{configuration}==""
    ATTRS{devnum}=="2"
    ATTRS{devpath}=="1"
    ATTRS{idProduct}=="ea60"
    ATTRS{idVendor}=="10c4"
    ATTRS{ltm_capable}=="no"
    ATTRS{manufacturer}=="Silicon Labs"
....
    ATTRS{rx_lanes}=="1"
    ATTRS{serial}=="12132"
    ATTRS{speed}=="12"
    ATTRS{tx_lanes}=="1"
    ATTRS{urbnum}=="341706"
    ATTRS{version}==" 1.10"
... (more to follow)
```

Above is one of nodes which corresponds device plugged into hub 1 (busnum==1) at port 1 (devpath==1). While linux kernel might have different initialization sequence for some cases, the hardware part should never change, because one thing for sure will be consistent - it means usb hub port numbers. This is also where `KERNELS` value comes to play, in above example it is set to 1-1, which is bus + port number. Obviously there are more complex use cases such as hub under hub, under hub.

## The ultimate udev rule to rule identifiers

Because we successfully identified what linux kernel knows about port numbers we can try to bake udev rule for it. I won't dive into details of how udev rules are matched or constructed as there are many materials describing that. Below I explain rationale behind this rule:

```plain
KERNEL=="ttyUSB[0-9]*", ATTRS{bDeviceClass}=="00", ATTRS{bDeviceProtocol}=="00", ATTRS{bDeviceSubClass}=="00", ATTRS{busnum}=="*", ATTRS{devnum}=="*", ATTRS{devpath}=="*", ATTRS{serial}=="*", SYMLINK+="ttyUSB$attr{busnum}.$attr{devnum}.$attr{devpath}.$attr{serial}"
```

Looking at above - we can see there `KERNEL=="ttyUSB[0-9]*"`, which should match devices which got already identifier assigned by kernel. We wish to re-assign it, hence there are few more conditions.  
The `bDeviceClass`, `bDeviceProtocol` and `bDeviceSubClass` are all set to 00, in order to match single edge. The hub infrastructure above will have different protocol. While it might seem redundant, it helps to keep uniqueness of results. It ensures that we will match udev node (I believe) which represents information where both descriptor and hub information is present and not usb device side.  
The later part specifies that busnum, devnum and devpath as well as serial must be present (even if its empty). Finally we add symlink which uses above port identification.  
This is really it - there are a lot of examples where you will find `SYMLINK+="myDevice"`, which is fine, but since we look for generic solution of generic issue, we want to stick to information which is always available and do not change until device is not re-wired.

Lets have a look on results:

```bash
$ ls -la /dev/ttyUSB*
crw-rw---- 1 root dialout 188, 0 May  9 08:42 /dev/ttyUSB0
crw-rw---- 1 root dialout 188, 1 May  9 08:42 /dev/ttyUSB1
lrwxrwxrwx 1 root root         7 May  9 08:42 /dev/ttyUSB1.2.1.12132 -> ttyUSB1
lrwxrwxrwx 1 root root         7 May  9 08:42 /dev/ttyUSB1.3.4.2701CDF2 -> ttyUSB0
```

We can see multiple `ttyUSB1` nodes, however for these longer 1 is actually bus identifier, followed by devnum, devpath and serial numbers. I've used all these for causes when device have multiple buses and multiple USB hubs. Lets see if it will last!
