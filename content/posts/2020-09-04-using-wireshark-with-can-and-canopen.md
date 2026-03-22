---
author: splatch
category:
  - rants
date: "2020-09-04T23:19:44+00:00"
guid: http://dywicki.pl/?p=972
summary: '[Wireshark](http://wireshark.org/) is outstanding piece of software. I had few chances to make use of it, early in my IT days, then in one of bigger projects I worked on, and finally now - when I began working on industrial integration protocols.{{ double-space-with-newline }}This blog post will describe a journey I had with Wireshark over last couple of weeks and tell you why clicking couple of times everywhere you _CAN_ is sometimes better.'
tag:
  - canopen
  - wireshark
title: Using Wireshark with CAN and CANopen
url: /2020/09/using-wireshark-with-can-and-canopen/

---
[Wireshark](http://wireshark.org/) is outstanding piece of software. I had few chances to make use of it, early in my IT days, then in one of bigger projects I worked on, and finally now - when I began working on industrial integration protocols.  
This blog post will describe a journey I had with Wireshark over last couple of weeks and tell you why clicking couple of times everywhere you _CAN_ is sometimes better.

Most of us knows Wireshark as network traffic analysis tool. Over years it really evolved and permits to scan USB and even Bluetooth traffic. This makes it great candidate for other "serial" protocols, especially ones used in hardware and automation. I used this tool couple of months ago to implement [Apache PLC4X](http://plc4x.apache.org) and [MSpec](https://plc4x.apache.org/developers/code-gen/protocol/mspec.html) describing Link-Layer-Discovery-Protocol (LLDP) as well as for Profinet Discovery and Configuration Protocol (Profinet-DCP).  
Recently I've started working on CANopen implementation in Java and faced sad reality of automation market. Almost each and every tool is paid. Existing open-source toolkits offer quite poor functionality or function as a library with limited functionality.

Naturally I did search for Wireshark + CAN multiple times over Google, DuckDuckGo. Each time I was getting the same answer: [Enable CANopen protocol in Wireshark](https://osqa-ask.wireshark.org/questions/41447/enable-canopen-protocol). It is simple, it must be simple, am I right?  
I looked at CAN preferences, but options mentioned in this answer were gone. Other suggestions on using next dissector lead me to window which did not give a CANopen anywhere.

Now, the real answer you probably look for - you need to know which column to click. I kept clicking first column and getting everything but not CANopen. Below you can see Wireshark popup window you probably don't use most of the time:

![Wireshark Decode as..](/wp-content/uploads/2020/09/image.png)Decode as .. Wireshark dissector selection.

Finally, after being caught in this trap for two weeks, if not longer, I found answer. Exhausted by lack of success I finally went to Windows and started clicking over all columns. It turned out that LAST column is editable and that's the place where you can actually pick higher layer protocol for CAN.  
You just need to click into it, if it doesn't work (as it didn't for me) then click twice. Pay attention cause Wireshark GUI sometimes gets tricky with "clicks", at least that was case for my linux distro. Just now I been trying to see if I was _that_ dumb that I did not click this bloody column or my X manager played fool with me. Probably both, cause now by trying I managed to "freeze" row and block the dropdown.  
When you click last column in a right spot it will show a drop down list.

![Wireshark dissector setup.](/wp-content/uploads/2020/09/wireshark-canopen.gif)Changing Wireshark dissector.

Hope that it will help others who ran into same trouble. I must admit that I did not expect myself being stuck on such basic issue for so long. Especially with program I knew for more than decade. It seems that the older I get, the easier I get stuck with such basic things. Remember, if something doesn't work just keep clicking, eventually it will.
