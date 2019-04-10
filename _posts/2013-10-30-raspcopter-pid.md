---
layout: post
title: Raspcopter - PID (Proportional Integral Derivative) controller
---
<img src="http://pix.toile-libre.org/upload/original/1386360951.jpg" style="width: 100%; height: auto;"/>

In the previous article, we started studying a quadcopter flight system. The first step was to recover the attitude, that is to say the angular position of the drone in the air. Today we want to exploit these angle measurements in order to stabilize the quadcopter around values specified by the ground control station.

What we want
============

While it is true that the drone must be able to remain parallel to the ground when no command is received, it also must know how to rotate on its axis in order to move. These rotations are specified by the "ground control station", a software running on a laptop and processing the data of a joystick. The joystick position reflects a desired angle which is the transported by WiFi and interepreted by the Raspberry Pi.

We would like to have a fluid flight despite the sudden changes in position of the joystick, while keeping control on the more or less aggressive engines response.

What we have
============

When the quadcopter confronts the three Euler angles measured by the accelerometer as seen in the previous article and the desired angles sent by the ground station, the difference between these two data produce a sudden *error*. If the engine speed is changed at the same time as the occurence of the error, such as as the square wave graph below: The powerful and sudden impulse risks at best to exceed the desired angle and go back indefinitely creating instability, at worst to overthrow the quadcopter immediately. It is therefore understandable that it is necessary to have an algorithm "smoothing" the transitions.

<center><img src="http://pix.toile-libre.org/upload/original/1381347244.jpg"/></center>

How can we get from what we have to what we want ?
==================================================

This problem is pervasive in engineering and requires the use of a "regulator", we will discuss and use the PID controller (for Proportional, Integral, Derivative).

In our quadcopter project, measurements being made on three angles, it is necessary to use three different PID controllers. This algorithm takes as input the difference between the desired angle and the measured angle, for example when the ground sends nothing this error is the orientation of the UAV relative to the ground. Mathematical operations are applied to these errors and determine the speed that should be given to the four motors.

*The PID controller is an algorithm that takes an error as its input and returns a linear combination of this error with the integral of this error and the derivative of this error.* **That's it.**

One quickly sees the benefits of this algorithm: first it is very simple to understand and implement. But we also know that it is extremely reliable, this controller is the most used in the world and is found everywhere... even in the flush of your toilet !

<center><img src="http://pix.toile-libre.org/upload/original/1386350513.jpg"/></center>

Proportional
============

The proportional term is obtained very simply by multiplying the error by a constant named "proportional gain". The greater the gain is, the greater the response speed is, but it may be unstable. The smaller the gain is, the more the response speed is "soft" and likely to be ineffective. It is important to find a good intermediate between these two extremes.

<center><img src="http://pix.toile-libre.org/upload/original/1386351053.png"/></center>

Here we can see that a proportional gain (Kp) that is too large results in a significant overshoot of the desired angle (the blue reference signal).

Integral
========

Unlike a simple proportional control system, the PID controller takes into account the history of angular errors. For this, the integral term is introduced, it is the sum of all the errors accumulated over time multiplied by a constant, the "integral gain" Ki.

<center><img src="http://pix.toile-libre.org/upload/original/1386353656.png"/></center>

The integral gain directly affects the height (and therefore the number) of the target value overshoots. Too low gain is problematic in certain situations such as when the wind is too strong. However a too high gain causes an oscilation around the desired angle.

Derivative
==========

The derivative term is sometimes called "accelerator" since it can compress the response time. It is obtained by substracting the current and previous errors multiplied by the derivative gain. This term, however, is to be taken lightly because it is very sensitive to data noise.

<center><img src="http://pix.toile-libre.org/upload/original/1386353681.png"/></center>

Once is not a custom, chart allows us to better understand the impact of the gain.

And then ?
==========

Once the code of the PID controller implemented (the very simple PID class is available on github) it is to determine the three gains of each controller. As every quadcopter has special features, there is no universal PID constants, however the determination of these values is not random either.

First, it should be noted that a rapid determination method, called "Ziegler and Nichols method" exists and provides correct Ki and Kd from the only value of Kp. The gains can then be adjusted more precisely based on parameters seen above.

Moreover, a quadcopter (unlike a tricopter of Hexacopter) is almost symmetrical, so the pitch and roll PID's gains should be similar.

Finally, it is important to note that for security reasons the testing of these PIDs is never done in real conditions outside. By firmly hanging the quadcopter to a bar parallel to an axis of rotation, we are able to block the rotation of the other angles and work on one PID at a time.

Sources
=======

- [A Guide To Proportional Integral and Derivative Control (PID)](http://aeroquad.com/showwiki.php?title=A+Guide+To+Proportional+Integral+and+Derivative+PID+Control)
- [PID régulateur ? Comment ça marche ?](http://wiki.openpilot.org/pages/viewpage.action?pageId=21857142)
- [PID controller - Wikipédia](http://en.wikipedia.org/wiki/PID_controller)
- [Quadcopter PID Explained and Tuning](http://blog.oscarliang.net/quadcopter-pid-explained-tuning/)
- [PID Tuning, how "I did it my way"](http://blog.pistuffing.co.uk/?p=1314)
- [PID Tuning](http://aeroquad.com/showwiki.php?title=PID+Tuning)
- [Stabilization Tuning Multirotor](http://wiki.openpilot.org/display/Doc/Stabilization+Tuning++Multirotor)
- [AIDE: réglage empirique des correcteurs](http://www-hadoc.lag.ensieg.inpg.fr/hadoc/ateliers/reglageEmpCorr/ReglEmpCorrAide.html)
