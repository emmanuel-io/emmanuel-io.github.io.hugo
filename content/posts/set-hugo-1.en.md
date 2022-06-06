---
title: "How to set up this website using Hugo part 1"
date: 2022-06-04T01:122:42+02:00
author: "Emmanuel IO"
linktitle: How to set up this website using Hugo
next: /set-hugo-2
# prev: /using-redis-with-spring-boot
weight: 3
authorAvatar: image/logo.svg
image: image/3/selfBlog.png
tags : [
    "hugo",
    "blog",
    "tutorial",
]
categories : [
    "tutorial"
]
draft: false
---

My career is shifting, and I'm steering it towards web development, mostly backend. Coming from digital electronics & embedded systems, I have a broad view on the digital ecosystem and I need a space where to show case my work and ideas. Having a github account is nice, but I wanted a place where I could publish articles online. It can be of any form like tutorial, blogs, reviews, editorials and much more. Having my own blog provide the chance to add one more aspect of social presence. This blog website will as well be my portfolio.

## What were the options for blogging

There are some free options, a couple of them are:

* [WordPress](https://wordpress.com/)
* [Medium](https://medium.com/)
* [Ghost](https://ghost.org/)
* [Wix](https://www.wix.com/blog)

These paid or partially free platforms provide a end to end solution to setup a website. They provides many benefits like :

* Focus mainly on content. Formatting is configured on the platform
* SEO (Search Engine Optimization) to promote the article in google search
* Easy interface to write article in browser itself
* And many more like support for images, videos and plugin extensions
* Abstract database, themeing, hosting and updates management

As the title of the suggest, I wanted to create blog for free and github allow you to have [hosting for free](https://pages.github.com/).
The only catch here is that you can only have static content, no server, no database allowed.

## Why Hugo?

From the github pages constraints came an idea I wanted to try since a while: **using a static site generator**.
While github pages provides a convenient way to use [Jekyll](https://docs.github.com/en/pages/setting-up-a-emmanuel-io-github-io-hugos-site-with-jekyll), a slow but proven solution, I wanted to try [Hugo](https://gohugo.io/) as it is fast and could use the github actions pipeline to do automatic site generation and it is written in the [Go](https://go.dev/) language which I wanted to have a taste of.

Quoting from the official website:

>Hugo is one of the most popular open-source static site generators. With its amazing speed and flexibility, Hugo makes building websites fun again.

Hugo can be used for complete Content Management where content can be provided in many ways. It has lot of feature for customization,content organizing and important to me, multilingual support out of the box.

While geeky and loving to play with tech stacks, I needed a straightforward solution with great potential for evolution. So that I can save myself from the hassle of managing UX, Styling, Hosting, Database and other elements of website management.
With Hugo, I just write the content at correct location and **there it is!!**.

## Now the process

Ok.. Lets do that

I went through these 5 steps to setup this blog based on [Bhanu Chaddha's article](https://bhanuchaddha.github.io/create-your-own-blog-website-using-hugo-in-less-than-1-hour-for-free/):

1. Install Hugo
2. Create Project
3. Write Your Blog
4. Set up git repositories
5. Push your content and Done!!

Everything which follow is based on my own exeperience using Ubuntu 22.04, adapt it to your own needs using the [setup instructions](https://gohugo.io/getting-started/installing/)!

### **1. Install Hugo**

Installing Hugo on ubuntu is a breeze, just open a console and run:

```bash
snap install hugo --channel=extended
```

After set up is completed. You can check if Hugo is installed correctly using below command. It should print current installed versions of Hugo:

```bash
hugo version                                                                                                            
```

For me the returned string looked like this:

```text
hugo v0.96.0-2fd4a7d3d6845e75f8b8ae3a2a7bd91438967bbb+extended linux/amd64 BuildDate=2022-03-26T09:15:58Z VendorInfo=mage
```

Lets move on to next step.

### **2. Create Project**

Then I went to folder where I created this hugo project:

```bash
cd ~/Documents
```

Now it's time to create the project:

```bash
hugo new site emmanuel-io-github-io-hugo
```

You would see below result. Basic hugo project is ready now.

```text
Congratulations! Your new Hugo site is created in /home/emmanuel/Documents/emmanuel-io-github-io-hugo.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.
```

Hugo is a very flexible static site generator. So flexible that it doesn't impose a look and feel for your website. You could create everything from scratch by hand but there is a better way (they thought about it), use an existing [Hugo's themes](https://themes.gohugo.io/). You can always customize it later if you want.

For this blog I used the [ananke](https://themes.gohugo.io/themes/gohugo-theme-ananke/) theme.

While I could have installed the theme directly in the project tree, it's nicer to use it as a git submodule as I now I want to integrate everything in the github action pipeline. Furthermore this makes upgrades and git actions cleaner.

Installing theme here is not same as installing Hugo. We would just map theme as sub-module in our git project. By doing this we would not clone the theme in the project but just add the reference of [casper-two](https://github.com/eueung/hugo-casper-two.git) theme. Hugo will automatically use the theme while generating the static content. For the context of this blog you dont need to know about the git sub-modules. You can just follow the commands as specified. If you wish to read about git sub-modules, you can read about it [here](https://github.blog/2016-02-01-working-with-submodules/)

#### Git repository creation

Type below commands to initialize the project as a git repository.

```bash
# Move into the project directory
cd emmanuel-io-github-io-hugo

# Initialize git repository    
git init                                                                                
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint: 
hint:     git config --global init.defaultBranch <name>
hint: 
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint: 
hint:     git branch -m <name>
Initialized empty Git repository in /home/emmanuel/Documents/emmanuel-io-github-io-hugo/.git/
 ```

Type below command if like me your default branch is still master and not main.

```bash
# Change branch to main
git branch -m main
 ```

#### Theme Installation

Type below command to install the theme.

```bash
# Adding theme as sub-module
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

You will see below output

```bash
Cloning into '/home/emmanuel/Documents/emmanuel-io-github-io-hugo/themes/ananke'...
remote: Enumerating objects: 2529, done.
remote: Counting objects: 100% (552/552), done.
remote: Compressing objects: 100% (308/308), done.
remote: Total 2529 (delta 279), reused 431 (delta 210), pack-reused 1977
Receiving objects: 100% (2529/2529), 4.46 MiB | 2.05 MiB/s, done.
Resolving deltas: 100% (1375/1375), done.
```

#### Adding Configuration and content

To have a website running, I first copied the exemple from the theme inside the current project using the command below:

```bash
cp -R ./themes/ananke/exampleSite/{config.toml,content*,static*} ./
```

#### Test it on local

Finally I launched hugo web server using the below command:

```bash
hugo server
```

This will start a local server at [http://localhost:1313](http://localhost:1313).
Right now this shows the theme demo website

### **3. Create my own site**

This part is out of the scope of this article, basically I created this new article and adjusted the configuration to fit my needs
