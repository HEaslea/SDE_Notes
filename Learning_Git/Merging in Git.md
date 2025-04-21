![[Pasted image 20250415231207.png]]
State of the local repo. 

Now that we are done with this `feature` branch - how do we get to combine it with `main`. 

Whenever we are merging - there is the **source branch** and the once branch that we are merging <u>into</u>, the **target branch**. 
**Source** - containing all the changes that will be integrated into the **target**. 
**Target** - not changed, but will be when it's merged into/receiving by the **source**. 

Say that we have a Book project. 
Every time that I start work on a new chapter, I will make a new branch off the `main` branch. 
Then we merge into `main` after that work has been reviewed. 
Say that I work on chapter 4 : make a new branch called `chapter_four` off the `main`. Then we merge into `main` in my local repository. 

#### Types of Merges
Two types of merges: 
- Fast-forward merges
- Three-way merges
The factor determining which of these is whether the development of the two branches have diverged. 
This is seen by following the parent links of commits. 

A **Fast-forward merge** is one that the development paths have not diverged - like in the picture above - you just move `target` to the `source` (fast forward it to the "new" version). 
Git takes the pointer of the target branch and moves it to the commit of the source branch. 

![[Pasted image 20250415232326.png]]

It's clear that the paths are not diverging here -> fast-forward - moving along the commit "line". 

![[Pasted image 20250415232400.png]]![[Pasted image 20250415232440.png]]
This last one, it's clear that the commits have clearly diverged, there is no way to just go through the parent pointers from `chapter_eight` to get to `main`. 

Here the merge gets a little wacky, it can't be fast-forwarded, instead there has to be a **merge commit**. 
Which will be created in order to tie the two together. 
Can't just merge them into one of the commits already there - there has to be a new commit. 

Here with a three-way merge - the idea is that the source doesn't move, however, the target branch will move to the new `merge commit` that was created. 
This new `merge commit` will have two parents. And notice that `chapter_eight` hasn't moved (it's the source). Remember here that we are merging `chapter_eight` **into** `main`, so `chapter_eight` is the **source** and `main` is the **target**. 
![[Pasted image 20250415232952.png]]
The **three-way merge**, commit M looks back to both commit J and commit L, it will look for the common ancestor of the two - so in this case G. 

This one is more complex and there will sometimes be **merge conflicts** - this is when there are different changes made to the same parts of the same file(s), or if in one branch a file was deleted that was edited in another branch. 
These will have to be resolved before merging. 

I think the main thing to realise is that the **source** has all the changes that will be integrated into the **target**. 

#### Doing a Fast-Forward Merge
Again - just to reiterate - the **source** has all the changes that will be merged into the **target**: 

Let's look at merging `feature` branch into `main`. 

These fast forward merges are very straight forward. 

The idea is this: 
**We switch onto the branch that we want to merge into. As in we move to the branch that is the target branch (where source will merge into, adding all the changes)**. 

Then we use the `git merge <branch_name>` where `<branch_name>` is the source, we are merging into the branch that we are on. 

**We are merging into the branch that we are currently pointing to (currently on), from the branch name that we provide.**

Git will do its absolute best not to lose work that we have not committed, and changing branches may cause the contents of the working directory to change. 

**Changing branches may change the contents of the work directory to change**. 

#### Switching Onto the Branch you are Merging Into
Remember what happens when we switch onto the target branch (or switch onto a branch in general). 

First we are going to merge **feature into main**, therefore we need to be on `main`. 
`git switch` or `git checkout` : 
1. Changes the `HEAD` pointer to point to the branch that we are switching onto
2. Populates the staging area with all the files and dirs that are part of the commit you are switching onto. 
3. It copies the contents of the staging area into the working directory

Largely meaning : that if the branches point to different commits (remember that branches are just pointers to a commit), changing branches also changes the contents of your working directory. 

##### Git Protects You From Losing Uncommitted Changes
Changing branches changes the contents of your working directory. 
So what if there are modified files in the working directory (files that we have edited), that are not yet committed?  
If git notices this, that you would lose some work if you switched, it will give us an error message. 
This happens only if the files that contain uncommitted changes have conflicting changes in the branch you are switching onto. 

**An Example**: 
Say we are working on a book that has two different approaches for chapter 5, in the `book` repo. 
Working on `chapter_five.txt`. 
There are two branches, `chapter_five_approach_a` and `chapter_five_approach_b`. 
Say that we go on `a` make a couple of commits. 
Then onto `b`, editing the same file extensively - however - there are no commits made. 
(Remember, however, that commits should be made often). 

Now, I'm on `b`, however, I want to go back to `a`. This is an issue, as that change on `b` - that is in the working directory (looking at the deltas and the changes in that commit) - would be discard - as when switching branches - the working directory changes. 
This would change the `chapter_five.txt` of `b` with that of `a` without saving any of those changes. 
Git Won't Let This Happen. 

It will send an error message. 


Let's do something like this: 
```
git status
// nothing on branch feature
// add in rainbowcolours.txt -> "Green is the fourth colour of the rainbow", then save

git status // will show the changes we just made, that they are modified and not staged

git switch main // error local changes to the following files woul be overwritten
// ie rainbowcolours.txt in main will overwrite feature's in working dir
```

![[Pasted image 20250418203327.png]]

We would lose all the work we did on `feature` if we were to just swap. 

Git will not prevent you from switching branches if you make changes to files without saving them in the text editor, because those files will be considered unmodified files. So, always remember to save files in your text editor when you’re done working on them!



#### Working Directory - Quick Recap
AKA - **Working Tree**
Directory on your filesystem where your project files live - the actual files you see and edit. 
Everything outside the `.git` folder. 


#### Switching Branches Changes Files in the Working Dir
![[Pasted image 20250418204205.png]]
This is what we are looking at before we switch onto the `main` branch. We are on the `feature` branch here. 

NOTICE : 
- The version of the `rainbowcolours.txt` file in the working dir and staging area is the one that mentions the colours red, orange and yellow. Represented as version 3 (v3) of the file.
We haven't added green yet, `feature` is still on yellow right now. 

That's a commit, therefore we would be ready to switch over to the `main` branch. 

```
// it's wise to have the `rainbowcolours.txt` open here, so that we see what's happening when we execute the upcoming commands

git switch main // switching to main

// now look at the contents of rainbowcolours.txt

git log // we'll see HEAD -> main
```

When we swap branches : 
- The version of `rainbowcolours.txt` that was in the text editor before the switch onto `main`, mentioned the colours red, orange, yellow. 
- The version *after* the switch, will only contain red and orange. 
- Then when we `git log`, shows only the red and orange commits. NO LONGER showing the yellow commit.

![[Pasted image 20250418204940.png]]

Now we are on the main branch, `HEAD->main` that is clear. 

This is only pointing to the orange commit - therefore we are only going to see red and orange in the file. 
See that the working dir and the staging area have both changed. 

### How does Git track these changes TLDR
Git doesn't just store deltas, in fact, unlike over VCS (version - control - systems), Git actually takes a snapshot, however, it's super elegant in **hashing** and **compression**. 

Say that we have
```
main :     commit A
feature :  commit B (based on A ) // SomeCommit -> A(main) -> B(feature)
```
When we make a commit for `B` -> stores a new snapshot of your working dir -> not just the changes. 

Files that haven't changed between commits aren't duplicated. 

Git stores a **tree object** that points to the same **blob objects** (content hashes) for unchanged files. 

**Git Objects:**
1. **Blob** - Contents of a file (`file.cpp`)
2. **Tree** - Represents a dir, pointing to blobs and other trees. 
3. **Commit** - Points to a tree and a parent commit
4. **Tag** - Optional for tagging commits

```
Commit A
│
├── tree A
│   ├── file1 → blob X (hash of contents)
│   ├── file2 → blob Y
│
Commit B
│
├── tree B (maybe only file1 changed)
│   ├── file1 → blob Z (new content)
│   ├── file2 → blob Y (unchanged)
```

This is from my own work: 
![[Pasted image 20250418205930.png]]
![[Pasted image 20250418210129.png]]
This is what a tree looks like apparently. 


#### Viewing A List of All Commits
`git log` - reverse chronological order. 
Showing a list of commits that are reachable by following the parent links from the commit that we are on, when the command is executed. 

To see the list **for all branches in the local repo** - `git log --all`

![[Pasted image 20250418211804.png]]

That's what this will look like with - `git log --all`. 

### Using the Git Merge Command
The source is `feature`. This has all the changes that we could want. 
Making sure that we are on the target branch - `main`

```
git merge feature // will tell us the type of merge that this will be

// take note of the contents of `rainbowcolours.txt`

git log // HEAD->main, feature, main has been brought up to feature
```
![[Pasted image 20250418212617.png]]Remember that really - we are just moving the pointers, that's what commits are. 
The commits themselves are **immutable snapshots**. The branches therefore have to be the movable labels pointing to those commits. 

Commits really only point to their parent. 

```
A -- B -- C  ← main
       \
        D -- E  ← feature
```

Say we have this, then we merge feature into main (feature is the source and main is the target (t of target is t of to merge into)). 

Merging Between:
- The common ancestor (B) - the earliest commit that both can access. 
- The tip of the current branch (the one we are merging into (passing changes into)) (eg. C)
- The tip of the branch we're merging in (the one with the changes) (eg. E)

```
A -- B -- C -------- M  ← main
       \          /
        D -- E  ← feature

```

Looky here -> M is a new commit for this merge. 

Fast-forward merging doesn't need a new commit, we're just moving pointers. 

Now a merge commit is normal - but it does have two parent pointers. 

![[Pasted image 20250418214138.png]]

Merging does not delete. 

### Checking Out Commits
`git checkout` may be used to switch branches - and other things too. 
Also used to `checkout` commits. 

At the moment we are on `main` and we want to go further back to an older version of the project. 

Want to see what it was like on orange commit?

There is no branch on that commit - `git checkout` - passing the commit hash of that orange commit. 

`git checkout <commit_hash>`

This will carry out three actions that are similar to the ones earlier: 
1. Moving the `HEAD` pointer to point to the commit we gave. 
2. Populates the staging area with all the files and dirs that are part of the commit that we are switching to. 
3. It copies the contents of the staging area into the working dir. 

The main difference being that the `HEAD` pointer will point directly to the commit and not to a branch now. 
This is **Detached HEAD State**: Allowing us to look at any commit at any point. 

Try not to make changes to a repo while in this detached `HEAD` state. 
Again this will change what is in the working directory. 

