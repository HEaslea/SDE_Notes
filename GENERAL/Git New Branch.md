## Making a new Branch
`git checkout -b openGl-implementation`

`checkout` - switches branches

this will move us there as well

## View Branches
`git branch`

## Switching Between Branches 
```
git checkout main
git checkout openGl-implementation
```
## Making Changes
Now we are staging for that other branch
```
git add . 
git commit -m "Implementing OpenGL windows feature"
```
This will only commit to that branch

## Merging Changes to `main`

`git checkout main` - Go to main

`git pull origin main` - pull the latest changes from remote`main` branch. 

Then `merge` will mean - Grab from that branch and put it into this one
`git merge openGl-implementation` Then we might need to resolve any deltas manually. 

Then we push to the remote one
`git push origin main`

## Handling Merge Conflicts
If added new things to `main` while working on `openGl-implementation`. 
Git will indicate the conflicts, needing to edit those files manually to decide what to keep. 
Then you need to commit again 
`git commit -m "Resolved merge conflicts"`

## Deleting the Branch
Once we have merged, there are no issues, we can delete
Locally:
`git branch -d openGl-implementation`

and Remote: 
`git push origin --delete openGl-implementation`

## Rebasing 
If the main branch has moved on, then we want to apply our `openGl-implementation` **on top of the latest** `main`, instead of merging, we can rebase: 
```
git checkout openGl-implementation
git fetch origin
git rebase origin/main
```
`git fetch` - Download objects and refs from another repository

## Stashing
If we are working on `openGl-implementation`, and we need to switch to `main` temporarily. 
```
git stash 
git checkout main
```

then we can go back
```
git checkout openGl-implementation
git stash pop
```
Temp saving, then going back later, then deleting that stash

## Tracking Remote Branches
`git push -u origin openGl-implementation`
`-u` will mean we can only use `push` and `pull` later one without the full name, however that's not a big deal. 

## Viewing Remote Branches
`git branch -r`

## Git Fetch
Will retrieve latest changes from remote - without merging them into current branch. 
Downloads new commits, branches, and tags from the remote, does not modify you working directory. 

This means that `git pull` - is a combination `git fetch` and `git merge`, immediately integrating them in. 
`git fetch` gives us time to look at the changes. 

