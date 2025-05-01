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
