---
layout: post
title: "Problématiques de rollback() : JSF, @ConversationScoped et JPA"
permalink: blog/jsf-cdi-weld-conversation-rollback
tags: [jpa, java, jsf]
---

J'ai rencontré une incohérence due, comme souvent, à l'imbrication de plusieurs technologies. Si vous voulez utilisez le scope @ConversationScoped de CDI, Seam(3) persistence : atttention aux rollback() !
la spécification JPA 2.0 qui indique :

<!--more-->

_"For both transaction-scoped and extended persistence contexts, transaction rollback causes all pre-exist- ing managed instances and removed instances[31] to become detached. The instances’ state will be the state of the instances at the point at which the transaction was rolled back. Transaction rollback typically causes the persistence context to be in an inconsistent state at the point of rollback. In particular, the state of version attributes and generated state (e.g., generated primary keys) may be inconsistent. Instances that were formerly managed by the persistence context (including new instances that were made persistent in that transaction) may therefore not be reusable in the same manner as other detached objects—for example, they may fail when passed to the merge operation.[32]"_

Qu'est-ce que ça signifie concrètement ? A chaque fois que vous allez effectuer un appel à rollback(), toutes vos entités deviennent détachées. Ce qui peut entrainer des LazyInitializationException en série (vu qu'on utilise le scope @ConversationScoped justement pour éviter de merge() chaque entité à chaque requête HTTP).

Maintenant pour solutionner ce problème revoyons comment fonctionne Seam Persistence.

L'entity manager est étendu à la durée de la conversation, lorsqu'un appel à Conversation.begin() est rencontré.
Une transaction est démarrée à chaque requête JSF (en fait 2, mais je simplifie volontairement) et est fermée de 2 manières à la fin de la requête (en phase 5 INVOKE_APPLICATION) :

*   Par commit() si aucune exception n'a été déclenchée (ou catchée) dans les phases précédentes.
*   Par rollback() si une exception non cachée est remontée jusqu'ici.

A partir de là :

#### Solution avec merge()

La première solution est de merge() chaque entité à chaque requête, exactement comme si l'entity manager n'était pas étendu. Bof, étant donné que c'est justement (un des) l'intérêt de ce scope conversation. D'autant que cela risque de provoquer un besoin de créer des DTO entre la vue et la couche métier pour éviter qu'un appel dans la vue ne déclenche un LazyInitialization... Bref, pas vraiment conquis.

#### Solution brute force

La seconde solution serait de prendre tous les objets managés par JPA, créer un nouvel entity manager et rattacher tous les objets à ce nouveau manager avant de déclencher le rollback de l'ancien. Cette solution ne peut fonctionner que si on a la main sur l'entity manager qui va être utilisé pour la suite de la conversation, (à ma connaissance) ce n'est pas le cas avec SeamPersistence. A retenir si vous gérez vous même le cycle de vie de l'entity manager.

#### Solution "maîtrise du flush"

Là dernière solution, celle que je retiens, n'est malheureusement possible qu'avec Hibernate comme implémentation. Il faut passer en flush MANUAL. Cela signifie simplement que si aucun appel à flush() n'est lancé lors d'un traitement, le commit() n'enverra aucune instruction en base de données. En quoi cela nous aide ? Et bien il suffit de supprimer nos propres appels à rollback() ... Je vous vois venir avec votre "WTF?" ... En fait, l'idée est de gérer nos erreurs **métiers**. Si une erreur est rencontrée, on informe l'utilisateur mais on ne rollback() pas la transaction. SeamPersistence va donc commit() ... rien du tout. On laisse donc le soin à SeamPersistence de rollback() en cas d'erreur "grave" qui ne permet pas de continuer la conversation (c'est à dire n'importe quelle exception non catchée par nos soins) . Cette façon d'utiliser le rollback() est, à mon avis, la manière qui est prévu par les spécifications de JPA.

Du coup lors d'erreurs métier, on continue comme si de rien n'était notre conversation.
Les contraintes de cette solution sont :

*   Hibernate obligatoire étant donné que JPA ne propose pas de flush manuel.
*   Être rigoureux sur l'architecture de façon à n'avoir qu'un seul flush en fin de traitement (si aucune erreur rencontrée).
*   Faire attention à la méthode de génération des ID (prochain article sur le sujet !).

Si vous avez déjà été confronté à ce problème et que vous avez une autre solution (ou alors que j'ai dit de la merde ;)) n'hésitez pas à partager.