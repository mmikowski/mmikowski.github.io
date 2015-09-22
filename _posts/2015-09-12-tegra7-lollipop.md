---
layout: post
title: Fixing Lollipop 5.1 on Tegra Note 7?
---
*OTA 3.0 Screwed the pooch*

![Tegra Note 7 and stylus pimp for the camera](/images/NVIDIA-Tegra-Note-7-420x240.png)

### The problem
When my wife and I first updated our [Tegra Note 7](http://www.nvidia.com/object/evga-tegra-note-tablet.html) to Lollipop we found this once very-fast, cool, and battery-sipping tablet became unbearably laggy, hot, and power-hungry.

We found this odd because we own the Shield Portable with the same SoC and 5.1 Lollipop runs *great* on that device.  This is just a guess, but we think the problem of accumulated system or user data slows the little tablet down because the Note 7 only has half the RAM (1GB).

### Reset didn't fix it
When we first saw the laggy performance and overheating, we performed a factory reset. This did not resolve any of the problems.

### The solution
Our initial intent was to download the OTA 3.0 GSM recovery image from [Nvidia downloads page](https://developer.nvidia.com/gameworksdownload) and install it. Unfortunately, the OTA 3.0 link *is broken*.  Instead we decided to load the 2.5 GSM recovery image and then try updating to Lollipop using the OTA process.

Once we had finished the tablet runs **much** better than the previous Lollipop install.  As an added bonus, if we want to revert to OTA 2.5 (KitKat 4.4) - which is an excellent ROM - we can do so in 10 minutes or so. The entire procedure took less than a half hour.  Here are the individual steps we took:

1. We unlocked the boot loader and installed the NVIDIA Tegra NOTE7 GMS Recovery OS Image from the [Nvidia downloads page](https://developer.nvidia.com/gameworksdownload). While there *is* a link to the 5.1 OTA ROM, the link *is broken*.  If Nvidia makes the 5.1 image available again, we might try that later.

2. We carefully read and followed the [instructions](https://developer.nvidia.com/sites/default/files/akamai/mobile/docs/HowTo-Flash-TN7-Recovery-Image.txt).  I used 'root' user account on Linux to avoid any access permissions issues.  Once we flashed all the images using the `flash-all.sh` script, we had to select `continue` on the tablet's bootloader menu and press the power button to continue with the update.

3. When we signed-in for the first time we disabled `BACKUP & RESTORE` to minimize update time.  We also disabled the `For improved accuracy ...` option to maximize battery life.

4. Once our initial sign-in was complete, we allowed automatic updates to proceed. It took about 15 minutes for all apps to update. We had to approve 5 of them. Because we had disabled `BACKUP & RESTORE` earlier, we probably saved time by avoiding downloading and updating data and applications.

5. Immediately after all updates were applied, we clicked on the OTA 3.0 update in the top-left corner of the screen and pressed `Restart and Install`.

6. After the OTA was complete - in other words, Android 5.1 had been installed - we went to `Settings > Backup & Reset` and enabled `Back up my data` and `Automatic restore`.  We also created a power-control shortcut using [these instructions](http://forum.xda-developers.com/nvidia-tegra-note-7/general/attention-how-to-to-power-control-menu-t3164381). We optimized all apps, and we turned off WiFi during sleep. **This is important to maximize battery life and reduce heat**! 

7. We found that **adding a second account** reduced performance noticeably.  This is probably due to the limited RAM.  Just to be safe, we repeated this procedure using my wife's account as the primary and only account.

We have found the upgrade to be very much improved.  The tablet is responsive and cool. The battery life is good again, and it charges completely in under 2 hours (using a Shield charger).

### Parting thoughts

One thing I really appreciate about Nvidia is that they *usually* provide some of the best Android support outside of the Google Nexus program and Motorola. Our family have no carrier contracts and we own all of our Android phones and tablets - Nvidia, Nexus, Moto, ASUS, and EVGA devices, in that order.

So I was really jazzed about the Tegra Note 7 getting the Lollipop upgrade because it had been overlooked for the last year. It's too bad that we had to wrestle with the problem, which I suspect affects the early adopters more than recent purchasers.  I wish Nvidia would do a bit more to *own* this problem, because as the above experience shows, they are so close to having a really great update for almost everyone.


