In order to integrate, we have `merge` and `rebase`. 

### State of the Local and Remote Repositories 
See [[Merge Conflicts]]

### Integrating Changes in Git
Fast-forward just moves the branch pointers to the latest commit. 

Some people prefer to maintain a linear history, for clear reasons. 

We can use rebasing to avoid three-way merges and merge commits and maintain a linear project history. 

Rebasing takes all the work you have done in the commits on one branch and reapplies the work, on the other branch. 

![[Pasted image 20250725011156.png]]![[Pasted image 20250725011211.png]]


In order to do a rebase, we have to be on the branch that we want to rebase. 

`git rebase <branch_name>`

The idea is that we are creating entirely new commits, changing the commit history. 

There is the idea of "Golden Rule of Rebasing" as well. 

### Why is it Helpful
Say that we have another book example. 
![[Pasted image 20250725011812.png]]
Then commits are made, `book` makes a `chapter_five` commit, `D`, and `coauthor-book` makes a `chapter_six` commit `C`. 

![[Pasted image 20250725012115.png]]
When it comes to `book` to merge, one option is to `fetch` the changes, then do a three way merge. 


![[Pasted image 20250725012315.png]]

Then we get a merge commit here, however, we see that `C` and `D` are side by side, and is that really what we want? 
It doesn't really matter, but it's not as clean as some people like.
It would be great if we had 5 and then 6. 

Or we could rebase, by pulling the changes from the remote repo. 

![[Pasted image 20250725012449.png]]

And then we can rebase `chapter_five` onto `main` which will be `chapter_six` commit here. 

This will take all the commits made on `chapter_five` and reapply them onto the `main` branch. 

Creating new commits in the process, therefore, we will get rid of  the `D` branch and get a `D'` commit, along the same line instead. 

![[Pasted image 20250725012810.png]]

it will have an entirely new commit hash as well. 

It will include all the changes made in the original D commit. 

And then we just need to merge, which will of course be a fast-forward merge. 

![[Pasted image 20250725012932.png]]

We maintain a linear commit history, and avoid making additional merge commits. 

Again we have to diverge a branch away in order to set this up. 
So `rainbow` will commit using `grey` and then the `friend` will have to commit something as well, without updating, therefore, the commits diverge. 

### Unstaging and Staging Files
Suppose you add files to the staging area, then you realise that you want to separate out the commits, therefore we have to unstage a file. 

Suppose that you have worked on chapters 1, 2, 3 for a book. If we stage all three files, then they will all be present in the next commit. 
Say that we only want the changes to chapter 2 to be reviewed, therefore we need to get rid of some files: 
The general idea is that we have to use `restore`: using either `reset` or `rm` can see some unintended consequences. 

`git restore --staged file-to-unstage.txt`. 

`git restore --staged <filename>`

Moving on: 
The `friend-rainbow` then makes a `black` commit: 
`git commit -m "black"`

Then we can use `git status` and then `git log` to see all about that. 


![[Pasted image 20250725015021.png]]

Then they add another commit. 
`git add rainbowcolors.txt` and then `git commit -m "rainbow"`

![[Pasted image 20250725015209.png]]

### Preparing the Rebase

Now the `friend-rainbow` is going to fetch the updates to the remote. 

```
git fetch

git log --all
```

![[Pasted image 20250725015602.png]]

Pretty clear. 

### The Five Stages of the Rebase Process
Visualizing this is very helpful too. 

The key here is that using the `git rebase` will automate these process. 

##### Stage 1: Finding the Common Ancestor
Here this is obviously M2. 

![[Pasted image 20250725020213.png]]


##### Stage 2 : Store info about the Branches Involved in the Rebase
Saving the changes of each commit into a temporary area. 

![[Pasted image 20250725020703.png]]

##### Reset Head
Git will have to reset `HEAD` to point to the same commit as the branch you are rebasing onto. 
![[Pasted image 20250725020942.png]]

We are rebasing **onto** `Gr`. 

##### Stage 4: Apply and Commit the Changes
Then, one by one, git will apply the changes to the new `HEAD`. 

Here, first it will apply `black` and then it will apply `rainbow` onto `HEAD`. 

![[Pasted image 20250725021300.png]]
##### Stage 5: Switch onto the Rebased Branch
![[Pasted image 20250725021421.png]]
See how `main` has moved, and then we are checking it out with `HEAD` on it. 

Remember that these will be initiated automatically by just using the `git rebase` command. 

### Rebasing and Merge Conflicts
Again, if we find that there are any of the same areas that have different data, then we need to remedy that. 

There will be a slight difference than that of a three-way merge. 

In three way merges, the conflicts are presented at the same time, we resolve them all, add all the updated files to the staging area and them the final merge commit is done. 

In a rebase, as the commits are being added one by one, it will pause the process if it encounters merge conflicts in any reapplied commit. 
So they will appear one by one. 

Then we will need to add them again, and instruct Git to resume by using `git rebase --continue`. 
Of course then, git will continue doing what it's doing. 

At any point during the conflict resolution, we can do `git rebase --abort` like with `git merge --abort`. 

```
git rebase --continue

git rebase --abort
```

Remember that when we do this, we are on the branch that we want to move. 

Same with the `merge` we give the source, then we fast forward the branch that we are currently on. 
So the one that we are on, that is the one that we are "moving". 

Say with merge that we are on `main` and we want to merge with `feature-x`, then we do `git checkout main`, then `git merge feature-x`. 

In the follow along here, `git rebase origin/main`, I believe we are on `main`, will "move" `main` with the five stages above, we don't even need to find the common ancestor, git does that for us. 

### The NUKE
The key is that we have to remember that old commits are replaced with new one. 
When we do `git rebase main` - the old commits will be gone, therefore, if something is branched of that, then we're in trouble. 

My trouble was forcing through a push afterwards too. 

```
        A---B---C (feature-x, you and your teammate)
      /
D---E---F (main)
```

Now we are going to rebase onto main: 
```
git checkout feature-x
git rebase main
```

```
                A'--B'--C'  (feature-x, rewritten history)
               /
D---E---F (main)
```
You can see the new commits. The old commits are now "orphaned". 

`git push --force`

Now of course they won't match, their local branch no longer matches remote, they may have to do something funky in order to get it right. 

If we do `git push --force-with-lease` this will only push if remote hasn't changed. If our partner has pushed before us doing this, then it won't push  as we need to update. 

![[Pasted image 20250725125812.png]]

Here we see that `black` had an issue, a merge conflict. 
Again, Git is very good at telling us exactly what we need to do. 
Here we need to `Resolve all conflicts manually, mark them as resolved with git add/rm <conflict_files>, and then run "git rebase --continue"` . 

Then `git status` gave us more information about that. 

We need to go through any merge conflicts. 
There will be an `interactive rebase` in progress, those are the words that you will see. 

![[Pasted image 20250725130729.png]]
See the `interactive rebase in progress`. 

We add the file that we need to, and then do a `git rebase --continue`. 

![[Pasted image 20250725131050.png]]
![[Pasted image 20250725131106.png]]

Now we see the complete shift. 

![[Pasted image 20250725131146.png]]

The new commits (entirely).

### The Golden Rule of Rebasing
Remember, always, that rebasing will change the commit history, and that can be a dangerous thing. 

Whenever change the commit history, we have to be very careful. 

The golden rule is such: 
> We should never rebase a branch that other people may have based work on. 

I'm sure that this will become more obvious, when and where to use `rebase`. 

Here's another look. 

![[Pasted image 20250725132720.png]]

Now imagine that `chapter_five` has been pushed to the remote branch. 