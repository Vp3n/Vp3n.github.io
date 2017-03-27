---
layout: post
title: "AngularJS : La voie du testeur - les scopes"
permalink: blog/angularjs-la-voie-du-testeur-les-scopes
tags: [angularjs, javascript, jasmine, tdd]
---

AngularJS est un framework javascript permettant de faciliter l’écriture d’application single page. Il y a de nombreuses fonctionnalités très intéressantes proposées par ce framework, mais selon moi il y en a une très importante : il est développé de manière à ce que le code écrit puisse être testé en intégralité, ce qui facile fortement une approche TDD.

Cet article est destiné aux personnes qui démarrent sur l’écriture de tests avec AngularJS, ou celles qui cherchent des trucs et astuce pour le quotidien. Une connaissance basique d’AngularJS et de Jasmine sont nécessaires.

<!--more-->

#### Pré-requis et outils

Nous allons utiliser [Jasmine](http://pivotal.github.io/jasmine/) pour l’écriture de tests, [Karma](http://karma-runner.github.io/0.10/index.html) comme exécuteur. Si vous avez utilisé [Yeoman](http://yeoman.io/ ) pour créer votre projet c’est déjà tout prêt. D’ailleurs, si vous démarrez, je vous conseille de partir comme ça. Sinon, créez un fichier karma.conf.js qui aura une tête plus ou moins comme ça :

<script src="https://gist.github.com/Vp3n/525f91fc61c0386bfbd5.js"></script>

Pour le reste vous aurez besoin d’installer :

*   [Node.JS](http://nodejs.org/)
*   [PhantomJS](http://phantomjs.org/)
*   Karma globalement (npm install -g karma)

Vous pouvez lancer vos tests via la commande

<pre>karma start</pre>

Let’s test !

#### Structure d’un test angularjs

Il faut savoir que l’écriture de tests Angular peut-être au premier abord compliquée, mais en fait c’est toujours les mêmes pattern qui reviennent. D’ailleurs la structure de base est quasi toujours la même :

<script src="https://gist.github.com/Vp3n/8800d7d1e55326805561.js"></script>

Passons aux choses sérieuses et voyons maintenant comment tester la plupart des cas que l’on peut rencontrer sur un scope.

#### Les scopes

#### 1\. Propriétés du scope

Le premier cas est le plus simple. Supposons qu’on veut développer une page qui affiche hello word à partir d’une valeur du score. (ouais même dans les tests on y a droit, ils nous emmerdent les mecs quoi !)

Ecrivons un test qui ne passe pas :

Comme prévu on se fait jeter. Je vous laisse le soin de faire passer ce test =).

Ce qu’il faut retenir dans ce test basique c’est que l’on a mock le scope passé au contrôleur. Grâce à l’injection de dépendance, tout ce que l’on a a faire est de créer un objet basique et le passer en paramètre.

#### 2\. $watch()

Testons quelque chose d’un peu plus sympa avec les scopes.

Imaginons une application avec une liste déroulante contenant des code postaux, et une autre qui affiche la ou les ville(s) correspondante(s).

Ecrivons un test qui crash violemment d’abord (oui j’aime bien cette partie) : les puristes noteront que j’ai un peu sauté un cycle TDD, mais c’était pour montrer que l’on ne veut pas de ville(s) tant que le code postal n’est pas choisi.

<script src="https://gist.github.com/Vp3n/540f36f15851fea570e6.js"></script>

Maintenant ajoutons notre fonctionnalité :

<script src="https://gist.github.com/Vp3n/c6d48030dbaee59487a2.js"></script>

Voilà on est tout fier de nous, mais le navigateur lui ne l’est pas, puisqu’on obtient l’erreur :

TypeError: 'undefined' is not a function (evaluating '$scope.$watch’)

![wat](http://fc05.deviantart.net/fs71/i/2011/166/9/8/wat_duck_by_linkzer-d3j0spr.jpg)

Effectivement on a mock le scope un peu à la bourrin, donc il n’a effectivement aucune méthode. Comment faire pour tester le comportement du $watch ? On va créer un scope réel que l’on passera au contrôleur nous même pour en garder la maîtrise. N’importe quel scope a la possibilité de créer un enfant via la méthode $new(). Il nous suffit de récupérer le $rootScope et d’en créer un !

<script src="https://gist.github.com/Vp3n/3c84e9b68d31810965de.js"></script>

Vous noterez également la ligne scope.$apply(); très importante, puisqu’un $watch se déclenche sur un [digest cycle](http://code.angularjs.org/1.2.13/docs/guide/scope#what-are-scopes_integration-with-the-browser-event-loop), dans un test vous devez déclencher ce cycle manuellement !

Dans cette partie, ce qu’il faut retenir, c’est que si vous avez besoin de tester des méthodes particulières sur le scope, un objet javascript simple ne suffit plus. Dans les autres cas vous pouvez vous en tenir au mock simpliste.

#### 3\. $emit() et $broadcast()

Nous allons maintenant voir comment tester des événements. Pour ceux qui dorment au fond de classe, $broadcast() et $emit() étant des fonctions du scope, nous allons devoir de nouveau créer un scope avec $rootScope.$new(). Autre rappel $emit() a une propagation ascendante, donc seul les parents du scope pourront l’intercepter.

Etant donné que l’on veut uniquement tester le contrôleur courant, on va éviter d’aller écrire des assertions sur les éventuels parents. Dans ces conditions comment on peut vérifier ça ? On va se servir des fonctions d’espionnage de Jasmine (Vous regarder la [doc de spyOn](http://pivotal.github.io/jasmine/#section-Spies)) le but est simplement de vérifier que la méthode est appelée avec les bonnes valeurs. On ne teste pas l'interception dans le $rootScope !

Voyons avec un exemple :

L’exemple ici est un message de notification. Supposons que vous ayez une zone de notification qui affiche des messages provenant d’une liste contenue dans le $rootScope. Pour ajouter des messages, on supposera que le $rootScope prévoit un événement $notify qui prend en paramètre un objet (message, level)

Maintenant vous êtes sur un bon vieux CRUD des familles, et vous voulez afficher un message de succès après l’enregistrement.

Ecrivons un test qui FAIL

<script src="https://gist.github.com/Vp3n/00cd5fdab5c9d7913c33.js"></script>

Vous noterez la syntaxe de [spyOn](http://pivotal.github.io/jasmine/#section-Spies), ainsi que le fait qu’il soit appelé AVANT le save() pour des raisons évidentes :)

Maintenant, l’implémentation du contrôleur qui est simpliste :

<script src="https://gist.github.com/Vp3n/e23db7f2f93094577db5.js"></script>

Le principe est applicable exactement de la même manière à $broadcast()

#### 4\. $on()

On a vu comment vérifier que l’on déclenche bien un événement, comment faire si on veut vérifier que l’intercepteur marche correctement ?

Reprenons notre exemple de notification plus haut. On veut vérifier que lorsque un évènement $notify est envoyé le contrôleur l’intercepte et l’ajoute à la liste des messages à afficher. Pour ça il suffit en fait de déclencher l’événement sur le contrôleur actuel via un $broadcast() qui a une propagation descendante MAIS inclus le scope courant.

Ecrivons un test qui CASSE TOUT !

<script src="https://gist.github.com/Vp3n/ef056b0cdb3070768c81.js"></script>

Vous remarquerez que je n’ai pas utilisé le $rootScope comme écrit plus haut, c’était pour montrer un $on() sur le scope du contrôleur testé (et qui n’est donc pas parent), cela marche exactement de la même manière pour le $rootScope.

Voilà on a fait le tour de la plupart des cas d’utilisations que l’on peut rencontrer en testant des scopes. Si le format plaît je continuerai sûrement sur les autres aspects d'AngularJS.

Et n’oubliez pas … La première chose à écrire doit produire du gros rouge d’erreur qui tâche à l’écran :)

<small>Remerciement pour l’image dans cet article : [Wat duckby LinkZer](http://fav.me/d3j0spr)</small>