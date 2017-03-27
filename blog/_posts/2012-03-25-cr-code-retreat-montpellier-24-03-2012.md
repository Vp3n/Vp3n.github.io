---
layout: post
title: "Compte rendu code retreat Montpellier du 24/03/2012"
permalink: blog/cr-code-retreat-montpellier-24-03-2012
tags: [craftsmanship]
---

Hier s'est déroulé le premier [code retreat à montpellier](http://vivian-pennel.fr/blog/code-retreat-montpellier-24-mars-2012) dans les locaux d'Epitech.
L'évènnement a organisé par Julient Lafont, Sylvain Fraïssé, et moi-même, sponsorisé par Zenexity.
Nous avons démarré par une petite présentation de l'organisation, puis du fonctionnement de la journée et 4 principes de développement à respecter sur l'ensemble des sessions par les facilitateurs. Rôle tenu par Antoine Vernois et Sylvain Fraïssé.
 [![presentation](http://farm8.staticflickr.com/7191/7013719921_489a789e56_n.jpg)](http://www.flickr.com/photos/78328871@N02/7013719921/ "Présentation de la journée")Ces principes sont issus de l'eXtreme Programming :

<!--more-->

*   Les tests passent
*   Pas de duplication (DRY)
*   Le code est expressif
*   Le code est petit

Le début a été un peu difficile, notamment à cause du réveil à 7h, voir plus tôt pour certains, un samedi matin (mé y son fou !). Mais après un café (une cafetière a été maltraitée dans l'opération caféine), ça a pu démarrer sur les chapeaux de roue.

#### 1ère session : Introduction

[![déroulement d'une session](http://farm8.staticflickr.com/7069/7013733987_3f19903ac6_n.jpg)](http://www.flickr.com/photos/78328871@N02/7013733987/ "Concentration intense")Première session sans règle particulière, si ce n'est les 4 évoquées en présentation et la pratique du TDD.
 J'ai pu commencer par travailler en Scala, avec une solution très intéressante (je n'évoquerai pas ici les solutions pour ceux qui participent à de futurs coderetreat) et pour le moins concise.
 J'ai eu un peu de mal avec la syntaxe et l'API, j'ai pu avoir heureusement beaucoup d'aide de la part de mon binôme.
 Difficultés pour moi ici, n'ayant pas l'habitude : réfléchir de manière fonctionnelle.
Mon sentiment sur la première session : 45min ca passe TRES vite !

#### 2ème session : Ping-pong programming

Session sympatique en Java, j'ai pu voir comment travailler efficacement à 2\.
L'idée est de passer le clavier de l'un à l'autre très rapidement pour ça le TDD fournit un très bon cadre.
Mon binôme se chargeait d'écrire un test, puis je devais l'implémenter et ainsi de suite.
J'ai pu apprendre la grande importance d'écrire des tests simples et courts, mais aussi de répondre à ces tests de la manière la plus simple et directe qui soit. Ce point a d'ailleurs étonné beaucoup de monde (par exemple retourner une valeur en dur qui permet juste de faire passer le test).
Le clavier passe d'une personne à l'autre très rapidement, on en devient beaucoup plus efficace, sans parler du fait que les 2 personnes maîtrise mieux le code.

#### 3ème session : Pas de boucle, pas de structure de contrôle

Session en Java à nouveau pour moi, et exercice assez difficile au vu des règles.
Nous avons bien pris 10 minutes avant d'écrire la moindre ligne de code.
Au final nous avons réussi à résoudre assez rapidemment le problème des boucles, par contre éviter les structures de contrôles nous a pris quasiment tout notre temps, même si nous sommes arrivés à trouver comment le faire en fin de session.

#### 4ème session : Silence

Session particulièrement intéressante, la règle est tout simplement l'interdiction de parler dans un binôme.
Nous avons dû commencer par choisir une technologie connue par les 2 personnes du binôme (dans notre cas PHP) afin d'éviter les échanges liés au langage.
Ensuite nous avons commencé, toujours dans le même mode, un décrivant un test, l'autre implémentation une solution pour passer ce test.
Malgré quelques problèmes de départ, nous avons été agréablement surpris d'avoir avancé loin dans l'exercice sans rencontrer de difficulté majeure.
Evidemment au début je n'avais pas la moindre idée d'où mon pair souhaitait m'emmener mais petit à petit avec des tests expressifs, l'objectif est devenu clair.
Cette session m'aura également rappellé que .. <troll>OMG PHP</troll>

#### 5ème session : Tell, don't ask

La règle de cette session est une pratique [recommandée en orienté objet](http://pragprog.com/articles/tell-dont-ask) qui consiste en résumé à n'exposer aucun état de l'objet à ses clients.
Developpement en python cette fois pour moi, j'ai voulu un peu voir le langage n'ayant jamais pratiqué.
Au final la contrainte de cette session nous a aidé à obtenir une solution très simple et élégante. Même si nous sommes passés par quelques remaniements.

#### Rétrospective finale

[![Rétrospective](http://farm8.staticflickr.com/7097/6867624766_ff85a83068_n.jpg)](http://www.flickr.com/photos/78328871@N02/6867624766/ "Rétrospective")Nous n'avons malheureusement pas pu faire la 6ème session le repas ayant pris un peu plus de temps que prévu.
L'étape suivante a donc été de faire la rétrospective de la journée, un tour de l'ensemble des participants avec une réponse à apporter aux 3 questions posées :

*   Qu'avez-vous appris aujourd'hui ?
*   Qu'est-ce qui vous a le plus surpris ?
*   Qu'allez-vous faire lundi en reprenant le travail ?

Globalement, beaucoup de personnes ont pu apprendre quelques bases sur de nouveaux langages, certains ont appris le TDD, d'autres à écrire des tests moins long moins compliqués, ou encore l'utilité des tests unitaires.
 Pour la seconde question, le plus surprenant a été principalement la session de silence ou encore l'interdiction de structure de contrôle et de boucle. Les participants ont été forcés sur ces sessions de créer des tests expressifs, mais aussi de redoubler de créativité pour contourner le problème.
Enfin, pour certains la prochaine étape au travail sera de mettre en place des tests, pour d'autres d'en écrire plus, ou encore de réduire leurs complexités.

Personnellement, j'en ai retenu que l'on pouvait exprimer clairement les intentions du code grâce à des tests, que cela permettait également de faciliter la construction d'une solution, non pas pour une personne mais aussi pour plusieurs.
J'ai été surpris par la solution en scala orienté fonctionnelle qui a été bien différente de tout ce que j'ai vu le reste de la journée.
Enfin, après l'organisation du coderetreat, ma prochaine action au travail qui est déjà lancée est d'organiser des sessions de code dans un format se rapprochant du coding dojo pour continuer de propager le mouvement dans le coin =)