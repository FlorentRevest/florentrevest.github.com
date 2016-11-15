---
layout: post
title: Raspcopter - Attitude measurement
---

<img src="http://pix.toile-libre.org/upload/original/1380988375.jpg" style="width: 100%; height: auto;"></img>

I inaugurate today the suite of technical articles about my Raspcopter project by starting as promised with the attitude.

What we want
============

While this may seem naive it's true: the first mission of a quadcopter is not to fall... Even when all four engines run at equal and constant speed, the drone ends up twisting and falls alone. This natural rotation is caused by the imperfection of the drone (center of gravity moved, for example) but also and especially by the various physical constraints applied by the environment (typically: the  wind).

It is therefore essential to create a flight system dependent on the attitude of the quadcopter, that is to say, its angular position in space. A quote from Wikipedia is better than a long speech: the attitude is "the orientation of an object with respect to an inertial frame of reference or another entity (the celestial sphere, certain fields, nearby objects, etc.)."

So we need three Euler angles which are named: yaw, pitch and roll.

<center><img src="http://pix.toile-libre.org/upload/original/1380985932.jpg"></img></center>

What we have
============

Many sensors allow to obtain the attitude of the system :

- The accelerometers measure linear acceleration on three axes.
- The gyroscopes, providing a relative angular position on those same axes.
- The magnetometers measuring the magnetic field in order to deduce the position of the north in the manner of a compass.
- GPS receivers, the satellites provide absolute coordinates in latitude and longitude and they also give the rotation.

They all have their advantages and disadvantages. They are never used alone, always combined. By constraints of money and time, my project will only use the first two sensors. The MPU6050 of Invensense brings them together in a single chip, this is called an IMU (Inertial Measurement Unit) with six degrees of freedom. The MPU6050 is also inexpensive and often used by quadcopter projects.

<center><img src="http://pix.toile-libre.org/upload/original/1380986384.jpg" style="width: 100px; height: auto;"></img></center>

This chip is connected to the Raspberry Pi's GPIO via the i2c standard with four wires (SDA, SCL, Ground and 3.3V). So it is first necessary to remove the i2c driver from the Raspbian's modprobe's blacklist.

The use of this sensor is relatively well documented and made easy by the work of Jeff Rowberg on I2CDevLib. So it is easy to retrieve acceleration and rotation values.

How can we get from what we have to what we want ?
==================================================

The heart of the problem lies in the passage of the raw values of the MPU6050 to usual angles. Indeed raw values have three problems :

- Firstly, they contain noise that is to say that the signal is not stable.
- Secondly, they do not have any unit, these value do not match anything tangible.
- Finally, those acceleration and rotation data are not combined into three angles as we wish.

<img src="http://pix.toile-libre.org/upload/original/1380986108.png" style="width: 100%; height: auto;"></img>

A Kalman filter is often used to stabilize values, but its implementation is pretty complex and costs some CPU time.

A much lighter and easy solution would be to create a complementary filter, using the formula :

<center><img src="http://pix.toile-libre.org/upload/original/1380986043.gif"></img></center>

Applied to the roll angles (phi) and pitch (rho) calculated by

<center><img src="http://pix.toile-libre.org/upload/original/1380985903.png"></img></center>
<center><img src="http://pix.toile-libre.org/upload/original/1380985892.png"></img></center>

This formula gives much more weight to the gyro's values as it takes into account the fact that the gyro measurements are stable in the short term but have a "drift" (a shift that occurs with time) over the long term (ie. returning twice in a row a gyroscope wouldn't return the same value). The accelerometer's value measures plenty of forces other than gravity so they have a lot of noise but are much more stable over the long term. This formula isn't totally efficient either.

The choice of a an IMU filter must often be made between those two techniques. But the good news is that the MPU6050 has a "DMP" (Digital Motion Processing) chip which filters those values out of the Raspberry Pi, in real time and more effectively than a home-made algorithm. The bad news is that Invensens jealously protects the functionning of the chip and refuses to document its operation.

This is where Noah Zerkin, an augmented reality entrepreneur who got memory dumps by reverse engineering the chip in DMP mode from the manufacturer demos helps us. I based my code on the excellent work he integrated to I2CDevLib.

My implementation is located in the Accelerometer class of [the server's code](https://github.com/FlorentRevest/Raspcopter/tree/master/raspberry-server). The MPU6050 initialization is done in the constructor and the Euler angles are obtained by the getYawPitchRoll() method.

The Problem with Euler angles
=============================

A final problem is remaining : if the Euler angles describe all the possibilites of angular positions, they are not immune to a "gimbal lock". This problem happens when two gyro axes point in the same direction, the system loses a degree of freedom and pursues an unpredictable movement. This problem seems trivial but it could have stopped the Apollo 11 mission, we will only retain a quote from Michael Collins "How about sending me a fourth gimbal for Christmas ?"

<center><img src="http://pix.toile-libre.org/upload/original/1380985991.gif"></img></center>

The solution, when one does not believe in Santa Claus, would be to use Quaternions, hypercomplex numbers made of a linear combination of four parameters, three of which define an axis and the last sets a rotation around this axis. The handling of this set seems too complex for a 16 year old boy, so I will stick to Euler Angles.

And then ?
==========

The first phase of the control system, data acquisition, is successfully completed. Euler angles: yaw, pitch and roll must now go through a PID controller to return the engines speed. This will be discussed in the next article.

Sources
=======

- [Euler angles](http://en.wikipedia.org/wiki/Euler_angles)
- [Attitude control](http://en.wikipedia.org/wiki/Aircraft_attitude)
- [MPU6050 Triple Axis Accelerometer & Gyro Breakout](http://www.geeetech.com/wiki/index.php/MPU-6050_Triple_Axis_Accelerometer_%26_Gyro_Breakout)
- [i2c sur le Raspberry pi](http://poivron-robotique.fr/I2C-sur-le-Raspberry-Pi.html)
- [Relative attitude sensors](http://en.wikipedia.org/wiki/Attitude_control#Relative_attitude_sensors)
- [Reading a IMU without Kalman: The Complementary Filter](http://www.pieter-jan.com/node/11)
- [MPU-6050: DMP Data from i2cdevlib](http://www.geekmomprojects.com/mpu-6050-dmp-data-from-i2cdevlib/)
- [Gimbal lock](http://en.wikipedia.org/wiki/Gimbal_lock)
- [Quaternions et rotation](dans l'espace: http://fr.wikipedia.org/wiki/Quaternions_et_rotation_dans_l'espace)
- [PiBits, MPU6050 Pi Demo](https://github.com/richardghirst/PiBits/tree/master/MPU6050-Pi-Demo)
- [i2cdevlib MPU6050: ](https://github.com/jrowberg/i2cdevlib/tree/master/Arduino/MPU6050)
