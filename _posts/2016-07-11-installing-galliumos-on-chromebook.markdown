---
layout: post
title:  "Installing GalliumOS on a Toshiba Chromebook 2 (2015)"
date:   2016-07-11 08:54:15 -0400
categories: hardware
---
![Desktop screenshot]({{ site:baseurl}}/img/posts/desktop.png)
*my customized linux setup*


Ever since Apple declined to release new Retina Macbook Pros at WWDC 2016, I've been on the hunt for a laptop to tide me over until the eventual release sometime in the fall. While I don't exactly *need* a new laptop just yet (after all, the lab computers at Penn State would be perfectly serviceable for the first few months of my first semester) I decided I would prefer to have something portable to take to class. I wasn't planning on breaking the bank, so I settled on a Chromebook, and after researching a bit (with the help of [Reddit](http://reddit.com/r/chromeos)) I settled on a [Toshiba Chromebook 2](https://www.amazon.com/Toshiba-Chromebook-CB35-C3300-Backlit-Keyboard/dp/B015806LMM), the 2015 edition with the backlit keyboard and beautiful IPS screen. I was able to find a great deal on a refurbished one for $230.
<!--more-->

One of the reasons Chromebooks are so cheap is that they aren't often built with high-powered components. My version came with a 1.7 GHz Celeron processor, 4 GB of ram, and a 16 GB SSD. Most limiting, however, it came installed with ChromeOS. ChromeOS, while great for casual web browsing, doesn't offer much when it comes to setting up a dev environment. While there are options for setting up a full linux environment within ChromeOS (see: [Crouton](https://github.com/dnschneid/crouton)), I decided I didn't want ChromeOS a single bit.


Yet, I had to give ChromeOS credit where credit was due. Because it's so lightweight and its kernel is so optimized, it runs great on the limited hardware most Chromebooks provide. While installing full Linux (and even Windows!) is possible, it's not always the best, performance-wise, and battery life can take a hit. Adding to the complexity, oftentimes it's a pain to set up drivers and keyboard shortcuts to get your hardware working properly on the new OS.

That's where  [GalliumOS](https://galliumos.org/) comes in. I found GalliumOS after searching around a bit, and reading the description convinced me it was what I needed. It's super lightweight, and optimized almost as much as ChromeOS is, utilizing drivers and core files from the ChromeOS kernel itself to run near seamlessly on many popular Chromebooks.

![GalliumOS Homepage]({{ site:baseurl }}/img/posts/gallium.png)
*the GalliumOS homepage*

There's a few options when it comes to actually installing GalliumOS. The least invasive involves using a utility called [chrx](https://chrx.org/). There's a simple step-by-step process which involves enabling Developer Mode (more on that later), running a script to flash the firmware, and then running another script to install GalliumOS alongside ChromeOS.

Like I said before however, I didn't want to see ChromeOS *at all.* Not to mention there's other problems with going the dual-boot route. You'll see a scary white screen warning you of the terrible damage you've done to your system (not really) and you'll always have to enter either `Ctrl+L` or `Ctrl+D` to enter GalliumOS or ChromeOS, repsectively. And there's a few minor graphical glitches while booting up. 
![os verification example]({{ site:baseurl }}/img/posts/osv.png)
*example of the scary white screen*


While 

TBD


Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
