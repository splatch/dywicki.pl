---
author: splatch
category:
  - automation
date: "2023-10-04T11:43:56+00:00"
guid: http://dywicki.pl/?p=1034
summary: The TwinCAT is automation software - both runtime and engineering environment from Beckhoff Automation. Since its early days it embedded "win", which was a shortcut from windows. Today I've finally got a TwinCAT/BSD working within VM and had to enable old fashion ADS connection to integration developed within [Apache PLC4X](https://plc4x.apache.org) library and embedded in [ConnectorIO Agent](https://connectorio.com/industrial-and-building-automation-monitoring-agent/) software.
tag:
  - beckoff
  - bsd
  - twincat
title: TwinCAT/BSD with insecure ADS connection
url: /2023/10/twincat-bsd-with-insecure-ads-connection/

---
The TwinCAT is automation software - both runtime and engineering environment from Beckhoff Automation. Since its early days it embedded "win", which was a shortcut from windows. Today I've finally got a TwinCAT/BSD working within VM and had to enable old fashion ADS connection to integration developed within [Apache PLC4X](https://plc4x.apache.org) library and embedded in [ConnectorIO Agent](https://connectorio.com/industrial-and-building-automation-monitoring-agent/) software.

## Problem statement

The TwinCAT/BSD comes with secure ADS by default and firewall which blocks most of the ports. Because secure ADS uses TLS, currently not supported yet by Apache PLC4X, I had to find a way to enable "old" and insecure connection.

## Solution

In order to get everything sorted we need first to elevate user permissions. Default user we log into via shell is Administrator, which is not permitted to amend system configuration.

For that reason we first have to do `sudo sh`.

After getting root permission, we can edit firewall configuration by adding following line to `/etc/pf.conf` (information taken from [official Beckhoff docs](https://infosys.beckhoff.com/english.php?content=../content/1033/twincat_bsd/6423780747.html&id=)). The "pf" stands for packet filter which is firewall software for Free BSD. You can use `vi` to conduct editing.

```
pass in quick proto tcp to port 48898 synproxy state
```

Once change is saved we need to reload firewall rules through `pfctl -f /etc/pf.conf` command.

## Results

With above changes, it is finally possible to retrieve symbol list through ADS. :-) Please note fancy ASCII dump of exchanged payload. ;-)

```bash
╔═AdsTableSizes═══════════════════════════════════════════════════════════════════════════════════╗
║╔═symbolCount═╗╔═symbolLength══╗╔═dataTypeCount╗╔═dataTypeLength═╗╔═extraCount════╗╔═extraLength╗║
║║0x00000013 19║║0x00000850 2128║║0x00000037 55 ║║0x00002c18 11288║║0x000007d0 2000║║0x00000000 0║║
║╚═════════════╝╚═══════════════╝╚══════════════╝╚════════════════╝╚═══════════════╝╚════════════╝║
╚═════════════════════════════════════════════════════════════════════════════════════════════════╝

      |           PLC4X Field         |          Hex          |
##### |          Query Syntax         |   Index   |   Offset  |    Type   | Name                                   | Size (B) | Type | Value (type)
------+-------------------------------+-----------+-----------+-----------+----------------------------------------+----------+------|
    0 |           0x4040/0x5dffd:BOOL |    0x4040 |   0x5dffd |      BOOL |Constants.bFPUSupport                   |        1 |   33 | true (java.lang.Boolean)
    2 |           0x4040/0x5dfeb:BOOL |    0x4040 |   0x5dfeb |      BOOL |Constants.bLittleEndian                 |        1 |   33 | true (java.lang.Boolean)
    4 |           0x4040/0x5e002:BOOL |    0x4040 |   0x5e002 |      BOOL |Constants.bMulticoreSupport             |        1 |   33 | false (java.lang.Boolean)
    6 |           0x4040/0x5dffc:BOOL |    0x4040 |   0x5dffc |      BOOL |Constants.bSimulationMode               |        1 |   33 | false (java.lang.Boolean)
    8 |        0x4040/0x5dff4:VERSION |    0x4040 |   0x5dff4 |   VERSION |Constants.CompilerVersion               |        8 |   65 | {uiMajor=3, uiMinor=5, uiServicePack=13, uiPatch=40} (java.util.Map)
   10 |          0x4040/0x5e008:DWORD |    0x4040 |   0x5e008 |     DWORD |Constants.CompilerVersionNumeric        |        4 |   19 | 50662696 (java.lang.Long)
   12 |           0x4040/0x5e000:UINT |    0x4040 |   0x5e000 |      UINT |Constants.nPackMode                     |        2 |   18 | 8 (java.lang.Integer)
   14 |           0x4040/0x5dffe:WORD |    0x4040 |   0x5dffe |      WORD |Constants.nRegisterSize                 |        2 |   18 | 64 (java.lang.Integer)
   16 |        0x4040/0x5dfec:VERSION |    0x4040 |   0x5dfec |   VERSION |Constants.RuntimeVersion                |        8 |   65 | {uiMajor=3, uiMinor=5, uiServicePack=13, uiPatch=0} (java.util.Map)
   18 |          0x4040/0x5e004:DWORD |    0x4040 |   0x5e004 |     DWORD |Constants.RuntimeVersionNumeric         |        4 |   19 | 50662656 (java.lang.Long)

```

Clearly program on PLC is not doing anything, as there are no application specific symbols, just plain symbols available in (probably) every runtime.

As a remark - I am not a PLC programmer, what helped me a lot was tutorial on [TwinCAT/BSD in a virtual machine by AllTwincat](https://alltwincat.com/2021/12/13/tc-bsd-in-a-virtual-machine/). Thanks to it, and above firewall update, I have finally a working TwinCAT3 runtime without a need to launch windows. :-)
