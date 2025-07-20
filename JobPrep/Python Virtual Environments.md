This is a self contained directory that has: 
- Its own Python interpreter
- Its own `site-packages` / directory
- Its own `pip` and dependencies

This will isolate Python packages from global environment so projects don't interfere with each other. 


###### Creating It
`python3 -m venv myenv` in the dir that we want to be in

```
myenv/
├── bin/            # executables (Linux/macOS)
├── Scripts/        # executables (Windows)
├── lib/            # libraries + site-packages
├── pyvenv.cfg      # config file
```

Remember that `-m` runs a module as a script, so the code above will run the `venv` module code as if it were a script. 

Then we need to: 

###### Activating It
Linux: 
`source myenv/vin/activate`

Windows: 
`myevn/Scripts/activate.bat`

There may be a different file for PowerShell. 

Then prompt will change in the terminal too. 

In VSCode, it's a ps1 file, however, it's also forbidden on VSCode off the bat. 

###### Installing Packages on It

`pip install flask` for instance.

This will install into the `lib/python3.x/site-packages/`  folder. 

Then at the end: 
###### Deactivate
`deactivate`, then we return to the global Python environment. 

### How? 
When we use the `activate` script, we override `PATH`, so that we use the environment's Python and `pip`. 

Modifies environment variables so `import` and `pip` go to `myevn/` folders. 

We can confirm this with `which python` and will point to python in the environment

`which pip` also in the virtual environment. 

You will see in the virtual environment, that Python is in there as well. 

It should be clear now that we are keeping the global python environment clean, and lets us use different library versions per project. 

Best Practices are to always us `venv` for Python projects. 

Then we can put all the packages that we need into a `requirements.txt`, using `pip somePackage > requirements.txt`. 

