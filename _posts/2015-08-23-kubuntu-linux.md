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
But Linux doesn't have to be used just for servers.  Using
Linux as our development OS provides us with multiple benefits:

- Deployments almost always go smoother because the mistmatch between
  development and deployment environments is greatly reduced.
- Developers discover and use Linux tools in their daily workflow that
  they would otherwise not consider in other OSes.  This increases
  productivity.
- The nightmare of having development systems that spans multiple OSes
  goes away.  Ever see a system that require OSX, Windows,
  *and* CentOS to complete a single build?  Trust me, they're out there.
- Client set-up is often much faster.  Developer set-up for some
  systems and OSes can take multiple days due to all the manual steps required.
  I've seen comparable Linux set-ups take less than an hour because the
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
- They seen problems running Linux on certain hardware.
- The OS supports tools and enterprise software they can't find on Linux.

Yet Linux **can** be used as a primary development platform by addressing
these these downsides.  First, let's reduce the work.

### Reduce the work

The [Kubuntu 15.04 Preferred Software and Hardware](https://docs.google.com/spreadsheets/d/1kLIYKYRsan_nvqGSZF-xJNxMkivH7uNdd6F-xY0hAUM/pubhtml)
details lots of information that can be used to get (K)ubuntu Linux running sooner, faster,
and better for web developers.  Now let's look at the hardware we might need.

### Get compatible hardware

#### Buy a pre-configured system?

The best (K)ubuntu systems, just like the best Mac or Windows systems, are those built
specifically to run the OS. The easiest approach is to get a system
pre-configured with Linux. I recommend [System76](https://system76.com/)
or the [Dell XPS 13](http://www.dell.com/us/business/p/xps-13-linux/pd).
Another approach for the system-builder hobbyists in the audience is to 
build their own systems with Linux compatible hardware.

#### Or use one on-hand?

Another approach to get a (K)ubuntu system is to install it on a computer
we have on-hand.  Most desktops and many Dell and HP laptops work great with this OS,
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

While many systems will work completely, some may require a configuration or
hardware adjustment. Some systems will be simply too incompatible to justify
a (K)ubuntu install.  MacBooks, for example, are rarely worth the trouble.

Once we've confirmed the hardware is compatible, we can try the OS out for a
few days or weeks. If we decide to commit, I advocate a clean wipe and install
as with any OS.

#### Get a compatible GPU

Currently Nvidia provides the best GPU support, with excellent first-day support
and top-notch drivers. Intel iGPU support is pretty good, and is generally
the least trouble for laptops. AMD GPUs tend to be troublesome, and I
recommend they be avoided if possible.

Once we have the hardware in place, it's time to select the desktop
environment.

### Get the desktop

Changing desktop environments for most people isn't fun.
We can select a Linux desktop that is similar - but not identical -
to OSX or Windows.  Here are the desktops I recommend:

- **Windows:** Consider Kubuntu 15.04 with the KDE 5 desktop
- **OSX:** Consider Ubuntu 15.04 with either the Unity or Gnome
  interface.

Now we need to consider the tools we will run on our desktop environment.

### Get the tools

The good news for Linux is that the entire software world has evolved where
platform-agnostic tools such as *Google for Work* or *Office 365* have replaced
the legacy tools tied to a specific OS. Chrome works very on Linux, as does JetBrains IDEs,
VPNs, GoToMeeting, Skype, Dropbox, Hangouts, Google Drive (Insync), and many more.
While a few proprietary packages such as Office and PhotoShop remain unavailable
for Linux, many find alternatives that are just as good for web graphics or 
complex documents.

If we want to game on Linux, the best solution is Steam plus an Nvidia GPU.
Steam now has over 1,250 titles available including Shadows of Modor,
Counter Strike, Bioshock Infinite, Borderlands 2, and the Metro series.
We can have a great development workstation *and* a pretty great home computer
the kids can love.

### Want to share?

If you decide to give (K)ubuntu a go, you can always use the comments or email me at
the link below to ask a question or share an experience.  You can also add
comments to the 
[spreadsheet](https://docs.google.com/spreadsheets/d/1kLIYKYRsan_nvqGSZF-xJNxMkivH7uNdd6F-xY0hAUM/pubhtml)
which I could then incorporate into later revisions.  Any help would be greatly appreciated!

