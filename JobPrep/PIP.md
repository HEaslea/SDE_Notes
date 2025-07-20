### What are Python Packages? 
[Python PIP](https://www.w3schools.com/python/python_pip.asp)
Pip is a package manger for Python Packages, or modules.

3.4, PIP will be included in Python by default. 

Package, contains all the files that we need for a module. 

Remember, go to PYTHON_COOK_BOOK: module is a single `.py` file, eg. `math.py`, `utils.py`. 

Package - An actual directory with an `__init__.py` (can contain modules or sub-packages) - eg. `requests/`, `numpy/` etc. 
This is the thing that we are downloading with `pip`. 

`import mymodule` 
`import mypackage.toll # module inside a package` 

Usually some sort of library (lib) or tool, that can be utilised. 
They can be found on `PyPI` - Python Package Index (similar to the `atp`). 

We use `pip` - in order to install them. 

A package can include : `.py` source code, (something I need to do more research in) sometimes C extension or binaries, or some metadata. 

#### How? 
`pip`

##### Without a Virtual Environment
`pip install requests`

Then we can do `import requests`

This could install to a cole of places

`/usr/lib/python3.x/site-packages/ # system wide`

or `~/.local/lib/python3.x/site-packages/`

These are going to be shared about all Python programs using that interpreter, not just for one single environment. 

##### With a Virtual Environment
[[Python Virtual Environments]]
```
python3 -m venv venv
source venv/bin/activate
pip install requests
```

Then this will install to: 
`./venv/lib/python3.x/site-packages`

Only code, that is running inside the virtual environment sees these packages. 

### On Disk
```
python3 -m venv venv
source venv/bin/activate
pip install requests
```

```
your-project/
├── venv/
│   ├── bin/                # Python, pip, activate script
│   ├── lib/
│   │   └── python3.x/
│   │       └── site-packages/
│   │           ├── requests/
│   │           ├── urllib3/
│   │           └── ...
│   └── pyvenv.cfg
└── main.py
```

#### Updating `pip` - Upgrading etc. 

`pip install --upgrade pip`

