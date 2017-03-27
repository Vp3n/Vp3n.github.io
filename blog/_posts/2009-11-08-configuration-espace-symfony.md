---
layout: post
title: Configuration d'un environnement de travail pour symfony sous Windows XP
permalink: blog/php-symfony-windows-environnement-config
tags: [xampp,php,symfony,windows]
---

Dans ce billet je vais expliquer comment installer et configurer Apache/PHP/Windows XP pour Symfony.
Ce sera la première partie d'un article visant à montrer comment travailler dans de bonnes conditions avec cet environnement.
Je n'ai pas testé Windows Vista ou Seven mais je suppose que cela doit pouvoir s'adapter facilement.

<!--more-->

#### **Etape 1 : Installation d'apache et de PHP.**

Pour installer un environnement de développement Apache/PHP sur windows, nous avons 2 choix : WAMP ou XAMPP. La principale différence entre les deux réside dans le fait que wamp permet de switcher entre plusieurs versions d'apache et de PHP très facilement. Autrement j'ai trouvé Xampp un peu plus stable. Nous prendrons dans ce billet la solution xampp pour exemple, cependant tout ce qui suit est transposable à wamp. Téléchargez la dernière version (1.7.2) de Xampp qui intègre PHP 5.3\.

Pourquoi utiliser PHP 5.3 ? Sur Windows c'est la version la plus performante qui existe, de plus elle est compatible avec symfony (même si actuellement le framework n'utilise pas ses fonctionnalités). Pour plus d'informations je vous invite à lire [cet article](http://www.developpez.net/forums/d832538/php/langage/php-5-3-sous-windows-plus-nouveautes-quon-croit/).

Si vous avez choisi la version .exe de xampp cliquez sur le fichier obtenu et choisissez le chemin de destination pour l'installation. Une fois extrait, un batch va s'exécuter pour finaliser l'installation. Si vous avez choisi la version .zip, il suffit d'extraire les fichiers à l'endroit souhaité et d'exécuter manuellement le batch "setup_xampp.bat".

#### **Etape 2 : Variable d'environnement**

Une fois l'installation terminée, il va falloir ajouter la variable d'environnement PHP à windows pour pouvoir exécuter les commandes de symfony. Pour se faire, clic droit sur poste de travail > propriétés > avancé > variables d'environnements. En bas dans les variables systèmes, cherchez le "Path" puis cliquez sur Modifier. Chaque chemin est séparé par un ";", ajouter le chemin de votre exécutable PHP à la suite. Si ce chemin contient des espaces encadrez le par des guillemets.
Par exemple :

chemins;C:\xampp\php

#### **Etape 3 : Configuration d'apache.**

Nous allons maintenant configurer Apache pour symfony.

*   Ouvrir %xampp%\apache\conf\httpd.conf
    Vérifiez (ou ajoutez ) les élements suivants :
    - Listen 80
    - ServerName localhost:80
    - LoadModule rewrite_module modules/mod_rewrite.so
*   Ouvrir %xampp%\apache\conf\extra\httpd-vhosts.conf
    Ajoutez la ligne suivante :
    NameVirtualHost *:80
*   Ajouter les lignes suivantes à la fin du fichier, ce virtual host évite de perdre la page principale de xampp lorsque vous tapez simplement localhost dans la barre d'adresse (et que vous avez configuré d'autres virtual host pour des projets symfony).

**NB :** Cette configuration pourrait être faite directement dans le fichier httpd.conf, mais ceci permet d'en améliorer la lisibilité. Il y a en fait un Include "conf/extra/httpd-vhosts.conf" dans le fichier httpd.conf.

#### **Etape 4\. Vérifiez les modules PHP installés.**

Ouvrez le fichier %xampp%\php\php.ini

*   Vérifiez que les lignes suivantes ne sont pas commentées (";" devant) :
    - extension=php_pdo.dll
    - extension=php_xmlrpc.dll
*   Vérifiez que :
    - short_open_tag est à off
     - magic_quote_gpc est à off
    - register_globals est à off
    - session.auto_start est à off

**NB :** Cette liste contient les pré-requis définis dans le fichier check_configuration.php livré avec symfony.

La configuration de l'environnement est terminée, vous êtes maintenant prêt pour installer Symfony.

Le prochain billet traitera de [l'installation et configuration du couple Symfony/EclipsePDT](http://blog.developpez.com/vivian-pennel/p8307/php/symfony/installation-symfony-eclipsepdt-windows).
