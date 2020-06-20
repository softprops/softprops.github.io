---
title: dev togo
cover_image: https://images.unsplash.com/photo-1503919545889-aef636e10ad4a
tags: tools,showdev
---

> portability should be the default.
> \- Larry Wall

---

In the last post I touched a bit on the writing flow I'm iterating towards, with an eye towards portability. In this post I wanted to talk a bit more about the details of how I intend to make that actually work.

To recap, the workflow I want is

1) write content locally offline
2) track that in a GitHub repo
3) on pushes, publish to dev.to and possibly a personal site where I can scratch some design idea itches

My goal is set myself up to build up a consistent writing routine. Posting to dev.to helps hold me accountable so my first stop was checking that box.

Introducing [devtogo](https://github.com/softprops/devtogo)! Devtogo is a small Rust program that communicates with the dev.to API and synchronizes local content with a dev.to account. You can learn more about the dev.to API [here](https://docs.dev.to/api/).

Here's how it works.

To do anything useful with the dev.to API you first need an API key. You can create these on your [dev.to account page](https://dev.to/settings/account) by providing a project name and clicking "Generate API Key". I called mine "devtogo".

Next, I need to get a list of all of my dev.to articles. I need this list to compare with what I content I have locally to avoid creating duplicate posts. I use the [User's all articles API](https://docs.dev.to/api/#operation/getUserAllArticles) to fetch this list.

Dev.to provides a useful page documenting its own editor [here](https://dev.to/p/editor_guide). All posts at a minimum require a title. A title makes an article unique.

With that list, devtogo collects a list of markdown files in a directory and parses out frontmatter, validating some basic correctness checks.

If a local article exists with a title that already exists it calls the [update article API](https://docs.dev.to/api/#operation/updateArticle) other wise it creates a new article with the [create article API](https://docs.dev.to/api/#operation/createArticle).




