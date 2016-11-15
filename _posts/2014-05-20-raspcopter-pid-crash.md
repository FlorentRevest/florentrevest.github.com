---
layout: post
title: Raspcopter - First crash
---

<img src="http://pix.toile-libre.org/upload/original/1404331500.png" style="width: 100%; height: auto;"></img>

The project progresses! Indeed, the code base is almost finished, logging functions are expected to come later but this is not a priority for now. This means that the rather technical articles that I have written so far should now let room for a serie of more practical, visual and general-public-oriented articles. But as we can suspect, the transition from theory to practice is always accompanied by small unexpected effects.

First test means invariably first crash. And that's exactly what happened recently, when I added a non-null integral gain to the PID parameters of the quadcopter.

The UAV was in operation for more than an hour and half, the integral variables that contain the sum of the angles errors thus contained gigantic values. They suddenly found themselves multiplied by a gain. This produces a maximum engine speed and the drone overturned immediately before breaking propellers. Further investigations on the behavior of the integral gain will therefor be needed.

Fortunately, only the propeller were damaged, but the new parts order will delay the project. This time I took care of ordering 20 propellers ahead of the inevitable next crashes !
