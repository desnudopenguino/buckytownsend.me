---
layout: post
title: "Installing OpenBSD on a Lenovo 11e"
date: 2016-11-30 10:07:14 -0700
permalink: /blog/:year-:month-:day-:slug/
categories: hardware OpenBSD lenovo OS 11e laptop
---
I recently purchased a new laptop to replace my currently aging netbook which is a Lenovo x120e. I like Lenovo and their generally easy to use and rugged machines, so I figured I would pick up another one. After a day or two of searching, I landed on the ThinkPad 11e. I chose the AMD version because I am still on the bandwagon after all these years. I didn't look much into the specific hardware, but figured everything should be supported in OpenBSD, and I would go a similar route to the one I went when I purchased my x120e and install a new OS ASAP. But this time around, I am going with OpenBSD from the beginning since it is my current OS of choice. Last time I went with Ubuntu, then switched to Mint Linux, Debian, and finally landed with OpenBSD about a year ago.

I managed to snag an used 11e off of ebay for about $140. That's about half price of a new one, which is always a plus. I received it a couple days ago, and got around to installing OpenBSD on it yesterday. I pulled out my trusty flash drive and downloaded the [OpenBSD 6.0 Install filesystem](https://ftp.openbsd.org/pub/OpenBSD/6.0/amd64/install60.fs) and off I went. If you don't know how to create an OpenBSD bootable flash drive, [directions for creating bootable install media](https://www.openbsd.org/faq/faq4.html#MkInsMedia) should get you off and running. Specifically, you can run

{% highlight term %}
# dd if=/location/install60.fs of=/dev/sd1c bs=1m
{% endhighlight %}

In this command, dd copied the contents of the /location/install60.fs (the location of the install filesystem) to my flash drive's (sd1) root (sd1c) with a block size of 1 megabyte. After this was done, I plugged my flash drive in and crossed my fingers.

The first time, it booted into Windows setup, so I had to go in and rearrange the boot order to boot the USB hard drive first. Then I got "Image failed to verify with ACCESS DENIED" during boot. So I went back into the BIOS and disabled the secure boot function. The third time everything went as planned, and I followed the familiar prompts to install OpenBSD. After the install was complete I added a few useful packages and started poking around with my new machine. Things were chugging along smoothly until I ran into an issue when running the startx command. To make a long story short, the radeon drivers don't currently support the graphics. I spent most of the evening digging around for a solution, and finally came upon one on freenode's #OpenBSD channel. The solution was to use the wsfb driver instead of the radeon one.

To do this, I edited the /etc/xorg.conf file and added:
{% highlight config file %}
Section "device"
	Identifier "default device"
	Driver "wsfb"
EndSection
{% endhighlight %}

Then I rebooted and everything was working as I had expected, except for the wireless. I have unfortunately come across one of those Broadcom wifi cards that isn't supported by OpenBSD. I'll have to wait until I get an intel card delivered to be able to finally have a free floating laptop again. That's not a huge deal, and still puts me a bit under the $250 price tag. I also plan on installing an 8GB stick of ram in the near future.

After some playing, I found that waking from sleep is a mess on this laptop. If you close the lid or type zzz in a terminal, the system goes to sleep. As far as I know, you can only wake it by opening the lid. Pressing the power button shuts down the system, and typing on the keyboard does nothing to my knowledge. After waking from sleep the system is up, but the screen stays totally off (no backlight or anything). To get around this hurdle, I put the 11e into hibernate mode by typing ZZZ into the terminal. At least it is an easy command to type without having a screen to look at. Once the computer is totally in hibernation, I press the power button for 2-3 seconds and it boots up and everything comes up without a problem. You can either run hibernate from the start by typing ZZZ into the terminal before closing the lid, or you can run it after the fact when you open the lid to recover from the dead screen with sleep mode. I am sure there is a more elegant method to handle this issue, I just haven't found it yet.

Beyond these issues, the newer Lenovo laptops come with buttonless track pads (like many other laptosp have for quite a while). This was a bit annoying at first, and I needed to add some functionality to it that wasn't set up by default. For most of my everyday work, I like to have the ability to use the three major mouse buttons, and scroll. To do this I edited the file found at /etc/X11/xorg.conf.d/50-synaptics.conf to include the following:

{% highlight config file %}
Section "InputClass"
	Identifier "touchpad catchall"
	Driver "synaptics"
	MatchIsTouchpad "on"
	Option "VertTwoFingerScroll" "true"
	Option "HorizTwoFingerScroll" "true"
	Option "TapButton1" "1"
	Option "TapButton2" "3"
	Option "TapButton3" "2"
EndSection
{% endhighlight %}

This allowed me to be able to scroll by dragging two fingers, single finger tap to left click, two-finger tap to right click, and three-finger tap to click the center button (like opening a new tab, or close one in a browser). There are a bunch of other settings as well, but you would have to check out the [synaptics(4) man page](http://man.openbsd.org/OpenBSD-current/man4/synaptics.4).
