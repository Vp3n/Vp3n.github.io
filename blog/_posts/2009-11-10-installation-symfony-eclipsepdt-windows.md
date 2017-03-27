---
layout: post
title: Installation de Symfony/EclipsePDT sur Windows
permalink: blog/php-symfony-windows-environnement-config
tags: [xampp,php,symfony,windows]
---

Suite à la [configuration de notre environnement](http://blog.developpez.com/vivian-pennel/p8294/php/symfony/php-symfony-windows-environnement-config), nous allons maintenant installer un IDE ainsi que Symfony lui-même.

<!--more-->

Pourquoi Eclipse ? c'est un bon IDE PHP avec lequel j'ai l'habitude de travailler, notamment parce qu'il offre beaucoup de possibilités via ses plugins.
Maintenant Netbeans est intéressant aussi, surtout depuis la 6.8 qui apporte la gestion de Symfony. Cependant il est pour le moment en BETA je le testerai à sa release.

#### **1\. Installation d'Eclipse.**

Commençons par [télécharger EclipsePDT](http://www.eclipse.org/pdt/downloads/), je vous conseille de choisir le package all-in-one. (Eclipse 3.5 + PDT) pour une installation facilité.

Pour l'installation rien de plus simple, décompressez le .zip dans le répertoire souhaité.

Ce billet n'étant pas un tutoriel sur le fonctionnement d'Eclipse, je vous invite à vous documenter sur le sujet si vous ne connaissez pas l'IDE.

#### **2\. Installation des plugins**

Les 2 principaux plugins à installer pour cet article sont [Yedit](http://code.google.com/p/yedit/) et [Subclipse](http://subclipse.tigris.org/servlets/ProjectProcess?pageID=p4wYuA). Vous pouvez les installer via l'installer d'Eclipse (Help > Install New Software > saisir l'URL dans "work with") ou télécharger les .jar et les copier dans le dossier plugin d'Eclipse. Dans les deux cas, redémarrez Eclipse une fois terminé.

Yedit va nous servir à avoir une coloration syntaxique ainsi qu'un validateur basique pour le langage YAML.

Quant à Subclipse nous allons nous en servir pour passer à l'installation de Symfony.

#### **2\. Installation de Symfony**

La suite de ce billet explique une manière d'organiser les projets avec symfony, il en existe en fait plusieurs. (Le [tutoriel Jobeet](http://www.symfony-project.org/jobeet/1_2/Doctrine/en/01) en présente une, on peut aussi installer globalement le framework via PEAR)

Accédez à la perspective "SVN repositories" (Window > Open perspective > Others), puis cliquez sur le bouton "add repository"
![add repository](http://blog.developpez.com/media/add_repository.JPG)

Entrez l'URL du repository de symfony à savoir : http://svn.symfony-project.com

Dans le dossier apparu, allez dans branche puis sélectionnez la version 1.3, clic droit > checkout. Laissez les options par défaut, cliquez sur Next. L'assistant de nouveau projet s'ouvre, sélectionnez bien "PHP project", puis finalisez la configuration (nom, chemin etc..)

Après quelques temps de chargement vous avez maintenant un projet Symfony 1.3. L'idée étant d'avoir un projet séparé de votre projet réel pour éviter une surcharge dans les fichiers ou de dupliquer le framework pour chacun des projets existants. De plus le changement de version du framework se fera simplement en changeant une ligne dans le ProjectConfiguration de votre projet sans avoir a remplacer ou déplacer des fichiers.

#### **4\. Intégration des commandes Symfony sur Eclipse**

Pour éviter de faire des allers retours entre la console et Eclipse, il est possible de taper les commandes de Symfony directement sur Eclipse. Pour ça, cliquez sur le bouton "Run As" > External Tools configuration comme sur l'image ci-dessous.

![run as](http://blog.developpez.com/media/run_as.JPG)

Clic droit sur "Program" puis New. Tapez le nom du programme (par exemple symfony13)

![tools_config](http://blog.developpez.com/media/tool_config.JPG)

Explications :

*   Location: Doit contenir le chemin vers le fichier symfony.bat qui interprète les commandes. La variable workspace_loc contient le chemin de votre workspace. Vous pouvez générer directement ce chemin en cliquant sur "Browse Workspace .."
*   Working Directory: La cible du programme, dans notre cas c'est votre (ou vos) projet(s) pour rendre cela générique on utilisera la variable project_loc
*   Arguments: Précise de quels type sont les arguments qui seront passés au programme. Ici ce sera des chaînes de caractère, d'où la variable string_prompt

Allez maintenant dans l'onglet "Refresh" en haut. Sélectionnez "The Project containing the selected resource". A chaque fois que vous exécuterez une commande symfony les fichiers de votre projet seront rafraichis, ce qui évite les problèmes de désynchronisation avec Eclipse.

**NB:** Si vous utilisez plusieurs versions de symfony, il faudra créer un programme pour chacune des versions.

#### **5\. Tester l'installation**

Afin de vérifier notre installation, créons un nouveau projet PHP sur Eclipse. Une fois que c'est terminé, sélectionnez votre projet dans l'explorer d'Eclipse puis cliquez sur Run as > Symfony13 Une boite de saisi apparait, cliquez sur OK en laissant le champ vide.

- Si votre installation est correcte, la liste des commandes de symfony devrait s'afficher dans la console d'Eclipse.
- Si vous obtenez le message "Variable references empty selection $(project_loc) c'est que n'avez pas sélectionné votre projet ou au moins un élément de celui-ci. (c'est d'ailleurs le seul inconvénient de ce système)
- Si vous obtenez une erreur en console, c'est sûrement un problème de configuration du chemin du fichier symfony.bat.

Vous avez maintenant un environnement complet prêt pour développer sous Windows.

PS : N'hésitez pas à me signaler d'éventuelles erreurs.