---
layout: post
title: Raspcopter - Communication réseau
---

<img src="http://pix.toile-libre.org/upload/original/1399577725.jpg" style="width: 100%; height: auto;"></img>

Jusqu'à présent, les problématiques rencontrées lors du développement du Raspcopter concernaient uniquement le système de vol embarqué au RaspberryPi. Puis plus récemment nous avons vu la station de contrôle au sol du projet. Ces deux derniers projets sont donc déjà dotés de plusieurs fonctions de contrôle, mais sans aucun moyen interactif de communiquer l'un avec l'autre. L'enjeu de cet article est d'expliquer les choix de communication sol-quadcopter qui ont été faits ainsi que leur implémentation.

Ce que l'on veut
================

Tout d'abord il est important de rappeller l'objectif principal du projet : comprendre le fonctionnement du système de vol d'un quadcopter, les problématiques liées au réseau ne sont donc évidemment pas la priorité et la facilité de développement et de déploiement est recherchée en priorité. Pourtant, il ne faut pas faire de concessions :

Les commandes de vol restent des données critiques qui doivent nécessairement transiter de manière sûre et rapide. Lors d'un virage, une perte de paquets ou une latence trop importante ne pardonnent pas.

On cherche également une certaine modularité de manière à pouvoir contrôler davantage de choses que les simples commandes de vol.

Pour finir, le quadcopter étant probablement amené à voler loin de la station de contrôle au sol, il est nécessaire d'utiliser une communication de longue portée.

Ce que l'on a
=============

À ce point, le RaspberryPi ne comporte plus qu'un port USB libre puisque le second est occupé par le Pololu Maestro et tous les GPIOs sauf l'i2c. Il faut donc trouver un moyen de communication exploitant ces ports.

Ne l'oublions pas, le RaspberryPi fonctionne sous Raspbian (ou tout du moins, [une version hardfloat très allégée](https://www.linuxsystems.it/raspbian-wheezy-armhf-raspberry-pi-minimal-image/)) et donc sous un système d'exploitation Linux complet. Notre quadcopter bénéficie donc de tous les avantages de ce dernier, particulièrement en matière de pilotes de périphériques. Le choix des méthodes de communication est donc considérablement élargi par rapport à un autre système d'exploitation comme ChibiOS RT, FreeRTOS ou RTEMS.

À l'autre bout de la communication, le quadcopter devrait échanger avec un PC portable tournant également sous une distribution Linux standard et/ou avec un smartphone Android.

Comment passer de l'un à l'autre
================================

Plusieurs choix de méthodes se présentent à nous, en voici les principaux :

La première, la plus évidente car utilisée par la plupart des quadcopters "normaux" est une communication analogique depuis une télécommande standard de quadcopter. Ce choix est intéressant dans la mesure où il utilise du matériel habituel pour un quadcopter et que la portée est excellente, mais il n'offre aucune modularité puisqu'il ne possède généralement qu'un canal par angle de rotation et pas de moyen de faire transiter des données autres dans le sens inverse... De plus le traitement du signal analogique sur le Raspberry Pi serait une tâche énorme et ne semble pas facilement envisageable.

<center><img src="http://pix.toile-libre.org/upload/original/1399562740.jpg" style="width: 200px; height: auto;"></img></center>

Une solution un peu plus haut niveau consisterait à utiliser un module de communication sans fil comme le XBee, ces petites puces devenues assez standards sont déjà beaucoup plus modulaires et faciles à implémenter. Elles offrent également des portées suffisantes pour un projet de drone. Cette solution semble donc assez optimale si ce n'est qu'elle nécessite l'achat de composants couteux sur le RPi comme sur le PC portable, sans parler du smartphone Android...

<center><img src="http://pix.toile-libre.org/upload/original/1399562728.jpg" style="width: 200px; height: auto;"></img></center>

Finalement, la solution retenue se trouve déjà probablement tout autour de vous... c'est le Wifi. Les avantages sont très nombreux. Premièrement, et ce n'est pas négligeable : le coût. Mais également la facilité de développement et de déploiement. Ainsi que la modularité de la communication Wifi, en effet la possibilité de garder une liaison SSH en cas de pépin ou de pouvoir éventuellement plus tard créer un stream vidéo d'une webcam facilement est très attrayante. Mais des questions restent encore en suspend : quid de la portée du Wifi, de la présence d'un routeur, ainsi que de la rapidité et la sûreté des données ?

<center><img src="http://pix.toile-libre.org/upload/original/1399562752.png" style="width: 200px; height: auto;"></img></center>

Le déploiement physique du réseau
=================================

Le Wifi permet de n'effectuer aucune modification matérielle au niveau du PC portable et du téléphone, en revanche le choix de la clef présente sur le Raspberry Pi est plus important. Par chance, un vieux mediacenter attendait sous la poussière de mon placard que quelqu'un vienne lui débrancher sa clef wifi USB longue portée. Une antenne est donc présente sur le quadcopter pour bénéficier d'une communication sur de longues distances. Un test de portée est peut-être à prévoir à l'avenir. Idéalement la clef devrait permettre la création d'un hostpot wifi sur le quadcopter lui-même, mais malheureusement cette fonctionnalité n'est pas supportée par le driver Linux du chipset utilisé par le Raspcopter.

Le système de vol devra donc se connecter lui-même à un émetteur Wifi au sol. Deux possibilités seront présente: soit le téléphone émettra un réseau tethering auquel se connecteront le quadcopter et le PC portable à la manière d'un réseau local, soit ce sera le PC portable qui émettra le wifi.

Sur les appareils au sol, un logiciel client sera responsable de maintenir la communication avec le serveur présent sur le quadcopter.

Les détails de la communication
===============================

Selon le modèle OSI, les couches basses du réseau sont déjà assurées. Mais en ce qui concerne le protocole, des choix sont encore à faire :

À commencer par trancher entre une base TCP ou UDP. Une communication TCP a l'avantage d'être très sûre (tous les paquets arrivent et dans l'ordre) mais plus lourde et lente qu'une UDP qui elle n'offre pas ces contrôles, mais est beaucoup plus rapide. Finalement, le meilleur des deux mondes a été retrouvé dans une bibliothèque réseau nommée ENet. Premièrement destinée au développement de jeux vidéos, cette bibliothèque implémente un protocole UDP, mais gardant les fonctionnalités majeures de TCP. Le résultat est donc un excellent compromis entre les deux.

<center><img src="http://pix.toile-libre.org/upload/original/1399563468.jpg" style="width: 600px; height: auto;"></img></center>

La libenet est donc utilisée sur le quadcopter et les clients au sol pour supporter un protocole spécifique. Chaque paquet de ce protocole est composé d'un octet entête d'opcode qui définit l'opération à effectuer puis d'une longueur variable contenant les données à transférer. La liste des opcodes se trouve dans l'header de la classe Network du code disponible sur GitHub.

Par exemple la commande permettant de modifier les valeurs du contrôleur PID du quadcopter depuis le sol est nommée SET_PID_VALUES et correspond à l'opcode hexadécimal 0x04. Le paquet associé commencera donc par un octet (char) valant 4 puis 9 floats contenant les trois valeurs des trois PIDs.

Sources
=======

- [Shield de Connexion Xbee et Arduino](http://www.evola.fr/raspberry-shield-connexion-xbee-arduino-p-391.html)
- [RSPPI - RaSPberry PI: Configurando el mando a distancia del Quad-Raspi-copter](http://rsppi.blogspot.fr/2013/04/configurando-el-mando-distancia-del.html)
- [Enet](http://enet.bespin.org/)
- [TCP / UDP : Quelle différences ?](http://www.commentcamarche.net/faq/7294-tcp-udp-quelles-differences)
- [Network.cpp](https://github.com/FlorentRevest/Raspcopter/blob/master/raspberry-server/Network.cpp)
