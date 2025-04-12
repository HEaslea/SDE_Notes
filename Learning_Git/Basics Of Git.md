### Repositories
Repo - copy of Git project. 
Local - Remote. 

### Commits
A version of the project. 
![[Pasted image 20250330132311.png]]

### Visualizing Git
Probably the best way to do any of this. 

#### What is Git
Track changes and version of a project - VCS (Version Control Software/System).
Tracks changes to files that are in a folder/project. 
LINUS TORVALDS. 
Version of a project - a snapshot of it - That's a commit. 

Can compare between versions. 

Folder - Storing files
Directory - can store files/subdirectories etc. 

![[Pasted image 20250330133009.png]]
can use `git bash` in order to use CLI more easily, however, readily available in vscode as well. 
pg 6

When it comes to `-` and `--` in CLI: 
`-` - shorter form `-v`
`--` - longer form `--version`


`clear` - does the obvious. 

**SEE - GITHUB ACTIONS**

`pwd`- show path to the current directory.  -> `cd` on Windows. 

`ls` - > `ls -a`(all)

`cd <path_to_directory>`-> `..` parent directory

**Creating a Dir** -  `mkdir <directory_name>`

**Setting Git Configs** : Git config file, variables, values, stored in a couple of different files. 
Just check that some settings that we need are set up. 
`git config --global --list` -> getting the config vars and their values. 
Getting the hidden file `.gitconfig`

The main ones are the username and the email address. 

You can set them: 
`git config --global user.name "<name>"`
`git config --global user.email "<email>"`

`Git` - works with all sorts of files - even basic text files

### Local Repos
`repo` -> project version controlled by Git. 
- Local - Stored on the computer
- Remote - hosted on a hosting service - GitHub - GitLab - BitBucket

Local repo is just a `.git`, within the project directory. 
Containing all the data on the changes that have been made to the files in the project. 

Initialize with `git init` -> cd must be the one that our project is in: 
`-b` -> `--initial-branch` -> setting the initial branch name
`git init -b <branch_name>`

![[Pasted image 20250405114922.png]]
![[Pasted image 20250405114956.png]]
The `.git dir`  has all of these files, that will do untold Linus things. 

### The Areas of Git
- Working Directory - The "Workbench"
- Staging Area
- Commit History
- Local Repository

**Working Directory**: 
Bit obvious, contains all files and subdirs, in the project dir, representing one version of a project. 
![[Pasted image 20250406120233.png]]
See the Project Directory contains the local repo. 
Within the local repo, there are two important areas: 
- Staging area
- Commit history

**Staging Area:**
Similar to a rough draft -> add and remove files, what we want to include in the next saved version of the project (your next commit). 

It's represented by a file in `.git` called `index`. 

`index` only made if we add one file to the staging area. 

![[Pasted image 20250406120716.png]]

**Commit** : version of the project - a snapshot - standalone version of the project that contains references to all the files that are part of the commit. 

Each one has a *commit hash* (*commit ID*). 40 char hash composed of letters/numbers, acting like a name for the commit, providing a way to refer to it. 

`51dc6ecb327578cca503abba4a56e8c18f3835e1`

**Commit History**: 
Commits existing, `objects` dir inside the `.git` dir. 
Every commit is going to be saved in the commit history. 

 ![[Pasted image 20250406121118.png]]

##### Adding a File to the Git Project
Say that we have a file that is `rainbowcolors.txt`, listing the colours. 
Never include spaces - just don't do it. 
Say that we have this `.txt` - we add the line `Red si the first colour of the rainbow` - then we save. 

Once this is added, it's not part of the repo just yet. 
![[Pasted image 20250406121504.png]]
This is a `U` file, meaning it is currently **untracked**. 
Never been staged, never committed, so not part of Git just yet. 

Once added to the staging area, it becomes `T` - **Tracked**. 

Every new file in a project version controlled by Git needs to be explicitly added to the staging area and then included in a commit in order to become a tracked file. 

## Making a Commit
Given that we are in the setup above. 

Essentially a save -> backing up the work. 
Once you have a commit, the work is saved. 
Will be able to go back and look at that commit at that time. 
No strict time to make a commit. 
That depends on many factors -> may depend on the team that you are working with, and what you are working on. 

`Commit Early, Commit Often` -> preferable to have too many commits, than too few. 

1. Add what we want to the staging area. 
2. Make a commit with a commit message

`git status` - very useful, telling us the state of the working dir and the staging area. 

Easy to lose track of what state the files in the working dir are in. 

**Adding Files to the Staging Area**: 
`git add` simple as. 
```
git add <filename>

git add <filename> <filename> ... 

git add -A // Add all files int he working dir
```

Adding a file to a the staging area will create the `index` file -> it's a binary file, contents are gibberish , 
![[Pasted image 20250408013842.png]]
There is a copying of files from the working dir to the staging index. 

###### Making a Commit
Commit is a verb and a noun. Commit-ing is saving this version of a project. 

`git commit -m "<message>"`
Say : `git commit -m "red"`

![[Pasted image 20250408014225.png]]

`git log` - seeing a commit history. 
1. Commit hash 
2. Author name and email address
3. Date and time commit was made
4. Commit message

![[Pasted image 20250408014333.png]]
