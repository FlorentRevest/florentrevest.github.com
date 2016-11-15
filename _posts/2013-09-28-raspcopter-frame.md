---
layout: post
title: Raspcopter - Frame building
---

<a href="http://pix.toile-libre.org/upload/original/1380366834.jpg"><img src="http://pix.toile-libre.org/upload/original/1380366834.jpg" style="width: 100%; height: auto;"></img></a>

After two months of unbearable waiting, the package of the quadcopter parts ordered from HobbyKing was finally delivered. I started to build the X525 v3 frame with the Brushless motors FC 28-22 Outrunner 1200kv. I may flash the ESC 20A UBEC HobbyKing with the famous SimonK firmware before soldering the engines (however the procedure is delicate so uncertain). This whole assembly phase that is common to all quadcopters is already well documented, so I won't detail those steps on this blog.

However, my programming work and documentation will cover the design of the flight control system integrated on the Raspberry Pi. I will start with the measure of the "attitude" (rotation in space) of the quadcopter in filtered Euler angles, then we will study the PID controller and speed regulation via the Pololu Maestro.

Meanwhile, here is the first photo of the installation. The plastic plate will be directly attached to the X525 and will be used to support the Raspberry Pi, the MPU6050 (the accelerometer) and the Pololu Maestro (interface with ESCs).

For any futher information, do not hesitate to contact me or comment.

P.S: The full list of the equipment is maintained along the code on github: <a href="https://github.com/FlorentRevest/Raspcopter">https://github.com/FlorentRevest/Raspcopter</a>
