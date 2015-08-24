---
layout: post
title: (K)ubuntu Linux 15.04 for web dev
---
*Who needs Mac or Windows for Linux development?*

![(K)ubuntu Linux](/images/2015-08-23-kubuntu.png)


### TL;DR

Are you a web developer?  Are you interested in running Ubuntu or
Kubuntu as as your development OS? Then you may want to check out the
[Kubuntu 15.04 Preferred Software and Hardware](https://docs.google.com/spreadsheets/d/1kLIYKYRsan_nvqGSZF-xJNxMkivH7uNdd6F-xY0hAUM/pubhtml)
which provides lots of information that can be used to get (K)ubuntu Linux
running sooner, faster, and better for web developers.

### The benefits of using Linux

Web developers almost always deploy to Linux systems.
But Linux isn't just for servers.  Using Linux as our development OS 
can provide multiple benefits:

- Deployment is smoother because the mistmatches between
  development and deployment environments is greatly reduced.
- We discover and use Linux tools in our daily workflow that
  we would otherwise not consider. This increases
  productivity.
- The nightmare of having development systems that spans multiple OSes
  goes away.  Ever see a system that require OSX, Windows,
  *and* CentOS to complete a single build?  Trust me, they're out there.
- Client set-up is often much faster.  Developer set-up for some
  systems and OSes can take multiple days due to all the manual steps required.
  Comparable Linux set-ups can take less than an hour because
  configuration is easily automated.
- Most work setting up a Linux development environment is directly
  applicable for deployment.

Unfortunately Linux use also comes with some pain points, which we will
explore next.

### ... And the downsides

Many web developers have good reasons to prefer to use Macs or Windows as
their development platform despite all the benefits of using Linux.
Here are some of the most common:

- Moving platforms can be a lot of work.
- They are invested in the Mac or Windows interface.
- They've seen problems running Linux on certain hardware.
- Their OS supports tools and enterprise software that they are uncertain are supported by (K)ubuntu.

Yet (K)ubuntu Linux **can** be used as a primary development platform by addressing
these these downsides.

### Reduce the work

The [Kubuntu 15.04 Preferred Software and Hardware](https://docs.google.com/spreadsheets/d/1kLIYKYRsan_nvqGSZF-xJNxMkivH7uNdd6F-xY0hAUM/pubhtml)
details lots of information that can be used to get (K)ubuntu Linux running sooner, faster,
and better for web developers.

### Get compatible hardware

#### Buy a pre-configured system

The best (K)ubuntu systems, just like the best Mac or Windows systems, are those built
specifically to run the OS. The easiest approach is to get a system
pre-configured with Linux. I recommend [System76](https://system76.com/)
or the [Dell XPS 13](http://www.dell.com/us/business/p/xps-13-linux/pd).
Another approach for the system-builder hobbyists in the audience is to
build their own systems with Linux compatible hardware.

#### Or use one on-hand

Another approach to get a (K)ubuntu system is to install it on a computer
already on-hand.  Most desktops and many Dell and HP laptops work great with this OS,
but it is always wise to check compatibility simply by searching for `Ubuntu
computer_model`. If all looks well, then we can proceed to test the OS on
a virtual machine (Virtual Box is recommended) or using a "Live CD".
We should be certain to test all aspects of the hardware including:

- Network
- Wireless
- Webcam
- Microphone
- Speakers and sound
- Video driver
- Keyboard and mouse
- Printer, NAS, Router
- Other peripherals
- GPU

Currently Nvidia provides the best Linux GPUs, with excellent first-day support, 
excellent performance, and stable OGL 4.5 drivers. Intel iGPU support is pretty good, 
and is generally the least trouble for laptops. AMD GPUs tend to be troublesome, and I
recommend they be avoided if possible.

While many systems will work completely, some may require a configuration or
hardware adjustment.  Some systems will be simply too incompatible to justify
a (K)ubuntu install.  Most MacBooks, for example, are rarely worth the trouble,
IMO.

### Get the desktop

Changing desktop environments for most people isn't fun.
We can select a Linux desktop that is similar - but not identical -
to OSX or Windows.  Here are the desktops I recommend:

- **Windows:** Consider Kubuntu 15.04 with the KDE 5 desktop
- **OSX:** Consider Ubuntu 15.04 with either the Unity or Gnome
  interface.

### Get the tools

Getting developer and enterprise tools on Linux has never been easier.

Software has evolved to where platform-agnostic tools such as *Google for Work* 
or *Office 365* have replaced most tools tied to a specific OS.  At the same time,
many applications available for OSX and Windows now work very well on 
(K)ubuntu Linux: Chrome, Firefox, JetBrains IDEs, VPNs, GoToMeeting, Skype,
Dropbox, Hangouts, Google Drive (Insync), and many more.  And many popular
tools such as MongoDB and NodeJS are Linux-first, which means they work
*best* on a Linux OS.

Steam works very well with (K)ubuntu 15.04 and an Nvidia GPU.  There are now 
over 1,250 titles available including Shadows of Modor, Counter Strike, Bioshock Infinite,
Borderlands 2, and the Metro series.  We can have a great development workstation *and* 
a pretty great home computer the kids can love too.

Some proprietary packages such as Office and PhotoShop remain unavailable
for Linux.  But I don't need those tools. I find Gimp is an excellent web graphics
editor and LibreOffice 5 works just great for spreadsheets and presentations. Gimp can be used to
edit PSD files and LibreOffice can edit Office documents with generally good fidelity.
The minor inconvenience of occassional importing one of these documents - for me - is a small
price to pay for the vast productivity benefit I see from using Kubuntu as my "daily driver."  As always, YMMV.

### Want to share?

If you decide to give (K)ubuntu a go, you can always use the comments or email me at
the link below to ask a question or share an experience.  You can also add
comments to the
[spreadsheet](https://docs.google.com/spreadsheets/d/1kLIYKYRsan_nvqGSZF-xJNxMkivH7uNdd6F-xY0hAUM/pubhtml)
which I could then incorporate into later revisions.  Any help would be greatly appreciated!

Cheers, Mike
