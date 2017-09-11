---
layout: post
title: Revert the Pixel C from Oreo to Nougat
---
*The bleeding edge is where you bleed*

I was pleasantly surprised to find Oreo (Android 8) ready to install on my Pixel C tablet a few days ago. The OTA update went smoothly but it had one unacceptable flaw: Oreo broke the one game I play regularly (War Robots, if you must know). Here's how I fixed it.

## Download platform tools
This step was precautionary. One could probably use the `fastboot` and `adb` tools already installed on Ubuntu 17.04. Oh well, this worked. One can find these tools for Linux, Mac, and PC on the [Android Studio site][_01]. After download, I opened a terminal and set up the tools.

```bash
  mkdir PixelC
  mv ~/Downloads/platform-tools-latest-linux.zip ./PixelC

  # Add tools to path
  cd PixelC
  export PATH=`pwd`/platform-tools:$PATH
```

## Download latest Nougat
I downloaded the latest [ryu build for the Pixel C][_03] release August 2017 from the [offical Google Android image site][_02], moved it to a `PixelC` directory, and then unzipped it.

```bash
  mv ~/Downloads/ryu-n2g48c-factory-0bc0ee15.zip ~/PixelC/
  cd ~/PixelC
  unzip ryu-n2g48c-factory-0bc0ee15.zip
```

## Prepare the tablet
**Warning: The following procedure erases all your data. Do not proceed without a backup!**

First I enabled developer options by vistiting `Settings` > `About Tablet` and tapping on the `Build` seven times. A message on the bottom confirms that I'm now a "developer" - thanks Google! Next selected `Settings` > `Developer options` and enabled `USB debugging` and `OEM unlock`. I attached the tablet to the computer using USB C cable and unlocked its bootloader.

```bash
  sudo adb start-server
  sudo adb devices # should show tablet
  sudo adb reboot bootloader
  sudo fastboot oem unlock
```

The last step took about 5 minute and **erased all user data on the tablet**. The tablet then rebooted and presented me with plain "Waiting for fastboot command" message.

## Flash the image
Here is were things got tricky. The *official* means to flash failed because the archive does not contain `boot.sig`, `recovery.sig`, or `system.sig` files. But an Android Central [forum post][_04] a [Nexus 7 article][_05] from five years ago saved the day. Back to our trusty terminal to work the magic.

```bash
  cd ryu-n2g48c

  # Running sudo ./flash-all.sh failed.
  # This is the alternate solution that worked.

  unzip image-ryu-n2g48c.zip

  sudo fastboot devices # should show tablet, if not, fix before proceeding
  sudo fastboot flash bootloader bootloader-dragon-google_smaug.7900.50.0.img
  sudo fastboot reboot-bootloader
  sleep 5

  sudo fastboot flash boot boot.img
  sudo fastboot flash system system.img
  sudo fastboot flash cache cache.img
  sudo fastboot flash vendor vendor.img
  sudo fastboot flash recovery recovery.img

  # IMPORTANT Do this here as it erases all user data again!
  # sudo fastboot oem lock

  sudo fastboot reboot
```

After this the device warned the the bootloader was unlocked and that I would have to wait 30s for a reboot.
In retrospect, I *should* have relocked the bootloader before rebooting as shown above since it re-erases all user data. I set up the like-new tablet from a backup and patiently wait for all the apps to reinstall - around 30 minutes.

## Relock bootloader
If you're playing along at home and still haven't relocked your boot loader, well you're in trouble because doing so is going to completely delete your data again. I had to again enabled developer options and USB debugging [as before](#prepare-the-tablet). With the tablet still atached to the computer via USB C, I re-locked the bootloader again and rebooted as shown below.

```bash
  sudo adb start-server
  sudo adb devices # should show tablet, if not, fix before proceeding

  sudo adb reboot-bootloader

  # This will again erase all user apps and data
  # and requires confirmation on the tablet

  sudo fastboot oem lock
  sudo fastboot reboot
```

... And then I got to install my apps and data all over again.

Cheers, Mike

## End

[_01]:https://developer.android.com/studio/releases/platform-tools.html
[_02]:https://developers.google.com/android/images
[_03]:https://dl.google.com/dl/android/aosp/ryu-n2g48c-factory-0bc0ee15.zip
[_04]:https://forum.xda-developers.com/showthread.php?t=1992063
[_05]:https://forums.androidcentral.com/google-nexus-7-2012-rooting-roms-hacks/191477-guide-nexus-7-factory-image-restore.html

