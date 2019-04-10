---
layout: post
title: Raspcopter - Network communication
---

<img src="http://pix.toile-libre.org/upload/original/1399577725.jpg" style="width: 100%; height: auto;"/>

So far, the problems encountered during the development of the Raspcopter concerned only the embedded flight system on the Raspberry Pi. Then, more recently we have seen the ground control station. Those two projects are already equipped with several control functions, but without any interactive way to communicate between each other. The aim of this article is to explain the choices made about the ground <-> quadcopter communication and their implementation.

What we want
============

Firstly, it is important to remind the main objective of the project: to understand the inner working of a quadcopter flight system. Therefore, network-related issues are not the priority and ease of development and deployment is sought in priority. Yet, no concessions can be made :

The flight controls remain critical data that must necessarily be carried safely and quickly. When flying, packet loss or excessive latency do not forgive.

We also seek some modularity in order to get access to more things than simple flight controls.

Finally, the quadcopter is probably going to fly far from the ground control station, so it is necessary to use a long-range communication.

What we have
============

At this point, the Raspberry Pi has almost all GPIOs (except i2c) and one USB port free (since the second is occupied by the Pololu Maestro). We must find a means of communication exploiting those ports.

The Raspberry Pi works under Raspbian (or at least [a very lightweight hardfloat version](https://www.linuxsystems.it/raspbian-wheezy-armhf-raspberry-pi-minimal-image/)) therefore under a full Linux operating system. Our quadcopter thus enjoys all the benefits of this kernel, especially in terms of device drivers. The choice of communication methods is significantly expanded compared to other operating systems like ChibiOS RT, FreeRTOS or RTEMS.

At the other end of the communication, the quadcopter should talk with a laptop also running a standard Linux distribution and/or an Android smartphone.

How can we get from what we have to what we want ?
==================================================

Several choices are presented to us, here are the main ones:

The first, most obvious, as used by most "normal" quadcopters is an analog communication from a standard quadcopter remote control. This choice is interesting in that it uses the usual material for a quadcopter and that the scope is excellent, but it offers no modularity since it generally has one channel by rotation angle and no way to pass other data in the opposite direction... Moreover, the processing of the analog signal on the Raspberry Pi is a huge task and does not seem easily feasible.

<center><img src="http://pix.toile-libre.org/upload/original/1399562740.jpg" style="width: 200px; height: auto;"/></center>

A slightly high level solution would be to use a wireless communication module such as the XBee, these small chips become fairly standard and are already much more modular and easy to implement. They also offer sufficient scope for a drone project. This solution therefore seems quite optimal, however it requires the purchase of expensive components on the RPi and on the Laptop, not to mention the Android smartphone...

<center><img src="http://pix.toile-libre.org/upload/original/1399562728.jpg" style="width: 200px; height: auto;"/></center>

Finally, the solution is probably already all around you... it's WiFi. The benefits are numerous. First, and this is not negligible: the cost. But also the ease of development and deployment. There is also the modularity of the wireless communication, for exemple we can keep an SSH connection if something goes wrong, we could also possibly stream a webcam easily, which is quite attractive. But questions still remain unanswered: what about the scope of the WiFi, the presence of a router and the speed and security of data ?

<center><img src="http://pix.toile-libre.org/upload/original/1399562752.png" style="width: 200px; height: auto;"/></center>

The network physical deployment
===============================

WiFi allows us to avoid any changes on the laptop and smartphone, however the choice of the key on the Raspberry Pi is important. Luckily, an old mediacenter was waiting under the dust of my closet for someone to pull its long range wifi USB key. An antenna is present on the quadcopter to receive a communication over long distances. A range test gave decent results. Ideally the key should generate a wifi hotspot on the quadcopter itself, but unfortunately this feature is not supported by the chipset's Linux driver.

The flight system will have to connect itself to a ground based WiFi transmitter. Two possibilities are present: either the phone will emit a tethering network on which the quadcopter and laptop connect, or it will be the laptop that will emit the wifi.

On ground based devices, a client software will be responsible for maintaining communication with the quadcopter's server.

The details of the communication
================================

According to the OSI model, the network's lower layers are already insured. But with regard to the protocol, choices are still to be done :

Starting with deciding between a TCP or UDP basis. TCP communications have the advantage of being very safe (all the packets arrive in sequence) but heavier and slower than UDP, which does not offer such safety, but is much faster. Finally the best of both worlds was found in a network library named ENet. Firstly designed for video games development, this library implements a UDP protocol but keeps major features of TCP. The result in an excellent compromise between the two.

<center><img src="http://pix.toile-libre.org/upload/original/1399563468.jpg" style="width: 600px; height: auto;"/></center>

The libenet is used on the quadcopter's server and clients in order to support a specific protocol. Each packet of this protocol is composed by a header opcode byte which defines the operation to be performes followed by a variable length containing the data to be transferred. The list of opcodes is in the header of the Network class, available on GitHub.

For example the command to change the value of the PID controller from the ground is named SET_PID_VALUES and corresponds to the hexadecimal opcode 0x04. The associated packet starts with a byte (char) equal to 4 and 9 floats containing the three values of the three PIDs.

Sources
=======

- [Shield de Connexion Xbee et Arduino](http://www.evola.fr/raspberry-shield-connexion-xbee-arduino-p-391.html)
- [RSPPI - RaSPberry PI: Configurando el mando a distancia del Quad-Raspi-copter](http://rsppi.blogspot.fr/2013/04/configurando-el-mando-distancia-del.html)
- [Enet](http://enet.bespin.org/)
- [TCP / UDP : Quelle différences ?](http://www.commentcamarche.net/faq/7294-tcp-udp-quelles-differences)
- [Network.cpp](https://github.com/FlorentRevest/Raspcopter/blob/master/raspberry-server/Network.cpp)
