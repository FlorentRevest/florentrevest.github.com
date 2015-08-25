---
layout: post
title: Raspcopter - Video of the first PID tuning tests
---

<iframe src="//player.vimeo.com/video/99163238?color=ffffff" width="100%" height="360" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

<center><a href="http://vimeo.com/99163238">Raspcopter - First series of test-flights</a></center>

As we have seen, the UAV flight system is based on three separated PID controllers, one for each angle. However theses regulators must be tuned one after the other independently and safely before we can hope to achieve a flight to the open air. To set these values, it is convenient to block possible rotations to only one angle. This is accomplished by holding the UAV by a bar parallel to an axis of rotation.

The first idea was tested using two ropes tied to fences. If this idea has mainly led to failures, we can still note some interesting aspects and also learn from them. The video above shows a handful of tests.

Let's start with the positive: we can rejoice because the quadcopter lifts off at 40% of engines power. This suggets that the power is sufficient and that we can add additional weight. These tests have also allowed me to test the drone and the control station in duration. A tab in the ground control station specially dedicated to remotely changing PID values proved to be partiularly useful.

But there are also some negative points in this video. First, the range of motion left to the quadcopter does not actually blocks it on an angle so when working on the roll, we get fluctuations on the pitch and yaw axis which make working with the PID difficult or impossible. Furthermore, the height amplitude left by the strings allows the quadcopter to rise at high speed before a shock occurs when the strings are stretched violently. The drone is then turned down to the bottom which is bad for testing. (we can see this effect at 0:55)
