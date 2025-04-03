The idea here, is that we define a `.projectionist.json`file in the root of our files/project. 
Which we will have to `git.ignore`. 
### What It Do
The whole idea is that we project types onto files, so that we can do more with them in vim. 
See page on projectionist in the modern vim book. 
But the main thing is, when we are in a file that we have defined with the glob, then we can use certain commands in order to get to another file very quickly, like `Harpoon` that `primeagen` uses. But better, as we can configure it in a file that is found in the root of the project. 

The main benefit is that we can swap to the alternate files with `:A`. 
Meaning that we can have `.cpp` files and then the alternate is some `.hpp` file, that we can swap between, because that's really important, however, we can also do that with windows as well. 
The other thin that you can do with it, is that we have the type of the files, and we can use that in the `vimrc` file. 

