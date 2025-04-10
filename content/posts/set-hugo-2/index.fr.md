---
title: "Déployer un site Hugo sur GitHub Pages avec CI/CD"
description: "Configurez GitHub Actions pour automatiser la génération de votre blog Hugo et son déploiement avec une clé SSH et une double stratégie de dépôts."
date: 2022-06-04T01:12:42+02:00
image: image/3/selfBlog.png
linktitle: "Déployer Hugo avec GitHub Actions"
tags:
  - hugo
  - blog
  - tutoriel
categories:
  - tutoriel
series: ["hugo-deployment"]
prev: /fr/set-hugo-1
draft: false
weight: 2
---


Dans la partie 1, nous avons exploré comment j'ai créé un site Web en utilisant Hugo, maintenant nous allons créer une action github pour déployer automatiquement le site sur les pages github lorsque nous créons un commit.

### **Set up of Git repositories**

I created 2 GitHub repositories for the project:

1. First Repository for the hugo project `emmanuel-io.github.io.hugo`
2. Second repository to host my blog as Github User page `emmanuel-io.github.io`

You can find on a lot of articles who are using 2 differents branches of the same repository ("main" for the project, "gh_pages" for the generated static files), however I prefere to have two distinct repositories, each one dealing with a different concern.

Now go to the root of the project and execute below commands.

```bash
# create the .gitignore file
touch .gitignore

# add the public folder to the list of ignored files
echo "public/" >>.gitignore 

# commit any uncommitted changes
git commit -a -m "init"

# set remote origin branch
git remote add origin https://github.com/emmanuel-io/emmanuel-io.github.io.hugo.git

# push changes
git push -u origin main
```

I have ignored the `public` directory as we don't need to handle it in the repository.

Now we need to generate a pair of public/private keys to be able to use github actions from the 1st directory to write in the 2nd repository.
To understand what I did, you can check these links from the [GitHub Action](https://github.com/marketplace/actions/github-pages-action) used to deploy my hugo website.

Have a particular look at this part:
[Create SSH Deploy Key](https://github.com/marketplace/actions/github-pages-action#%EF%B8%8F-create-ssh-deploy-key)

Do as stated but **NOT IN YOUR PROJECT DIRECTORY !!!**

Now in the root directory I created a github workflow yaml file to make sure that every time I push to remote, my blog is updated by github. In VSCode I created this file in ".github/workflows/deploy.yml"

```text
# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "deploy"
  deploy:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2
        with:
          submodules: true
          fetch-depth: 0
      
      # Github Actions fo Hugo
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: "latest"
          extended: true
      
      # Creates ./public directory
      - name: Build
        run: hugo --minify
      
      # Upload/Commit the contents from publish_dir -> external_repository
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: ${{ github.ref == 'refs/heads/main' }}
        with:
          deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          # github_token: ${{ secrets.GITHUB_TOKEN }}
          personal_token: ${{secrets.GH_TOKEN}}
          publish_dir: ./public
          external_repository: emmanuel-io/emmanuel-io.github.io
          publish_branch: main
          user_name: emadio
          user_email: emmanuel.amadio@mail.com
```

### **Push your content**

Now we have set up our repositories and created our static content in `public` directory . The only step remain is pushing our local content to GitHub. Lets do that now. Run below commands in the terminal

```bash
# commit any uncommitted changes
git commit -a -m "deploy with actions"

# push changes
git push -u origin main
```

And thats it. 

Now my blog is updated on every push :-)

You can match your content and configuration with below repositories. The only difference is the value of `baseurl` that is because I have set up this demo website as Project Site and not the User Site.

1. ["hugo" Project Repository](https://github.com/emmanuel-io/emmanuel-io.github.io.hugo)
2. [Blog Repository](https://emmanuel-io.github.io/)
