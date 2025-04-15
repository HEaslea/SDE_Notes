### WHY? 
- To work on the same project in different ways
- Multiple people working on the same project at the same time. 

A line of development. 
Branch - One line of development, and off that we create secondary branches, *topic branches* or *feature branches*. 

These branches are short lived. 
They are incorporated back in to the primary branch and then deleted.
![[Pasted image 20250408014820.png]]
**Branches are movable pointers to commits.**

![[Pasted image 20250408015045.png]]

`HEAD -> main`, `HEAD` is not a branch. 

![[Pasted image 20250408015133.png]]

The dir `heads` will hold a file for reach local branch in the local repo - `refs` stands for references.
There would only be `main` at that moment. 

`main/master` - **just a pointer to a commit**. 

A branch - like `main` - is a named pointer that moves forward as you make new commits. 
When we have a new pointer to a commit - that's just it - having all these trees is great, however, visually they are a lot to handle. 
The general idea is this, there are snapshots of the project. Having a new pointer just means that that commit is with that pointer, if you do something and save it to commit with this pointer (pointing to a certain snapshot of the project), then it will look like a branch from main. 
The way I like to think about it is this - it just saves the snapshot of the project under that pointer, that's it, nothing crazy complicated with the branches etc. 
A commit is just a snapshot (saved) of a project at that time. Instead of saving the whole file, we just need to save the delta of it.
### Modified and Unmodified Files
Unmodified - not been changed since last commit. 
Modified - changed since last commit. 
For git to know this, the file must have been saved in the text editor. 

`git status` - shows a list of all the modified - and whether they have been added or not - files. 

**![[Pasted image 20250414192859.png]]**

Then we need to add this to the staging area: 
`git add rainbowcolors.txt` 
`git status` 

### Making Commits on a Branch
`git commit -m "Orange"`

`git log` - use Enter or the down arrow, then `q` to exit. 

`HEAD` - is your current position, pointing to a branch. It's another pointer really - meaning where we are (which branch we are on). 
Then the `main` branch just points to a commit. 

```
HEAD -> main  // where you are
main -> abc123 (commit hash) // what commit main is pointing to
```

Think about the idea of having parent commits (every one has one bar the first commit). 

Everything will point to the parent. 

**Checking which commit is the parent of a given commit** : `git cat-file` command with the `-p`: 
`git cat-file -p <commit_hash>`

### Creating a Branch
At the beginning there is `main`. 
To list the branches - `git branch` - that just lists them out. 

Then creating a new branch is : `git branch <new_branch_name>` - providing a name will create a new branch. 

Keep the names descriptive: 

```
HEAD → branch (like main)
branch → commit (like abc123)
```

```
git branch // will list the branches that we have

git branch feature // makes a new branch

git branch  // will show *main and feature (* - denoting which branch we are on) 

git log // important bit is (HEAD->main, feature) - showing what we are currently on
```

When we make a new branch, it has to point to the commit that we are currently on : seems obvious, but think about the trouble if the branch started at another commit : say if we go back to an older commit, then we make a new branch, then we would have to move that branch pointer back to that commit etc. 
It is made to whatever commit that `HEAD` resolves to. 
`git checkout <commit>` - will move this pointer to a commit given - note that this will create a detached head. 


## What is `HEAD`
At any given point - think about this - you are looking at a particular version of the project. 
`HEAD` simply a pointer that tells you which branch you are on. 

There are times that we have a HEAD that is not pointing to a branch - but rather just straight to a commit. 

![[Pasted image 20250414195015.png]]

`git switch <branch_name>` - switch branches
`git checkout <branch_name>` - also switch branches

`git switch` - is literally just two switch

However `git checkout` - does a few more things (see page 80 or / Checking Out Commits). 

When we are `switch` branches : three things happen: 
1. Changes `HEAD` pointer to point to the branch that you are switching onto (makes total sense)
2. Populates the staging area with a snapshot of the commit you are switching onto (so changes the staging area to what we are moving to)
3. Copies the contents of the staging area into the working directory

**Working Directory - folder on local machine, contains files that you're actively working on, synced with the git repo. A local copy of the project that contains the files you're currently editing, the space where we make changes to files (coding/writing ) before we commit them - The actual files that we are working on - separate from the repo and the staging area**

Changing the working directory does mean - it will update the editor to reflect the files from the branch you're switching to. 

Changing branches - changing the commit that we are looking at. 

```
git branch // will list the branches

git switch feature // duh

git branch // list but now *feature, main will be shown

git log // HEAD->feature, main will be shown
```
![[Pasted image 20250415223329.png]]

![[Pasted image 20250415223616.png]]

#### Working on a Separate Branch
Say that we add to this book's text file now : "Yellow is the third colour of the rainbow" then save. 
`git add rainbowcolours.txt`
`git commit -m "yellow"`
`git log // will show just HEAD->feature`  (no main will be included here). 

Meaning - very simply that `feature` points to `yellow` and `main` still points to `orange`. 

![[Pasted image 20250415230623.png]]



