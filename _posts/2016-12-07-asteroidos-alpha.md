---
layout: post
title: AsteroidOS - Alpha 1.0 Release
---

[AsteroidOS](https://asteroidos.org/) is an open-source operating system for smartwatches on which I've been working for the past two years. Today, I'm delighted to announce the availability of the first alpha release of AsteroidOS for four different devices.

<iframe width="640" height="360" src="https://www.youtube.com/embed/cVg-vEJlUkU?rel=0&amp;showinfo=0" frameborder="0" allowfullscreen></iframe>

A Vision
========
A smartwatch is a connected device that can get access to a wide range of personal information. Many users believe that the current proprietary platforms can not guarantee a satisfactory level of control over their privacy and hardware. Hence, I noticed a need for an open wearable platform and AsteroidOS is my attempt to address this issue.

Several core values led the development of AsteroidOS:

* **Freedom**: Embedding a maximum number of free software
* **Privacy**: Staying neutral on the usage of user's data
* **Community**: Gathering people passionate about this goal
* **Portability**: Supporting as many devices as possible
* **Modularity**: Allowing the user to tweak the OS in depth
* **Interoperability**: Communicating flawlessly with other devices

<img src="{{ site.url }}/assets/2016-12-07-launcher-screenshots.jpg" style="width: 100%; height: auto;">

A platform
==========
Today, AsteroidOS is a complete platform able to run numerous apps on several smartwatches. The default set of apps covers the basic needs of a smartwatch user:

* **Agenda**: Schedules a set of events to remember
* **Alarm Clock**: Schedules an alarm at a specific time
* **Calculator**: Quickly computes simple calculations
* **Music**: Remotely controls your phone's music player
* **Settings**: Configures AsteroidOS to your needs
* **Stopwatch**: Measures an elapsed time between two taps
* **Timer**: Vibrates after a specified amount of time
* **Weather**: Shows following days' weather forecast

AsteroidOS can be ported to many smartwatches and [new port contributions](https://asteroidos.org/wiki/porting-guide/) are always welcome. For now, four ports have reached a satisfactory level:

* **LG G Watch**: *(dory)* Default development platform, benefits from the best support
* **LG G Watch Urbane**: *(bass)* Well supported but Bluetooth doesn't work yet
* **Asus ZenWatch 2**: *(sparrow)* Good support but Bluetooth doesn't work yet either
* **Sony Smartwatch 3**: *(tetra)* Still has some graphic glitches and no Bluetooth

<img src="{{ site.url }}/assets/2016-12-07-apps-screenshots.jpg" style="width: 100%; height: auto;">

A Story
=======
Pushing AsteroidOS from an idea to a proof of concept and then eventually to a viable system has been an incredible journey full of encounters and a beautiful story to share. This project brought me the chance to be a speaker at diverse conferences and to meet so many friends.

In the year 2016 only, I spoke at [FOSDEM](https://archive.fosdem.org/2016/schedule/event/foss_smartwatch/) in Brussels, [QtCon](https://conf.qtcon.org/en/qtcon/public/events/366) in Berlin, [Open Source Summit](http://student.opensourcesummit.paris/projet/asteroidos/) in Paris, [Capitole du Libre](https://2016.capitoledulibre.org/programme.html#asteroidos-la-liberte-sur-votre-poignet) in Toulouse, [Embedded Linux Meetups](https://2016.capitoledulibre.org/programme.html#asteroidos-la-liberte-sur-votre-poignet), [C++/Qt workshops](https://www.agendadulibre.org/events/11336) and also specialized meetups around AsteroidOS in [London](https://twitter.com/applehq/status/724539058173673472) and [Berlin](https://twitter.com/AsteroidOS/status/772909811902078976). Each and every one of these events was an opportunity to exchange ideas with community members which is invaluable to me.

Despite its early status and lack of official releases so far, AsteroidOS has also benefited from a broad mediatic coverage with articles and interviews in countless websites and magazines ([PCWorld](http://www.pcworld.com/article/3039253/an-open-source-alternative-to-android-wear-os-for-smartwatches-emerges.html), [xda-dev](http://www.xda-developers.com/asteroidos-is-an-open-source-alternative-to-android-wear/), [YCombinator](https://news.ycombinator.com/item?id=11977267), [Linux Users](https://www.gadgetdaily.xyz/smartwatches-go-open-source/) etc...). It's always a very pleasing experience to discover articles about AsteroidOS.

More recently, I've also been working with Jolla on a [demonstration of their Sailfish technologies running on top of AsteroidOS](https://blog.jolla.com/watch/). This has been a very interesting collaboration on both technical and community levels and I'm deeply interested in strengthening the links between our projects.

<img src="{{ site.url }}/assets/2016-12-07-conferences.jpg" style="width: 100%; height: auto;">

A Future
========
This alpha release represents a lot of work to be proud of but the road to a stable version is still quite long. In the big picture, I want to see the community get much larger than it is today. This translates into two key axes:  reaching a larger user base with new ports and synchronization apps and also being more present in articles, forums, twitter, events, contests etc...

On a purely technical side, my main goals for the platform are: expanding battery life beyond its current limitations and supporting more sensors. These two aspects are very important in the success of a smartwatch OS and also exciting technical challenges to work on. Until now, they have been volontarily left apart in favor of more fundamental tasks.

I'm also looking forward to collaborating with more companies interested in using AsteroidOS technologies in demos or products and can be contacted at any time.

<img src="{{ site.url }}/assets/2016-12-07-other-screenshots.jpg" style="width: 100%; height: auto;">

Acknowledgements
================
Community is an important part of this project, therefore I would like to give special thanks to various members for their valuable contributions.

* **Emilia Wojnicka**, *bebechiyo*, for her work on the UI and UX
* **Daniel Cordero**, *TheAppleMan*, for his work on the LG G Watch Urbane, the overall porting process and the continuous integration
* **Larson Thorpe**, *Lrs121*, for his work on the Asus ZenWatch 2 and on the xda forum
* **Sylvia van Os**, *SylvieLorxu*, for her work on i18n, watchfaces, F-Droid and bugfixes
* **Aleksi Suomalainen**, *locusf*, for his work on Glacier, the Asus ZenWatch 1 and the Sony Smartwatch 3
* **Julius-Paul Jann**, *Nokius*, for having supported my travel at FOSDEM and for his work on wallpapers and continuous integration
* **Tim Suberkrub**, *suebt*, for his work on qml-asteroid and asteroid-stopwatch
* **Carsten Munk**, *Stskeeps*, for having made this whole project possible via libhybris and for his very strong support along the way
* **Andrea Bernabei**, *faenil*, for his work on Glacier
* **ledixus** for providing and maintaining the AsteroidOS's server
* **Mattia Basaglia**, *Melanosuchus*, for providing a nice IRC bot
* **Sebastian Schmidt**, *EACFreddy* for his work on IRC logs
* **Isaac Salazar**, *iktwo*, for his work on qml-asteroid
* **Simonas Leleiva**, *sledges*, for his valuable help with the very early porting efforts
* **Leo Testard**, *Leo`*, for being there before the project even had a name or a goal
* **Jolla Oy** for their invaluable work on the Linux mobile stack
* **Sony Mobile** for providing me a Smartwatch 3 and the Hero Open Source Developer title
* **KDE e.V.** for having supported my travel at QtCon
* **INSA Toulouse**, my school, for freeing me time to work on AsteroidOS
* **Juraj Perényi** for his work on the slovak and hungarian translations
* **dr_gogeta86** for his work on the italian translation
* **Rasti K5** for his work on the Kurdish Sorani translation
* **Egleson Beltran Quezada** for his work on the spanish translation
* **Chupligin Sergey** for his work on the russian translation
* **nils-developersinblue** for his work on the german translation
* **Klemens Nanni** for his work on the german translation
* **Nathan Follens** for his work on the dutch translation
* **Michal** for his work on the polish translation
* **Pavel Fric** for his work on the czech translation

If you want to join the community, check out the [Get Involved](https://asteroidos.org/community/) page of the AsteroidOS website.
