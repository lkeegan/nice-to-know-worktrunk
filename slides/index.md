---
marp: true
theme: ssc
paginate: true
title: "Nice to Know: Worktrunk"
description: Nice to Know
---

<!-- _class: title -->
<!-- _paginate: false -->
<!-- _footer: "Last updated: 2026-11-17" -->

# Nice to Know: Worktrunk

## Liam Keegan, SSC

---

# Git branches

When you are working on code as a person, Git branches are great.

You typically only work on one branch at a time, and changing branches is easy and convenient.

But when using agents, branches don't work so well - instead you really want to give each agent its own independent copy of the code.

The naive way to do this is to clone the repo in a new directory for each branch/PR. This works but it's not very convenient.

---

# Git worktrees

A better option is a Git worktree, which is a checked out branch in another working directory, e.g. starting in `repo`:

```console
git worktree add -b new-feature ../repo.new-feature
cd ../repo.new-feature
```

And once the `new-feature` branch gets merged you can remove the worktree and delete the local branch:

```console
cd ../repo
git pull
git worktree remove ../repo.new-feature
git branch -d new-feature
```
---

# Worktrunk

There are also many new tools created in the last year to make working with worktrees and agents easier.

They are mostly some kind of wrapper around Git worktree commands + some kind of agent integration or skill.

The one I've been using recently (and quite like so far) is [Worktrunk](https://worktrunk.dev/).

---

# Installing Worktrunk

If you have Cargo (Rust’s package manager):

```console
cargo install worktrunk
```

Alternatively you can use brew/conda:

```console
brew install worktrunk
```

```console
conda install -c conda-forge worktrunk
```

After installing, enable shell integration with:

```console
wt config shell install
```

Then restart your shell.

---

# Creating a new branch

Starting in our repo, we want to create a new feature branch:

```console
wt switch -c new-feature
```

This creates a worktree at `../repo.new-feature` and changes into that directory.

Once we are done and the feature is merged we can do:

```console
wt remove
```

This removes the worktree/directory, deletes the branch, and changes back into the original repo directory.

---

# Existing branch / PR

To create a worktree for an existing branch:

```console
wt switch branch-name
```

This creates a worktree of this branch in `../repo.branch-name` if not already there, then changes into that directory.

You can also just do `wt switch` then interactively choose an existing worktree.

To include all open PRs in the interactive chooser do `wt switch --prs` (requires the `gh` CLI).

---

# List worktrees

To see all worktrees for the current repo:

```console
wt list
```

Or to include more information including CI status:

```console
wt list --full
```

---

# Advanced options

I don't yet use any of these, but there are plenty of hooks / customisation points / config options.

And they provide plugins/skills for Claude/Codex/pi/etc.

The `-x` argument runs a command in the created worktree, so for example an alias for "create worktree of new branch, launch Claude there with prompt" could be

```console
alias wsc='wt switch --create -x claude'
```

Which can then be used as:

```console
wsc fix_322 -- 'Fix GH #322'
```
