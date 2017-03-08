---
layout: post
title: Git Cheatsheet
permalink: /git-cheatsheat
published: true
---

## Setup local branches for all remote branches
```bash
git branch -r | grep -v '\->' | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
git fetch --all
git pull --all
```

## Alias

## Ignore (stored in .gitconfig in your home directory)
* Remove errors when your local branches are behind their remote
```terminal
[push]
    default = simple
```
* Use Aliases to shorten commands
```terminal
[alias]
	st = status
	ci = commit -a
	cu = commit -u
	co = checkout
	br = branch
	last = log -1 HEAD
	pu = pull --rebase
```

