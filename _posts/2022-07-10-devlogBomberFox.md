---
layout: post
title: "[FR] Devlog - BomberFox"
date:   2022-07-10 19:50:00 +0200
categories: Unity GameDev
---




**Description :** Il s’agit d’un simple clone de BomberMan. Une idée pas très original mais bien cadré qui me permettrait de pouvoir finir le projet sans trop me décourager. L’idée est donc d’abord de cadrer l’expérience (quelles fonctionnalités implémentées etc …)
On va y aller pas à pas et l’objectif est d’avoir un produit un peu aboutit (même sur la partie graphique) et pas un vieux prototype moche.

Par contre, pour cette première expérience, on ne va pas implémenter de multijoueur.

---
## 01 Gestion des déplacement : 4 Juillet 2022
> Déplacement, 3rd Person, Joystick

Dans cette partie j’ai développé un simple système de mouvement. Le joueur ne peut que se déplacer sur un plan (le plan du jeu) . Pour se faire j’ai simplement utiliser la `Transform` du joueur en faisant attention à ne pas être frame dépendant.
* Je sens qu’il y a un léger problème dans les rotations (ce n’est pas assez smooth), j’ai voulu réutiliser la fonction `Mathf.SmoothDampAngle()` mais le code ne fonctionnait plus et le renard tournait en rond.
* J’ai aussi essayé de mettre en place les animations mais il faut encore que je débug le système qui ne marche pas bien.
Voilà une *vidéo* de ce que j’ai pour l’instant 
<iframe width="600" height="400" src="https://www.youtube.com/embed/JQEze0mEl1g" title="DevLog 01 - BomberFox" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


Finalement, j’ai fini par résoudre le problème (ne pas oublier d’associer les composants aux scripts …) 

### Petit sous thème : Collisions et mur invisible
J’ai essayé de géré ça avec des collider et la physique de Unity. J’ai obtenu un résultat qui marche mais qui a l’air de ne pas être très solide (les bugs ont l’air facile à apparaître). 
Il faudra sûrement *refactor la physique* un peu complètement dans le futur.

---

## 02 Placement des bombes : 7 Juillet 2022
> Input d’action, script de bomb, refactor

Aujourd’hui j’ai traiter plusieurs sujet assez court : un petit refactor de la détection des collisions pour le futur, le positionnement des bombes (sur input) et la gestion de leur apparition et disparition.
### Refactor Collision
J’ai retravaillé la manière dont  `PlayerLocomotion.cs` traite les collision pour que ce soit fait dans une classe externe (puisque je devrais traiter les collisions différemment plus tard, notamment lorsque l’on voudra pousser une bombe, écraser un joueur, etc …) 
Pour ça j’ai juste fait une classe externe qui gère les collisions (on pourra aussi l’utiliser pour l’IA plus tard) 

### Les bombes
Pour ça globalement, il fallait : 
* Placer un gameObject représentant les bombes ;
* Lancer un timer avant l’explosion ;
* Faire exploser la bombe ⤵️ 
	* Vérifier ce qui est détruit, tué ; ❌
	* Lancer un cd pour recharger ses bombes ;
	* La détruire ;
Pour l’instant, je n’ai pas traité la vérification de destruction, je ferai ça une prochaine fois.

*Quelques points à voir pour la prochaine fois*
#### Refactor le positionnement de la bombe
En effet, pour l’instant, le placement n’est pas bien géré et une bombe peut être placer partout (même dans les murs indestructibles …). Il faudrait les positionner sur une grille. Pour faire ça j’ai quelques idée :
* Placer des cubes avec collider et un layer spécifique (/bombPlacer/), puis utiliser un Raycast  lors du placement de la bombe pour savoir dans quelle case positionner la bombe (plus simple pour ne pas mettre deux bombes au même endroit) 
* Avoir une matrice de positionnement des bombes et chercher l’ancre la plus proche pour positionner la bombe (mais sûrement un peu trop coûteux)

> *Remarque :* Ce positionnement n’est pas exactement celui mis en place dans un BomberMan classique (où la bombe est placée sur la case du joueur puis il se déplace et la bombe devient bloquante).
> En fait, il semblerait que le déplacement même de BomberMan se face sur une grille, mais ben je préfère garder mon approche pour commencer.


#### Taille de la bombe
Il faudra faire attention à ce que le joueur ne puisse pas traverser la case de la bombe.
#### UI
Il faudrait faire un peu d’UI pour voir le nombre de bomb restante (le cd etc …) 

Voilà le résultat de mon travail jusqu’à présent :
<iframe width="600" height="400" src="https://www.youtube.com/embed/udR5sBUSC3A" title="DevLog 02 - Bomberfox" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---

## 03 UI & Pos des bombes : 10 juillet 2022 (nuit/matin)
> UI, positionnement des bombs

Aujourd’hui j’ai fait un petit travail assez léger.
* J’ai corrigé un petit problème de collision avec les bombes pour que le joueur ne puisse pas les traverser (il fallait faire attention aux layers et j’ai remplacé la /sphere collider/ par un /box collider/ (moins permissif).
* Pour positionner les bombes, j’ai créé des GameObjects ne possédant qu’un box collider avec un layer particulier. Il suffit alors de lancer un Raycast pas trop long et de récupérer la case touché par le raycast pour savoir où positionner la bombe (sachant que la case dans laquelle se trouve le joueur n’est pas considéré lors de l’appel du Raycast) ;
* En outre, j’ai ajouté un peu d’UI pour mieux voir le nombre de bombes en possession et quand est-ce que les bombes sont utilisées. J’ai lié le script qui gère les bombes (`PlayerInteraction.cs`) avec celui de l’UI (`UiManager.cs`) via des événements. 
On peut voir le résultat de l’UI sur l’image ci-dessous et la vidéo lié
![UI of the bombs](/../../resources/BomberFox-UIBomb.png)
Pour la vidéo la voilà : 
<iframe width="600" height="400" src="https://www.youtube.com/embed/MOQAIodrJtw" title="DevLog 03 - BomberFox" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

On peut cependant identifier un petit problème : on peut positionner plusieurs bombes sur la même case. Pour empêcher ce problème, je vais ajouter un petit script aux colliders qui définissent les cases où l’on peut poser la bombe pour faire cette vérification.
Ça fonctionne un peu mieux mais ça reste pas très propre à mon goût.

---

## 04 Refactor du mouvement : 10 Juillet (soirée)
> Mouvement sur une grille

Aujourd’hui j’ai surtout retravaillé la manière dont le joueur se déplace et le système de case. En effet, je me suis rendu compte que mon système précédent présentait quelques failles et il me semble que ça ne me dirigerait que vers plus de problème plus tard (notamment d’optimisation).
Ainsi, afin de moins pleurer devant le méfait accompli plus tard, j’ai décidé de retravailler tout ça.
Ainsi voilà les principales modification que j’ai apporté : 
* Déplacement contraint sur la grille (pas de mouvement libre)
* Création d’une grille initialement
* Lien entre les position dans le monde et les grilles : on peut récupérer la grille la plus proche en fonction d’une position dans le monde
Une petite vidéo qui montre le résultat de ces modifications (non intégrées) :
<iframe width="600" height="400" src="https://www.youtube.com/embed/dCSpd-yfGMw" title="DevLog 04 - BomberFox" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Avec ce travail de fait, il ne reste plus qu’à faire un peu de nettoyage dans le code pour avoir quelque chose de bien propre et de réintégrer les fonctionnalités développées auparavant. 
Par exemple, je pense qu’il n’y aura que peu de modification au système de bombes à importer.
Par contre, il faudra revoir le système de collision, qui devrait par ailleurs être bien plus simple à maîtriser maintenant que les déplacement se font sur un espace discret.

En outre, j’aimerai pour la prochaine fois pouvoir créer facilement un niveau (positionnement des briques incassables et des briques cassables) au moyen d’un fichier externe (.csv) pour le moment. Il faudrait voir comment développer un outil intégrer à Unity qui permettrait de faciliter le travail (mais en vrai je pense qu’utiliser le grid snapping et les prefabs suffit).


