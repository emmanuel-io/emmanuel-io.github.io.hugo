
---
title: "Comment j'ai migré un site WordPress vers un nouveau plan d'hébergement et évité un prix de renouvellement élevé"
description: "Découvrez comment migrer un site WordPress entre des plans d'hébergement, éviter des frais de renouvellement élevés et résoudre des problèmes comme les préfixes dans .htaccess causant des erreurs 500."
date: 2025-05-01T23:00:00+04:00
tags:
  - wordpress
  - hosting
  - migration
  - blog
categories:
  - reflections
draft: false
comments: false
hidden: false
---

## Contexte : La décision de migration

Face au coût élevé du renouvellement d'un plan d'hébergement, j'ai dû faire un choix : soit payer pour une nouvelle année, soit déplacer le site vers un plan plus abordable. Comme le site est plus un outil de **branding** pour son auteur qu'une machine générant des revenus, l'option la plus **économique** s'est imposée. Après tout, il ne s'agissait pas de rentabilité — mais de maintenir le site sans le fardeau des frais de renouvellement élevés. 

Étant impliqué dans ce projet **pro bono**, je cherchais des moyens de maintenir les coûts bas jusqu'à ce que l'objectif ultime soit réalisé : **migrer tout vers Hugo**. Cela demandera une **planification minutieuse** étant donné qu'il y a plus de **3 000 articles** à prendre en compte. Mais pour l'instant, la priorité était d'éviter de payer un prix élevé de renouvellement tout en gardant le contrôle du site.

J'ai donc opté pour **migrer le site WordPress** vers un plan d'hébergement plus abordable. Ce n'était pas aussi simple que je l'avais imaginé, mais l'objectif était clair : déplacer le site, **économiser sur les frais de renouvellement**, et espérer apprendre quelque chose en cours de route. 🚀

---

## Étape 1 : Sauvegarde, la méthode facile

La première étape de la migration a été de sauvegarder l'ensemble du site WordPress — y compris les **fichiers** et la **base de données**. Pour cela, j'ai profité des **méthodes faciles** proposées par Hostinger, qui offraient un moyen simple de créer un fichier tar.gz pour l'ensemble du site et une sauvegarde de la base de données via leur interface hPanel. Ces étapes étaient simples et m'ont fait gagner beaucoup de temps pour mettre en place les sauvegardes, le temps de me préparer un café ☕.

## Étape 2 : Défis liés au téléchargement

Le véritable défi est survenu lorsque j'ai essayé de télécharger le tarball que je venais de télécharger. Le fichier de **30 Go** était bien trop volumineux pour être téléchargé en une seule fois via le client web : avec un temps estimé de transfert de 8 heures — une durée beaucoup trop longue — c'était tout simplement trop lent et, franchement, risqué de se fier à cette méthode.  
Surtout avec une connexion à **1 Mo/s**, probablement limitée par l'interface web. 😤

C'est à ce moment que j'ai vérifié si l'accès **SFTP** et **SSH** étaient disponibles sur mon plan. Cela a été un véritable **tournant** : cela m'a donné beaucoup plus de contrôle sur le processus de transfert. Et j'ai pu télécharger les fichiers à **deux fois la vitesse** du client web. ⚡

Avec l'accès SSH en main, je me suis assuré que les commandes `cat` et `tar` étaient disponibles sur le serveur (on n'est jamais trop sûr). Puis après avoir découpé mon archive en plusieurs fichiers, j'ai pu télécharger les parties via SFTP et, une fois toutes les parties sur le serveur, j'ai réassemblé les fichiers dans le fichier tar.gz d'origine avant de les extraire. Cette méthode a fourni plus de stabilité et de vitesse que de se fier à l'interface web.

Résumé des étapes pour le téléchargement des fichiers :

1. Découper l'archive localement.

```bash
split -b 1G large_backup.tar.gz large_backup_part_
```

2. Télécharger les parties vers le nouvel hébergement en utilisant SFTP.

3. **SSH** sur le nouveau serveur.

4. Réassembler les parties.

```bash
cat large_backup_part_* > large_backup.tar.gz
```

5. Extraire les fichiers.

```bash
tar -xzvf large_backup.tar.gz -C public_html
```

6. Enfin, déplacer les fichiers extraits là où c'est nécessaire.

**Remarque** : À ce moment-là, je commençais à douter de ma décision. Le temps que je passais ici aurait sûrement pu être utilisé ailleurs, peut-être en payant le renouvellement et en gardant les choses simples. 🤔

## Étape 3 : La migration ou comment j'ai tout cassé

Une fois les fichiers téléchargés sur le nouveau plan, l'étape suivante était la migration de la base de données. L'importation de la base de données via **phpMyAdmin** s'est très bien passée, j'ai simplement supprimé toutes les tables existantes avant l'importation, et voilà. ✔️

Passons maintenant à la configuration de WordPress pour utiliser cette base en éditant `wp-config.php`.

1. J'ai mis à jour les détails de la base de données dans `wp-config.php` :

```php
define('DB_NAME', 'new_database_name');
define('DB_USER', 'new_database_user');
define('DB_PASSWORD', 'new_database_password');
define('DB_HOST', 'new_database_host');
```

2. Mettre à jour les URL du site dans la table `wp_options` de la base de données pour pointer vers le nouveau domaine :

```sql
UPDATE wp_options SET option_value = 'http://yournewdomain.com' WHERE option_name = 'siteurl' OR option_name = 'home';
```

## Étape 4 : Le problème `.htaccess` - Une leçon de dépannage

Tout allait bien jusqu'ici. Mais lorsque j'ai testé le site, j'ai rencontré un problème.

C'est là que les choses sont devenues intéressantes. Après avoir migré le site et effectué les tests, je me suis retrouvé face à une erreur **500 serveur interne**.  
Il s'avère que mon fidèle fichier `.htaccess` avait des règles de **préfixage**; merci au plugin **Wordfence** qui causait des conflits avec les redirections de WordPress.

Le plugin préfixait le mauvais chemin, ce qui a conduit à des redirections cassées. Un peu de fouille dans le fichier `.htaccess` a révélé le problème.

Voici une version anonymisée de la section problématique :

```apache
# Wordfence WAF
<IfModule LiteSpeed>
php_value auto_prepend_file '/home/user-identifier/domains/politicothon.com/public_html/wordfence-waf.php'
</IfModule>
<IfModule lsapi_module>
php_value auto_prepend_file '/home/user-identifier/domains/politicothon.com/public_html/wordfence-waf.php'
</IfModule>

# FIN Wordfence WAF
```

**La solution** : J'ai simplement commenté les lignes défectueuses, en m'assurant que les redirections fonctionnaient correctement, puis j'ai réinitialisé les modifications de `.htaccess` depuis le plugin. Ça a finalement fonctionné. 🎉

## Étape 5 : Déplacement des emails et autres tâches imprévues

Bien sûr, aucune migration n'est complète sans le déplacement des emails. Heureusement, Hostinger a facilité ce processus.

## Tests finaux et ajustements

Une fois que tout a été téléchargé, configuré, et que la plupart des problèmes ont été résolus, j'ai effectué quelques vérifications finales :

1. **Vider les permaliens** dans WordPress : Cela a permis d'assurer l'absence de liens cassés.
2. **Tester le site** : J'ai visité manuellement le site pour vérifier que toutes les pages, publications et médias étaient intacts.
3. **Vérifier les emails** : J'ai envoyé et reçu quelques emails de test pour confirmer la réussite de la migration. ✅

---

Conclusion : Le temps passé en valait-il la peine ?
En repensant à la migration, c'était une expérience d'apprentissage. Bien sûr, j'ai économisé sur le coût du renouvellement, mais le temps passé à résoudre des problèmes, déplacer des fichiers, gérer les soucis de .htaccess et transférer les emails m'a probablement coûté plus en temps que simplement renouveler le plan.

Mais au final, j'ai acquis une compréhension précieuse des configurations WordPress, du dépannage côté serveur et de la gestion d'hébergement. C'est une leçon qui m'aidera assurément lors de futures migrations — bien que la prochaine fois, je pourrais simplement payer le renouvellement et éviter les tracas. Après tout, **Le temps c'est de l'argent.**

À venir : Migrer un site WordPress vers Hugo
Si la migration de WordPress à WordPress a été correcte, la prochaine fois, je vais relever le défi de passer de WordPress à Hugo. Ce sera sûrement une aventure bien plus complexe — et cette fois, je pourrais même gagner plus de temps (ou dépenser moins dans le processus !).

---

**Leçon apprise** : Parfois, la **commodité de ne pas bouger** se paye par le **temps passé** dans le processus. 😜
