---
layout: post
title: Raspcopter - Régulateur PID (Proportionnel Intégral Dérivé)
---
<img src="http://pix.toile-libre.org/upload/original/1386360951.jpg" style="width: 100%; height: auto;"></img>
Au cours de l'article précédent nous avons entamé l'étude du système de vol d'un quadcopter. La première étape consistait à récupérer l'attitude c'est à dire la position angulaire du drone dans l'air. Il s'agit aujourd'hui d'exploiter ces mesures d'angles dans la perspective de stabiliser le quadcopter autour de valeurs spécifiées par la station de contrôle au sol.

Ce que l'on veut
================

S'il est vrai que le drone doit savoir rester parallèle au sol lorsqu'aucune commande du sol n'est reçue, il se doit également de savoir pivoter sur ses axes pour pouvoir se déplacer. Ces rotations sont spécifiées par la "station de contrôle au sol", un logiciel tournant sur un ordinateur portable et traitant les données d'un joystick. La position du joystick traduit un angle voulu qui est ensuite transportée par wifi et interprété par le Raspberry Pi.

On souhaiterait avoir un vol fluide malgrès les changements brutaux de position du joystick, tout en gardant un contrôle sur la réponse des moteurs plus ou moins aggressive.

Ce que l'on a
=============

Lorsque le quadcopter confronte les trois angles d'euler mesurés par l'accéléromètre comme vu dans l'article précédent et les angles voulus envoyés par la station au sol la différence entre ces deux données produit une *erreur* soudaine. Si les vitesses des moteurs sont changées en même temps que l'apparition de l'erreur, comme selon le signal carré du graphique ci-dessous. L'impulsion puissante et soudaine risque: au mieux de dépasser l'angle souhaité et de revenir en arrière indéfiniment créant une instabilité, au pire de renverser immédiatement le quadcopter. On comprend donc bien qu'il est nécessaire d'avoir un algorithme "lissant" cette transition.

<center><img src="http://pix.toile-libre.org/upload/original/1381347244.jpg"></img></center>

Comment passer de l'un à l'autre ?
==================================

Ce problème est omniprésent en ingénieurie, et nécessite l'utilisation d'un "régulateur", nous allons évoquer et utiliser le régulateur PID (pour Proportionnel, Intégral, Dérivé).

Dans le cadre de notre quadcopter, les mesures se faisant sur trois angles il est nécessaire d'utiliser trois régulateurs PID différents. Cet algorithme prend pour entrée la différence entre l'angle voulu et l'angle mesuré, par exemple lorsque la télécommande au sol n'envoit rien cette erreur correspond au défaut de parallèlisme au sol. Des opérations mathématiques sont appliquées à ces trois erreurs et déterminent les vitesses à envoyer aux quatre moteurs.

*Le régulateur PID est un algorithme qui prend en entrée une erreur. Il fait la somme d'une multiplication de cette erreur, d'une intégrale de cette erreur et d'une dérivée de cette erreur, puis renvoie une nouvelle valeur.* **C'est tout.**

On perçoit vite les avantages de cet algorithme: tout d'abord il est très simple à comprendre et à implémenter. Mais il faut également savoir qu'il est extrèmement fiable, ce régulateur est le plus utilisé au monde et on le retrouve partout... jusque dans la chasse de vos toilettes !

Ce système est dit "en boucle fermée" car il s'auto régule : lorsque l'erreur d'angle est grande les moteurs concernés accélèrent et l'erreur se réduit. La sortie de l'algorithme influe donc sur son entrée.

<center><img src="http://pix.toile-libre.org/upload/original/1386350513.jpg"></img></center>

Proportionnel
=============

Le terme proportionnel s'obtient très simplement par la multiplication de l'erreur par une constante nommée "gain proportionnel". Plus le gain est grand plus la vitesse de réponse est rapide et risque d'être instable. Plus le gain est faible plus la vitesse de réponse est "molle" et risque d'être inefficace. Il s'agit de trouver une bonne valeur intermédiaire entre ces deux extrêmes.

<center><img src="http://pix.toile-libre.org/upload/original/1386351053.png"></img></center>

Ici on voit qu'un gain proportionnel (Kp) trop grand résulte en un dépassement significatif de l'angle recherché (le signal de référence bleu).

Intégral
========

Contrairement à un simple système de contrôle proportionel, le régulateur PID tient compte de l'historique des erreurs angulaires. Pour celà le terme intégral est introduit, il représente la somme de toutes les erreurs accumulées dans le temps multiplié par une constante "gain intégral" Ki.

<center><img src="http://pix.toile-libre.org/upload/original/1386353656.png"></img></center>

Le gain intégral influe directement sur la hauteur (et par conséquent le nombre) de dépassement de la valeur cible. Un trop faible gain est problématique dans certaines situations comme lorsque le vent est trop fort. En revanche un gain trop grand provoque une oscilation autour de l'angle voulu.

Dérivé
======

Le terme dérivé est parfois nommé "l'accélérateur" puisqu'il permet de compresser dans le temps la réponse. Il s'obtient par la soustraction de l'erreur actuelle et de l'erreur précédente multipliée par le gain dérivé. Ce terme est à prendre avec des pincettes car il est très sensible au bruit de données.

<center><img src="http://pix.toile-libre.org/upload/original/1386353681.png"></img></center>

Une fois n'est pas coutume, un graphique nous permet de mieux comprendre l'impact du gain.

Et après ?
==========

Une fois le code du régulateur PID implémenté (la très simple classe PID disponible sur le code github) il s'agit de déterminer les trois gains de chaque régulateur. Comme chaque quadcopter a des caractéristiques particulières il n'existe pas de constantes PID universelles, cependant la détermination de ces valeurs ne révelle pas non plus du hasard.

Tout d'abord il faut noter qu'une méthode de détermination rapide, nommée "méthode de Ziegler et Nichols" existe et permet d'obtenir des Ki et Kd corrects à partir de la seule valeur de Kp. Les gains peuvent ensuite être ajustés en fonction des paramètres vus ci dessus.

Par ailleurs, un quadcopter (contrairement à un tricopter ou hexacopter) est à peu de choses près symétrique, les constantes des PIDs pitch et roll sont donc similaires ce qui fait gagner du temps.

Pour finir, il est important de noter que pour des raisons de sécurité évidentes l'experimentation de ces PIDs ne se fait jamais en conditions réelles en exterieur. En accrochant solidement le quadcopter à une barre parallèle à un axe de rotation on est en mesure de bloquer la rotation des autres angles et de travailler sur un seul PID à la fois.

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
