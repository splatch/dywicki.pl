---
author: splatch
category:
  - rants
date: "2018-08-28T20:22:49+00:00"
guid: http://dywicki.pl/?p=904
tag:
  - linux
  - osx
title: Migration from OSX to Arch Linux
url: /2018/08/migration-from-osx-to-arch-linux/

---
It is already 12 months since I started using my (first since very long time) desktop PC. In my previous blog post I've described reasons why I decided to resign from Apple hardware. In this one I will tell you how I managed to drop OSX and its ecosystem with minor pains.

I feel that I owe you also some insights - which applications I had to swap and which I was lucky to keep. Because I work with Java on daily basis I do not have any major troubles with portability of my software, however just few out many programs is actually written in Java.

### Why the heck arch manjaro?

From my personal observation it looks that most of people who have any contact with Linux ecosystem can distinguish major distributions such Debian, Ubuntu, CentOS and Red Hat. Of course there are many more. I've chosen arch based distribution mainly because at time I was installing it, it was one of very few which supported F2FS. I have NVME drive and I wanted to get as much performance as possible. Java is compiled language and compilation always involves reading sources and writing compiled code to disk. I build quite large open source projects, sometimes few times a hour. Other Linux distributions supported F2FS, however not as system partition. Keep in mind it was almost a year ago and Ubuntu/Debian likely improved its support for this filesystem.

From biggest benefits of running arch-based distribution I really appreciate fact that I don't need to reinstall system in order to migrate to new major version. Again, it is possible with other distributions, however its not recommended way for many of them and/or requires manual steps. As learnt from OSX experiences - update process can get tricky. Last thing I wanted to suffer after dropping Mac was waiting long hours for backup restoration. Manjaro/Arch, are only one distribution which I know, giving a simple promise - there is a rolling release. It means that system which you have installed will be moving forward. You have guarantee you stay on latest versions of packages or very close to them.
This is pretty much how open source development looks a like - using latest releases if possible.

### Porting applications

One of biggest changes I had to accept was package system. As almost every user who program under Apple OSX, I used Homebrew for several years. I installed with it many things, including vim, sublime as well as quite a lot others who I can not pull right now. Two years ago I managed to get brew (actually linuxbrew) working on linux in order to provision vagrant box. Sadly brew project for years didn't want to support linux. I can understand that as variety of software versions used under Linux can easily overwhelm. I ended up with a fresh system installation and no "package" manager.

What saved my life was Docker. If I would have Facebook account and I would need to describe my relation with Docker, then most likely I would summarize it as "it's complicated". I enjoy running some things in Docker on my NAS, but I don't need to upgrade these containers to often. Each time when I have to do anything with them I have to remind myself command syntax. Till I was forced to use it I felt that I didn't know docker well enough. I think I still miss a lot of knowledge about it.
Docker solved for me issues with lack of good quality ActiveMQ and Cassandra packages under Linux. I was also able to use company specific containers to run parts of product responsible for user interface, report generation and so on which previously I ran using nginx and some fancy tools such phantomjs.

#### Development environment setup

Development environment which includes IDE (IntelliJ) - I got via toolbox installed via arch packages. I use DiffMerge and I was able to get it installed via package system too. I had to tweak checksums to match with latest releases, but beside that - it went well. Ad hoc editor (sublime) - is also available via packets.

Biggest change which I accepted while moving away from Apple was change of shell. I have used ZSH for years and I was happy with it. But as usual, there is always someone who will crush your confidence in a tool. That person for me was Zachary who I worked with at Rocana (acquired by Splunk). He said me that FISH is much better in handling completion and it is able to keep history of commands executed in given directory. This was something which got me interested but not that much to test it immediately. It turned out to be a very good recommendation and I started using fish as my primary shell after moving to linux. Beside that I also started using Terminator who makes a lot of sense on 4k screen where tabs in terminal emulator leave simply to much of empty space.

I been also a long time user of Source Tree, a handy application allowing to manage git repositories.  

Don't get me wrong, I am able to work with git from command line for most of the time, but sometimes when I have more changes or refactoring I prefer to be picky in checking in files or even their parts. I never got used to any IDE for that because there are too many distraction points in there - switching to application which is limited to version control allows to focus on commit. Source Tree was very good in that, simple and clean user interface allowing to select chunks of file to be staged and then committed. Sadly Atlassian didn't decide to port Source Tree to Linux so I had to look for alternatives.
I ended up buying Git Kraken. It offers subset of Atlassian's product functionality but gives all things I been looking for, and it works. I was concerned by fact that it is yet-another-electron-app, meaning javascript running inside embedded browser pretending to be a desktop app, but as said before it works.

For quite long time I don't use Adium nor Pidgin nor any other chat client. Under OSX I switched to Franz, yet due to its strange policy in version 5 which forces account registration I decided to move to Rambox. It does the same thing - wraps web page into tab inside of own window. There is one window to keep all instant messanger distractions in one place. Since most of communications nowadays goes over some form of cloud services, having a single window to aggregate them is useful to keep all distractions in one place.

### Browser and surroundings

If you would ask me what I miss mostly from OSX - I would definitely point Apple Mail and synchronization of contacts between phone and computer. This thing alone is something I regret most. I do use Thunderbird, but it's not the same. It really feels far worse than osx default app. I been using Thunderbird before 2008, then around 2014 for corporate mailbox and come back to it last year. General feeling after launching Thunderbird after several year break is - it didn't change at all. In fact it looks as ugly as before, with confusing settings panel as always and lack of good contact plugin. I could have missed one, if you can recommend something - just post a name in comments.

While moving away from OSX I decided to drop Chrome. Why? Because chrome is the best tracking software ever made and I felt that I have to give it up. After my passwords, which I stored in the browser went to password.google.com where I can get them back in plaintext I got really concerned. It is not that I wasn't aware that Google have them in the cloud, yet it was told everywhere it is not able to read encrypted copy of mine data.

My perception changed dramatically once something which is stored in browser was shown to me first time on web page. I remember old screen for looking passwords in Chrome. It wasn't particularly great, but I would prefer it over having these on web page. More importantly I did not expect that change. Because of that I stopped using google as default browser and moved to Firefox with DuckDuckGo. It's different, but not that much. I still turn google for some edge cases, however my development box - is owned by duck. ;-)

### Drawbacks

I'm really happy with my PC, yet - to be fair with you - there are several things which are quite annoying:

#### 4k/hires/hidpi support

X11 window manager used by default with Gnome 3 under Manjaro does not support partial scaling. This means that it supports 100, 200 or 300 percent scale, but not 125%. This is a real pain because for me 100 is to little and 200 is way to big. It needs to be manually adjusted with xrandr. Usage of xrandr have side effects. For example not all apps gets scaled properly with it. Firefox is one of them. Wayland, which is supposed to solve that have troubles with nvidia cards. For now, I use 100% scale and have adjusted all apps to work with this scale (Qt4, Qt5). However each time when I visit new webpage I have to adjust it by zooming in somewhere between 130 and 170% depending on its layout.
I can stand differences between Qt5, GtK and other toolkits with no issues. I keep complaining on Thunderbird, but if I would be forced to stay with it, I still could manage it.
There is one more, even more annoying thing - I can't get 60 Hz on my display for whatever reason. This means that there is no point in watching any movies on this box. Even on youtube as you will experience flickering. It's good for staying away from movies, but lets face it - its a issue.

#### Linux drivers

Nvidia drivers are available, but they cause some troubles with Wayland (a replacement for X Server). Open source driver does not solve it too. If you consider getting there it might be good idea to check with Intel cards which are used for mobile devices. Friend of mine managed to get Wayland up on his ubuntized Dell XPS 15 with no issues.
I also have 7 years old Canon laser printer which was awful in terms of getting it work under macos. Debugging of issues was usualy solved via resetting CUPS system completely. I didn't even try to get it running under linux. All printing and accounting I still do on OSX with dedicated software.

Power management works fine - I can put computer into sleep and it will wake up. It happens that it doesn't get up, mainly after update of system without restart, then I need to ssh into it and restart display manager. I do see from time to time core dumps in logs, but I do not track them unless they become a source for real troubles.

## Overall summary

I am quite happy with my current setup. Despite of many minor issues and troubles with displays I experienced under Linux I feel myself more productive than before. It's not only about tools, because they are available under OS X, but computer performance. I don't use it for entertainment and it works as expected. All issues so far I was able to solve with answers found online.

Linux as desktop is definitely not as nice nor as stable as OS X, there is still huge distance in terms of usability of both.

Yet, I'm happy to say that "This is Linux year", at least for me.

PS. It took me just 7 months to finish this blog post since first draft made on February 2018. ;-)
