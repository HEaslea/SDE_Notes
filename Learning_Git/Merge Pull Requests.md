This is focussing on the remote merging, so taking remote branches and then merging them remotely.

![[Pasted image 20250728131622.png]]

This is the current state. 

### Introducing Pull Requests
`pull request` - is a feature offered by a hosting service, allowing you to share work done on a branch with your collaborators, potentially gather feedback on that work, and finally integrate that work into the project remotely on the hosting service. 
We have to go to the hosting service in order to make this work, there is no `git merge-request` etc.

We are requesting the manager of the `main` remote branch to `pull` our remote branch into main. 

They can be merged through merging or rebasing. 

The talk about it as `merging a pull request`. 

We "open" a pull request. 

There will be a review, approval and a merge, then it's considered "closed". 

1. Creating a branch in the local. 
2. Add work by making commits on the branch
3. Push the branch to the remote repository
4. Create (open) a pull request in the hosting service
5. Get that reviewed, incorporate feedback. 
6. Get it approved
7. merge it
8. You can remove/delete the remote branch, specially if it's just a feature branch. 
9. Pull the changes, syncing the local repo with the remote repo, clan up by deleting the local branch and the remote-tracking branch
The last part is crucial so that the local branch matches. 

This is not the same as `git pull` at all. 

On the hosting service there will be people that are authorized to merge. 

Suppose that we have.

![[Pasted image 20250728134621.png]]
Say that the editor has access to the remote repo, meaning that they don't need a local to `pull` and get the updates through that way. 

Then we make a pull request in the remote repo, to ask here, that the `chapter_nine` branch into the remote `main` branch. 

You can even send that URL to the pull requests or simply tell them to go to the remote repo and find the pull request titled "Chapter 9 Updates". 

The editor will provide feedback etc. 
If we update after that, the pull request will be updated, we don't need to update and re-request. 

### How Are They Merged
We know that the fast-forward merge, when there is no diverging of development histories. 
If they have diverged, then we need a three way merge. 

Merging remotely is, by default, different to merging locally. 
Remote merging is usually done with the option `non-fast-forward`. 

Even if the histories have not diverged, there will still be a merge commit

Remember that, a `non-fast-forward` merge will still make a merge commit. 

There is the option that this can be changed on the hosting service. 

### Preparing to Make a Pull Request
In order to make a branch and switch to it in one go. 
Remember that this is `git switch -b <branch_name>` however, in the newer 2.23+, the preferred syntax is `git switch -c <branch_name>`
`checkout` and `switch` essentially do the same thing. 
`git switch -c <branch>` and this will create a branch as well.
`-c` for create.

`git switch -c topic` 
Then write to a file, `othercolors.txt` - "Pink is not a colour in the rainbow". 
`git add othercolors.txt`
`git commit -m "Pink"`
Then `git log` - then we will see that `origin/main` is behind one. 

![[Pasted image 20250728141647.png]]

At this moment there is no upstream branch here, so we would have to `git push` and then specify. 

Setting one up we need `git push -u origin <branch_name>`. 

Or `git branch --set-upstream-to=origin/<remote-branch> <local-branch>`

We can check with `git branch -vv` .`-vv` -- very verbose. 

##### Quickly on Upstream Branches
They are remote branches, that your local branch tracks. 

This means that we know where to `push` when we are on one branch. 
We know where to `pull` from as well. 

And will give us more information about whether we are ahead/behind with `git status`, `git log`. 

Say we are on `feature-x` local branch. 

`git push -u origin feature-x`, this will set up the upstream branch. This will create the remote repo 

`git branch --set-upstream-to=origin/<remote-branch> <local-branch>`

`-u` or `--set-upstream`. 

`git` will also warn us if we don't have an upstream branch as well. 

It will give us the command too, as `git push --set-upstream <shortname> <branch_name>`. 

Remote-tracking branches look like `origin/<branch_name>`, remember that they are local references to branches on the remote. 

Git will assume, rightly so that the remote branch will have the same name as the local branch. 

Usually, you just copy and paste that command that git provides. 

We might do: 
`git branch -vv` 
![[Pasted image 20250728143017.png]]**
I'm pretty sure that `git push --set-upstream` and `git push -u`> 

![[Pasted image 20250728143258.png]]

They will be the same name, which is what you want 99% of the time, but can change that as well. 

### Creating a Pull Request on a Hosting Service
`topic` is the source branch and `main` is the target branch, aka we are merging `topic` into `main`. 

Then we have to create the pull request, which we have to do on the hosting service's UI. 

We enter information about the pull request. 

### Reviewing and Approving a Pull Request
The hosting service will display any differences, any deltas. 
When we push to the branch, that will auto update the pull request. 
Someone can also decide to take it to their local, make commits, and then work on it from there. 

![[Pasted image 20250728144419.png]]

Remember that Remote Merges are always going to make a merge commit, and that's what we have here. 


see that `topic` on remote is still pointing to `P`. 

### Deleting Remote Branches
It's common to delete this after merging. 
We assume that the work on that branch has been completed. 

Again this has to be done on the hosting service. 

![[Pasted image 20250728144738.png]]

Then we have to make sure that everyone is pulling after this update

#### Syncing the Local Repos and Cleaning Up
`git pull -p` = `git pull --prune`
`-p` will delete any remote-tracking branches that correspond to remote branches that have been deleted in the remote repository. 

Then we can use `git branch -d topic` to delete the local branch `topic` as well. 

![[Pasted image 20250728145335.png]]![[Pasted image 20250728145350.png]]

Remember that `git pull` is `git fetch + git merge`. 

And that is everything you need to know about `git` for the time being, that is the whole book done and dusted. 

