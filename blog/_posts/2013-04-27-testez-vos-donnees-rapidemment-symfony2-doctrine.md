---
layout: post
title: "Testez vos données plus rapidement avec Symfony2 et Doctrine"
permalink: blog/testez-vos-donnees-rapidemment-symfony2-doctrine
tags: [php, symfony, doctrine, tdd]
---

Ce petit article a pour but d'expliquer comment mettre en place un environnement de test de données avec symfony2 et doctrine. Cet environnement devra respecter 3 critères très importants :

*   Rapidité : La suite de test, même si elle accède à une base de données ne doit pas être trop longue à s'exécuter
*   Prédictibilité : On doit pouvoir se baser sur des données de tests déterminées
*   Isolation : Les tests ne doivent pas interférer dans l'exécution des autres.

<!--more-->

#### Réfléxion préalable

Pour répondre à nos critères c'est assez simple :

*   Pour s'assurer que notre suite de test est rapide, il ne faut charger notre base de données qu'une seule fois, au début.
*   Pour que nos tests soient prédictibles, il faut figer un jeu de données, et pour conserver la rapidité il doit également être chargé qu'une seule fois au début.
*   Comment faire pour que nos tests soient isolés ? facile les SGBD fournissent cette fonctionnalité pour rien ! Il suffit de démarrer une transaction avant chaque test et la rollback à la fin du test.

Reste à voir comment on fait ça avec Symfony 2

#### 1\. Configuration de la base de test

Ici, rien de particulier, on suit simplement la documentation symfony2, configurez votre base de données de test. Etant donné que cette base va être construite et chargée qu'une fois, mon conseil ici est de reproduire au mieux votre environnement cible. Surtout si vous avez du métier en trigger et/ou procédure stockée, vous pourrez ainsi les tester également.

Exemple avec Sqlite :

<script src="https://gist.github.com/Vp3n/b8142bc5ac68eeed7d8a.js"></script>

#### 2\. Créer un bootstrap personnalisé

On va créer un bootstrap personnalisé dans lequel on va faire tous nos traitements pré-suite.
Ce fichier (que je vais appeler test.bootstrap.php mais c'est au choix) sera ajouté dans /app

<script src="https://gist.github.com/Vp3n/5472469.js"></script>

#### 3\. Configuration de phpunit

Il faut maintenant indiquer à Phpunit de charger notre boostrap.

<script src="https://gist.github.com/Vp3n/5ffd90c7031b3cb3ee45.js"></script>

#### 4\. Classe utilitaire

Maintenant que tous est configuré, il reste à démarrer la transaction avant chaque test et rollback après. On va donc créer une classe de test générique histoire de rester DRY. Cette classe étend WebTestCase et fournit en plus un accès facilité à l'EntityManager qui va bien.

<script src="https://gist.github.com/Vp3n/5472509.js"></script>

#### 5\. Let's test !

Il n'y a plus qu'à tester ! pour cela il n'y a qu'à créer une classe de test comme d'habitude qui étend DatabaseWebTestCase.

<script src="https://gist.github.com/Vp3n/c96b82c2a4b9c826ee35.js"></script>