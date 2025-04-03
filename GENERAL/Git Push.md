### Getting the Repo
Make sure that we add the remote repo to the local. 
`git remote add <name> <URL to REPO>`


Remember that we do something along the lines of `git init` when we are in the right dir. 
Then we can add the README.md `echo "# MESSAGE" >> README.md`

Then something along the lines of
```
git remote add orgin git@github.com:User/UserRepo.git
git remote set-url origin git@github.com:User/UserRepo.git
```


Where `origin` is the name here: 


The issue that we had with `AggroEngine` is that we had local commits, then we made the remote repo, then we tried to push to it. 

The idea is that we should not commit locally before having the remote repo set up. 
If we were to clone the remote repo first, then the local repo auto starts with the same commit history as the remote. 

If we do the commits to local first, then git will see that the histories are unrelated. 

#### Best Practice
1. Create the remote repo first (on GitHub, GitLab)
2. Clone the remote repo to your local machine: `git clone https://github.com/username/Aggroengine.git`, This will set up both the locale and remote with the same initial commit history. 
3. If we already have locascl files and want to push them to a newly creawte remote, it's better to link the local repo to the remote first and then commit the changes, as the remote will be aware of the repo history from the start. 


In order to get the remote repo, we have to add: 
`git remote add original <URL>`, but then we have to pull with `allo_unrelated_histories`
`git pull origin main --allow-unrelated-histories`


So: 


```
mkdir AggroEngine
cd AggroEngine 

git init
git remote add origin https://github.....
```

There is no need to pull if the remote is empty, however, if there are files in there, then we need to pull might be worth anyways. 