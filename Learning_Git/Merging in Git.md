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
