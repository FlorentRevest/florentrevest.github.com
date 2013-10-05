---
layout: post
title: Raspcopter - Réception de l'attitude
---

<img src="http://pix.toile-libre.org/upload/original/1380988375.jpg" style="width: 100%; height: auto;"></img>

J'inaugure aujourd'hui la lignée d'articles techniques sur mon projet de Raspcopter en commençant comme promis par : l'attitude.

Ce que l'on veut
================

Si cela peut sembler naïf c'est pourtant vrai: la première mission d'un quadcopter est de ne pas tomber... Même lorsque les quatre moteurs tournent à vitesse égale et constante, le drone fini par vriller et tomber seul. Cette rotation naturelle est causée par l'imperfection du drone (centre de gravité déplacé par exemple) mais aussi et surtout par les diverses contraintes physiques du milieu (typiquement : le vent).

Il est donc indispensable de créer un système de vol dépendant de l'attitude du quadcopter, c'est-à-dire de sa position angulaire dans l'espace. Une citation Wikipedia vaut mieux qu'un long discours "L'attitude ou l'orientation, dans le domaine de l'astronautique, est la direction des axes d'un engin spatial par rapport à un trièdre de référence. Pendant le déplacement du véhicule spatial, il s'agit de contrôler les mouvements d'avant en arrière (tangage), de gauche à droite (roulis) et autour d'un axe vertical (lacet)."

On a donc besoin de trois angles d'Euler que l'on nomme en anglais : yaw, pitch et roll.

<center><img src="http://pix.toile-libre.org/upload/original/1380985932.jpg"></img></center>

Ce que l'on a
=============

De nombreux capteurs permettent d'obtenir l'attitude du système :
- Les accéléromètres, mesurant l'accélération linéaire sur trois axes.
- Les gyroscopes, fournissant une position angulaire relative sur les mêmes axes.
- Les magnétomètres, mesurant le champ magnétique et permettant de déduire la position du nord à la manière d'une boussole.
- Les récepteurs GPS, dont les satellites fournissent des coordonnées absolues en latitude et longitude et permettent d'obtenir une rotation.

Tous ont leurs avantages et inconvénients. Ils ne sont donc jamais utilisés seuls, toujours combinés. De par des contraintes d'argent et de temps, mon projet de Raspcopter ne fera usage dans un premier temps que des deux premiers capteurs. Le MPU6050 de Invensense les réunit en une seule puce, on parle alors d'IMU (Unité de mesure inertielle) à six degrés de liberté. Le MPU6050 est par ailleurs très peu cher et souvent utilisé par des quadcopters.

<center><img src="http://pix.toile-libre.org/upload/original/1380986384.jpg" style="width: 100px; height: auto;"></img></center>

Cette puce se connecte via la norme i2c par quatre fils (SDA, SCL, Masse et 3.3v) aux pins GPIOs dédiés du Raspberry Pi. Il est donc premièrement nécessaire de lever les drivers i2c de la blacklist Raspbian de modprobe.

L'utilisation de ce capteur est relativement bien documentée et rendue aisée par le travail de Jeff Rowberg sur I2CDevLib. Il est donc facile de récupérer des valeurs d'accélération et de rotation.

Comment passer de l'un à l'autre ?
==================================

Le coeur du problème se trouve dans le passage des valeurs brutes du MPU6050 à des angles habituels. En effet les valeurs brutes ont trois problèmes :
- premièrement, elles comportent du bruit c'est-à-dire que le signal n'est pas stable
- deuxièmement, elles n'ont pas d'unité, ce sont des valeurs qui ne correspondent à rien de concret
- pour finir, ces données d'accélération et de rotation ne sont pas combinées en trois angles comme nous le souhaitons.

<img src="http://pix.toile-libre.org/upload/original/1380986108.png" style="width: 100%; height: auto;"></img>

Un filtre de Kalman est souvent utilisé pour stabiliser les valeurs, mais son implémentation est extrêmement complexe et couteuse en temps processeur.

Une solution bien plus légère et rapide serait de créer un filtre complémentaire, selon la formule:

<center><img src="http://pix.toile-libre.org/upload/original/1380986043.gif"></img></center>

Appliqué sur des angles roll (phi) et pitch (rhô) calculés par 

<center><img src="http://pix.toile-libre.org/upload/original/1380985903.png"></img></center>
<center><img src="http://pix.toile-libre.org/upload/original/1380985892.png"></img></center>

Cette formule accorde beaucoup plus de poids aux valeurs du gyroscope qu'à celles de l'accéléromètre car elle tiend en compte le fait que les mesures du gyroscope sont stables sur le court terme mais ont un "drift" (un décalage qui se produit avec le temps) sur le long terme (c'est-à-dire qu'en retournant deux fois d'affilé un gyroscope il ne reviendra pas à la même valeur) et que les valeurs de l'accéléromètre qui mesurent énormément de forces en plus de la gravité ont beaucoup de bruit mais sont bien plus stables sur le long terme. Cette formule n'est pas totalement efficace non plus.

Le choix d'un filtre IMU de quadcopter doit souvent se faire entre ces deux dernières techniques. Mais la bonne nouvelle c'est que le MPU6050 possède une puce "DMP" (Digital Motion Processing) qui permet de filtrer ces valeurs hors du Raspberry Pi, donc en temps réel et de manière bien plus efficace que par un algorithme implémenté à la main. La mauvaise nouvelle c'est qu'Invensense protège jalousement le fonctionnement de cette puce au nom du secret industriel et refuse de documenter son fonctionnement.

C'est ici qu'intervient Noah Zerkin, un entrepreneur en réalité augmenté qui a obtenu des dumps mémoires par retroingénierie de la puce en fonctionnement DMP à partir de démos du constructeur. C'est sur son excellent travail, intégré à I2CDevLib que se base mon code.

Mon implémentation est située dans la classe Accelerometer du [code du serveur](https://github.com/FlorentRevest/Raspcopter/tree/master/raspberry-server) L'initialisation du MPU6050 se fait dans le constructeur et les angles d'Euler s'obtiennent par la méthode getYawPitchRoll().

Problème des angles d'Euler
===========================

Un dernier problème reste présent : si les angles d'Euler décrivent toutes les possibilités de positions angulaires, ils ne sont pas à l'abri pour autant d'un "blocage de cardan" ou Gimbal Lock. Ce problème arrive lorsque deux axes du gyroscope pointent dans la même direction, le système perd alors un degré de liberté et poursuit un mouvement imprédictible. Ce problème qui semble anodin aurait pu faire tourner court la mission Apollo 11, mais on n'en retiendra qu'une citation de Michael Collins "Que diriez-vous de m'envoyer un quatrième cardan pour Noël ?"

<center><img src="http://pix.toile-libre.org/upload/original/1380985991.gif"></img></center>

La solution, lorsque l'on ne croit pas au père Noël, serait d'utiliser des Quaternions, des nombres hypercomplexes fait d'une combinaison linéaire de quatre paramètres, dont trois définissent un axe et le dernier définit une rotation autour de cet axe. La manipulation de cet ensemble dépassant mes compétences de terminal S je me contenterai de les convertir en angles d'Euler.

Et après ?
==========

La première phase du système de contrôle, l'acquisition de données, est donc achevée avec succès. Les angles d'Euler : yaw pitch et roll doivent maintenant passer par un contrôleur PID afin de sortir des vitesses de moteurs. Ce sera l'objet du prochain article.

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
