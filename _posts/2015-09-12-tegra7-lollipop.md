---
layout: post
title: Fixing Lollipop 5.1 on Tegra Note 7?
---
*OTA 3.0 Screwed the pooch*

![Tegra Note 7 and stylus pimp for the camera](/images/NVIDIA-Tegra-Note-7-420x240.png)

### The problem
When my wife and I first updated our [Tegra Note 7](http://www.nvidia.com/object/evga-tegra-note-tablet.html) to Lollipop we found this once very-fast, cool, and battery-sipping tablet became unbearably laggy, hot, and power-hungry. 

We found this odd because we own the Shield Portable with the same SoC and 5.1 Lollipo runs *great* on that device.  We think the problem accumulated system crud slowed the little tablet down, since the Note has half the RAM of the Shield Portable.

### The solution?
Here are the steps we took to try to fix things.  The tablet now runs **much** better. We still aren't sure if we will keep the update, but so far, things look much, much better.

1. We unlocked the boot loader and installed the NVIDIA Tegra NOTE7 GMS Recovery OS Image from the Nvidia downloads from https://developer.nvidia.com/gameworksdownload. While there *is* a link to the 5.1 OTA ROM, the link is broken.  If Nvidia makes the 5.1 image available again, we might try that later.

2. We carefully read these instructions: https://developer.nvidia.com/sites/default/files/akamai/mobile/docs/HowTo-Flash-TN7-Recovery-Image.txt.  I used 'root' user account on Linux to avoid any access permissions issues. Once we flashed all the images using the flash-all.sh script, we had to select 'continue' on the tablet's bootloader menu and press the power button to continue with the update.

3. When we signed-in for the first time we disabled "BACKUP & RESTORE" to minimize update time.  We also disabled the "For improved accuracy ..." option to maximize battery life.

4. Once our initial sign-in was complete, we saw the OTA 3.0 in the top-left corner of the display.  We proceeded to download it without doing anything else. Since we avoided enabling "BACKUP & RESTORE" earlier, some unnecessary downloads and updates were likely skipped.

5. We wait about 15 minutes for all apps to update. We had to approve about 5 of them. Of course, it could have taken more or less time depending on WiFi speed and number of apps.

6. Immediately after all updates were applied, we accessed the OTA 3.0 update from the top-left corner of the screen and pressed [ Restart and Install ].

7. After the OTA was complete We went to "Settings > Backup & Reset" and enabled "Back up my data" and "Automatic restore".  We also created a power-control shortcut using the instructions found here: http://forum.xda-developers.com/nvidia-tegra-note-7/general/attention-how-to-to-power-control-menu-t3164381.  We optimized all apps, and we turned off WiFi during sleep.

8. We *had* added a second account but found this hurt performance noticeably.  This is probably due to the limited RAM.

We will report back if we see any remaining issues.  But so far, so good.

### Parting thoughts

One thing I really appreciate about Nvidia is that they *usually* provide some of the best Android support outside of the Google Nexus program and Motorola. Our family have no carrier contracts and we own all of our Android phones and tablets - Nvidia, Nexus, Moto, ASUS, and EVGA devices, in that order.

So I was really jazzed about the Tegra Note 7 getting the Lollipop upgrade because it had been overlooked for the last year.  I wish Nvidia would do a bit more to *own* this problem, because as the above experience shows, they are so close to having a really great update.


