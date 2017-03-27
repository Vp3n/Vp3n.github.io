---
layout: post
title: "[Symfony >= 1.2] Validation conditionnelle de formulaire"
permalink: blog/symfony_g_1_2_validation_conditionnelle
tags: [xampp,php,symfony,windows]
---

Le but de ce billet est d'apporter une idée d'approche pour l'implémentation de règles métiers sur les formulaires.

Supposons que nous ayons un formulaire A avec 2 champs A1 et A2. avec la spécification suivante : A1 est obligatoire, mais uniquement si A2 est saisi.

<!--more-->

Au premier abord on se dit "ouais bon ca c'est super simple" puis "comment je peux faire ça sur le framework de formulaire" puis selon les cas "aaaaaaaaaaaaa" ... bref.

La première idée qui vient à l'esprit c'est de créer un widget qui regroupe A1 et A2 et un validator qui vérifie notre condition, mais c'est une fausse bonne idée. Pourquoi ?

D'abord, si on prend le cas où A est issu du modèle et donc que A1 et A2 sont des champs d'une table, cela signifie qu'il va falloir après validation surcharger la méthode doUpdateObject() pour récupérer les 2 valeurs, les scinder et les remettre correctement dans l'objet. Pour N champs de M formulaires avec autant de règles différentes ca va vite devenir lourd de créer 1 widget et 1 validator dans chaque cas.

La solution est de modifier dynamiquement les validators en fonction de notre condition. Pour faire ça il faut avoir en tête l'architecture de validation. En effet lorsqu'on récupère un formulaire en POST, les valeurs sont nettoyées et vérifiées par la méthode sfForm::bind(), avant traitement cette méthode appelle doBind(). L'idée est de modifier les validators avant la validation globale. On surcharge donc la méthode doBind(array $values).

Les valeurs $_POST se trouvent directement dans $values, ce qui vous permet d'effectuer un pré-traitement dessus.

Cette méthode doit être uniquement utilisée pour des règles inter champs externes aux données. Si votre règle concerne un calcul complexe sur des données de plusieurs champs, dans ce cas là la validation doit être faite par le modèle ! (donc via un validator spécifique, voir la documentation sur les postValidator)

Notre méthode doBind() se chargera directement de la seconde condition côté serveur.