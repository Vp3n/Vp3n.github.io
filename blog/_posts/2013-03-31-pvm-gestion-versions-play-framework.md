---
layout: post
title: "Gestion de plusieurs versions de Play framework avec pvm"
permalink: blog/pvm-gestion-versions-play-framework
tags: [jpa, scala, play!]
---

Travaillant sur différents projets avec Play, sur différentes versions du framework j'ai trouvé qu'avoir plusieurs installations à gérer à la main c'était pas très pratique. Le but de ce post est de proposer une solution grâce à un outil très simple

<!--more-->

#### Le problème

Chez moi, le soucis c'est que je travaille sur un projet en Play 1.2.3, un en Play 1.2.4, un autre avec Play 2.1 mais aussi je m'amuse à coder un peu sur le framework lui même et donc en version 2.2 (master).
Jusqu'à maintenant je gérais ça plus ou moins bien en utilisant des alias (play123, play21 par exemple) jusqu'à ce que je découvre [pvm](https://github.com/kaiinkinen/pvm), ça tombait bien étant en pleine ré-installation

#### Play Version Manager

pvm pour Play Version Manager est basé sur un outil équivalent dédié à NodeJS (nvm comme vous aurez pu le deviner) Il s'agit d'un petit script sh qui permet d'installer une version de Play, d'en changer à la volée dans le shell ou de les supprimer (en résumé). Oui du coup exit les utilisateurs windows.. désolé je compatis.
Pour l'installer il suffit de se créer un répertoire cible, puis de cloner le projet dedans

<pre>mkdir ~/utils && cd ~/utils && git clone https://github.com/kaiinkinen/pvm.git . </pre>

Remplacer ~/utils par le chemin voulu.
Pour qu'il fonctionne au login sur le shell, il suffit d'ajouter la ligne suivante dans votre ~/.bashrc, ~/.bash_profile ou encore ~/.profile selon votre préférence.

<pre>. ~/utils/pvm/pvm.sh</pre>

En remplaçant bien évidemment le chemin par celui où vous l'avez installé.
Une fois fait il ne reste plus qu'à installer play, disons la version 1.2.4 avec

<pre>pvm install 1.2.4</pre>

Puis la 2.1,

<pre>pvm install 2.1</pre>

Vous pouvez maintenant basculer de l'une à l'autre simplement avec

<pre>pvm use x.y.Z</pre>

#### Installer une version personnalisée

le problème de pvm install c'est limité aux releases déposées sur le site de typesafe. Dans mon cas j'ai aussi besoin de travailler directement sur le master.
C'est assez simple à faire. pvm installe tout dans $pvm/install. On va donc créer un répertoire nous même, par exemple

<pre>mkdir ~/utils/pvm/install/master</pre>

puis récupérer la version de play souhaitée manuellement.

<pre>cd ~/utils/pvm/install/master && git clone https://github.com/playframework/Play20.git .</pre>

Il suffit ensuite d'exécuter :

<pre>pvm use master</pre>

Pour utiliser cette version (bien entendu, dans ce cas il faudrait également compiler les sources et construire le repository local du framework).