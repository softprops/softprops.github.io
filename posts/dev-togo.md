---
title: portable posting with devtogo
cover_image: https://images.unsplash.com/photo-1523469625121-6dcacbeb51c2
tags: tools,showdev,writing
---

> portability should be the default.
> \- Larry Wall

---

* [serving dev to go](#serving-dev-to-go)
* [dev delivery service](#dev-delivery-service)
* [takeaways](#takeaways)

In the last post, I touched a bit on the writing flow I'm iterating towards, one with an eye towards content portability and an offline focus. In this post I wanted to talk a bit more about the details of how I intend to make that actually work.

To recap, the workflow I want is

1) write content offline, locally
2) track that in a GitHub repo
3) on branch pushes, publish to dev.to and possibly a personal site where I can scratch some design idea itches

My goal is to set myself up to build up a consistent writing routine. Posting to dev.to helps hold me accountable so my first stop was checking that box.

## serving dev to go

👩🏽‍💻🎒Introducing [devtogo](https://github.com/softprops/devtogo)! Devtogo is a small [Rust](https://www.rust-lang.org/) command line app that communicates with the dev.to API and synchronizes local content with a dev.to account. 

> ⭐You can learn more about the dev.to API [here](https://docs.dev.to/api/).

Here's how it works.

To have any meaningful interaction with with the dev.to API, you first need an API key. You can create these on your [dev.to account page](https://dev.to/settings/account). Just provide a project name to refer to the key by and click "Generate API Key". I called mine "devtogo". Call yours whatever. 

Next, I get a list of all of my dev.to articles. I need this list to compare with what I content I have locally to avoid creating duplicate posts. I use the [User's all articles API](https://docs.dev.to/api/#operation/getUserAllArticles) to fetch this list.

This is made simple and easy with the [serde](https://crates.io/crates/serde) and [reqwest](https://crates.io/crates/reqwest) crates.

> ⭐A crate is unit of distribution in Rust, similar to a package if you are familiar with npm or jar if you are familiar with java.

Serde makes it easy to derive a deserialization scheme for a plain old Rustlang struct at compile time.

```rust
#[derive(Debug, Deserialize)]
struct Article {
    // other fields...
    body_markdown: String,
}
```

Reqwest makes it easy and ergonomic to make HTTP requests that can be deserialized from a JSON response.

```rust
async fn fetch(
    client: &Client,
    api_key: &str,
) -> anyhow::Result<Vec<Article>> {
    Ok(
      client
        .get(
          "https://dev.to/api/articles/me/all?per_page=1000"
        )
        .header(
          "api-key", api_key
        )
        .send()
        .await?
        .json()
        .await?
    )
}
```

The field of interest is an article's `body_markdown` which has all of the information needed to make this work. Dev.to provides a useful page documenting what goes into this markdown document [here](https://dev.to/p/editor_guide). All posts require a title. A title makes an article unique.

With a list of articles on hand, devtogo collects a list of local markdown files from a local directory and parses out article titles from their [front matter](https://jekyllrb.com/docs/front-matter/), validating some basic correctness rules. Front matter in this context is just structured meta data embedded within your markdown file.

If a local article exists with a title that already exists it calls the [update article API](https://docs.dev.to/api/#operation/updateArticle) other wise it creates a new article with the [create article API](https://docs.dev.to/api/#operation/createArticle).

## dev delivery service

I'd like for uploading articles to be based on pushes to a GitHub repository.

[GitHub Actions](https://github.com/features/actions) fits this need like a glove. GitHub Actions is a declarative workflow automation tool built directly into GitHub. If you aren't using Actions by now, you are actively losing out value you get for free when using GitHub!

So how do I make this work? To react to a repository event like a push you simply need to drop a yaml file into directory named `.github/workflows` telling GitHub what to do when a push happens. You can call this file anything. I tend to call this `main.yml` when it's the main workflow for my repository. Below is a full example.

```yaml
name: Main

on: push

jobs:
  publish-devto:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Setup devtogo
        run: |
          mkdir -p $GITHUB_WORKSPACE/bin
          export VERSION=v0.1.0
          curl -L "https://github.com/softprops/devtogo/releases/download/${VERSION}/devtogo-$(uname -s)-$(uname -m).tar.gz" \
            | tar -xz -C $GITHUB_WORKSPACE/bin
          $GITHUB_WORKSPACE/bin/devtogo --version
          echo "::add-path::$GITHUB_WORKSPACE/bin"
      - name: Publish
        run: devtogo -s posts
        env:
          DEVTO_API_KEY: ${{ secrets.DEVTO_API_KEY }}
```

Let's break this down, bit by bit.

This first line gives the workflow a name used for display in the actions UI, In your repository, this lives under the "actions" tab.

```yaml
name: Main
```

The second line tells GitHub what repository event to react to for this workflow. In this case, `push` events.

```yaml
on: push
```

The next lines declare a list of "jobs" to execute when a push happens. These can be arbitrary and run parallel or can be ordered by dependencies on one another and run sequentially. Here I'm only declaring a single job called "publish-devto".

```yaml
jobs:
  publish-devto:
    ...
```

The next line tells GitHub what kind of virtualized environment to run on. I tend to default to `ubuntu-latest`, but you can also run workflows on macos and windows if you'd like.

```diff
jobs:
  publish-devto:
+    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Setup devtogo
        run: |
          mkdir -p $GITHUB_WORKSPACE/bin
          export VERSION=v0.1.0
          curl -L "https://github.com/softprops/devtogo/releases/download/${VERSION}/devtogo-$(uname -s)-$(uname -m).tar.gz" \
            | tar -xz -C $GITHUB_WORKSPACE/bin
          $GITHUB_WORKSPACE/bin/devtogo --version
          echo "::add-path::$GITHUB_WORKSPACE/bin"
      - name: Publish
        run: devtogo -s posts
        env:
          DEVTO_API_KEY: ${{ secrets.DEVTO_API_KEY }}
```

The next lines starts list out the sequential steps performed in a job. The first step of my workflow  tells GitHub Actions to checkout the latest source code for my branch. I'll need this to get the latest content to publish.

```diff
jobs:
  publish-devto:
    runs-on: ubuntu-latest
    steps:
+     - name: Checkout
+       uses: actions/checkout@v2
      - name: Setup devtogo
        run: |
          mkdir -p $GITHUB_WORKSPACE/bin
          export VERSION=v0.1.0
          curl -L "https://github.com/softprops/devtogo/releases/download/${VERSION}/devtogo-$(uname -s)-$(uname -m).tar.gz" \
            | tar -xz -C $GITHUB_WORKSPACE/bin
          $GITHUB_WORKSPACE/bin/devtogo --version
          echo "::add-path::$GITHUB_WORKSPACE/bin"
      - name: Publish
        run: devtogo -s posts
        env:
          DEVTO_API_KEY: ${{ secrets.DEVTO_API_KEY }}
```

The next step installs the devtogo tool from a GitHub release asset and adds it to the jobs PATH.

```diff
jobs:
  publish-devto:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
+     - name: Setup devtogo
+       run: |
+         mkdir -p $GITHUB_WORKSPACE/bin
+         export VERSION=v0.1.0
+         curl -L "https://github.com/softprops/devtogo/releases/download/${VERSION}/devtogo-$(uname -s)-$(uname -m).tar.gz" \
+           | tar -xz -C $GITHUB_WORKSPACE/bin
+         $GITHUB_WORKSPACE/bin/devtogo --version
+         echo "::add-path::$GITHUB_WORKSPACE/bin"
      - name: Publish
        run: devtogo -s posts
        env:
          DEVTO_API_KEY: ${{ secrets.DEVTO_API_KEY }}
```

The last step runs to tool and performs the upload outlined above. 

Note the `DEVTO_API_KEY` environment variable. The tool expects this to exist and contain a valid dev.io API key. These are considered secret, don't store these in code or share with others! Instead you can safely and securely store them inside your repository's [encrypted Secrets](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets). The example below shows how you can dereference a secret and store it in a workflow steps environment. These steps are ephemeral. All other parts of your workflow will not have access to this secret.

```diff
jobs:
  publish-devto:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Setup devtogo
        run: |
          mkdir -p $GITHUB_WORKSPACE/bin
          export VERSION=v0.1.0
          curl -L "https://github.com/softprops/devtogo/releases/download/${VERSION}/devtogo-$(uname -s)-$(uname -m).tar.gz" \
          $GITHUB_WORKSPACE/bin/devtogo --version
          echo "::add-path::$GITHUB_WORKSPACE/bin"
+     - name: Publish
+       run: devtogo -s posts
+       env:
+         DEVTO_API_KEY: ${{ secrets.DEVTO_API_KEY }}
```

That's it for today. In a future post I'll likely post about experimenting with static site generators. Stay tuned.

## takeaways

* dev.to api is really straight forward and makes the platform that much more compelling to use.
* If you're into the idea of portable content, give [devtogo](https://github.com/softprops/devtogo) a whirl. I'm interested in your feedback.