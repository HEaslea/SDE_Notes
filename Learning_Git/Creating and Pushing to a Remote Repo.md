![[Pasted image 20250425210101.png]]

This is the current state of the local repo. 

### Two Ways to Start Work on a Git Project
##### Starting From Local Repository
Star the local using the `git init` - then make the remote repo on the hosting service. 
Then just upload the data from the local repo to the remote. 

 *pushing* - *push* - refer to the process of **uploading** data from a local repo to a remote repo. 

`git push` - upload data to a remote repo. 

![[Pasted image 20250428014206.png]]

##### Starting From Remote Repository
Either find a remote repo that you want to work on, or create a new one on the hosting service. 
Then we **clone** (copy) - that remote repo to our computer, which will create a local repo. 

![[Pasted image 20250428014413.png]]

#### Interaction Between Local and Remote Repositories
They act separately - there is not automatic interaction between the two. 
Any changes between them will be that enacted by us. 

There are obvious reasons as to why remote repositories. 

**A Quick Example**: Suppose there is a `book` local repo, and no remote repo. 
If someone stole my laptop, or the hard drive died, I would lose all that work I had done. 
Remote Repos are a great backup mechanism. 

Imagine now that there are two laptops - one at home and one in my office - say that the book is stored on some external SSD, I would always have to carry that everywhere that I go, only one copy etc. If we have a remote repo, although we rely on Git - it's a lot safer and more optimized. 
The collaboration aspect is greater as well. We can share with anyone. 

### Create a Remote Repository with Data
![[Pasted image 20250428111630.png]]

1. Create the remote repo on the hosting service. 
2. Add a connection to the remote repo in the local repo.
3. Upload (push) data from local to remote repo. 

Create - connect - upload. 

#### Creating the Remote Repo
*Remote Repo Project Name* : The hosting service will provide a *remove repo URL*. 
This URL will include the project name. 
The URL generated will also be that of an SSH URL and/or a HTTPS URL. 

This is all done with Git - we don't do any of this. 

There will be the idea of including a `README` and/or `.gitignore`. 

Can just make the repo on GitHub. 

![[Pasted image 20250428112445.png]]![[Pasted image 20250428112546.png]]

#### Adding a Connection From Local to Remote
This connection will have a name - the **remote repo shortname** or just **shortname**. 

A local repo **can have connections to multiple remote repos**, although this is not very common. 

In order to connect to a remote, we have to associate the remote repo URL to the remote repo shortname. 
In order to do this we have to add using `git remote add` command.
`git remote add <shortname> <URL>`
Add connection to a remote repo called `<shortname>` at `<URL>`. 

Once that connection is made - it is stored in a local repo, you are able to connect to the remot repo by referring to the shortname rather than the URL in the command line. 

When we clone from the remote - git will automatically add the shortname `origin`. 
Then we have to use that `origin` name in order to do anything with it. 

In order to see the number of remote repositories stored in the local rep, by shortname, use the `git remote` command. 
If passing in the `-v` option (`verbose`), will also include their URL's too. 

`git remote` - list the remote repos connections that are in the local repo by shortname. 
`git remote -v` - same but with the URLs (`verbose`). 

The idea being that we go get the URL for the protocol you have chosen (SSH or HTTPS). 
Then we got into our Git. 
`git remote` - see what there is

`git remote add origin <URL>`

`git remote // this time will show origin`

`git remote -v // will show origin and that URL`

![[Pasted image 20250428125434.png]]![[Pasted image 20250428125512.png]]
This means now that we have a shortname associated with the remote repo, the URL, called `origin`. 

The remote repo cannot find a list of the local repos that store a connection to that remote repo - that would be a little absurd - but possible. 

To upload data to a remote repo, we must push a branch to it. 
That will upload all the commits that are part of that branch. 

#### Remote Branches and Remote Tracking Branches
When we push a local branch to the remote, we create a **remote branch**. 
This is just the remote branch in the remote repository. 

They **do not update automatically when you update that local branch**. 

You **have to explicitly push commits from a local branch to a remote branch**. 

Every remote branch (that a local repo knows about) has a **remote-tracking branch**. 
Reference in a local repo to the commit a remote branch pointed at the last time any network communication happened with the remote repo. Think of it like a bookmark. 
This tracks the state of a branch in the remote repo, this allows us to see the changes on the branch without directly working on it. 

You can set up a tracking relationship between a local branch and a remote branch by defining which remote branch a local branch should track. 
This is known as a **upstream branch** : There are some cases this is set up automatically, in others, we have to manually set them up. 
This refers to the default remote branch that the local branch tracks. 
Typically the branch you pull from or push to. 
If the local `main` branch tracks the `origin/main`, then the `origin/main` is the upstream branch for `main`. 
Helping git know where to fetch updates or push changes to by default. 

When pushing from local to the remote, Git needs to know which remote branch to push to. 
If the local branch has an upstream branch defined for it (upstream - the remote branch that we are referring to), the work will be pushed automatically to that branch. 
If there is no upstream branch, you'll need to specify which remote branch that we are pushing to with the `git push`- if you don't, then you will get an error. 

### Pushing to a Remote 
`git push <shortname> <branch_name>` upload content from `<branch_name>` to the `<shortname>` remote repository. 

The first thing we are doing is pushing to `origin` and we will push `main` to it. 

If we push another type of `branch` then we will create that branch in the remote repo. 

A remote-tracking branch will be created in the local repository. 

Now the `git bracnh --all` will track all the remote-tracking branches in your local repository. 

`git branch --all` - 

Let's say that we do this: 
`git push origin main` - that will do a whole bunch of things, adding all the stuff to the remote branch. 
![[Pasted image 20250501183254.png]]
Then view this : 
![[Pasted image 20250428134749.png]]

In step 2 : `git push` - command indicates that you have pushed your branch to the remote repository. 
`push` - data from **local to remote**. 
`origin` - **remote name**, the **shortname**. Remember that this comes from the name given from `git remote add origin <url>`. 
`main` - the specific local branch that we will be adding - git will try and add it to the branch named `main` or `origin`. 

Quick reminder about the **upstream branch** - This is the remote branch that our local branch is tracking. 
Defines where your local branch **pulls from and pushes to by default**. 
When this is set, we can just do the `git pull` or `git push` and it will know which branch to do that from. 
This is done when we push for the first time: 
`git push -u origin main`

In step 3: looking at the `refs` folder - we will see `remotes`. `origin`, inside this, is a file called `main`. 
This represents the new `origin/main` remote-tracking branch. 
![[Pasted image 20250501185304.png]]


You can see that all the commits have been added up there. 

Take note that the `feature` branch is not on there as well. 

 If we did want to push the `feature` branch to the remote: 
 ```
 git switch feawture
 git push origin feature
 git branch --all
 *feature
 main
 remotes/origin/feature
 remotes/origin/main
 git log // (HEAD -> feature, origin/main, origin/feature, main)
```
You can now see, that the two **remote-tracking** branches, `origin/main origin/feature`.

![[Pasted image 20250501185943.png]]

##### What is `origin`: 
When we do `git add remote origin <url>`, what exactly is origin. 
`origin` is a reference name for a remote repo. 
In reality, it is quite clear that `origin` will be a nickname for a full URL like this: 
Again, in reality, this just means that we don't have to type the URL in order to access that remote repo. 

`origin`, or whatever name that you are using, can be considered the local alias for the remote repo. 

`origin/main` - `origin/feature` they are **remote-tracking branches**. 
Like `origin` - this is a reference to a remote branch - obviously `origin/main` on the `origin` remote. 
Git updates it when you `fetch` from remote. 

It's read only - as in, you will not commit directly to `origin/main`. 

> Essentially, these **remote-tracking branches** are snapshots of what the branch looked like on the remote the last time you synced.


When we do something like `git fetch origin`. 
`origin/main` - latest state of `main` on remote will update accordingly.
`origin/feature` - latest state of `feature` will be updated accordingly. 
HOWEVER, the local branches, actual `main` or `feature` **won't change** unless there is a `merge`, `rebase`, or `pull`. 

Imagine that we have: 

```
main           <-- local branch 
origin/main    <-- snapshot of main on GitHub
```

When someone else pushes a commit GitHub. 

You won't see it in `main` yet. 

After we `git fetch`, `origin/main` updates. 

Then, it's possible to `git merge origin/main`,  or `git rebase origin main` to update your local `main`. 

Remember that when we `merge` we have to be on the **target** branch. 

`git merge <source>` is the way that we should be doing it. 

```
git checkout main // put us onto main
git merge feature // merge feature into main
```

That's a simple way of merging. 

Imagine that we have an older commit. And a branch on that commit. 
Then we want to catch-up that commit. Therefore, as per above, we `checkout` the branch where we want the older commit to catch up to. 
`git checkout newPointer`
`git checkout oldPointer`

**The Three-Way Merge**: 
```
      A---B---C   (main)
     /
D---E             (merge base)
     \
      F---G       (feature)
```

There needs to be a common ancestor between the two, in this case, that would be E. 
That is the last time the two branches were the same. 

The `HEAD` commit. 

Here, Git will look at the changes between E -> C and E -> G. 

We will obviously need to make a new merge, we can't just catchup in this case. 

The outcome will be that Git makes a new commit. `main` will point to that. 

Remembering, that `feature` will continue pointing to that branch, that is on a separate line. 
      A---B---C--------M        (main)
     /                          /       
D---E-------------F---G---H     (feature)


The angle is pretty confusing, however, picture that E is the common ancestor. 
`feature` will remain on G, then there is another commit - H - and you can see how that continues. 

This is the most likely type of merging when there is. 

`main` pointer will move forward to the new merge commit - from C to M. 

`feature` pointer will not move until we create a new commit. 

**When we push to `origin`**: 

When we do `git push origin main` : we push to `origin main`. 

So outlining the idea of creating a new branch, switching to it, committing to it, then adding that new branch to the remote. 

`git checkout -b feature-xyz` 
Creating a new `feature-xyz`
And will switch to it as well. 

In order to make a new branch without switching to it: 
`git branch feature-xyz`
This will create without switching to it. 

Then we can switch to it. 
`git checkout feature-xyz`

Then stage: `git add .`
Then `git commit -m "New Feature"`

Push the new branch to the remote. 
`git push -u origin feature-xyz`
The `-u` will make sure that future `git push/ git pull` words without arguments. 

`git branch -vv` 
Shows what each local branch is tracking. 

The `-u` is `--set-upstream` tells Git: 
**Link my local `feature-xyz` to `origin/feature-xyz`**. 

Then in the future we can just put `git push` or `git pull` in order to push t the same branch, without having to do `git push origin feature-xyz`. 

`origin` is just a name for the remote repo (the whole repo hosted, not the branch). 
Then when we have `origin/main` , that is the URL for the branch specifically. 

An **upstream** branch is the remote branch that your local branch is connected to for: 
`git push` (sending changes)
`git pull` (getting updates)

The **upstream** branch will "remember" which one, when we have set up an upstream branch. 

When we say `git push -u origin feature-xyz`. 

Here's an odd thing to think about, that should realistically not happen, but might actually be pretty helpful. 

If we are on `main`, and we do `git push -u origin feature-xyz`. 
This will not set up a `main` to `origin/feature-xyz` upstream. It will, however, set up an upstream for `feature-xyz`, if it does exist locally. 

We should, in theory and in practice, we pushing branches to remote branches that have similar names. 

### Cloning a Remote Repository
**Cloning/Clone** referring to the process of copying a remote repo, onto a computer to create a local repo and the command we use to do this - `git clone`. 

`git clone <URL> <directory_name>`. 

This will : 
- create a project dir inside the current dir. 
- Create and init the local repo. 
- Download all the data from the remote repo
- Add a connection to the remote repo that was cloned, by default it will have the shortname `origin` in the new local repo. 

`git remote -v` 
Will visualize and show the URLs for the remote repo linked to your local Git repo. 
Might show something like this: 
![[Pasted image 20250624121424.png]]

Again - `origin` - which is automated after cloning repo, just a nickname/alias for the remote URL. 

Instead of writing: `git fetch <URL>` 
We just write `git fetch origin`. 

The idea being here that when we use either `fetch` or `push` with `origin` we will be using it with that URL. 

Assigning any name to a remote - 
`git remove add <new-name> <URL>`
You can do this even fi you are using the same remote repo. 

![[Pasted image 20250624122409.png]]

Something to note here : there is no local `feature` branch here, in the new clone. 

Here's the full cheese: 
Git uses `pointers` (refs/branches) to track commits. 
When someone pushes to remote : they are moving branch pointers on the remote repo, to point to the new commits and changes. 
The commit history on the remote grows (usually linearly) and the branch reference updates to point to the latest commit. 
When someone pulls, fetches or clones: They are copying remote's commits and pointers into their local repo. 
Git creates or updates `origin/main` for isntance. 
Local branches don't move unless you tell them, that's why we might get merge issues and differences. 

When you clone a repo, the `git clone` command will create remote-tracking branches for all the branches currently present in the remote repo that is being clones, but the only local branch that is created is the branch that `origin/HEAD` points to. 

In order to work on it, we have to switch onto it, as a local, to create the local `feature` branch. 

`git switch feature` -  will also set up to track. 

![[Pasted image 20250624124027.png]]

Whenever you see the `remotes/origin/branch` whatever `branch` will be - that is a remote-tracking branch. They are read only pointers, in the local repo, that track the state of the branches on the remote, the last time you fetched. 

`remotes/origin/main` - The last known position of the main branch on the `origin` remote. 

![[Pasted image 20250624124537.png]]

These remote tracking branches -  **do not move on their own** - your must `fetch` to update them: 
They somewhat say - this is what I last saw, I haven't checked again yet. 
They are literally just the local record of what the remote looked like the last time you checked. 

So our local branch - is what we have on that branch - the remote tracking - is a snapshot of what the remote repo looks like - which could theoretically be someone else's branch that they just pushed. 

This can see whether we are **ahead** or **behind** the remote. 

`git fetch` - Updates the remote tracking branch - no change to the local branch
`git pull` - Updates the remote tracking branch - AND merges into local
`git diff origin/main` - compares the latest remote state

#### Deleting Branches
To unclutter the project and keep everything organized. 
Make sure, that before deleting, that there is a merging, of you're absolutely sure that you don't need the work that was in that branch. 

Remember that you aren't deleting the commits as such, you are deleting the branches. 

To fully delete a branch - delete the remote branch, the remote tracking branch, and the local branch. 

To delete the remote branch and a remote tracking branch, you use `git push <shortname> -d <branch_name>`. 
Essentially, we are uploading a delete to the remote repo. Or you could just do it on GitHub itself. 

If we did: `git push origin -d feature` we are deleting the feature branch on the remote repo. 

However, we will still have that branch in our local repo: therefore we need to `git branch -d feature`. 

This however, does mean that other locals there will still be that `feature` branch and the remote tracking branch for feature. 

#### Branches in Teams and Conflict Avoidance
When merge two commits, that have changes to the same place, there will be some conflicts. This is a sign that we have created branches and worked on places that we really shouldn't have : there should be rules to eliminate conflicts as we go. 
We do this by having clear rules of when we make a branch, when we commit etc., that will be cleared out when you join a team. 

Consider, where added a new commit to the `friend-rainbow`. 

![[Pasted image 20250624132427.png]]

Obviously the remote has had nothing to update it yet. 

##### Pushing to the Remote Repository
If we have an upstream branch - meaning that we don't have to say which branch we are uploading to - then we can just use `git push`. 

If we did not have an upstream branch - then we have to specify which remote branch to push to when you enter the `git push` command. 

In order to see any upstream branches - `git branch -vv` (very verbose). 

`git fetch` updates the views of the remote repo - doesn't change your local branches, just updates. 
Imagine that you are looking at a shared whiteboard - you haven't looked at it in a while - therefore we use `git fetch` in order to have a peek at it, there is no copying of it into our own works yet, however, we see the updates and we are aware of those updates. 
Git will contact the remote repo, downloads new commits and branches, and tags. 
**Updates the remote tracking branches** eg. `origin/main`. 
Does not touch the working files or local branches. 

