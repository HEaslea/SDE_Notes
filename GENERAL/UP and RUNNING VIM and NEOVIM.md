The `Path` environment variable specifies a set of directories where executable programs are located. 

### Opening My VimRC
`.vimrc` is the file that allows us to configure and customize Vim
`:e! $MYVIMRC`

### Reset VIM
Delete everything in the VIMrc file, and then find the .vim file, which seems to be where all the plugins are going, and then delete those as well. KEEP THE FILES ALIVE, god knows what happens if we break that. 
Then we have returned to the very very beginning.
#### Getting NeoVim
`winget install neovim` or `choco install neovim`, then hit Y once of twice, then allow permissions. 

Then open NeoVim: 
Type in there `:version` . 

`winget` is windows package manager it seems, you can update `git` as well. 
`WinGet` is a tool to enable users to discover, install, upgrade, remove and configure applications on Windows etc. On Windows 11 as well. 

#### Getting Vim
`winget install vim.vim` should be it. I think that's about it. 
This will have python3 already installed. 
https://www.vim.org/download.php#pc
get to `gvim_9.1.0000_x64.exe`
Then you need to go to environment variables, and add to the path variable, the directory that the vim.exe is in. 
Then you can do the `vim --version`. 

![[Pasted image 20241017150910.png]]
That's where my vim .exe is. 

#### Packages -> Plugins -> Scripts
Scripts are written in vim srce code, and they do things when we use certain commands etc. 
A plugin is just a script that someone has made public. 
Then packages are a series of plugins that we can manage. 
A package is a directory that contains one or more plugins. 
You might have one package called `bundle` where we keep plugins that are made by other people. 
You might have another called `myplugins` which is where we keep our own plugins that w will be using. 

When VIM launches, it will search for plugins under `$VIMCONFIG/pack/*/start/` directory. 
Any plugins found here are added to the `runtimepath`. Then, VIM will run through those plugins and try and find script files. 
Therefore, the only thing we need to do to install plugins is to put them in our package, then VIM will do the rest. 

When we add a plugin to a package, all we need do is restart Vim, NeoVim. 
`:help user-manual` is a great way to start all this. 

One thing to remember throughout is `$` means the start of the command. 
#### Integrating Python
Using python in the command line is a basic apparently, therefore we need to actually implement it. It will do a whole bunch of things that wee need. 
**NeoVim ONLY**:
Put in the command line: `:py3 print("Hello World")` and you'll see that there is no python 3 provider. 

##### Providers
Neovim will use providers in order to implement some features whose behaviour depends on the system and environment. 
In order to get python3 working, we need to install it (?) whatever that means, however, this is just preliminary: 
`$ pip3 install --user --upgrade neovim`. 

Then when you write something like `:py3 print("Hello")` it will do exactly that. 

#### NeoVim Remote
By Marco Hintz, let's us control neovim processes remotely. 
Again, we install that with pip: 
`$ pip3 install --user --upgrade neovim-remote`
This package is `neovim-remote`, but is abbreviated to `nvr`. 


#### Installing Plugins
type in `:echo join(split($runtimepath, ','), "\n")`. 

What you're looking at is Vim's default configuration directories. When we install a plugin, you want to add that plugin's directory to your `runtimepath`. 

Let's first, make a directory, where we'll be installing all these lovely plugins. 
Back out of neovim or vim: 

In command prompt/terminal write:
`mkdir %VIMCONFIG%\pack\bundle\start`. 
Where `%ENVIRONMENTVARIABLE%`, so the `%` is for environment variables

Environment Variables are placeholders that store information used by the OS and applications. They can hold paths, configuration settings, and other data that programs can reference dynamically.
So here, imagine that `%VIMCONFIG%` is set to a path `C:\Users\user\vimFiles\` etc. 
Then that's all it is. 
To find these variables, you need to go to system, system properties, and then advanced and environment variables. 
They are just like variables that we use in programming, however, we can use them throughout the OS, and in our experience of Windows, they have global availability (system wide), they can be for users, for for the system as a whole. 
That's why when we write `%temp%` we are accessing environment variables, and we get the path. 
They can hold: 
- Paths  - eg. PYTHONPATH
- User and System Information - `USERPROFILE` the home directory of user eg. use windows + r, then type in %USERPROFILE% and it will take you to your user directory. 
- System Information - %OS% gives us the operating system name, or `%PROCESSOR_ARCHITECTURE%` eg. `echo %PROCESSOR_ARCHITECTURE%` then, on this computer we will get `AMD_64`. 
- Program Configuration - Defining program specific settings, configuring applications dynamically without editing code. eg. `%VIMCONFIG%` beautifully gets us to the Vim Configuration Folder. 
- Process Specific Information - `%PID%` The process ID of the current script of command
- Network Configuration - `http_proxy`

There are so many more.

With that out of the way, we are going to git clone into that file that we just made. 
 We want to go into that file: 
 `cd %VIMCONFIG%\pack\bundle\start`
 `git clone https://github.com//tpope/vim-unimpaired.git`
 
 Then we do the same thing as before, to look at the run time: 
`:echo join(split(&runtimepath, ','), "\n")`

Vim plugins have conventions on how the subdirectories within a plugin should be named. 
When we install plugins we need to add it to `runtimepath`. 
We can add by hand: 
`:set runtimepath+=%VIMCONFIG%/arbitrary/demo-plugin`. 

#### Adding to RunTimePath
To add to the run time path permanently, you want to go to the `:e! $MYVIMRC`. 
That will add it to the run time path permanently. 
I would add the whole path to ![[Pasted image 20241017152041.png]]
Make sure that you don't have a space after the `=`. 

[Installing Plugins Manually](https://opensource.com/article/20/2/how-install-vim-plugins)

When we first start vim, it runs through the `_vimrc` or the `.vimrc` file. 

The `$VIMCONFIG` is looking for the .vim file, so make sure you know where that is. 
The `.vim` file is in `Users/easle/.vim`
IT WILL MOSTLY GO THROUGH THE `vimfiles` folder that should be in the same dir that `.vim` is in.
There are a whole bunch of folders in there that will be useful later on. 

Like the book says, you can add the `start` folder in there, and that has special properties. 

Here I git cloned into my own folder in there, and Vim automatically found it. 

![[Pasted image 20241017153603.png]]

This is where I put it, and it auto updated the runtimepaths automatically. 

TO NOTE, IF YOU EVER GO OVER TO LINUX, I THINK THIS IS ALL IN THE `.vim` FOLDER AND SO THE BOOK IS MORE CORRECT HERE. 

[[VIMFILES]]

### Our Own Package Manager Minpac
Minpac is a very light package manager: It just means that we can update all of our packages at once: using a command that we devise later on. 

The twist is that minpac is a plugin that we are gonna install that will manage all of our plugins:
First let's make a package file that will hold minpac: put it into `opt`
`vimfiles> mkdir pack\minpac\opt` 
Then go into that file: 
`git clone https://github.com/k-takata/minpac.git`
Then we need to load and use the `minpac#init` to initialize it: 
```
// in $MYVIMRC
packadd minpac
call minpac#init()
```

Then reload however many times. 

Forgot to mention that we can create the whole run time paths thing: 
add to vimrc: 
`nnoremap <F9> : echo join(split(&runtimepath, ','), "\n")<CR>` 
`<CR>` stands for enter, we have to mention it here. 

###### Adding Plugins Using Minpac
the easiest way to add them, that we will be using is
`call minpac#add('tpope/vim-unimpaired')`
in the brackets, we just have the git of the name of the person, and the name of the plugin. 
This info can be found on the git page of the plugin usually. 
you can even add it to the `opt` file using
`call minpac#add('tpope/vim-scriptease', {'type' : 'opt'})`

Then we would usually call `:call minpac#update()` to update all the plugins, let's make that easier by adding a command to `vimrc`. 
`command! PackUpdate call minpac#update()`
`command! PackClean call minpac#clean()`

Then we can use `PackUpdate` etc. in order to call the more annoying to write. 
Everything in Vim is about customizing and making things easier. 

## Finding My Files
There was NerdTree, that wasn't great.
##### Using FZF
Fuzzy Finder
The basics are super easy, just add fzf and fzf.vim to plugins list. 
```
call minpac#add('junegunn/fzf.vim')
call minpac#add('junegunn/fzf')
```

This will add it all up, you need both, not sure why, but you do. 
There are binaries that fzf has to use in order to get to the files. 
When you come to use this after updating and installing etc. it will ask if you want to install the binaries, yes of course. The uninstaller can be found in the same file. 

Then if you want to make a command for this to be easier: 

