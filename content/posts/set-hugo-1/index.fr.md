---
title: "Configurer Hugo localement avec Git et un thème"
description: "Apprenez à configurer un blog Hugo depuis zéro en utilisant Git, les sous-modules et un thème de démarrage."
date: 2022-06-04T01:12:42+02:00
# image: 434284638_262047363631315_501358433474908523_n.jpg
linktitle: "Configurer Hugo localement avec Git et un thème"
tags:
  - hugo
  - blog
  - tutoriel
categories:
  - tutoriel
series: ["hugo-deployment"]
next: /set-hugo-2
draft: false
comments: false
hidden: false
---

J'avais besoin d'un espace pour présenter mon travail, partager des idées et écrire des articles — qu'il s'agisse de tutoriels, de critiques, d'éditoriaux ou de simples billets de blog. GitHub est idéal pour héberger du code, mais je voulais un espace à moi sur le web. Ce blog me sert aussi de portfolio numérique.

## Quelles options pour bloguer ?

Voici quelques plateformes populaires pour démarrer un blog :

- [WordPress](https://wordpress.com/)
- [Medium](https://medium.com/)
- [Ghost](https://ghost.org/)
- [Wix](https://www.wix.com/blog)

Ces plateformes tout-en-un offrent des fonctionnalités intéressantes :

- Mise en page automatisée pour se concentrer sur le contenu
- SEO intégré
- Éditeur dans le navigateur
- Gestion des médias, plugins et thèmes
- Hébergement, base de données et mises à jour gérés automatiquement

Mais je voulais quelque chose de gratuit, minimaliste, et entièrement sous mon contrôle.

GitHub Pages propose [un hébergement gratuit pour du contenu statique](https://pages.github.com/) — sans backend, sans base de données — juste du HTML, CSS, JS. Cette contrainte m'a amené à explorer les **générateurs de sites statiques**.

> 🚧 *Mes raisons de choisir Hugo, et une réflexion plus approfondie, seront abordées dans un article à part.*

---

## Mise en place du blog

J’ai suivi ces 5 étapes (inspirées de [l’article de Bhanu Chaddha](https://bhanuchaddha.github.io/create-your-own-blog-website-using-hugo-in-less-than-1-hour-for-free/)) :

1. Installer Hugo
2. Créer le projet
3. Initialiser Git
4. Installer un thème via un sous-module
5. Ajouter du contenu et lancer le site

---

### 1. Installer Hugo

Sous Ubuntu :

```bash
sudo snap install hugo --channel=extended
```

Vérifier l'installation :

```bash
hugo version
```

Exemple de sortie :

```text
hugo v0.96.0+extended linux/amd64 BuildDate=2022-03-26T09:15:58Z
```

---

### 2. Créer un nouveau projet Hugo

Se rendre dans le dossier de travail :

```bash
cd ~/Documents
hugo new site emmanuel-io-github-io-hugo
```

Cela crée la structure du projet Hugo.

---

### 3. Initialiser Git

```bash
cd emmanuel-io-github-io-hugo
git init
git branch -m main
```

---

### 4. Installer un thème avec un sous-module

J’ai choisi le thème [Ananke](https://themes.gohugo.io/themes/gohugo-theme-ananke/) et je l’ai ajouté comme sous-module Git :

```bash
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

---

### 5. Ajouter la configuration d'exemple et du contenu

Pour tester rapidement le site :

```bash
cp -R ./themes/ananke/exampleSite/{config.toml,content*,static*} ./
```

Puis lancer le serveur local :

```bash
hugo server
```

Accédez à [http://localhost:1313](http://localhost:1313) pour voir la version de démonstration du thème.

---

### 6. Personnaliser le site

À partir de là, j’ai commencé à adapter la configuration à mes besoins.

👉 *Dans le prochain article, j’expliquerai comment j’ai structuré le site, ajouté le support multilingue, configuré GitHub Actions et personnalisé le thème.*
