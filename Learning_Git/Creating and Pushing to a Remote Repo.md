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

Then view this : 
![[Pasted image 20250428134749.png]]
