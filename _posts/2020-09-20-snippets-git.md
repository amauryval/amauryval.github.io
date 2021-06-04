---
title: "Snippets - Git"
last_modified_at: 2020-09-20T12:37:45
categories:
  - Snippets
tags:
  - Git
toc: true
toc_sticky: true
toc_label: "Dans cette page..."
---

Some Git snippets

# Generals

## Clone a repository with credentials
```
git clone https://myusername:mypassword@github.com/path_to/my_repo.git
```

## Configure git user
```
git config --global user.email "john@doe.com" 
git config --global user.name "John"
```

NB:

* remove --global to apply this only on one project 
* git configuration infos are stored in:
    * ~/.gitconfig: global conf 
    * .git/config : local conf

## Store your credentials
```
git config --global credential.helper store
```
You'll be promtped for your credentials on your next commit. Then they will be stored


## Remote

### View existing remotes

```
git remote -v
```

### Change the origin remote's URL
```
git remote set-url origin https://github.com/user/new_repo.git
```

# Tags

## Add a tag
```
git tag 0.2.0 
```


## Pushing tag(s)

### All the tags
```
git push --tags
```

### Only 1 tag
```
git push origin 0.2.1
```


## Delete a tag
On local.
```
git tag --delete 0.2.2
```

On the remote.
```
git push --delete origin 0.2.2
```

# Commits informations

## Current commit 

The fastest way, but shows you the diff.
```
git show
```

More simplier.
```
git log -1
is fast and simple.
```

To get just the commit message.
```
git log -1 --pretty=%B
```

## Commit tree 

```
git log --graph --oneline --all
```

# Changes

## Reset

### Uncommit + unstage changes and left in working copy

```
git reset 
```

or 

```
git reset --mixed
```

To unstage a file
```
git reset nom_du_fichier.py
```


### Uncommit + changes left staged

```
git reset --soft
```

### Uncommit + unstage + delete changed files

```
git reset --hard
```

### Change commit message

Update the commit message of the last commit
```
git commit --amend -m "new commit message" 
```

### Add a forgotten file on the last commit

```
git add my_file.py 
git commit --amend --no-edit
```


## Stash

### Stashing

In order to saved stages and unstages files on working copy.
Useful, if you have to switch to an other branch, to not loose your current changes
```
git stash
```

or
```
git stash save "my stash name"
```

### Unstashing

To get your stash files
```
git stash pop
```

or
```
git stash pop "my stash name"
```


# Commits

## Delete the last commit
Keep files changed
```
git reset --soft HEAD~1
```

Delete files changed
```
git reset --hard HEAD~1
```

# submodules

## init submodule after a clone
```
git submodule update --init
```

## add
```
git submodule add git://github.com/url.git output/path
```

## refresh submodules
```
git submodule foreach git pull origin master
```

```
git submodule sync
git submodule update --init --recursive --remote
```
