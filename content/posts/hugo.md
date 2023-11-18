+++
#
# Configuration du post
#

# Titre
title = 'Découverte de la technologie Hugo'

# Date
date = 2023-11-18T17:48:02+01:00

# Brouillon
draft = false

# Auteurs
author = ['Lucas Boucher', 'Philippe Soubrane']

# Afficher une table des matières
showtoc = true

# Ouvrir la table des matières par défaut
tocopen = true

#
# Configuration de la miniature
#
[cover]

# Lien
image = 'images/hugo.webp'

# Description
alt = 'Logo de Hugo'
+++

# Qu'est ce que Hugo ?

Hugo est une petite technologie permettant la génération de site statique et provenant du langage de Google : Go.

{{< youtube 0RKpf3rK57I>}}

## Introduction

Elle est extrêmement simple puisque nécessite peu de connaissance en développement. On itilisera majorotairement du markdown pour rédiger des nos posts. En effet, par défaut, notre site se présente comme un blog, mais l'on verra qu'avec un peu plus de développement, les possibilités cette technologie offre une personnalisation quasi-infinie.

## Installation

Dans cet article je ne parlerais que de la façon d'installer Hugo sur macOS, mon système d'exploitation. Pour Windows ou une distribution Linux je vous invite à consulter la documentation de Hugo.

# Notre premier post

Mantenant que nous avons bien installer hugo sur notre système, nous allons ajouter du contenu à notre site/blog.

## Débuter un projet

Pour débuter un projet avec Hugo nous devons lancer la commande `hugo new site <nom-du-projet>`, celle-ci va générer les dossiers nécessaires pour développer correctement notre site.

Et il est également conseillé de faire un `git init` dans le répertoire de notre projet. Cela nous sera de toute façon nécessaire pour terminer ce tutoriel.

## Rédiger

Hugo est pratique puisqu'il va ici nous offrir une première micro-génération avec la commande `hugo new posts/article.md`. Un fichier du nom que l'on a choisi va donc se créer dans le répertoire */content/posts* avec déjà quelques informations par défaut comme le titre ou la date.

Et c'est donc sous ces méta-données que l'on va pouvoir commencer tranquillement notre rédaction en **markdown**. À savoir qu'il est aussi possible de créer un post en `.html`.

## Afficher

Voilà ! Vous avez terminé de rédiger votre tout premier post, et commencer à fournir votre blog. Mais fait-il encore voir ce que vous faite. Pour cela, Hugo permet de lancer un serveur web en local pour vous. Pour se faire, c'est très simple : vous n'avez qu'à lancer la commande `hugo server` dans votre invite et il ne vous faudra plus qu'aller sur l'addresse *http://localhost:1313/* (cela peux varier selon la configuration de votre *baseURL*)

Si vous ne voyez pas tout vos posts, cela peut-être normal. S'ils sont encore en draft vous pouvez les configurer sur **false** ou alors les afficher malgré tout avec la commande `hugo -D server`.

# Rendre notre site plus personnel

Nous voyons enfin ce que nous avons fait, mais cela ne vous convient surement pas

## Ajouter un thème

Pour ajouter un thème il existe plusieurs moyens de faire. :
- Par téléchargement de code source du thème
- Par téléchargement du repo du thème
- Avec les submodules

Ma technique préféré est cette dernière, avec l'utilisation de submodules. Je la préfère puisqu'elle permet de moins polluer notre propre repo avec un projet externe et facilite la mise à jour.

### Pour l'ajouter

```bash
git submodule add --depth=1 <url-du-repo>
git submodule update --init --recursive
```

### Pour le mettre à jour

```bash
git submodule update --remote --merge
```

## Le configurer et/ou le modifier

Le thème que vous avez ajouter permet probablement d'être configurer et d'ajouter plein de superbe fonctionnalité (comme celui que j'utilise, j'en parle [ici](/hugo-hetic/posts/papermod)). Donc n'hésitez pas à aller consulter sa propre documentation.


### Remplacer le thème

Cependant, s'il ne propose pas une fonctionnalité dont vous avez besoin ou alors que vous voulez ajuster son style, il est possible de faire un peu ce que vous voulez. Dans le fichier */layouts* vous pouvez tout simplement écraser tout ce que le thème propose.

### Ajouter du style

Pour ajouter du style il faudra créer un fichier CSS qu'on viendra ensuite lier dans au thème. Il y a parfois un fichier .html prévu par le thème dédié à l'ajout de fichiers externes.

# Comment publier notre site ?

Quoi qu'il arrive nous utiliserons GitHub pour l'héberger. Et ici aussi il y a deux façons de faire :
- En passant avec un deuxième repo
- Avec une seconde branche

Nous fonctionneront avec une seconde branche, cela nous évitera de devoir créer un second repo. On pourra la nommer **gh-pages** pour que GitHub comprenne et déploie automatiquement sur GitHub Pages. Avant cela il faudra refaire un `git init`, mais cette fois-ci dans la production */public*. Et bien envoyer ce projet Git dans le même repo GitHub.

## Générer

Notre site était accessible en local avec `hugo server` mais cela n'est pas la bonne pratique quand on veut le publier en production. Pour cela il faut dans un permier temps générer notre site avec `hugo` ou `hugo -D` pour inclure les brouillons. Et c'est dans le fichier */public* qu'on trouve un **index.html** qui est le bon chemin d'accès de notre site.

## Bonus : Automatiser

Logiquement cela nous demande de générer notre site, et de l'envoyer au repo **gh-pages** à chaque modification.

Pour remédier à cette perte de temps, on peut utiliser GitHub Actions. Cela nous permettra de déclencer une suite d'actions à chaque publication de nouveau commit. GitHub propose une intégration automatique mais il est également possible de le faire nous même en ajouter à *.github/workflows/hugo-deploy.yml* ce fichier :
```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.114.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v3
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v3
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

# Conclusion

Il resterait encore quelques éléments et beaucoup de subtilités à voir pour compléter cette article sur Hugo, mais nous allons en rester là et vous laisser découvrir le reste par vous-même.

Merci à M. Soubrane pour son cours à HETIC sur cette technologie. [Lien vers son LinkedIn](https://www.linkedin.com/in/philippe-soubrane-4b08691/details/experience/)