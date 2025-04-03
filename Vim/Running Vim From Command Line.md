When we run from Command Line: 

The shell will look through the list of directories in the `PATH` environment variable. 
Looking for an executable file named `vim`. 

The first match is then executed. 
We can check where this is: 
`where vim`
`which vim # on Linux/Mac`

Then the binary launch is executed. 

Detecting system type and version (adjust features accordingly). 
Loading system-wide configuration (`etc/vimrc` or equiv). 
Loading user config (`~/.vimrc` or `_vimrc` on windows). 

Loads plugins from that file, as per request. 

Essentially is a **chain of execution** where one process starts another. 

`gvim` -> initializes GUI libraries and opens a window. That's why gvim is different to normal vimF: 


If inside the terminal `ncurses` is used (text-based rendering). 

After all this, we go into the event loop : waiting for keystrokes :) :). 






