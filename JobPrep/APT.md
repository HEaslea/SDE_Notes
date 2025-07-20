### Advanced Package Tool (Ubuntu/Debian)
Works on top of dpkg, which is Ubuntu's low level package manager, but adds over that the automatic handling of: 
- Dependencies 
- Repositories 
- Package Indexing and Searching

Remember that a package is a collection of files needed to install and run software on your system. Containing, binaries, configs, and metadata. Essentially a ZIP file with instruction for the system to install, configure and manage the software properly. 

APT Package Index - database of available packages from the repositories defined in `/etc/apt/sources.list` in the directory. 

In order to update this, we have to do `sudo apt update`. 
This won't install anything, it will just update the index with the latest package versions. 
Always do this before installing or upgrading packages. 

Then in order to install things, and really update them, we have to `upgrade`. 

So `sudo apt upgrade`

##### Installing a Package
`sudo apt install <package-name>`. 

This will install whatever `package-name` is : eg. `sudo apt install build-essential`. 
##### Remove or Uninstall a Package
`sudo apt remove <package-name>`
Will uninstall, however, we have the config files remain. 

`sudo apt purge <package-name>`
Removes the package and its config files. The whole thing.

##### What is `sudo`? 
`super user do`
`apt` - is the package manager - we can see that we "invoke" it with `sudo`
`sudo` just runs a command with superuser (admin) privileges. 

`sudo apt install code` - we are saying : `sudo` - gives the permission to make system changes / `apt` is the package manager that we are asking to manage some packages. 

#### Clean Up
`sudo apt autoremove`
Removes packages that were automatically installed and are no longer needed. 

`sudo apt clean` - clears out cached `.deb` files to free space

#### Searching and Exploring Packages

```
apt search <term>       # Search available packages
apt show <package-name> # show package details
```

#### What Gets Installed and Where
- There will be executables : `/usr/bin/` etc. 
- Libraries : `/usr/lib/`
- Config files 
- Docs in : `/sur/share/doc`

```
sudo apt update && sudo apt upgrade
sudo apt install build-essential git curl vim
```

cURL - command line tool and library used for transferring data with URLs. 

`sudo apt install python3 python3-pip python3-venv`

[The Debian Wiki Apt Docs](https://wiki.debian.org/Apt)

#### `pacman`
They are very similar, however, they are used in different distributions: 

Because Ubuntu is Debian based, the package format is `.deb`. 

`pacman` is another package manager, used mainly on Arch Linux, Manjaro and MYSYS2. 

We are using `sudo` again, (super user do) `sudo pacman -S vim`. 

Overall - they are just tools that are tailored to their respective distros. 

APT - is used for Debian and its derivatives. 

[Leading to the Wiki](https://en.wikipedia.org/wiki/APT_(software))

The wiki does a good job of laying out the basics. 

