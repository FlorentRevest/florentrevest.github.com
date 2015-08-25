---
layout: post
title: Raspcopter - Station de contrôle au sol
---

<img src="http://pix.toile-libre.org/upload/original/1399577554.jpg" style="width: 100%; height: auto;"></img>

Le réseau étant un bien gros morceau qui fait prendre pas mal de retard au projet sans résultats forcément visibles, aujourd'hui nous allons brièvement parler du logiciel PC de contrôle au sol. L'article n'a pas pour but d'être véritablement technique comme les précédents, mais seulement informatif.

Un outil pratique
=================

Il existe déjà d'innombrable logiciels complets de stations de contrôle au sol de quadcopters, comme celui d'Ardupilot par exemple. Forker un de ces projets était possible mais finalement Raspcopter s'est doté de son propre logiciel client de vol.

Ce dernier est développé en C++ à l'aide du framework Qt, il est donc portable et facile à maintenir ou étendre.

#### Un contrôleur

Tout d'abord la première tache de cette station est d'agir en tant que contrôleur, c'est à dire de récolter les données d'un joystick. Ce dernier est un joystick de jeux vidéos standard acheté en magasin pour une dizaine d'euros. La lecture de ses données se fait directement par ioctl via l'[API noyau générique de contrôle de joystick](https://www.kernel.org/doc/Documentation/input/joystick-api.txt).

#### Un client réseau

Les données de ce joystick sont ensuite transférées au drone par le réseau via le réseau qui sera expliqué dans l'article suivant. Mais la station de contrôle est également capable de recevoir des données du quadcopter par le même moyen.

#### Des données de télémétrie

Les données recueillies depuis le quadcopter concernent principalement l'historique des positions angulaires du drone mais il est prévu à l'avenir de pouvoir également garder un oeil sur le pourcentage d'utilisation processeur et mémoire du Raspberry Pi.

Deux bibliothèques graphiques de tièrce partie très pratiques ont été utilisées pour l'affichage agréable des données télémétriques: [AnalogWidgets](http://qt-apps.org/content/show.php/AnalogWidgets?content=87780) et [QCustomPlot](http://www.qcustomplot.com/). Grâce à ces dernières, il est possible de garder un oeil sur des cadrans et un graphique des données mesurées, et ce en temps réel directement depuis le sol. La station de contrôle implémente également une fonction d'export de ces données au format .csv, ouvrable par un tableur comme LibreOffice Calc pour des diagnostiques ultérieurs en cas de problème, ce qui *ne nous le cachons pas* arrivera très souvent.

Le code source
==============

Comme le reste du projet, la station de contrôle est open-source et entièrement [retrouvable sur GitHub](https://github.com/FlorentRevest/Raspcopter) dans le dossier laptop-client. N'hésitez pas à star le projet si vous le jugez utile. Et si vous avez des questions diverses et variées, n'oubliez pas que vous pouvez me contacter.
