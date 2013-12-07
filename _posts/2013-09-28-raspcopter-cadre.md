---
layout: post
title: Raspcopter - Construction du cadre
---

Après deux mois d'attente insoutenable, le collis des pièces du quadcopter commandées chez HobbyKing a fini par être livré. J'ai commencé à monter le cadre X525 v3 avec les moteurs Brushless FC 28-22 Outrunner 1200kv. Je flasherai peut être les ESC 20A UBEC HobbyKing avec le célèbre firmware SimonK avant de les souder aux moteurs (cependant la procédure s'annonce délicate donc incertaine). Toute cette phase de montage qui est commune à tous les quadcopters et déjà amplement documentée, je ne la détaillerai donc pas sur ce blog.

En revanche, mon travail de programmation et de documentation concernera la conception du système de contrôle de vol intégré au Raspberry Pi. À commencer par la réception de "l'attitude" (la rotation dans l'espcace) du quadcopter en angles d'euler filtrés puis le contrôleur PID et la régulation des vitesses via le Pololu Maestro.

En attendant, voici la première photo du montage :

<a href="http://pix.toile-libre.org/upload/original/1380366834.jpg"><img src="http://pix.toile-libre.org/upload/original/1380366834.jpg" style="width: 100%; height: auto;"></img></a>

La plaque de plastique à droite sera directement fixée au X525 et servira de support au Raspberry Pi, au MPU6050 (l'acceleromètre) et au Pololu Maestro (interface avec les ESCs).

Pour toutes informations supplémentaires n'hésitez pas à me contacter ou à commenter.

P.S: La liste complète du matériel est entretenue au côté du code sur github: <a href="https://github.com/FlorentRevest/Raspcopter">https://github.com/FlorentRevest/Raspcopter</a>