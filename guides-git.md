---
layout: page
title: Git in 5 seconds
permalink: /guides/git/
---

### Shortcut command installation
```
gem install grb
gem install gpr
gem install gap
```

### Checking out a repo
```
git clone <repo>
cd <repo>
```

### Updating master
```
git fetch origin master
```

### Creating a new branch
```
grb new <user>/<project>/<feature/ticket/whatever you want>
```

_<do your thing>_

```
git add <files>
```

OR

```
gap
```

OR

```
git add --patch
```

```
git commit -m <message>
git push
```

```
git scmp
```

OR

```
gpr
```

_<follow instructions (add some reviewers) to create a pull request to master>_

### On commits and amending

In general, smaller commits (and PRs) are better. Descriptive commit messages are great; if you’re still working on something, it’s fine to go back and change the commit messages later.

```
git commit --amend
```

### Rebasing from master on your branch
(do this before merging on stale PRs!)

```
git rebase -i origin master
git push -f
```

### Rebasing to squash/amend/delete commits

You can squash, reorder, split, or even change the message of some commits. This is great if you had a bunch of nit commits that could all be squashed, or if you want to split a large commit into several smaller ones. The instructions are mostly straightforward...

```
git rebase -i HEAD~<number of commits>
```

### Changing branches
```
git checkout <branch>
```

### Holding onto temp changes without explicit commits
```
git stash
```

Note that this will stash any file changes, including ones that are unstaged. However it will not stash any untracked files.

### Branch history
```
git log
```

### Copying a commit from elsewhere
This is known as cherry-picking.

```
git cherry-pick <commit>
```