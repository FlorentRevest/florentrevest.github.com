---
layout: post
title: Raspcopter - Premier crash
---

<img src="http://pix.toile-libre.org/upload/original/1404331500.png" style="width: 100%; height: auto;"></img>

Mine de rien le projet Raspcopter avance, la base de code est même quasiment finie. Des fonctions de logging seraient à prévoir plus tard mais, ce n'est pas une priorité du tout, c'est même du luxe. Les articles plutôt techniques que j'ai pu écrire jusqu'à présent devraient donc maintenant faire place à une série d'articles davantage pratiques, visuels et grand public. Mais comme on s'en doute, le passage de la théorie à la pratique s'accompagne toujours de petits effets inattendus.

Qui dit premiers tests du drone dit immanquablement premiers crashs du drone. Et c'est exactement ce qui s'est produit dernièrement lorsque j'ai introduit un gain intégral non nul aux paramètres PID du quadcopter.

Le drone fonctionnait depuis plus d'une heure et demie, les variables intégrales qui contiennent la somme des erreurs d'angles dans le temps contenaient donc des valeurs gigantesques qui se sont soudainement retrouvées multipliées par un gain. Ceci a produit une vitesse de moteur maximale et le drone s'est immédiatement retourné avant de casser des hélices. Des investigations plus approfondies sur le comportement du gain intégral s'annoncent donc nécessaire.

Heureusement seules les hélices ont été endommagées, mais la commande de nouvelles pièces va faire prendre du retard au projet. J'ai donc pris cette fois le soin de commander 20 hélices pour avoir de l'avance sur les inévitables prochains crashs.
