---
title: 'digitally unmastered: the GitHub cli edition'
tags: github,git
published: true
---

> Names have power.
> \- Rick Riordan, The Lightning Thief

---

* [prelude](#prelude)
* [motivation](#motivation)
* [GitHub at your command line](#github-at-your-command-line)
* [branching out](#branching-out)
* [takeaway](#takeaway)

## prelude

There have been quite a lot of posts on the topic of renaming your default git branch from "master" to anything else recently [^1] [^2] [^3]. 

Many have covered how to manage doing so on the command line. Most or which have covered how to manage propagating that rename to GitHub though its web UI. 

This post covers how to close that gap with the ✨ [GitHub CLI](https://cli.github.com/)✨.

## motivation

I won't attempt to convince you to change your default git branch name based on its etymology, but I _will_ suggest that you do so for a pragmatic and practical reason. 

A common argument I've seen behind those avoiding changing default branch names so far is fear of what will break in doing so. This is typically anchored to a gap in understanding of what within your system depends on it. I invite you embrace a little bit of [chaos](https://en.wikipedia.org/wiki/Chaos_engineering) in exchange for a better understanding of your software systems. A good measure of system health is that is safe to change. When it is not, there's often an underlying issue worth exploring. In any scenario, you will walk away with more confidence in knowing how your system works.

## GitHub at your command line

The GitHub CLI, `gh`, is GitHub's now officially supported command line interface.

On MacOS, you can install it with `brew install github/gh/gh` or your [package manager of choice](https://github.com/cli/cli#installation) on other platforms.

The GitHub CLI comes bundled with a handful of helpful commands for interfacing with your GitHub repositories. 

```sh
$ gh
Work seamlessly with GitHub from the command line.

USAGE
  gh <command> <subcommand> [flags]

CORE COMMANDS
  issue:      Create and view issues
  pr:         Create, view, and checkout pull requests
  repo:       Create, clone, fork, and view repositories

ADDITIONAL COMMANDS
  alias:      Create shortcuts for gh commands
  api:        Make an authenticated GitHub API request
  completion: Generate shell completion scripts
  config:     Manage configuration for gh
```

What makes this CLI especially convenient is that it's git repo-aware. That means when you use `gh` on the command line within a git repository hosted on GitHub, the CLI is able to infer a GitHub repo context for you.

I leverage this often when I want to open the current git repo inside GitHub within a browser window.

```sh
$ gh repo view -w
```

## branching out

With it's recent [`v0.10.0` release](https://github.com/cli/cli/releases/tag/v0.10.0) the CLI gained a few new interesting features that add open a lot of new opportunities. 

`gh` now exposes direct client interface for the GitHub REST _and_ GraphQL APIs so anything thing you can do with the GitHub API, you can now technically do from the `gh` CLI. 🤯

```sh
$ gh api graphql -F owner=':owner' -F name=':repo' -f query='
      query($name: String!, $owner: String!) {
              repository(owner: $owner, name: $name) {
                      releases(last: 3) {
                              nodes { tagName }
                      }
              }
      }
'
```

It won't take long before typing all of this out each time loses its novelty as you'll like have to keep digging through the GitHub API docs to remember out how to compose these requests.

Fortunately, this release also added support for custom aliases which allow you to assign a name to an optionally parameterized command template and since API requests now supported commands, you can template aliased API requests.

```sh
$ gh alias set your-alias-name 'gh-subcommand arg1 arg2 argN'
```

The release of this version happened around the same time as folks were catching onto the git branch rename wave, [including me](https://twitter.com/softprops/status/1271896990398337025)

{% tweet 1271896990398337025 %}

I created a local alias to reset my GitHub repository's default branches

```sh
$ gh alias \
  set default-branch \
  'api -X PATCH repos/:owner/:repo --raw-field default_branch=$1'
```

Now you can just do the following when you're migrating a repository.

```sh
$ gh default-branch main
```

But I learned there's a problematic catch updating this on GitHub. Any open pull requests will not automatically be updated.

I recently learned through [another tweet](https://twitter.com/mislav/status/1271462959458463752) from a GitHub employee that you can reset the base branch your open prs to this newly set default branch as well.

{% tweet 1271462959458463752 %}


```sh
for num in `gh pr list -B master -L999 | cut -f1`; do
  gh api \
    -XPATCH \
    "repos/:owner/:repo/pulls/${num}" \
    -f base="$newbranch" >/dev/null
  echo -n .
done
```

## takeaway

Even if you are on the market for changing your git branches, its useful to try to learn what dependencies you don't know you have on your branches yet.

It's also worth exploring the GitHub CLI for other use cases. You can technically do anything with GitHub from the command line now.

[^1]: https://www.hanselman.com/blog/EasilyRenameYourGitDefaultBranchFromMasterToMain.aspx
[^2]: https://dev.to/horus_kol/renaming-your-master-branch-2a37
[^3]: https://dev.to/mrsaeeddev/how-to-rename-your-master-branch-to-something-else-58bj