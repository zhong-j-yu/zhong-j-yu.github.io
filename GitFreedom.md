# Git Freedom

## Project

Something a team of people works on; its deliverable is a FileTree

## FileTree

A tree of directories and files.
Two file trees are identical if their contents are identical.

## Commit

A commit contains
- a FileTree
- zero or more parent commits (ordered)
- author, message, time

Two commits are identical if their contents are identical.
Note that two non-identical commits may contain identical file trees.

Commit ID is a unique integer for a commit, 
actually just the SHA1 of the content of the commit.

Anyone can create any commit of any content;
whether it will be accepted by others is another story.

## Repository

A repo contains a collection of commits.

Team members have their own local collections of commits;
one member can have multiple local repos, even on the same computer.

## Exchanging commits

A member can publicize some commits somehow,
and others may take these commits into their repos.

With proper permissions, a member may fetch commits from another repo,
or push commits to another repo.
In most cases, a local repo starts as a clone of another repo.

## References

A member can give names to commits; 
these are references, including tags, branches.

Each repo contains local references, created by repo owner.

A repo can publicize its local references;
other repos can remember these as remote references,
and use them in communication with the repo that named them.

## WorkTree

While a member works on a project, he is editing a file tree,
which is his work tree. 

Typically, the work tree starts as a copy of a FileTree from a commit.
That commit is the HEAD of the WorkTree.

## StageTree (aka Index)

When a member wants to create a commit, he may not want to include
everything in his worktree. He picks some changes and stage them.

A stage tree is a file tree. The member can compare between
work tree, stage tree, and HEAD commit's file tree.

## Create a commit

A member creates a new commit with the StageTree, 
with HEAD as the parent commit,
and with a message. 

This new commit is added to his local repo;
and becomes the new HEAD of the work tree.

## Tag

A tag is a reference to a specific commit; 
usually, a tag should not be changed to reference another commit.

## Branch

A branch is a reference to a specific commit at any given time;
however, it usually moves when the commit gains a child commit.

A work tree typically is associated with a local branch name. 
At the time of commit, that branch is change to reference the new commit.

## Summary of structures

## Reset

## Merge

A commit can be created with two or more parents. 
It means that this commit is based on combined works of its parents.

What does it mean to combine multiple works is entirely semantical, 
and completely out of scope of Git.

However, git-merge tries to auto-merge file trees based on some algorithm,
and by default auto-commit if that's successful. That is of course very dangerous.
We can use git-merge for auto-merge which is often helpful.
But we should disable auto-commit.
Commit only after we've examined/fixed/improved the auto-merge result, 

## Rebase

Say a branch has a linear history of commits `[b0, b1, ..., bn]`.
Rebase this branch on another starting commit `b'0` is an attempt to:
- invent a commit chain `[b'0, b'1, ..., b'n]`
- `b'i == merge(b'0, bi)` , for the file trees

This is a lot harder than one merge which is hard enough.
It may be possible to do manually by humans.
It may be possible to do mechanically in simple situations.



xxxxxxxxxxxx