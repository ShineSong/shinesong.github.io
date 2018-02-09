---
title: Don't let L4T SDK break your ubuntu
date: 2018-01-18 14:33:55
tags: 
categories: Trick
---

# Overview
L4T uninstalled my opencv and replaced it with opencv4tegra, WTF!

# Remove L4T using its uninstall tool
Go to your L4T installer folder and run

`sudo ./JetPack_Uninstaller`

Select all of them then wait program done.
# Remove L4T dependencies
I don't like arm64 to be one of my host's architectures!

Run

`dpkg --get-selections | grep i386 | awk '{print $1}'`
And then if happy with them being removed, run

`apt-get remove --purge `dpkg --get-selections | grep i386 | awk '{print $1}'`
And then retry the

`dpkg --remove-architecture i386`