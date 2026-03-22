---
author: splatch
category:
  - uncategorized
date: "2018-07-16T19:33:53+00:00"
draft: "true"
guid: http://dywicki.pl/?p=938
title: Eclipse Smart Home and local development setup for bluetooth
url: /

---
Bluetooth 4 and low energy profile allows wide implementations. In order to get it running locally we need few things - first of all working bluez stack. For me it become a difficulty because tinyb is not up to date with latest bluez which got installed on my arch machine. Therefore I had to hack a little bit:

\- Run openhab in privileged mode:

docker run \
 --name openhab \
 --net=host \
 --tty \
 -device /dev:/dev
 -v /etc/localtime:/etc/localtime:ro \
 -v /etc/timezone:/etc/timezone:ro \
 -v /home/splatch/projects/docker-images/openhab/conf:/openhab/conf \
 -v /home/splatch/projects/docker-images/openhab/userdata:/openhab/userdata \
 -v /home/splatch/projects/docker-images/openhab/addons:/openhab/addons\
 -d \
 --restart=always \
 --privileged \
 openhab/openhab:2.3.0-amd64-debian

\- apt-get update
\- apt-get install -y --fix-missing libglib2.0
\- apt-get install -y dbus bluez mdbus2
\- /etc/init.d/dbus start
\- /etc/init.d/bluetooth start
\- usermod openhab -G bluetooth
