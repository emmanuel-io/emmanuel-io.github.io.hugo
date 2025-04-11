---
title: "Setting up Hugo locally with Git and a theme"
description: "Learn how to set up a Hugo blog from scratch using Git, submodules, and a starter theme."
date: 2022-06-04T01:12:42+02:00
# image: 434284638_262047363631315_501358433474908523_n.jpg
linktitle: "Setting up Hugo locally with Git and a theme"
tags:
  - hugo
  - blog
  - tutorial
categories:
  - tutorial
series: ["hugo-deployment"]
next: /set-hugo-2
draft: false
comments: false
hidden: false
---

I needed a space to showcase my work, share ideas, and write articles — whether tutorials, reviews, editorials, or blog posts. GitHub is great for hosting code, but I wanted a space of my own online. This blog also serves as my digital portfolio.

## What were the options for blogging?

Some popular options for getting started with a blog include:

- [WordPress](https://wordpress.com/)
- [Medium](https://medium.com/)
- [Ghost](https://ghost.org/)
- [Wix](https://www.wix.com/blog)

These platforms offer end-to-end solutions with nice features like:

- Focus on content with built-in formatting
- SEO optimization
- In-browser editing interfaces
- Media and plugin support
- Abstracted handling of themes, databases, hosting, and updates

But I wanted something free, minimal, and fully under my control.

GitHub Pages offers [free hosting for static content](https://pages.github.com/) — no backend, no database — just HTML, CSS, JS. That constraint inspired me to explore **static site generators**.

> 🚧 *My reasons for choosing Hugo, and a deeper look at why I use it, will be covered in a dedicated follow-up post.*

---

## Setting up the blog

I followed this 5-step process (inspired by [Bhanu Chaddha's article](https://bhanuchaddha.github.io/create-your-own-blog-website-using-hugo-in-less-than-1-hour-for-free/)):

1. Install Hugo
2. Create a project
3. Set up Git
4. Install a theme via submodule
5. Add content and run the site

Everything below is based on my experience using Ubuntu 22.04. You may want to adapt it using the [official Hugo setup instructions](https://gohugo.io/getting-started/installing/).

---

### 1. Install Hugo

On Ubuntu:

```bash
sudo snap install hugo --channel=extended
```

Then verify the installation:

```bash
hugo version
```

You should see output similar to:

```text
hugo v0.96.0+extended linux/amd64 BuildDate=2022-03-26T09:15:58Z
```

---

### 2. Create your Hugo project

Navigate to the folder where you want your blog project to live:

```bash
cd ~/Documents
hugo new site emmanuel-io-github-io-hugo
```

This will create the Hugo project structure:

```text
Congratulations! Your new Hugo site is created in /home/emmanuel/Documents/emmanuel-io-github-io-hugo.
```

---

### 3. Initialize Git

```bash
cd emmanuel-io-github-io-hugo
git init
git branch -m main  # If your default is still 'master'
```

---

### 4. Install a theme using Git submodule

For this blog, I used the [Ananke theme](https://themes.gohugo.io/themes/gohugo-theme-ananke/). I added it as a submodule to keep it clean and version-controlled:

```bash
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

---

### 5. Add example configuration and content

To get a working site quickly, I copied the sample content and config from the theme:

```bash
cp -R ./themes/ananke/exampleSite/{config.toml,content*,static*} ./
```

---

### 6. Run the Hugo development server

```bash
hugo server
```

Open [http://localhost:1313](http://localhost:1313) in your browser to view the site.

---

### 7. Start customizing

At this point, I began adapting the configuration and writing my first content.

👉 *In the next post, I’ll cover how I structured the blog, added multilingual support, integrated GitHub Actions, and customized the theme.*
