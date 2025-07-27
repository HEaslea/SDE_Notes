![[Pasted image 20250718122912.png]]![[Pasted image 20250718122920.png]]
Here is the current state of all the repos. 

### Introducing Merge Conflicts
When we work on different files to each other, we don't need to worry about merge conflicts, we are not changing the same files, and therefore, there is no conflicting differences. 
Git obviously can't automatically just merge the two together and there has to be some human input. 

Conflicts can occur, whether we merge or rebase. 

It is fairly normal for merge conflicts to arise, and if they do, it doesn't mean that there was a large error somewhere. This is just a regular part of the project. 

Let's say that we have a book project that has 10 text files, one for each chapter of the book. 
They are named accordingly. 

The primary line of development is going to be `main` branch : there is an agreement that we are to branch and then merge with `main`. 

There is another rule that we adhere to here, that we do not work on the same chapter at the same time, and this way we can often avoid merge conflicts. 

However, imagine that there is an error, and we both work on chapter 3 at the same time. 

We both make branches of, I make `chapter_three`, and the co-author `chapter_three_coauthor`. 

Then we both edit `chapter_three.txt` at the same time. 

Say that the co-author, merges their `chapter_three_coauthor` into `main` first. 
When I go to merge my own `chapter_three` in, there will be a conflict, this is pretty obvious. 
This will still be a three-way merge. 

Now imagine that we do the same for chapter 10. 

We make two branches off `main` again. Mine is `chapter_ten` and then also `chapter_ten_coauthor`. 
Here's an interesting one, in mine, I completely decide to delete `chatper_ten.txt`. However, the co-author is working hard on that chapter, to make it much better.

For the co-author, there is a merging into the `main` branch. 

Then the merge conflict here will just be about the keeping the file

### Resolving Merge Conflicts
When this happens, we will see **conflict markers**. 
`<<<<<<<` or `=======` or `>>>>>>>` all to reference the files in the merge. 

![[Pasted image 20250718131353.png]]
### Setting Up a Merge Conflict
Here, the basics of what the book is asking you to do, is in `rainbow` set up and add a `indigo` commit to `rainbowcolors.txt`
Then we `git push` that to remote repo. 

![[Pasted image 20250718131657.png]]

now in order to get into a situation with a merge conflict, we need to first make a commit without pulling the changes you made from `rainbow-remote` repo, into their local `main` branch. 

Therefore, the book i asking to make a a new commit on the `friend` local, that is going to be the `violet` commit. 

![[Pasted image 20250718132041.png]]

This is pretty clear. 

Now that there is a difference, the friend will not be able to just push to remote here. 

They will have to `fetch` and then use `git status` to see whether the changes diverge from each other. 

![[Pasted image 20250718132359.png]]

It's in plain English, that you can see that they have diverged. 

It's going to be very clear as well, that there is a `violet` commit, and we will have `fetch`'d the `indigo` commit from the other file. 

![[Pasted image 20250718132632.png]]
The friend, now needs to do a three-way merge in order to integrate that we have just `fetch`'d. We have to remember that we `fetch` first, just means that we have more time to know what to do with the files that we have. 

### The Resolution Process

#### Step 1
If, here, we were just to `git merge` - we will see git give us a bunch of conflict markers. 
Here we only have one merge conflict marker, however, there may be many files that have merge conflicts. 

Say that we want to keep both the sentence about the indigo colour and the sentence about the violet colour. 

We might want to make sure that some sentence is before another. 
Here, we want the indigo sentence to go first. 

![[Pasted image 20250718133701.png]]

We've done some rearranging here. 
![[Pasted image 20250718133801.png]]

#### Step 2
Now that we have finished all the conflict markers and fixed up the file in the way that we want it to be. 
We then add all the updated files to the staging area, and make a commit. 
Here there is only one file to add. 
However, there is the possibility that we have to add a bunch of files to the staging area. 

It's pretty obvious that we are going to need a three-way merge here. 

![[Pasted image 20250718134029.png]]

#### Aborting a Merge
`git merge --abort`

### Resolving Merge Conflicts in Practice
![[Pasted image 20250718134245.png]]

![[Pasted image 20250718134257.png]]It's clear that we can just use a `git merge --abort`

I think this will be incredibly clear when we actually come across this. 

![[Pasted image 20250718134635.png]]There's a cut off bit at the end. 

![[Pasted image 20250718134712.png]]

It should be really clear that the `friend` repo is ahead here. 

### Staying Up to Date with a Remote Repository
The general idea is that whenever we create a new branch, we use the most up-to-date commit. 

When working with someone on a branch, we must always make sure that we are fetching and merging, and updating etc.

Quickly, the workflow here is: 
`git checkout feature-x` make sure that we are on the right branch.

Then you can either do `pull`, which is going to do both a `fetch` and a `merge`: remember `git pull = git fetch + git merge (or rebase)`

then we might do `git fetch origin # fetch the latest changes`

```
git checkout main
git merge feature # remember that we have to be on the target, and we argue for the source
```

```
git checkout feature-x 

git fetch origin

git log HEAD..origin/feature-x --online # check whatever has changed

git merge origin/feature-x # this is what has been updated with fetch

# remember that origin here is the shortname for the remote repo

git rebase origin/feature-x # or rebase if that is preferred, that's the next chapter i believe
```

### Syncing the Repos 
We need to push the new commits on their local `main` branch, in the `rainbow` repo. 

Remember that setting an upstream branch, so that we can just use `git push`. 

Creating a new local branch: 
`git checkout -b feature-x`

If we try to `git push` without an `upstream branch`. 
Then we will see the error of: 
`fatal: The current branch feature-x has no upstream branch`

Using the upstream explicitly: 
`git push -u origin feature-x` this will set the upstream as well. 

Without it, and not setting the upstream up, then we use: `git push origin feature-x`

Then we can set the upstream later on: 
`git branch --set-upstream-to=origin/feature-x`

It's very simple to set up upstream : `git push -u origin feature-x` `-u` this will set it up. 

After doing some `git push` and `git pull`, all the three repos are synced up. 
![[Pasted image 20250725002542.png]]

Notice that `origin/main` - that is just the tracking branch, that is updated by `git fetch`, then we have to `merge/rebase`. 

