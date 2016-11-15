---
layout: post
title: Raspcopter - Engines control
---

<img src="http://pix.toile-libre.org/upload/original/1386413694.jpg" style="width: 100%; height: auto;"></img>

The latest article gave the Raspcopter a powerful angular self-regulation algorithm. Yet, one detail was ignored and not the least, the "Feedback" shown at the bottom of the PID controller scheme ensuring the closed loop function. This feedback is of course the engines speed management, the subject of today's article.

What we want
============

Like any flying machine, our little drone must have a sufficiently large vertical thrust force in order to release it of gravity. This strength is given by our old friend Newton in his third law of reciprocal actions. The propellers of the quadcopter push air downwards and by reaction, the drone is pushed up, it's as simple as that.

But the drone introduced a constraint: the propeller must be driven at a fairly substantial rate and by rather light engines. A type of motors solves this problem, the "brushless" motors.

<center><img src="http://pix.toile-libre.org/upload/original/1386704254.jpg"></img></center>

Their operating principle is quite simple, above you can see thre coils electricaly powered one after the other to create a magnetic field that will move the magnet at the center, carrying the propeller's axis. The Raspcopter engines use 12 coils but the principle is still based on the alternation of three of them, it is also for this reason that it is sufficient to reverse two wires randomly in order to change the direction of rotation.

What we have
============

We want to controle these motors from the Raspberry Pi with the minimum latency. The Raspberry Pi has several interfaces, mainly GPIOs and USB. The first idea seemingly most natural would be to directly control the motors from the GPIOs pine but it is in fact impossible because the bobbin exchange rate would never be sufficient and the energy delivered would also be far too low.

How can we get from what we have to what we want ?
==================================================

For this reason, we have to use "ESCs" (Electronic Speed Controllers) placed before each motor, these electronic circuits are fed by two cables from the Lipo battery and receive a PWM (Pulse Width Modulation) signal from three wires in exactly the same maneer as Servomotors. The output of these controllers is made of three wires welded to the brushless motor and alternating the coils power supply as seen previously. The chosen ESC is the 20A UBEC model of HobbyKing.

<center><img src="http://pix.toile-libre.org/upload/original/1387029010.jpg"></img></center>

We already simplified the problem because sending a PWM signal from the Raspberry Pi's GPIOs is no longer impossible. However, generating four PWM is a harder thing. Some projects still use the GPIOs directly however I don't have full confidence in it so I preferred to buy an external servo circuit management: the Pololu Micro Maestro which unloads the Raspberry from this heavy task. I chose the Maestro because of its USB connection, however Adafruit also offers excellent i2c circuits doing the same thing.

<center><img src="http://pix.toile-libre.org/upload/original/1387030267.jpg" style="width: 150px; height: auto;"></img></center>

The code managing the Pololu Maestro is in the "Motor" class of the flight system code hosted on GitHub, it uses libusb to manage the speed of each motor as required by the specifications of Pololu.

Sources
=======

- [A Crash Course on Indoor Flying Robots](https://www.khanacademy.org/partner-content/mit-k12/mit-k12-physics/v/indoor-flying-robots) 
- [HowStuffWorks "How does a brushless electric motor work?"](http://www.howstuffworks.com/brushless-motor.htm)
- [Hobbyking 20A ESC 3A UBEC](http://www.hobbyking.com/hobbyking/store/__15202__hobby_king_20a_esc_3a_ubec.html)
- [Adafruit 16 Channels controller](http://www.adafruit.com/products/815)
- [Pololu Maestro Servo Controller's User Guide](http://www.pololu.com/docs/0J40)
- [How to use GPIO on Raspberry Pi](https://sites.google.com/site/semilleroadt/raspberry-pi-tutorials/gpio)
- [Motors.cpp](https://github.com/FlorentRevest/Raspcopter/blob/master/raspberry-server/Motors.cpp)
