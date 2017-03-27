---
layout: post
title: "Play! Créer des composants MVC avec les FastTags"
permalink: blog/java-play-fasttags-mvc-template-component
tags: [java, play!]
---

J'ai beaucoup apprécié la facilité de développement rencontrée avec play!, qui sur beaucoup de points ressemble à Symfony (fixtures, controleur, tags etc..). J'ai malgré tout rencontré quelques points qui m'ont paru étonnant. Notamment, pour créer un template ré-utilisable qui a besoin d'accéder à la couche métier, il faut passer par les [FastTags](http://www.playframework.org/documentation/1.2.4/templates#fasttags)

Ce concept nous permet de faire à peu près tout ce dont a besoin, excepté qu'il casse le pattern MVC. En effet si on regarde de plus près la classe FastTags, on s'aperçoit que l'on a du HTML écrit dans des chaîne Java. Pas trop sexy. Du coup comment créer un Custom Tag, avec notre logique en Java et le template dans un fichier html séparé ?

<!--more-->

Ce principe ressemble au [components](http://www.symfony-project.org/gentle-introduction/1_4/en/07-Inside-the-View-Layer#chapter_07_sub_components) du framework Symfony en PHP L'idée est de transformer un FastTags en controleur qui va accepter des paramètres, s'en servir pour récupérer des données et rendre un template spécifique qui aura accès à ces données.

Nous allons réaliser un petit projet pour illustrer comment mettre en place ce principe. L'objectif est simple : on veut afficher un menu dynamique sur toutes les pages du site, et on veut choisir si le menu affiché est celui de l'administration ou du front Vous pouvez récupérer les sources complètes sur projet [sur github](https://github.com/Vp3n/playComponent).

Pour la suite, je pars du principe que vous avez un projet play! initialisé et paramétrer pour accéder à une base de données.

#### Etape 1 : Créer le modèle de données et un jeu de test

Nous avons simplement besoin d'une classe MenuItem, dont vous trouverez le code ci-dessous.

<script src="https://gist.github.com/1685449.js?file=playComponent.app.models.MenuItem.java"></script>

Ensuite, nous avons besoin de données pour faire quelques essais, on va donc créer un fichier à la racine du projet (où n'importe où dans le classpath)

**_10_menuItem.yml**

<script src="https://gist.github.com/1685466.js?file=gistfile1.yml"></script>

Enfin nous devons charger nos données. Pour ça on cré un Job play qui se lancera au démarrage de l'application.

**app.bootstrap.DataLoader.java**

<script src="https://gist.github.com/1685436.js?file=app.bootstrap.DataLoader.java"></script>

Rien de particulier à dire ici.

#### Etape 2 : Créer un template

Ce template sera le rendu à proprement parlé du menu, je préfixe habituellement le nom du fichier par _ pour indiquer qu'il s'agit d'un template de component.

Ici, on voit simplement qu'on va avoir besoin d'une liste de menuItems. Le template est volontairement simpliste.

#### Etape 4 : Créer notre Custom Tag.

Pour cela, nous devons créer une classe qui étend FastTags

**app.tags.MenuComponent**

<script src="https://gist.github.com/1685481.js?file=gistfile1.java"></script>

Le tag _render va nous permettre ici d'appeler un nouveau template.

#### Etape 5 : Inclure notre custom tag dans le layout

Rien de particulier ici, on appelle simplement le tag précédemment créé avec les paramètres qui vont bien.

**app.views.main.html**

<script src="https://gist.github.com/1685489.js?file=gistfile1.html"></script>

Pourquoi donner le nom du template en paramètre ?
Dans notre cas, ça n'a pas spécialement d'intérêt. Par contre, si on avait besoin de rendre le même menu sous un autre format, il suffit d'appeler le même Tag en changeant le template.

Si vous avez besoin de créer un layout d'administration et le style de votre menu est strictement le même ? Il suffit de ré-appeller le même Tag en passant le paramètre showAdminItems à true.

Ce principe peut s'appliquer à n'importe quel custom tag où vous avez besoin de rendre du HTML. Séparer la vue rend le code beaucoup plus lisible et plus facilement ré-utilisable. Maintenant cette façon de faire a un inconvénient : les performances. En effet les FastTags sont plus rapides que le rendu d'un template classique. Mais rappelons les [2 règles primordiales de l'optimisation](http://c2.com/cgi/wiki?RulesOfOptimization) :

*   Don't
*   Don't..yet (expert only)