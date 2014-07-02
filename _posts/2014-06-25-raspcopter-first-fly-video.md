---
layout: post
title: Raspcopter - Premiers tests de la configuration PID en vidéo
---

<iframe src="//player.vimeo.com/video/99163238?color=ffffff" width="100%" height="360" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

<center><a href="http://vimeo.com/99163238">Raspcopter - First series of test-flights</a></center>

Comme on a pu le voir, le système de vol du drone repose sur trois régulateurs PID distincts, un pour chaque angle. Or ces régulateurs doivent être réglés les uns après les autres et ce indépendamment et en toute sécurité avant de pouvoir espérer faire un vol à l'air libre. Pour régler ces valeurs il convient donc de bloquer les rotations possibles sur un seul angle. On réalise ceci en tenant le drone par une barre parallèle à un axe de rotation.

La première idée essayée a été d'utiliser deux cordes attachées à des barrières. Si cette idée a principalement débouché sur des échecs on peut tout de même relever quelques aspects intéressants et également en tirer des leçons. La vidéo ci-dessus présente une poignée de tests sur cette "base de travail"

Commençons par les points positifs : déjà on peut se réjouir du fait que le quadcopter décolle et ce dès 40 % de puissance des moteurs. Il semblerait donc que la puissance soit suffisante pour ajouter du poids supplémentaire (rappelons que la batterie du raspberry pi manque encore et que le drone est toujours relié par USB au sol...). Ces tests m'ont également permis de tester le drone et la station de contrôle dans la durée. Un onglet de l'interface au sol spécialement dédié au changement des valeurs PID à distance se révelle particulièrement utile maintenant.

Mais il y a également des points négatifs dans cette vidéo. Premièrement, l'amplitude de mouvement laissée au quadcopter ne le bloque pas réellement sur un angle donc si l'on travaille sur le roll on obtient des fluctuations du pitch et yaw qui rendent le travail difficile sinon impossible. De plus l'amplitude laissée en hauteur par les cordes pas assez tendues permet au quadcopter de s'élever à grande vitesse avant que le choc produit lorsque les cordes sont tendues rabatte violemment le drone vers le bas. (on voit particulièrement cet effet à 0:55).
