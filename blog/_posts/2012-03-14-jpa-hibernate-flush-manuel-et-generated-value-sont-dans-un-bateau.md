---
layout: post
title: "JPA/Hibernate, Flush manuel et @GeneratedValue sont dans un bateau"
permalink: blog/jpa-hibernate-flush-manuel-et-generated-value-sont-dans-un-bateau
tags: [jpa, hibernate, java]
---

Mon dernier article traitait de la [gestion des transactions et des rollback avec JPA](http://vivian-pennel.fr/blog/jsf-cdi-weld-conversation-rollback) se terminait sur la conclusion que si on utilise Hibernate en provider, mieux vaut utiliser le flush manuel pour avoir un contrôle complet.
Cependant, j'avais noté de faire attention à la **méthode de génération des ID**, qui peut transformer le flush manuel en semi-manuel... C'est l'objet de cet article.

<!--more-->

Dans quels cas faire attention ? Cela est lié à la stratégie de génération d'ID que l'on utilise.s
 JPA, dans le cadre de son cache de premier niveau, a besoin de l'ID d'une entité pour vérifier qu'elle n'existe pas en double.

#### Que se passe t-il lors d'un appel à em.persist(objet) ?

*   Si vous utilisez une séquence ou une génération d'ID via une table dédiée, Hibernate effectue un insert sur la table dédiée ou une requête de type select nextval() pour connaître l'ID.
*   Si vous utilisez une stratégie de type autoincrement ou identity (par défaut sur certains SGBD comme SQLServer, MySQL avec @generatedValue), **Hibernate exécute une requête INSERT dans la table cible pour connaître l'ID.**

Voilà exactement le problème. Vous êtes en flush manuel et pourtant un INSERT va être exécuté lors d'un persist() même si aucun appel à flush() n'est rencontré.

Conclusion, si vous avez réellement besoin d'un flush manuel, et que votre base de données ne permet pas l'utilisation de séquence, utilisez la stratégie de génération Table (ou alors gérez vous même les ID).

Si vous avez d'autres solutions pour contourner le problème notamment avec MySQL et SQLServer, je suis preneur !