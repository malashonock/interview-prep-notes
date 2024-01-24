---
tags: [Git]
title: 'Git: branches'
created: '2024-01-24T08:17:14.786Z'
modified: '2024-01-24T08:37:29.591Z'
---

# Git: branches


## Underlying data structures

All commits, local and remotes, are stored in `.git/objects` directory.  

A branch is just a reference (pointer) to a commit.

**Local** branches are stored in `.git/refs/heads` directory.  
To output the list of local branches, use `git branch`.

**Remote** branches are stored in `.git/refs/remotes` directory.  
To output the list of remote branches, use `git branch --remotes` (or `git branch -r`).


## Fetching branches

Fetching a remote branch = **downloading branches** (pointers) from a remote repo to the local `.git/refs/remotes` directory, as well as all underlying remote **commits** (to `.git/objects`).

`git fetch <remote>` fetches **all** branches from the specified remote.

`git fetch <remote> <branch>` fetches the specified branch of the specified remote.


## Merging branches

Given 2 commit pointers (usually, branch tips), merges new commits of one branch (merged branch) to the other branch (receiving branch).

**NB!** One must `git checkout` to the receiving branch before merging!

**NB!** Don't forget to `git fetch` latest commits from the remote before merging.

`git merge <merged-branch>` will apply latest commits from the merged branch to the receiving branch.

2 types of merging can occur:

- **Fast-forward merge** occurs when the receiving branch has no new commits since the branch being merged was created, i.e. both branches haven't diverged. In that case, git will simply move (_fast forward_) the pointer of the receiving branch to the tip of the merged branch, without creating a new merge commit.

- **3-way merge** occurs when the receiving branch has own new commits since the branch being merged was created, i.e. both branches have diverged. In that case, git will _try_ creating a new merge commit, if there are no merge conflicts. If there are any merge conflicts, they need to be resolved in an IDE before merge is committed.



