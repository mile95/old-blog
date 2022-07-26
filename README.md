# mile95.github.io

A place for me to practice my technical writing and document my learnings.

Visit the blog  [here](https://mile95.github.io)

## Run locally

```bash
$ bundle install
$ bundle exec jekyll serve
```

## Creating a new post

```bash
mkdir _posts/<post-short-name>
touch <yyyy-mm-dd-name-of-post>.md
```

The post should contain atleast the following metadata

```
---
layout: post
title:  "title"
date:   2021-12-14 10:10:15 +0700
tags: [tag1, tag2, etc]
---
```

## Analytics

The page is tracked using google analytics which was added by including the following

```js
 <!-- Global site tag (gtag.js) - Google Analytics -->
 <script async src="https://www.googletagmanager.com/gtag/js?id=G-C4XYG784RR"></script>
 <script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', ');
 </script>

```

in the `head` section of `_includes/header.html`

## Thanks to
[piharpi](https://github.com/piharpi/jekyll-klise) for the nice-looking theme!
