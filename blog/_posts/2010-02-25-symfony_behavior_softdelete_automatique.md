---
layout: post
title: "Automatiser le filtrage des requêtes avec le behavior SoftDelete de Doctrine 1.x"
permalink: blog/symfony_behavior_softdelete_automatique
tags: [php,symfony,doctrine]
---

Le behavior softDelete de doctrine est très pratique.
Il permet d'automatiser la gestion des suppressions non physique en base.

Techniquement pour doctrine inférieur à 1.2, ce behavior ajoute un champ deleted de type BOOLEAN dans la table cible,
qui passera à vrai lors de la suppression d'une ligne.
Pour doctrine 1.2, ce champ est renommé en deleted_at et, est de type DATE. On a donc maintenant en plus la date de suppression.

<!--more-->

#### **1\. Ajouter le behavior à une table**

Prenons pour exemple une table Article suivant le schema.yml suivant :

<script src="https://gist.github.com/1659113.js?file=gistfile1.yml"></script>

#### **2\. Ce qui nous intéresse dans ce billet, c'est filtrer les requêtes automatiquement.**

En effet, le behavior a la capacité de filtrer toutes les requêtes faite sur la table article pour ne récupérer que les lignes non supprimées.
Par défaut, cette possibilité n'est pas active.

Si on se réfère à la [documentation de Doctrine](http://www.doctrine-project.org/documentation/manual/1_2/en/behaviors#core-behaviors:softdelete)
on lit qu'il faut activer les DQL callbacks.

On nous donne même le code qui permet de l'activer

$manager->setAttribute(Doctrine_Core::ATTR_USE_DQL_CALLBACKS, true);

Le problème maintenant est de savoir où mettre cette ligne de code dans notre projet symfony. Dans le fichier ProjectConfiguration comme suit :

<script src="https://gist.github.com/1659124.js?file=gistfile1.aw"></script>

Maintenant toute vos requêtes sur la table article auront une clause WHERE ajoutée de façon automatique qui permet de ne pas récupérer les lignes supprimées.

C'est dans cette méthode vous pourrez d'ailleurs configurer [un certain nombre d'autres paramètres](http://www.symfony-project.org/doctrine/1_2/en/03-Configuration)
pour Doctrine.

#### **3 . Gestion des relations**

Il reste quand même un détail à régler, les suppressions en cascade. Supposons que notre schema.yml se soit transformé comme suit :

<script src="https://gist.github.com/1659126.js?file=gistfile1.yml"></script>

A la suppression de l'auteur les articles associés seront supprimés physiquement de la base, puisque le cascade est géré en SQL.
Il faut laisser cette gestion à doctrine en utilisant la syntaxe suivante pour continuer à utiliser le softDelete :

<script src="https://gist.github.com/1659130.js?file=gistfile1.yml"></script>

[Plus d'informations](http://www.doctrine-project.org/documentation/manual/1_2/en/defining-models:transitive-persistence)