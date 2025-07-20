![[Pasted image 20250718013543.png]]

### Why
Remember that merges take the source, and we are in the target. We argue for the source, to merge into the current branch that we are on. 

So far, we have, in the book at least, looked at fast-forward merges. 

Here, we can have **merge conflicts** arise, where we merge two files that are being merged, where there are different changes in the same place of the file. 

Or if there is a file deleted in one branch and not the other etc. 

![[Pasted image 20250718014005.png]]
The `coauthor-book` finishes first, so they push through `main` to the remote repo. 

![[Pasted image 20250718014053.png]]
Then our repo, we finish on `chapter_five`, this will be shown as D. 
We also want to merge out work into `main`. 

The coauthor let's us know that they have already added their work in. 
So we need to first update our local. 
![[Pasted image 20250718014301.png]]
Here we have `fetch`'d the updates from the remote repo. 
You can see that they have branched accordingly, as there really is no other way of doing it. 
One option is to merge D into `main`, which will be a three way merge. 

The other option is to carry out a merge in the remote through a hosting service feature called a `pull request`. 

This will be learned about in Chapter 12. 

Let's say that we go about the first option, that we just merge in the local. 

![[Pasted image 20250718015013.png]]#

You can clearly see the three way merge, made the new commit of `M`. 

Some people don't like merging, as they feel that the commits get a little bit messy. 

![[Pasted image 20250718015613.png]]

Great we've just made the brown commit. 

![[Pasted image 20250718015720.png]]

Before we push the commit to the remote repo, have a quick look at defining upstream branches. 

### Defining Upstream Branches
Remember that git needs to know which remote branch, git needs a way to know which remote branch you want to push the work to.
Remember that if we don't have an upstream branch, we will need to argue the remote branch that we are pushing to in the `git push`. 
However, if we do have an upstream branch, defined, we just use `git push` with no arguments. 

`git push origin main`> 

These are sometimes set up when you clone a repo, but not usually when the local repo is initialized. 

The `rainbow` repo was initialized locally, therefore we have no defined upstream branches yet. 

Imagine that we want to define an upstream branch for the `main` branch so that we can just use `git push`. 

`-u` is short for `--set-upstream-to`. 

`git branch -u <shortname>/<branch_name>`

Remember that `-vv` is `very verbose`. 

We can use `git branch -vv` in order to see all the branches. 



![[Pasted image 20250718020703.png]]

Here we see that we are ahead of the remote repo main branch by 1, **this is not saying that the remote repo is ahead by one**. (I believe, if that were the case, we would be one behind). 

Then we are able to just `git push`. 

![[Pasted image 20250718020811.png]]
![[Pasted image 20250718021238.png]]
If the friend just continues to work on their `main`, without fetching, then it's clear the `main`s will diverge. 

#### Editing the Same File Many Times Between Commits
If we add a file to the staging rea, then we make another change to the file, Git will interpret this as a new version fo the file. 
You will have to add to the stage again in order to get it to commit. 

![[Pasted image 20250718022345.png]]


#### Working at the Same Time as Others on Different Files

![[Pasted image 20250718022529.png]]
You can see how the friend repo is out of sync. 

If the friend wants to push to remote, they will get an error.
Say that they want to push a blue commit. 

![[Pasted image 20250718022641.png]]

The error message, quite literally tells you what to do. 
We have to update our local before we can push. 

![[Pasted image 20250718022745.png]]

Here we can see that the friend has a commit, however, there is no `Br` commit here. 

In order for our friend to integrate, they will be using a three way merge. 

### Three-Way Merge in Practice. 
This is just a two step process. 

1. Fetch the changes from the remote repo. 
2. Second, you integrate the changes into the local branch in the local repo. 

So fetch, then merge. 

Here, when we fetch, we have to make sure that we understand that the two paths will diverge. 

Somehow, and I don't know how, Vim comes into this. 

In order to create the merge commit, in order to do that, git will enter a text editor in the command ine. 

##### Vim, The Command Line Text Editor
Git will use Vim to write commit messages. 

Git does allow you to change the default command line. 

The book then goes into some Vim here, see the other book, Oreilly, physical book that we have in order to understand the basics. 

### Executing the Three-way Merge
Now, our friend, being able to read, is going to fetch from the remote `main` branch. 
`fetch` remember, does not change anything locally, it just updates the `origin/main` remote tracking branch with all the differences. 
In order to update the local branch, we need to merge the `origin/main` remote-tracking branch into their local `main` branch

![[Pasted image 20250718023604.png]]
Note that with `git merge origin/main` - we are on the target, and we are arguing the source, where we get the changes from, which in this case, is the updated remote tracking branch that was updated with `git fetch`.

![[Pasted image 20250718023859.png]]
Note that the merge commit has gone one ahead here, which it must in order get to the two together. 

![[Pasted image 20250718024016.png]]

The `M1`, has two parent commits. 

![[Pasted image 20250718024203.png]]

![[Pasted image 20250718024219.png]]
Then, it's clear that we need to sync the `rainbow` repo now. 
This means to that we need to learn how to `pull` - get your coat. 

### Pulling Changes from a Remote Repo
The idea that we have used so far is that we use `git fetch` in order to update the remote tracking branches, and then we are to merge, using those remote tracking again.

Pulling, will do this all in one go. 

`git pull`, if we don't have an upstream branch defined, then we have to specify the shortname or the remote repo and the branch name that we want to update. 

`git pull <shortname> <branch_name>`

Remember, in Git there are two ways to integrate changes: **merging and rebasing**. 
`git pull` can use either, and depends on whether the development histories of the branches have diverged, and, if so, on the option you choose when entering the command: 
- If the development histories of the local branch, and remote, in a `git pull` have **not** diverged, then a fast-forward merge will occur. 
- If they **have** diverged, then we will be asked, whether we want to integrate using merging or rebasing (otherwise we get an error apparently). 
In order to merge, we say `--no-rebase`, or in order to rebase, we have to pass in `--rebase`, seems obvious enough. 

In essence, `git pull` will do `git fetch` and either `merge` or `rebase`. 

![[Pasted image 20250718025418.png]]

Common to use `git pull` when we know that we are going to have a fast forward merge. 
Usually, we use a `git fetch` and chose on the next step, if we don't know about the fast forward merge. 
This just gives them more time in order to setup and chose what they want to do. 

![[Pasted image 20250718025606.png]]


![[Pasted image 20250718025703.png]]

We can see that we have a fast-forward merge. 

![[Pasted image 20250718025751.png]]

We can see that they are all the same now. 

[[Merge Conflicts]]