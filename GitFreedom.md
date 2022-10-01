# Git Freedom

## Project

Something a team of developers works on; its deliverable is a FileTree

## FileTree

A tree of directories and files.
Two file trees are identical if their contents are identical.

## Commit

A commit contains
- a FileTree
- zero or more parent commits (ordered)
- author, message, timestamp

Two commits are identical if their contents are identical.
Note that two non-identical commits may contain identical file trees.

Commit ID is a unique integer for a commit, 
actually just the SHA1 of the content of the commit.

Anyone can create any commit of any content;
whether it will be accepted by others is another story.

## Repository

A repo contains a collection of commits.

Team developers have their own local collections of commits;
one developer can have multiple local repos, even on the same computer.

## Sharing commits

A developer can publicize some commits somehow,
and others may take these commits into their repos.

With proper permissions, a developer may fetch commits from another repo,
or push commits to another repo.
In most cases, a local repo starts as a clone of another repo.

## References

A developer can give names to commits; 
these are references, including tags, branches.

Each repo contains local references, created by repo owner.

A repo can publicize its local references;
other repos can remember these as remote references,
and use them in communication with the repo that named them.

## WorkTree

While a developer works on a project, he is editing a file tree,
which is his work tree. 

Typically, the work tree starts as a copy of a FileTree from a commit.
That commit is the HEAD of the WorkTree.

## StageTree (aka Index)

When a developer wants to create a commit, he may not want to include
everything in his worktree. He picks some changes and stage them.

A stage tree is a file tree. The developer can compare between
work tree, stage tree, and HEAD commit's file tree.

TODO: add file to stage; modify the file again; does the stage have the new change?

## Create a commit

A developer creates a new commit with the StageTree, 
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

TODO

## Reset

TODO

## Merge

A commit can be created with two or more parents. 
It means that this commit is based on combined works of its parents.

What does it mean to combine multiple works is entirely semantical, 
and completely out of scope of Git.

However, git-merge tries to auto-merge file trees based on some algorithm;
if successful, auto-commit the result by default.
This is of course very dangerous.
We can use git-merge for auto-merge which is often helping.
But we should disable auto-commit;
commit only after we've examined/fixed/improved the auto-merge result, 

## Rebase

Say a branch has a linear history of commits `[B0 ... Bn]`.
Rebase this branch on another starting commit `C0` is an attempt to:
- invent a commit chain `[C1 ... Cn]`
- `Ci = merge(C0, Bi)` , but with a single parent C_i-1
- the branch is moved to reference `Cn` 

This is a lot harder than just one merge which is hard enough.
The benefit of rebase is that the commit graph is simpler.

Rebase may be possible to do manually by humans.
Rebase may be possible to do mechanically in simple situations.
If `n==1` rebase is not harder than merge; 

## Squash

Squash takes a chain of commits `[Bi ... Bj]`
and invents a chain of commits with only `[Bi, Bx]`
where `Bx` has the same file tree as `Bj`.

## Fork Repo

A developer may not have permission to push to another repo (call it 'central').
He needs to ask the owner of central to pull his commits (i.e. a pull request).

However, central cannot read from the developer's local repo.
For that reason, the developer creates a 'fork' repo somewhere as an intermediary.
Local can push to fork; central can pull from fork.

That's the sole purpose of the fork repo.

Usually local can pull from central directly.
This forms the so-called triangular workflow.

x x x x x x x x x x x x x x x x x x x x

x x x x x x x x x x x x x x x x x x x x 
