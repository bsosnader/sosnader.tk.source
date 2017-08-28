---
layout: post
category: technology
title:  "Installing GalliumOS on a Toshiba Chromebook 2 (2015)"
---
Ever since Apple declined to release new Retina Macbook Pros at WWDC 2016, I've been on the hunt for a laptop to tide me over until the eventual release sometime in the fall. While I don't exactly *need* a new laptop just yet (after all, the lab computers at Penn State would be perfectly serviceable for the first few months of my first semester) I decided I would prefer to have something portable to take to class. I wasn't planning on breaking the bank, so I settled on a Chromebook, and after researching a bit (with the help of [Reddit](http://reddit.com/r/chromeos)) I settled on a [Toshiba Chromebook 2](https://www.amazon.com/Toshiba-Chromebook-CB35-C3300-Backlit-Keyboard/dp/B015806LMM), the 2015 edition with the backlit keyboard and 1920x1080 IPS screen. I was able to find a great deal on a refurbished one for $230.
<!--more-->

One of the reasons Chromebooks are so cheap is that they aren't often built with high-powered components. My version came with a 1.7 GHz Celeron processor, 4 GB of ram, and a 16 GB SSD. Most limiting, however, it came installed with ChromeOS. ChromeOS, while great for casual web browsing, doesn't offer much when it comes to setting up a dev environment. While there are options for setting up a full linux environment within ChromeOS (see: [Crouton](https://github.com/dnschneid/crouton)), I decided I didn't want ChromeOS a single bit.


Yet, I had to give ChromeOS credit where credit was due. Because it's so lightweight and its kernel is so optimized, it runs great on the limited hardware most Chromebooks provide. While installing full Linux (and even Windows!) is possible, it's not always the best, performance-wise, and battery life can take a hit. Adding to the complexity, often it's a pain to set up drivers and keyboard shortcuts to get your hardware working properly on the new OS.

That's where  [GalliumOS](https://galliumos.org/) comes in. I found GalliumOS after searching around a bit, and reading the description convinced me it was what I needed. It's super lightweight, and optimized almost as much as ChromeOS is, utilizing drivers and core files from the ChromeOS kernel itself to run near seamlessly on many popular Chromebooks.

![GalliumOS Homepage]({{ baseurl }}/img/posts/gallium.png)
*the GalliumOS homepage*

There's a few options when it comes to actually installing GalliumOS. The least invasive involves using a utility called [chrx](https://chrx.org/). There's a simple step-by-step process which involves enabling Developer Mode (more on that later), running a script to flash the firmware, and then running another script to install GalliumOS alongside ChromeOS.

Like I said before however, I didn't want to see ChromeOS *at all.* Not to mention there's other problems with going the dual-boot route. You'll see a scary white screen warning you of the terrible damage you've done to your system (not really) and you'll always have to enter either `Ctrl+L` or `Ctrl+D` to enter GalliumOS or ChromeOS, repsectively. And there's a few minor graphical glitches while booting up. 
![os verification example]({{ baseurl }}/img/posts/osv.png)
*example of the scary white screen*


While these may be only minor annoyances, bearable to those who don't want to mess with their system *too* much, I knew I wanted to go the more permanent route, which involves completely flashing the BIOS and installing the GalliumOS .iso from a flash drive. Below are the steps I took.

# Preparations

Before I could get around to actually installing GalliumOS, I needed to get my Chromebook ready. The first thing I did was install a new 128 GB SSD, following [this guide](http://www.codedonut.com/chromebook/upgrade-ssd-toshiba-chromebook-2/). It's a little scary opening up the Chromebook, but using a plastic spudger tool and starting at the hinges of the laptop made it much easier than expected. 

Once inside, I replaced the SSD and removed the hardware write protect screw. Removing this screw and the metallic circle underneath is necessary to allow proper flashing of the BIOS. [There are pictures of the inside here](https://plus.google.com/105587851792537311339/posts/XhTMN2zdkHG). The screw to remove is right next to the SSD and wifi card. 

After restoring ChromeOS, I powered off the device and pressed `Esc+Refresh+Power` and then `Ctrl+D` to put the device into Developer Mode, which allows changes to be made to the core files. After 15 minutes or so, my laptop rebooted and I pressed `Ctrl+D` to get into ChromeOS for the last time.

# Flashing the BIOS

This step couldn't have been simpler. I used coolstar's Full ROM which included fixes for the graphical issues and removed the scary white screen. **Important:** this also removes the ability to boot ChromeOS, meaning it is possible to brick your device. You can still recover it, but it's a bit of a process. 

Anyway, while I was in ChromeOS, I opened up a Chrome tab, pressed `Ctrl+Alt+T`, typed "shell," and then `Enter` and then pasted the following command into the prompt and pressed enter. 

{% highlight shell %}
cd ~; curl -L -O https://mrchromebox.tech/firmware-util.sh; sudo bash firmware-util.sh
{% endhighlight %}

At this point, if you've suddenly decided you want to install Windows instead, you can continue with [coolstar's guide](https://coolstar.org/chromebook/windows-install.html). Otherwise, it's time to make a bootable USB drive of GalliumOS.

# Installing GalliumOS

I used [Win32DiskImager](https://sourceforge.net/projects/win32diskimager/)) to make my flash drive after downloading the GalliumOS .iso from their website. All I needed to do to install was plug it in and reboot my device. After following a few onscreen prompts, GalliumOS was installed!

# Customizing GalliumOS

GalliumOS is based on Xubuntu, and while it looks and acts pretty great out of the box, there's a couple things I did to make it prettier and more functional. 

Clicking the GalliumOS icon in the bottom left corner brings up a sort of Start Menu. From there, I navigated to Settings, and then Keyboard, and binded `Ctrl+Alt+T` to the command `xfce4-terminal`, which opens the Terminal. 

I installed [Albert](http://www.webupd8.org/2015/01/albert-fast-lightweight-quick-launcher.html) which is a task launcher kind of like Synapse or Alfred. I binded it to `Alt+A` and messed around with the functionality. Since the Toshiba Chromebook doesn't have a `Super` key, I wanted something to quickly launch applications.

I also installed [TLP](http://linrunner.de/en/tlp/tlp.html), which is supposed to improve battery life. I haven't really been able to test how well my battery is in GalliumOS, but I have not noticed it draining any quicker than I would have expected. TLP is in the Ubuntu repo for 16.04 so all it needs is a simple `sudo apt-get intall tlp`. 

I moved the bottom panel to the top, shrunk it a bit, and removed the functionality that showed open windows. Then I installed [Plank](https://launchpad.net/plank) to get more of an OSX feel.

After installing Chrome, I made shortcuts to my Chrome Apps and added them to Plank. Now, even when I'm offline I can edit documents using Google Docs and access my files in Google Drive.  

I've been a member of [Desktoppr.co](http://desktoppr.co) for a while now, and I've amassed a pretty large selection of beautiful wallpapers. It's really simple to get a slideshow of wallpapers in the XFCE environemnt. I just right clicked (clicked the touchpad with two fingers) on the desktop, selected to customize it, chose the folder where I downloaded my wallpapers, and checked the box to shuffle wallpapers with a timer. The end result of all this is pretty great.

![Desktop screenshot]({{ baseurl }}/img/posts/desktop.png)
*my customized linux setup*

And that's it! I'm completely happy with my laptop, and I'm even second-guessing whether or not I'll really need the new Macbook Pro when it comes out. This little thing looks beautiful and I haven't run into any hiccups or slowdowns yet. Certainly a great deal for only $280.