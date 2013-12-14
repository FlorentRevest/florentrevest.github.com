---
layout: post
title: Raspcopter - Contrôle des moteurs
---

<img src="http://pix.toile-libre.org/upload/original/1386413694.jpg" style="width: 100%; height: auto;"></img>

Le dernier article a doté le système de vol du Raspcopter d'un algorithme puissant d'autorégulation angulaire. Pourtant, un détail a été passé sous silence et non des moindres: le "Feedback" représenté en bas du schéma du contrôleur PID assurant la fonction de boucle fermée. Ce retour sur erreur est bien sûr la gestion des vitesses des moteurs, l'objet de l'article d'aujourd'hui.

Ce que l'on veut
=============

Comme tout objet volant, notre petit drone doit bénéficier d'une force de poussée verticale suffisamment grande pour le libérer de la gravité. Cette force c'est notre vieil ami Newton qui nous la donne dans sa troisième loi des actions réciproques. Les hélices du quadcopter poussent l'air vers le bas et par réaction, le drone est poussé vers le haut c'est aussi simple que ça.

Mais le drone introduit une contrainte : ces hélices doivent être entraînées à une vitesse assez conséquente et par des moteurs assez légers. Un type de moteur répond à cette problématique, les moteurs "sans balais" ou plus couramment appelés brushless.

<center><img src="http://pix.toile-libre.org/upload/original/1386704254.jpg"></img></center>

Leur principe de fonctionnement est assez simple, ci dessus vous pouvez observer trois bobines (légendées coils) alimentées électriquement les unes après les autres pour créer un champ magnétique qui mettra en mouvement l'aiment du centre, portant l'axe tenant l'hélice. Les moteurs du Raspcopter utilisent 12 bobines mais le principe reste fondé sur l'alternance de trois d'entre elles, c'est d'ailleurs pour cette raison qu'il suffit d'inverser deux fils au hasard pour modifier le sens de rotation du moteur.

Ce que l'on a
==========

On souhaite contrôler ces moteurs depuis le Raspberry Pi avec le minimum de latence possible. Le Raspberry Pi dispose de plusieurs interfaces, principalement les GPIOs et l'USB. La première idée semblant la plus naturelle serait de contrôler les moteurs depuis les pins GPIOs mais c'est en fait impossible car la fréquence de changement de bobines ne serait jamais suffisante et l'énergie délivrée serait elle aussi bien trop faible.

Comment passer de l'un à l'autre ?
============================

Pour cette raison, on fait toujours appel à des "ESCs" (electronic speed controlers) placés en amont de chaque moteur, ces circuits électroniques sont alimentés par deux câbles provenant de la batterie LiPo et reçoivent des données PWM (pulse width modulation, c'est à dire codées sur des longueurs de pulsations électriques) depuis trois fils, exactement de la même manière qu'un servomoteur. En sortie de ces contrôleurs, trois câbles sont soudés au moteur brushless et alternent l'alimentation des bobines comme vu précédement. L'ESC choisi est un modèle 20A UBEC de HobbyKing.

<center><img src="http://pix.toile-libre.org/upload/original/1387029010.jpg"></img></center>

On simplifie déjà le problème, car envoyer un signal PWM depuis les GPIOs du Raspberry Pi n'est plus chose impossible, mais en générer quatre est déjà chose plus ardue. Certains projets utilisent directement les GPIOs mais n'y faisant pas totalement confiance j'ai préféré acheter un circuit externe de gestion de servomoteurs, le Pololu Micro Maestro qui déchargera la framboise d'une lourde tâche. Le choix du maestro s'est fait grace à sa connectique USB, mais Adafruit propose d'excellent circuits i2c faisant la même chose.

<center><img src="http://pix.toile-libre.org/upload/original/1387030267.jpg" style="width: 100px; height: auto;"></img></center>

Le code gérant le Pololu Maestro est situé dans la classe Motors du code du système de vol hébergé sur GitHub et utilise la libusb pour gérer la vitesse de chaque moteur comme prévu par les spécifications de Pololu.

Sources
=======

- [A Crash Course on Indoor Flying Robots](https://www.khanacademy.org/partner-content/mit-k12/mit-k12-physics/v/indoor-flying-robots) 
- [HowStuffWorks "How does a brushless electric motor work?"](http://www.howstuffworks.com/brushless-motor.htm)
- [Hobbyking 20A ESC 3A UBEC](http://www.hobbyking.com/hobbyking/store/__15202__hobby_king_20a_esc_3a_ubec.html)
- [Adafruit 16 Channels controller](http://www.adafruit.com/products/815)
- [Pololu Maestro Servo Controller's User Guide](http://www.pololu.com/docs/0J40)
- [How to use GPIO on Raspberry Pi](https://sites.google.com/site/semilleroadt/raspberry-pi-tutorials/gpio)
- [Motors.cpp](https://github.com/FlorentRevest/Raspcopter/blob/master/raspberry-server/Motors.cpp)
