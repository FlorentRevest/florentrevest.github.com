---
layout: post
title: Raspcopter - Ground Control Station
---

<img src="http://pix.toile-libre.org/upload/original/1399577554.jpg" style="width: 100%; height: auto;"/>

The network is a very big piece that delays the project quite a lot without necessarily visible results, so today we will talk briefly about the Ground Control Station PC software. This article is not intended to be as technical as the previous, but only informative.

A convenient tool
=================

There are already countless complete ground control station softwares, like the Ardupilot's one for example. Forking one of these projects was possible but ultimately the Raspcopter got its own flight client software.

It is developed in C++ using the Qt framework, so it is portable and easy to maintain or expand.

#### A controller

First, the task of this station is to act as a controller, ie to collect data from a joystick. The latter is a standard video games joystick bought for ten euros in a store. Reading its data is done by ioctl via the [generic joystick control kernel API](https://www.kernel.org/doc/Documentation/input/joystick-api.txt).

#### A network client

Those joystick data are then transferred to the drone by the network which will be explained in the following article. But the control station is also capable of receiving data of the quadcopter by the same means.

#### Telemetry datas

The data collected from the quadcopter mainly concern the history of angular positions of the drone but it is expected in the future that we may also be able to keep an eye on the percentage of use of processor and memory of the Raspberry Pi.

Two 3rd party graphics libraries were used for the pleasant display of telemetry data: [AnalogWidgets](http://qt-apps.org/content/show.php/AnalogWidgets?content=87780) and [QCustomPlot](http://www.qcustomplot.com/). Thanks to those libraries, we can keep an eye on dials and graphs of measured data, in real-time directly from the ground. The control station also implements a function to export the data in .csv format that can be opened by LibreOffice Calc for further diagnostics in case of problems. And problems should happen quite often!

The source code
===============

Like the rest of the project, the ground control station is open-source and fully [retrievable on GitHub](https://github.com/FlorentRevest/Raspcopter) in the laptop-client folder. Do not  hesitate to star the project if you find it useful. And if you have questions, do not forget that you can contact me.
