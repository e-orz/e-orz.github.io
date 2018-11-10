---
layout: post
title:  "Webex on Linux - a Fairy Tale?"
date:   2018-03-15 18:30:25 +0200
categories: linux 
tags: webex linux
---
Hello
=====
Starting something new is always exciting. This is my first post in my new shiny (well, not really) blog. I hope for it to be useful.

Perhaps I shall begin with a nifty Scala trick? Could be, but there is already plenty information out there.

IMHO, every developer should know a bit of IT stuff. Most of us don't like it but it's an essential tool. Sometimes you need to get things done by yourself, otherwise they won't be done at all. As fate would have it, I found myself working for a company in which Linux support is shamefully neglected. I work on a Linux machine (I don't have Windows even in a VM) and I needed Webex to contact to the "outer world".

Some History
============

This leads us to Webex on Linux. There is plenty of information on how to do it, however, it is outdated and has limitations:
* no screen sharing
* no audio
* using docker - it requires privileged running of docker and tends to crash a lot

Thanks to the Firefox guys that removed support for java in the browser (from version 51, if I remember correctly), Cisco had to allow Webex connection by downloading and running a JNLP file and the game has totally changed[^1].

Installing Webex
================


High Level Instructions
-----------------------
0. Install the client dependencies
0. Copy Java JRE 32-bit version and install all its dependencies
0. Run the JNLP using the above Java version 
0. Done!

Detailed Instructions
---------------------
### 1. Getting Java 32bit and Installing Its Dependencies
0. Download the latest Java JRE (tested with the Oracle one) and extract it somewhere
0. Create a link for the `javaws` in the bin directory in your `~/bin`. E.g. `ln -s theRealJavaWs ~/bin/javaws`
0. Install the Java 32bit dependencies
   0. Run `sudo apt install openjdk-8-jre:i386 -s` and look for `The following additional packages will be installed:`
   0. Copy-paste the list **without the ones starting with openjdk**. This way you will have the list of all the dependencies needed to properly run Java 32bit
   0. Run `sudo apt install` _theList_

### 2. Getting the Webex Client 
0. Set a meeting (either yourself or get an invite from a colleague)
0. Click the meeting request from the mail (or copy paste to the browser)
0. Save the JNLP file
0. Double click on it using Nautilus and choose javaws as the running application
0. After the installation is finished you will have a running client (currently without audio or screen sharing)
0. Close the client
0. Go to Firefox preferences and add javaws to the list of the running applications of JNLP files.

### 3. Installing Client dependencies
0. Install the dependency checker:
   0. `sudo apt-get install apt-file`
   0. `apt-file -a i386 update`
0. Go to the Webex client directory. (e.g. `~/.webex/something`. Just search the directory with lots of `.so` files under `~/.webex`)
0. Run `ldd *.so`
0. If you get an error that the files are not dynamically something it means that the files are 32bit and `ldd` cannot work on them without dependencies (don't remember what to install). Use `file filename.so` to see that the files are actually executables
0. Run `ldd *.so | grep not` to get the list of the missing dependencies
   0. For each missing dependency:
      0. `apt-file -a i386 search dependency`
      0. Use some common sense to know from the list what to install. Install it using `sudo apt install packageName:i386`.

Done! Now you can use Webex to your heart's content
 
"Quick Win" for Ubuntu 17.10
----------------------------
The most cumbersome step is finding the dependencies. Here are the lists for Ubuntu 17.10 (might work on other versions, too)

For java 32 bit install:

`libasound2:i386 libasyncns0:i386 libatk-wrapper-java-jni:i386 libflac8:i386 libgail-common:i386 libgail18:i386 libgif7:i386 libgl1-mesa-glx:i386 libglapi-mesa:i386 libgtk2.0-0:i386 libnspr4:i386 libnss3:i386 libogg0:i386 libpcsclite1:i386 libpulse0:i386 libsndfile1:i386 libvorbis0a:i386 libvorbisenc2:i386 libwrap0:i386 libxcb-glx0:i386 libxtst6:i386 libxxf86vm1:i386 libasound2-plugins:i386 librsvg2-common:i386 gvfs:i386`

Additional packages for Webex:

`libxmu6:i386 libpangoxft-1.0-0:i386 libxft2:i386 libxv1:i386`

Do all the other steps and you can be up and running in 5 minutes!

Known Limitations
-----------------

Almost none!

* No cam support - It is the client's limitation (other participant can be seen, though)
* No personal room support
  * As a workaround just use set a meeting and choose _meet now_

Future Work
-----------
The reason for the 32 bit Java version was originally due to some Firefox limitation that could not run the audio using the 64 bit version. Now that the JNLP is run totally outside Firefox the 64 bit might be good as well.

Also, originally Firefox was a must. I have a feeling that Chrome will be as good now that the JNLP is run outside the browser.

Epilogue
========
I spent about a year (!) of occasional fiddling with Webex until perfecting this technique. I hope you will find it helpful and productive. Please write your comments (good, bad, anything that needs more clearance or just anything) down below.

Thanks for bearing with me until here. See you next time.

Eli

[^1]: Besides the suggested technique in the post, nowadays Cisco has made a WebRTC Webex client that works flawlessly in Linux (all that is needed is a modern browser) but:
    0. no screen sharing support 
    0. the bandwidth is 3 times greater than the native client one

