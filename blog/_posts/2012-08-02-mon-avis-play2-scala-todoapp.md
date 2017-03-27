---
layout: post
title: "Mon avis sur Play2 / Scala au travers d'une TodoApp"
permalink: blog/mon-avis-play2-scala-todoapp
tags: [play!, scala, angularjs, coffeescript]
---

Après plusieurs lectures notamment de "Programming in Scala" de Martin Odersky, Lex Spoon, et Bill Venners et en lien mes objectifs personnels 2012, à savoir maîtriser Javascript et apprendre Scala, je me suis lancé dans l'écriture d'une petite application pour mettre tout ça en pratique.

L'article est découpé en 2 parties, d'abord le côté serveur avec play/scala puis le côté client avec angularjs/coffee script.
 Le but est de donner mon avis / mes retours d'expérience sur les langages et technologies utilisées.

Cette première partie est dédiée à play et scala.

<!--more-->

Vous pouvez retrouver les sources du projet complet [sur github](https://github.com/Vp3n/TodoApp)

#### Scala et web, encore jeune ?

Sachant que je voulais utiliser AngularJS côté client, le rôle du serveur est simplement de fournir un stockage de données et une API Json simple pour la communication avec le client.
 Pour cela, je voulais utiliser un framework à la fois léger mais aussi ne me demandant pas d'installer / configurer 20 librairies annexes (oui je fais du Java :)).

Je suis donc allé à la pêche aux framework web scala, j'en ai retenu 3 :

*   [Lift](http://liftweb.net/)
*   [Scalatra](http://www.scalatra.org/)
*   [Play2](http://www.playframework.org/)

Le choix :

*   **Lift** propose une architecture statefull, le peu de temps que j'ai passé à parcourir la documentation me fait penser un peu à du JSF version scala. L'architecture statefull n'étant pas vraiment ce que je recherche dans le cadre de ce projet, je ne suis pas allé plus loin.
*   **Scalatra**, à l'exact opposé, propose une solution très légère (routing + connecteur pour templates en gros), c'était mon premier choix. J'ai abandonné (quasi) à cause d'une raison : pas de redéploiement automatique avec le plugin web de sbt (enfin si mais c'est un "redémarrage automatique" plutôt)
*   **Play2**, mon choix final pour les raisons suivantes :
    *   Déploiement à chaud
    *   Toutes les librairies dont j'avais besoin sont proposées en natif (BD, Json..)
    *   J'ai beaucoup aimé playframework premier du nom (sur lequel tourne ce site d'ailleurs)

Conclusion, pour le moment il n'y a pas une myriade de choix (ce qui n'est pas non plus un mal soyons franc) mais surtout aucun des framework que j'ai pu trouver ne me donne une impression de maturité (à part Lift peut-être) comme l'on peut trouver dans d'autres langages (java, python, ruby au hasard).

#### Play reloaded

1ère étape, installation du framework (2.1 à partir des sources), aucun problème l'application était démarrée en moins de 5 minutes.
 Pour ceux que ça intéresse direction [Building from source](https://github.com/playframework/Play20/wiki/BuildingFromSource) puis [Nouveau projet](https://github.com/playframework/Play20)

Ensuite, vérifier les composants nécessaires :

*   Json : librairie native de play à disposition
*   Base de données : Anorm natif play2 également
*   Routing : natif également

Bon maintenant qu'on a tout ce qui faut y'a plus qu'à.

#### Les routes

Premièrement écrire les routes, ou autrement dit définir l'API client.
 Nous avons besoin de pouvoir lister les tâches, puis ajouter, archiver, et terminer une tâche.
 Ce qui nous donne : [routes](https://github.com/Vp3n/TodoApp/blob/master/conf/routes)

Il est important de noter que le fichier de routes est compilé en scala et donc type safe.
Pour avoir travaillé sur des projets symfony avec des centaines de routes, savoir immédiatement qu'une route peut planter ou ne plus correspondre à rien, je dois dire que l'apport est réellement intéressant.
 Vous aurez aussi remarqué 2 routes qui, au premier abord, n'ont un peu rien avoir avec notre problème.

*   /assets/javascripts/routes va nous permettre de donner un accès restreint aux routes côté client (ce qui évite de mettre des URLs en dur comme des crados)
*   /assets/*file est une route générée par défaut par play qui va mapper les ressources du répertoire public sur l'URL qui va bien. Ce genre de route est très classique, on va beaucoup s'en servir par la suite.

#### Controlling everything

Secundo, écrire le minimum requis au niveau Controller afin de corriger nos erreurs de compilation.
 On peut implémenter chacune des actions avec le retour "TODO" définit dans le framework qui va automatiquement renvoyer un message de type "en construction" :) dans un premier temps.
 En dehors bien entendu de l'index qui va retourner la page HTML basique avec de quoi charger le code client.

Les actions liées à Todo devraient être dans un Controller distinct, je reverrai ça un peu plus tard.
 Mon idée ici est de séparer les responsabilités en ressources REST, permettant également de clarifier un peu l'API client (même si dans notre cas ça va pas bien loin nous sommes d'accord :)).
 Ici nous avons 2 ressources : Todo et Archive

Vous pouvez consulter le [Controller Application final](https://github.com/Vp3n/TodoApp/blob/master/app/controllers/Application.scala).
 Pour le moment, on se s'intéresse qu'aux déclarations des actions.

Ceux qui n'ont jamais vu de Scala commencent peut-être déjà à prendre peur, faut pas, y'a pire après (ouais j'suis un mec rassurant moi)/

#### Modelize your body

Bon maintenant on peut appeler notre serveur, sauf que pour le moment il fait approximativement pas grand chose =).
 Il nous faut donc des données.

Nous allons créer une classe Todo représentation de ce qui va être utilisée dans l'application.
 Une seule ligne de code est nécessaire.

<pre>case class Todo(id: Pk[Long], label: String, isDone: Boolean, isArchived: Boolean)
</pre>

Qu'est-ce qu'une case class me dire-vous ?
En très résumé, c'est une classe qui va nous permettre de profiter de plusieurs fonctionnalités automatiquement. En effet le compilateur scala va ajouter un certains nombres de choses pour nous comme :

*   Mettre à disposition une factory pour créer des instances (à la main ça revient à déclarer une méthode apply dans le singleton Todo).
*   décliner les arguments du constructeur automatiquement en champ de la classe (par défaut en tant que val)
*   Implémenter des méthodes equals, hashCode et toString par défaut
*   Enfin ajouter une méthode copy pour créer une instance à partir d'une autre en changeant juste les champs souhaités.

(Pour aller plus loin, ce genre de déclaration est également ce qui permet d'utiliser le Pattern Matching en Scala)

Maintenant vous comprenez mieux pourquoi on a pu déclarer notre classe en une seule ligne.
 En scala, une fois qu'on a compris que le compilateur fait le café, tout va beaucoup mieux =)

Autre fait notable, qui nous emmène au sujet suivant, est la déclaration de notre Id. Il est noté de type Pk[Long]
 Pk n'étant pas pour "Pourkoi", vous aurez compris qu'on parle de Primary Key.

#### L'Anorm-alité selon play

Voyons maintenant dans le fichier [Todo.scala](https://github.com/Vp3n/TodoApp/blob/master/app/models/Todo.scala) ce qui se trouve dans object Todo.
 En Scala, "object" sert à déclarer un singleton, qui va servir notamment à créer des factory différentes via la méthode apply ou encore remplacer les méthodes statiques dont on a l'habitude dans d'autres langages. En effet, il n'existe pas de static en Scala.

Premièrement, j'ai déclaré 2 méthodes "apply" (ou 2 factory) : une permettant de créer un nouveau Todo juste à partir d'un libellé (cas le plus fréquent) l'autre permettant de mettre à jour notre objet une fois l'identifiant de base de données trouvé (qui pourrait d'ailleurs être changée par un appel à "copy").

Le champ mapper est ce qui permet de mapper les résultats SQL en objet Todo.
 Quant à la méthode "~", c'est là où ça devient un peu "WTF?", ci-dessous la déclaration de la méthode "~" :

<pre>def ~[B](p: RowParser[B]): RowParser[A ~ B] = RowParser(row => parent(row).flatMap(a => p(row).map(new ~(a, _))))
</pre>

([la classe SqlParser au complet](https://github.com/playframework/Play20/blob/master/framework/src/anorm/src/main/scala/anorm/SqlParser.scala))

Je n'ai toujours pas compris précisément ce que cette méthode fait, je dois avouer que je n'y ai pas forcément mis de la bonne volonté.
 Pourquoi ? Toutes les bonnes pratiques de développement indiquent que la lisibilité et la clarté du code importe sur tout le reste.
 Le code doit être lisible, le cas échéant proposer au minimum une documentation. Ici, nous n'avons ni l'un ni l'autre.

Débutant en scala, je suis curieux de savoir combien de temps une personne qui maîtrise le langage met pour comprendre exactement ce que fait cette ligne ?
 Scala est un très bon langage mais je vois beaucoup trop de codes de ce genre qui pourrissent littéralement sa réputation.
 La définition d'opérateur permet dans certains cas d'améliorer grandement la lisibilité (comme pour des opérations mathématiques par exemple) mais les symboles utilisés doivent être connus universellement. Qu'est-ce ~ est censé signifier ? ne parlons même pas de ~> (j'ai du me reprendre à 2 fois pour l'écrire correctement)

Bientôt sur vos controllers des méthodes ":(" pour HTTP 500 ou ":)" pour HTTP 200\. Bref je m'égare.

Le reste des méthodes du singleton sont assez parlantes, ce sont simplement les différentes requêtes SQL dont nous avons besoin.
 Je n'ai pas trouvé comment inclure simplement un paramètre de type liste (pour créer un "where in"), ce qui explique la petite bidouille de la méthode archive().

#### Communication

Dernière partie, envoyer les données au client en JSON, retour au code du controller.
 L'API de play de ce côté là est assez claire si on a compris le fonctionnement des paramètres implicite en scala.
 Le singleton Json propose les méthodes toJson et fromJson qui a besoin d'un serializer/deserializer implicite pour l'objet ciblé.
Pour résumer les implicits permettent au compilateur de "corriger" les erreurs qu'il peut trouver en compilant le code, ce qui permet d'étendre des classes existantes (conversion implicite de type) ou de fournir des paramètres automatiquement.
 Par exemple lorsque nous écrivons :

<pre>val todo = Json.fromJson[Todo](json)
</pre>

Le compilateur va déterminer le paramètre manquant en cherchant une déclaration de Deserializer implicite dans le scope de cette ligne.
 Nous allons donc déclarer nos serializer/deserializer pour se faciliter la vie, vous pouvez voir le code dans le singleton Todo.

Dernière partie, utiliser tout ça dans nos controllers, vous devriez maintenant comprendre le code de la classe [Application.scala](https://github.com/Vp3n/TodoApp/blob/master/app/controllers/Application.scala)

#### Conclusion

L'article étant déjà relativement long, je suis peut-être allé trop vite sur certains détails, n'hésitez pas me faire des retours sur ce point ou même sur le contenu du code (dont je ne doute pas, doit pouvoir être bien mieux écrit).

En conclusion, je suis très content de Scala, un peu moins de play2\.

J'étais plutôt enthousiaste au début, mais Anorm m'a vite refroidit. Mon conseil, si devez utiliser une base relationnelle avec play2 n'utilisez pas Anorm (surtout que la "road map" le concernant ne rassure pas vraiment [Futur d'Anorm](https://groups.google.com/forum/?hl=fr&fromgroups#!searchin/play-framework/slick/play-framework/XBgOz1_Nc50/hGA7bakmy7MJ)).
C'est pour le moment, mon seul vrai reproche pour le moment vis à vis de ce framework. Ce qui est dommageable étant donné que le reste a l'air d'éviter ce genre d'écueils.
On pourrait également citer le manque de "fonctionnalités" à proprement parler mais ça a l'air plutôt d'être la philosophie (un noyau solide et des extensions autour) et évidemment play2 est encore très jeune.

Concernant Scala, j'aime beaucoup le langage qui évite beaucoup de verbosité et de code répétitif. Il permet également de créer des API très avancées.
Mais comme dirait l'autre "With great power comes great responsibility". Il faut d'autant plus être attentif à écrire du code propre, lisible et éviter l'engouement du "one liner".
Les anti fonctionnels décrieront ce langage sûrement autant que les anti impératifs, pourtant avoir un langage permettant d'écrire à la fois de l'objet, du procédural ou du fonctionnel ouvre des perspectives très intéressants sur la façon de penser notre code et de s'adapter au problématique du monde réel.
Je suis assez d'accord avec [cette réponse sur SO](http://stackoverflow.com/a/2079678/982085), il y a des cas où l'OO est mieux adaptée, d'autres ou le fonctionnel l'emporte, pourquoi pas un langage qui allie les deux ?

La suite (angularJS, Coffee Script) au prochain article !

<br />
<br />
<br />
<div class="archives">
    <h4>7 commentaires-archivés</h4>
        <div class="comment">
                <div class="comment_author">
                    <a href="http://mandubian.com" target="_blank">
                    <span>mandubian</span>
                    </a>
        </div>
        <div class="comment_meta"><span>02/08/2012 à 21:23</span></div>
                <p>Hello,
Ton article a le mérite de montrer le point de vue de quelqu'un qui se met à Scala et qui découvre Play2 et il est très intéressant! 
Il faut le dire: quand on démarre Scala en venant de Java, ça pique un peu au début et il y a une marche à passer pour piger 2 ou 3 trucs et ensuite ça devient vraiment intéressant et revenir à Java devient très très difficile rapidement... Je conseille de bien piger les notions fonctionnelles de base, type map/flatMap, les closures qui font toute la différence entre les langages de type Scala et les langages comme Java. Mais je trouve que Play2 est un moyen fantastique pour apprendre le langage en passant.

Concernant Anorm, ne t'arrête pas à la première approche et pousse un peu loin dans Scala et reviens y et tu comprendras sûrement la puissance de la bête. Je peux vraiment te dire que c'est une approche non seulement très puissante et proche de SQL mais en plus extrêmement efficace et productive. Pour info, les opérateurs ~/~&gt; peuvent paraître curieux mais ce sont en fait des opérateurs standards dans Scala et les parseurs combinateurs... regarde un peu de ce côté-là... Mais je suis d'accord avec toi sur le fait qu'il ne faut pas plonger dans ce travers des langages très évolués où les gens ont tendance à trop intellectualiser leur code et ça en devient incompréhensible... mais ce n'est pas lié à Scala: en C/C++, on avait les mêmes risques au final avec la gestion mémoire à faire en plus :)

Concernant la courbe d'apprentissage de Play2/Scala, je pense qu'il faut passer la marche au départ plus liée à Scala que Play2 et après une fois que tu as décollé, tu peux creuser toujours plus loin car les possibilités de la bête sont vraiment énormes et on va très très loin au-delà des frameworks classiques Java et même rails. (D'ailleurs la future Play 2.1 va ouvrir de nouvelles fonctionnalités très prometteuses)
Finalement, je peux dire qu'une fois qu'on pige un peu l'approche fonctionnelle, elle devient vite prépondérante et on change complètement sa manière de coder... J'y ai gagné énormément en design de mon code et en robustesse globale (quand tu compiles, tu sais exactement ce que ça va donner)... je codais en C/C++ en réfléchissant un peu, je codais en Java à la machette, je code en Scala en pensant: c'est beaucoup plus enrichissant...</p>
            </div>
                        <div class="comment">
                <div class="comment_author">
                                        <a href="http://vivian-pennel.fr" target="_blank">
                                        <span>Vivian</span>
                                        </a>
                                    </div>
                <div class="comment_meta"><span>03/08/2012 à 12:02</span></div>
                <p>Merci ton retour

Effectivement il y a encore quelques points obscurs pour moi en Scala mais le bouquin m'a beaucoup aidé à progresser rapidement.
je pense aussi que l'investissement en vaut la peine à tout les niveaux.

Pour Anorm pour être honnête je me suis douté que c'était une sorte de convention Scala, cependant dans le langage la grande majorité des opérations proposées par des symboles ont aussi leur équivalent littéral, c'est peut-être ce qui manque ici.

Mais effectivement, Scala a déjà changer ma façon de développer en Java au quotidien, et ce n'est que le début !</p>
            </div>
                        <div class="comment">
                <div class="comment_author">
                                        <a href="http://masgui.wikidot.com" target="_blank">
                                        <span>Guillaume Massé</span>
                                        </a>
                                    </div>
                <div class="comment_meta"><span>04/08/2012 à 04:47</span></div>
                <p>Avec play, tu as un 20 secondes par compilation vs &lt; 1 seconde si tu avais utilisé JRebel et scalatra.</p>
            </div>
                        <div class="comment">
                <div class="comment_author">
                                        <a href="http://coffeebean.loicdescotte.com" target="_blank">
                                        <span>Loic</span>
                                        </a>
                                    </div>
                <div class="comment_meta"><span>04/08/2012 à 21:52</span></div>
                <p>Merci pour cet article
Un aspect de Play2 qui me plait bien (et c'est surtout pour ça qu'il a été conçu) est tout ce qui tourne autour du real time : comet, websockets, iteratee/enumerator... En plus ces API ont le mérite de vraiment bien exploiter la puissance de Scala.

Cet exemple est pas mal pour ce familiariser avec ces concepts : https://github.com/playframework/Play20/tree/master/samples/scala/comet-live-monitoring

Pour anorm je  ne suis pas fan non plus mais on peut utiliser squeryl par défaut aussi avec play2 en version scala

Bonne soirée
Loic</p>
            </div>
                        <div class="comment">
                <div class="comment_author">
                                        <span>jeeps</span>
                                    </div>
                <div class="comment_meta"><span>30/01/2013 à 14:43</span></div>
                <p>Dans votre conclusion, vous dites : "Il faut d'autant plus être attentif à écrire du code propre, lisible et éviter l'engouement du "one liner"."

C'est vrai au plus haut point. Scala offre tellement de liberté et permet d'écrire les choses de façon tellement concise qu'on arrive rapidement à du code illisible.
Sur mon projet, un des développeurs est assez moyen et son code scala est juste illisible.
A mon avis, juste par ce fait, Scala a peu d'avenir dans le monde de l'industrie. Il n'apporte pas assez d'avantages par rapport aux inconvénients.</p>
            </div>
                        <div class="comment">
                <div class="comment_author">
                                        <span>Jean Burlier</span>
                                    </div>
                <div class="comment_meta"><span>31/01/2014 à 21:17</span></div>
                <p>Elle est ou la suite ? :p</p>
            </div>
                        <div class="comment">
                <div class="comment_author">
                                        <a href="http://vivian-pennel.fr" target="_blank">
                                        <span>Vivian</span>
                                        </a>
                                    </div>
                <div class="comment_meta"><span>01/02/2014 à 21:55</span></div>
                <p>Je comptais en faire une au début (c'est pourquoi c'est écrit) mais en fait au moment de la rédaction de celui-ci c'était la découverte d'AngularJS. Par la suite j'en ai fais beaucoup plus, et l'article par rapport à ce projet là ne me paraissait plus trop pertinent. 

J'en ferai sûrement dédié à Angular, reste à savoir quand ;)</p>
            </div>
                            </div>