### The Three States
**Modified Staged Committed**: 
- Modified - we have made changes, but we have not committed it to our database yet. 
- Staged - marked a modified file in its current version to go into your next commit snapshot
- Committed - the data is safely stored in your local database. 
![[Pasted image 20241124165650.png]]
The working tree is a pull of one version of the project. They are pulled out of the compressed database in the git director and placed on disk for you to use. 
The staging area, a file, generally contained in your git directory, storing information about what will go into your next commit. 
**The technical name for this is the index** but we can also understand it to be the staging area as well. 
The repository is where Git stores the metadata and object database for you project. 
The most important thing here is: this is what's copied when we clone a repository. 

The basic workflow might look something like this: 
- Modify the files on the working tree
- You selectively stag just those changes you want to be part of your next commit, adding only those changes to the staging area. 
- You do a commit, which takes the files as they are in the staging area and stores that snapshot permanently to your git dir. 

If a particular version of a file is in the git dir, it's considered **committed**. 
If it has been modified, and was added to the staging area, **staged**. 
If it has been changed, but not **staged**, then it will be considered just **modified**. 

#### Getting a Git Repo
1. Take a local dir, and turn it into a Git rep
2. `clone` an existing Git Repo from elsewhere. 

```
cd C:/Users/user/my_project
git init
```

This creates a new subdir named `.git`
Go to a file and try `git init` and it will just add the empty `.git` file. 
That file is a git repo skeleton. 
If we want to start tracking files here, then we need to do something along the lines of: 
```
git add *.c
git add LICENSE
git commit -m 'Initial project version'
```


###### Git Clone
Will get a full copy of nearly all data that the server has. 
This will include all the versions as well. 
Meaning that most computers can be used in order to redo the online rep. 
`git clone <url>`
`git clone https://github.com/ligbit2/libgit2`

#### Changes Into a Repo
![[Pasted image 20241124211143.png]]Each file in working directory can be tracked or untracked. 
Tracked files are files that were in the last snapshot, as well as any newly staged files. 
They are files that git knows about. 
Any new files in your working dir that were not in the last snapshot and not in the staging area. 
When first cloning, all the files will be tracked, but unmodified. 
We will selectively stage these modified files, and then finally commit them. 

`git status` will tell us. 

Say we add a new `README` file to the project, `git status` will tell us which files are untracked. 

Then in order to track it, we need to add it to the index (the staging area). 
`git add README`. 
`git init` just creates an index for that file. 
If we add a directory, the command will add all the files in that dir as well. 

Let's change a file that was already being tracked, `CONTRIBUTING.md`
Imagine that we changed an already tracked file. 

Remember that in order to stage a file, we had to `git add X` (adding it to the index). 
The stating area is a little like a draft, where we prepare changes we want to commit. 

Then when we commit, we take a snapshot of the changes that we made, and save it to a local repo. 

`git add CONTRIBUTING.md`: this will stage the file. 
Now that those files are staged, they will be part of the next commit. 

Were we to modify the file further, then we commit, we wouldn't be adding the modified file to it. 
We would have to `git add` again in order to commit the new file. 

#### Ignoring Files
We will sometimes have files that we don't want git to interact with, and automatically add. These would be the files that are produced in the build system. 
That's where we need to use `.gitignore`. 

```
$ cat .gitignore
*.[oa]
*~
```

The first line tell sthe file to ignore any files that end in `.o` or `.a` - object and archive files. 
The second line tells git to ignore all files to end in `~`. 

The rules for patterns you can put in the `.gitignore` file are as follows: 
- Blank lines or lines starting with # are ignored
- Standard glob patterns work, and will be applied recursively throughout the entire working tree. 
- You can start patterns with a  `/` to avoid recursivity. 
- You can end patterns with a forward slash to specify a directory. 
- Negating a pattern by starting it with a `!`


Glob patterns are like simple regex, that shells us . 
`*` matches zero or more chars

`[abc]` matches any characters inside, they are separate of course, so here: `a` or `b` or `c`. 

`?` matches a single char

`[0-9]` matches any char between them (in this case 0 through 9). 

You can use two asterisks to match nested directories: 
`a/**/z` would match `a/z, a/b/z, a/b/c/z` and so on. 

```
#ignore all .a files
*.a

# but do track lib.a, even though we are ignoring other .a files
!lib.a

#only ignore the TODO file in the current Direcotyr, not subdir/TODO
/TODO

#ignore all files in any dir named build
/build

#ignore doc/notes.txt but not doc/server/arch.txt
doc/*.txt

#ignore all .pdf files in the doc/ dir and any of its subdirectories
doc/**/*.pdf
```

Github has a great list of `.gitignore` files. 

### Viewing Stage and Unstaged Changes
`git status` can be a little vague on this. 
When we want to know what has changed specifically: `git diff` is the guy. 
To see what we have staged and what has changed there then `git diff --staged`. 
This will only be the changes that have changed since the last staging, not the last commit. 
For most of git `--staged` and `--cached` are synonyms. 
Therefore, `git rm --cached <file>` to unstage.

### Committing to the Changes
When we have the staging area set to whatever we need it to be. 
Anything that hasn't had `git add` done to it will not be added, they will remain as modified files on your disk, essentially the staging area is where we add files, one by one, that we want to commit to next. 

`git commit -m "Here is a commit message"`

##### Skipping the Staging Area
We add `-a` to the commit. 
Therefore we can skip all the `git add` parts. 
`git commit -a -m "New Message"`
This will add all modified files as well. 


### Removing Files
In order to remove, we have to remove it from tracked files (remove from staging area) and then commit. 
The `git rm` command does that, removing the file from your working directory so you don't see it as a untracked file the next time around. 

```
rm PROJECTS.md
git status # will show us that we have deleted the file
```

```
git rm PROJECTS.md
git status # this will commit like the upper one didn't do 
```
`-f` in order to force the removal. 
In order to just remove from the staging area, and **note remove entirely** would be to use `git rm --cached README`. 

We can also pass files, dirs and file glob patterns to the `git rm` command. 
`git rm log/\*.log`
This removes all files that have the `.log` extension in the`log` dir. 
`git rm \*~` will remove all files whose names end with a `~`. 


### Moving Files
if we rename a file, nothing in git will tell it that we have renamed that file. 
Renaming will look something like this: 
`git mv file_from file_to`



#### Viewing the History
`git log` the most recent commits are put first. 
Adding `-p` will show the difference introduced in each commit. 
There is also the `git log --stat`. 


![[Pasted image 20241124231202.png]]

##### Limiting Log Output
`git log --since=2.weeks`. 
`--since` and `--until` are very useful indeed. 

There is also the `-S` option, known as the "pickaxe" option. 
It takes a string and only outputs the changes that have changed for that string. 

Finding the last commit that added or removed a reference to a specific function, 
you would call: 
`git log -S function_name`

We can also limit with the idea of using a path. 
Where we use `--` to separate the options from that path. 

![[Pasted image 20241124231633.png]]


Adding this all together: 
`git log --pretty="%h - %s" --author='Junio C Hamano' --since="2008-10-01" \`
`--before="2008-11-01" no-merges --t/`
The `\` line indicates a new line. 
