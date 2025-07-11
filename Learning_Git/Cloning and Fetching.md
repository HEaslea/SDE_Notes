### Cloning a Remote Repository
Let's say that we want to clone this project, we are not on the same computer. 

#### The Collaboration Simulation
Each person will have their own repository on their own computer, each will contribute to one remote repository. 
`clone` or `cloning` in order to refer to the process of copying a remote repo, onto a computer to create a local repository, and the command we are using for this is `git clone`. 
`git clone <URL> <directory_name>` - clone a remote repository. 

If there is no project dir name given, the local repo will be assigned the remote repo project name. 

`git clone` : does the following things: 
1. create a project dir inside the current dir
2. create (initialize) the local repo. 
3. download all the data from the remote repo
4. Add a connection to the remote repo that was cloned ; by default it will have the shortname `origin` ion the new local repo. 

`git clone <URL> <directory_name>` - this will create a new dir, with the name put in `<directory_name>`. 

Then we would need to`cd friend-rainbow`. 

`git remote -v` - will show the remotes. 

Then when we do `git branch -all` for what we have been doing so far, we should see something like this: 

![[Pasted image 20250501191210.png]]
Then `git log` will do what it did before, essentially the same. 
All those `remotes/...` are the remote-tracking branches, representing the **last known state** of that branch on the remote `origin`. 

`origin/main` - your last known copy of the `main` branch on the `origin` remote. 

`origin/feature/login` - your local copy of the remote's `feature/login` branch. 

They are read only references - can't commit to them directly. 
Git updates them when you run `git fetch`. 
```
  main
* feature/local-work
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  remotes/origin/feature/login
```
`main` and `feature/local-work` are **local branches**

`remotes/origin/...` are **remote-tracking branches**. 

`remotes/origin/HEAD -> origin/main` -`origin`'s default branch is `main`. 

Currently within the book, there is not `feature` branch in this clone. 

![[Pasted image 20250501192039.png]]


### What is `origin/HEAD`
When we clone a repo, Git needs to know which branch it should be on when it's done cloning. 
`origin/HEAD` pointer determines which branch this is. 
This is pointing to the `main` branch. 
When we clone `origin` essentially means the place that we cloned this from. 

`HEAD` - locally - what we are looking at. 

`origin/HEAD` = what Git thinks is the default branch on the remote `origin`. 

This is all to do with the `remote` repo and nothing really to do with the `local` repo. 

#### Cloning Repositories and Different Types of Branches
In `git log`, there is no reference here to the local `feature` branch. 
However, there is a reference to `origin/feature` remote-tracking branch. 
When we `git clone` will create a remote-tracking branch for all the branches currently present in the remote repo, that is being clones. 
**But the only local branch that will be created when cloned will be the one that the `origin/HEAD` points to**. 

In order to work on the `feature` branch, we have to switch onto it. Then Git will create a local `feature` branch based on where the remote-tracking branch was pointing. 

![[Pasted image 20250501193327.png]]

Notice here the no local `feature` branch on the `git branch --all`

Then when we do `git switch feawture` 
branch `feature` set up to track `origin/feature`

![[Pasted image 20250704214704.png]]

### Git Collaboration and Branches
Teams will have varying rules about how to manage branches. 
Here are some conventions: 
- Which branches are allowed to be merged into one another. 
- When branches need to be created. 
- What the review process for work on a branch should be. 

An idea is that someone should be working on their own topic branches and avoid working on other people's topic branches. This will avoid merge conflicts. 

### Making a Commit in the Local Repository
![[Pasted image 20250704223718.png]]

![[Pasted image 20250704223747.png]]

Notice that `origin/main` is pointing to yellow, is because it is a remote tracking branch to the remote repo. It's showing you the state of the remote repo, which is pointing to yellow. 

##### Pushing to the Remote Repo
If there is an upstream branch, we just `git push` with no further arguments. 
Remember that we can use `git branch -vv` for `very verbose` in order to see every single branch. 

Say that we have `git branch -vv` 

```
* main      abc1234 [origin/main] Some commit message
  feature   def5678 [origin/feature] Another message
  temp      ghi9101 Some local commit
```

And this the output: 
Here `main` has an upstream of `[origin/main]`
`feature` has an upstream of `[origin/feature]`
`temp` does not have an upstream. This means that we have to give arguments. 

If our local branch is ahead, as it is here, then we might see something like this: (the book will also make it clear too). 
`* main      abc1234 [origin/main: ahead 1] Committed something`

![[Pasted image 20250704225312.png]]
Here we see that our local repo is one ahead of the remote repo. 

You can see that the first `git branch -vv` shows that the upstream branch set up for local `main`,  and the `origin/main` (`origin` being the shortname for the remote rep), we see that the **local is ahead by one commit**. 
Although it does look like it's saying that `origin/main` is ahead 1, it's saying that the local branch is one ahead. 

`git status` is showing us the same thing here. 
![[Pasted image 20250704232759.png]]

Notice that in the `rainbow` local repo, the remote-tracking branch still points to the yellow commit. 

#### Incorporating Changes from the Remote Repo
Remember that local repos do not automatically update, we have to be explicit in the updating. 
First we need to fetch, then integrate those changes into the local branch in the local repo. 

##### Fetching Changes from the Remote Repo
In order to get data from the remote, we have to use the idea of **fetching**. 
`git fetch` : **this downloads all the necessary commits to all the remote-tracking branches in the local repo, so that they reflect the state of the remote branches**. 
When no remote repo shortname is provided as an argument to the `git fetch` command, the remote repo with shortname `origin` will be used, unless there is an upstream branch defined for the current branch. 

`git fetch <shortname>` will download from remote repo by `<shortname>`. 
`git fetch` - by default `origin` is used. 

VERY IMPORTANT: This will only update the remote tracking branches. 
This will not affect local branches. 
We have not, so far, done anything to update the local branches, and integrate any of the data that we have in the remote tracking branches. 

![[Pasted image 20250704235543.png]]
![[Pasted image 20250704235713.png]]

Here you can see that the remote tracking branches that we are using have updated, notice there is no change in `origin/feature`. 
However, nothing has been done to the local branches, as they are still pointing to the yellow commit. 

#### Integrating Changes into the Local Branch
Once we have fetched the changes, we have updated the remote-tracking branches in the local repo, we are ready to update the local branch. 

There is **merging** and **rebasing**. 

There are two types of merging : fastforward and three way merges. 

Here, we are going to merge the `origin/main` remote-tracking branch into the local `main` branch. 

Look at the diagram above, this is going to be a fast-forward merge. 

Remember that when we merge, we have to be on the destination, and then argue for the source. 

This time the source will be the `origin/main` branch, which can be used like a normal branch here, which it is, as we have downloaded the commits. 

resume at pg 136.


