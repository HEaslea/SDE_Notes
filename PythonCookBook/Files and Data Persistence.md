- Files and Dictionaries
- Compression
- Network  and streams
- The JSON Data-interchange format
- Data persistence with `pickle` and `shelve` from standard library
- And with SQLAlchemy
- Configuration Files

### Opening Files
```
fh = open("fear.txt", "rt") # r : read, t : text

for line in fh.readlines(): # I guess this returns an iterator
	print(line.strip())

fh.close()
```
`fh.readlines()`- returns a list of strings

Tangential - however, if we were to write, `for line in fh` that would return one line at a time. 
This works as `fh` is an iterator. 
As we know, when we write this, we use `fh.__iter__()`, then repeatedly `fh.__next__()` gets called, or just `next(fh)` in user code.  
That is until `StopIteration`
`iter(fh)` - called `fh.__iter__()`. 
This way of doing it, is **lazy**, in that we get one line at a time. 

Here `readlines()` will force it to read all the way through and get a list of the lines from the file=. 

If there is no path given to `open()`, it will assume that the file is the same folder the script is run from. 

I'm not sure about the way the book is talking about `readlines()`, it returns a list of strings of lines, not iterate itself. The list is iterated over, not `fh.__iter__()` in that case. 

`fh.close()` is very important.

We don't want to risk the idea of failing to release the handle `fh`. 

When we call `f = open("data.txt", "r")`
The OS allocates a file descriptor (a low-level resource). Internally python wraps that in a **file object**, includes a buffer and metadata. 
This handles is what Python uses to read and write to the file. 

This means that we have `file.close()`. 
There is a limited number of file descriptors (therefore open files). 
Leaving files open leaks system resources. 
There is the chance that we have **too many files open** as an error here. 

Buffers are used to write to files, therefore, if we don't close, that data might not be saved. 
`close()` will flush the buffer an safely writes everything to disk. 

Read that amazing book about OS's that we have. 

A file descriptor, FD, is just a small integer that the OS uses to refer to an open file, I/O resource. 

Think of it like a ticket number: 
`int fd = open("file.txt", O_RDONLY);`

The kernel will find the file on disk. 

Allocating an internal structure (`struct file`) with: 
- File Path
- Current position (cursor)
- Open Mode (read/write/append)
- Buffer Pointers (perhaps)
Adding the structure to a table indexed by an integer : the file descriptor
The integer is returned to you. 

Remember that windows is written in `C`, and is most of Linux (if not in C++ too, and some parts of Kernel in Python too) . 

We can context manager this with `open()` etc.: 
The general idea is this: 

```
fh = open("fear.txt", "rt")

try: 
	for line in fh.readlines(): # get a list of lines in the file
		print(line.strip())
finally:  # do regardless of what happened before this
	fh.close()
```

Now the book is talking about the lazy evaluation, which was the whole point of `yield` etc. 
```
fh = open("fear.txt") # the default is rt

try: 
	for line in fh: 
		print(line.strip())
finally: 
	fh.close()
```
These both produce the same output. 

### Using a Context Manager to Open

```
with open("fear.txt") as fh: 
	for line in fh: 
		print(line.strip())
```

Remember that as we go into the object given by `open()` , then we use `__enter__()` and `__exit__()` as we leave. 

This means that we don't forget to use `fh.close()`. 


### Reading form and writing to a file

```
with open("print_example.txt", "w") as fw: 
	print("Hey I am printing into a file!!!", file=fw) # print directly into a file with file=fw
```

Another way: 
```
with open("fear.txt") as f: 
	lines = [line.rstrip() for line in f]
	
with open("fear_copy.txt", "w") as fw: # w - write 
	fw.write("\n".join(lines)) # at the end of each in the list, we put a "\n"
```


### Reading and Writing in Binary Mode
Notice that we have been writing in text mode so far, there is also binary mode. 
This means that everything is interpreted as chars. 

To handle files in binary mode, just put the `b` when opening them. 

```
with open("example.bind", "wb") as fw: 
	fw.write(b"This is binary data...")

with open("example.bind", "rb") as f: 
	print(f.read())
```

### Protecting against overwriting an existing file
`w` is used to truncate - meaning that the file is overwritten with an empty file, and the original content is lost. 

```
with open("write_x.txt", "x") as fw: # this succeeds
	fw.write("Writing line 1")
	
with open("write_x.txt", "x") as fw: # this fails
	fw.write("Writing line 2")
```
`x`- will work only if the file does not exist. 

So we might say: 
```
try: 
	with open("write_x.txt", "x") as fw: 
		fw.write("...")
except FileExistsError as e: 
	print(e)
```

Remember that we can use `with` with two things at once, `with open(...) as f1, open(...) as f2:`
### Checking for File and Dir Existence
Using `pathlib`

```
from pathlib import Path

p = Path("fear.txt")
path = p.parent.absolute()

print(p.is_file()) # True
print(path) # /Users/fab/code/lpp4ed/ch08/files
print(path.is_dir) # True

q = Path("/Users/fab/code/lpp4ed/ch08/files")
print(q.is_dir()) # True
```

`parent` we use to get the folder in which the file is contained. 

Then we use `absolute()` -> extract absolute path information. 

Read about `pathlib` documentation here: 
[Pathlib Docs](https://docs.python.org/3/library/pathlib.html)

Unrelated: however, `glob` using pattern matching to find **filenames** `*.txt`, is one example. 
`grep` however, is looking into files, eg. `grep "hello" file.txt`. 

### Manipulating Files and Directories
```
from collections import COunter
from string import ascii_letters

chars = ascii_letters + " "

def santizie(s, chars): 
	return "".join(c for c in s if c in chars)

def reverse(s): 
	return s[::-1]

with open("fear.txt") as stream: 
	lines = [line.rstrip() for line in stream] # Iterate through

with open("raef.txt", "w") as stream:
	stream.write("\n".join(reverse(line) for line in lines)) 

lines = [sanitize(line, chars) for lin ein lines]

whole = " ".join(lines)

# performing coms comparisons
cnt = Counter(whole.lower().split())

# printing the most common N words
print(cnt.most_commont(3))
```

For reference, `ascii_letters` looks something like this: 
`abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ`

Sanitize - takes a string, will create a string given, as long as the char is given in `chars`. 

`shutil` - high level operations on files, and collections of files. 

```
import shutil
from pathlib import Path

base_path = Path("ops_example")

# initial cleanup just in case
if base_path.exists() and base_path.is_dir(): 
	shutil.rmtree(base_path)

# create the dir
base_path.mkdir()

path_b = base_Path / "A" / "B"
path_c = base_Path / "A" / "C"
path_c = base_Path / "A" / "D"

path_b.mkdire(parents=True)
path_c.mkdir() # no need for parents now, as 'A' has been created. 

# add three files in 'ops_example/A/B'
for filename in ("ex1.txt", "ex2.txt", "ex3.txt"): 
	with open(path_b / filename, "w") as stream: 
		stream.write(f"Some content here in {filename}\n")
		
shutil.move(path_b, path_d)

#rename files
ex1 = path_d / "ex1.txt"
ex1.rename(ex1.parent/"ex1.renamed.txt")
```


Here we are declaring a base path, which will contain all the files, and the folders that we are going to creawte. 

Then we use `mkdir()` to create two dirs, `ops_example/A/B` and `ops_example/A/C`, we need to `parents=True` as we have made that in the one prior. 
 
We use `/` to concatenate dir names; `pathlib` takes care of using the right path separator for us, behind the scenes. 
Then we use a loop to create the three named files `.txt` files, which is a really cool way of doing it. 

Remember that one, I think the same thing with C++, we make a small container of all the names that we need and we are going to loop through the container. 

![[Pasted image 20250716143453.png]]
This is the end result from the book. 

### Manipulating Pathnames
Using `pathlib` a little bit more: 

```
from pathlib import Path

p = Path("fear.txt")

print(p.absolute())
print(p.name)
print(p.parent.absolute())
print(p.suffix)

print(p.parts)
print(p.absolute().parts)
readme_path = p.parent / ".." / ".." / "README.rst"

print(readme_path.absolute())
print(readme_path.resolve())
```
![[Pasted image 20250720185607.png]]

### Temporary Files and Directories
Sometimes we need to create temporary files and directories. 

```
from tempfile import NamedTemporaryFile, TemporaryDirectory

with TemporaryDirectory(dir=".") as td: # our context manager again
	print("Temporary Directory: ", td)
	with NamedTemporaryFile(dir=td) as t: # context manager again
		name = t.name
		print("Name: ", name)
```

Here we just creating a temporary dir, in the current one, hence the `"."`, and we create a temporary named file, then we just print that name. 

```
Temporary Directory:  C:\Users\easle\OneDrive\Desktop\ArmInterview\tmp6j2jauwp
Name:  C:\Users\easle\OneDrive\Desktop\ArmInterview\tmp6j2jauwp\tmp5ysltuey
```

### Directory Content
Python can also give us the contents of a directory. 
There are couple ways of doing that: 

```
from pathlib import Path

p = Path(".")

for entry in p.glob("*"): # globbing, remember is for files, not grepping, which is in files
	print("File: " if entry.is_file() else "Folder: ", entry)
```

Notice that `Path` objects have the `glob()`method. 

It obviously returns some iterator that we can iterate over. 

`entry` here is a subclass of `Path`

Remember that an iterable is the object that we can loop over such as `list, str, dict`. 

Iterator is that which has `__next__()`, meaning that it holds state. 

#### Quick Recap on Iterators
```
# when we write
for x in [1, 2, 3]:  # or we could do : for x in (1, 2, 3, 4) etc.  as tuples are iterables as well

# python will do this under the hood

_iter = iter([1, 2, 3])
while True: 
	try: 
		x = next(_iter)
		# loop body right here
	except StopIteration: 
		break
```

Remember with context managers there is this idea: 
Say: 
```
with SomeContext() as val: 
	do_something(val)

# what we really get
ctx = SomeContext()
val = ctx.__enter__() # going into the block here, that's the first thing we do
try: 
	do_something(val) # inside the block here
finally:              # do this no matter what
	ctx.__exit__(None, None, None) # or with exception info if raised
```
Remembering that `__enter__(self)` - runs at the start of the `with` block
`__exit__(self, exc_type, exc_val, exc_tb)` - runs at the end, even on error (which is what the error type is). 

Might as well also mention that `_attribute` (on underscore) is protected. 
`__attribute` is Name Mangling, Python will put the name of the class, this will change to `_ClassName__attribute` under the hood. 
These are not only private, but they are also intentionally harder to access.

Then we also have the Magic or Dunder Attribute, `__attribute__`. 

And might as well mention the idea, generator functions or generator expression, which is very similar to a list compression. 

They are similar to generators, `__iter__()` and `__next__()`. 

Whenever we use `yield` - Python pauses the function's state and resumes on the `next()` call. 

```
def count_up_to(n): 
	i = 1
	while i<=n:
		yield i
		i += 1
gen = count_up_to(3)

print(type(gen)) # <class 'generator'>

for num in gen: 
	print(num)
```

##### Continuing with Dir Content
We could do the same: 
Here `walk` - returns an `Iterator[tuple[Path, list[str], list[str]]]`
```
for root, dirs, files in p.walk():  # walk is similar to os.walk() walking throught the tree
	print(f"{root=}") 

	if dirs:  # if there are dirs
		print("Directories: ")
		for dir_in dirs: 
			print(dir_)
		print()

	if files: # if there are any files
		print("Files: ")
		for filename in files: 
			print(filename)
		print()
```

## File and Directory Compression
 There is the idea that we can create a `.zip` file and a `.tar.gz`

The difference between the two here, is that `.zip` - is archive and compression together. Archive means having one file that bundles multiple files/directories together, without necessarily compressing them. 

`.tar.gz` `.tar` - is the archiving process - hence the `ar` at the end. The compression `.gz` is at the end, shrinking the archive. 

**Archive** - grouping
**Compression** - shrinking

```
from zipfile import ZipFile

with ZipFile("example.zip", "w") as zp: 
	zp.write("Content1.txt")
	zp.write("Content1.txt")
	zp.write("subfolder/Content3.txt")
	zp.write("subfolder/Content4.txt")

with ZipFile("example.zip") as zp: 
	zp.extract("Content1.txt", "extract-zip")
	zp.extract("subfolder/Content3.txt", "extract-zip")
```

As you can see, that we have a context manager, that we use to write four files into a zip file, two of which of are in a sub-folder, to show how Zip can even preserve the full path. 

Then we open that compressed file, and extract a couple of files from it. 

Definitely would need to do more work on this, but this is just showing what is possible here. 

### Data Interchange Formats
Choosing the right format that we are going to exchange information and data is crucial. 

There has to be a flow, and an intermediate language between the two frontend and backends, that likely won't speak the same language. 

**Serialization** and **Deserialization**. 

The process of translating a data structure or object state into a format that can be stored (eg. in file or memory data buffer) or transmitted (over a computer network etc.) and reconstructed later (possibly in a different computer environment).

Choosing a language-agnostic format. 

Popular choices are XML, YAML, and JSON. 
XML - extensible markup language

YAML - yet another markup language

JSON - JavaScript Object Notation

`xml` and `json` modules on PyPI. 

There are couple different packages for YAML as well. 

XML tends to be quite verbose, and harder to read. 
JSON tends to be the victor when it comes to choosing. 

#### Working with JSON
Hugely popular, been around a long time. 

A collection of name/value pairs
Ordered list of values

There is a mapping between `dict` and `list` here in Python with JSON. 

```
import sys
import json

data = {
	"big_number" : 2**3141, 
	"max_float"  :  sys.float_info.max, 
	"a_list"     : [2, 3, 5, 7], 
}

json_data = json.dumps(data)
data_out = json.loads(json_data)

assert data == data_out # json and back, data matches
```

`json.dumps()` converting data into a JSON formatted string. 

Then `json.loads()` which does the opposite. 

This will reconstruct the JSON back into a python, usable object. 

This is a very big thing, we construct strings from Objects, and then we can take strings, that are in JSON format, and create an actual, usable object from them. 

This is what the JSON data would look like if wee just printed it. 

![[Pasted image 20250722004029.png]]
![[Pasted image 20250722004049.png]]

The similarity between JSON and Python is very close, however, we still need this serialization step right here. 

There is a difference, placing a comma on the list element of a dictionary, as is customary in Python, JSON will not like that. 

```
import json

data_in = { 
	"a_tuple" : (1, 2,3 ,4 ,5), # notice the , here
}

json_data = json.dumps(data_in) # taking in an object here
print(json_data) # {"a_tuple" : [1, 2, 3, 4, 5]}

data_out = json.loads(json_data)
print(data_out)  # {'a_tuple' : [1, 2, 3, 4, 5]}
```

Notice here, that we are using a tuple in the `data_in` -> however we get a `list` back, they are considered the same in JSON. 
This is something to consider, that we need to think about any data loss when we use this. 

It's clear that all objects in Python can be converted to JSON, and it's usually obvious, but not always obvious how they revert back to python objects. 

It can deserialize in a number of different ways. 

The reality of dealing with data interchange. 

We often need to transform our objects into a simpler format prior to serializing them with JSON. 

The simpler we can make it, the easier it will be to  represent that data in a format like JSON, of course there are limitations. 

### Custom encoding/decoding with JSON
Encoding/Decoding - Synonyms for - Serializing/Deserializing

```
import json

class ComplexEncoder(json.JSONEncoder): 
	def default(self, obj): 
		print(f"ComplexEncoder.default: {obj=}")
		if isinstance(obj, complex): 
			return { 
				"_meta" : "complex", 
				"num" : [obj.real, obj.imag],
			}
		return super().default(obj)

data = { 
	"an_int"    : 42, 
	"a_float"   : 3.14159265,
	"a_complex" : 3 + 4j,
}

json_data = json.dumps(data, cls=ComplexEncoder) # passing in the object that has default
print(json_data)
```

Honestly, there is more to it here, however, I don't want to go into too much detail about this before I have to utilise what is going on here. 

## I/O, Streams, Requests

I/O broadly communication to the outside world. 
### Using an in-memory stream
RAM being much faster than hard disk. 

```
import io

stream = io.String()
stream.write("Learning python programming.\n")
print("...", file=stream)

contents = stream.getvalue()
print(contents)

stream.close()
```

Here, we are using `StringIO`, an in-memory buffer in which we have written two sentences, using to different methods, as we did with files in the first example of this chapter. 

It acts like a file, however, it reads and writes to a string in memory. 

This is great for testing file code without real files. 

Pretending that a string is in a file. 

This will be quicker, than opening, using and closing files. 

eg. 
```
from io import StringIO
print(f.readline())  # 'hello\n' 
f.write("new line")  # Appends to the in-memory string 
```


```
with io.StringIO() as stream: 
	stream.write("Learning PYthon Programming.\n")
	print("Become a python ninja!", file=stream)

	contents = stream.getvalue()
	print(contents)
```

### Making HTTP Requests

Using the `requests` module. 

Need `pip` to install `requests`. 

![[Pasted image 20250722124146.png]]

```
def get_content(title, url): 
	resp = requests.get(url)
	print(f"Response for {title}")
	print(resp.json())

for title, url in urls.items(): 
	get_content(title, url)
	print("-" * 40)
```

Above, is a dictionary of URLS, and we do HTTP requests for each one. 

We are performing a `GET` request through `requests.get(<some url>)`. 

Then we use `resp.json()` to get the json of the response, and the `title` as well. 

We get a `response` object, that encapsulates all the data that was returned by the server, that we performed the get request from. 

The response object, might be a JSON object. 

##### What is a `GET` Request
This is merely just to retrieve data. 

The server will return a response, that can be in certain specified formats: 
- HTML (web pages)
- JSON (apis)
- XML (older APIs (seems like XML doesn't get much love, apart from in games))
- Plain Text, Images, Files etc. 

There are two ways to know about the content-type in the header. 

`Content-Type : application/json`. 

An API in this sense : sending a request to a server to ask for data or perform an action. 

We use requests in order to ask the server something, then we get the response in some format. 

A very simple idea: 
```
import requests

response = requests.eget("some url")
data = response.json()
print(data["name"])
```

`GET` is only one of the HTTP verbs, one of the most commonly used ones. 

`POST` this is what we do when we need to send Data to the server. 

Every time that you submit a form on the web, you're making a `POST` request. 

```
import requests

url = "some url"
data = dict(title="Learn Python Programming")

resp = requests.pos(url, data=data)
print("Response for POST")
print(resp.json())
```

Notice how we get something back. 

It is definitely worth going through the `requests` and go through what is there. 

Again, this is not something that I think I'm going to use right off the bat, therefore, we will go into detail when we get there. 

#### Persisting Data on Disk
### Serializing Data with Pickle
Converting data into `byte` streams, and vice versa. 

There is a slight overlap, between `pickle` and `json`. 

Json is a text format, that is obviously readable and understandable, most importantly, language-agnostic. 

`pickle` makes unreadable, python specific, translate to bytes. 

Unpickling erroneous or malicious data from an untrusted source can be dangerous. 

We need to be careful of the code that comes in, from an untrusted source. 

Using a cryptographic signature to ensure that no one has messed with. 

```
import pickle
from dataclasses import dataclass

@dataclass
```

[Dataclass from Docs](https://docs.python.org/3/library/dataclasses.html)

`@dataclass` - will add generated magic methods (special methods)


```
@dataclass
class InventoryItem: 
	"""Class for keeping track of an item in inventory"""
	name : str
	unit price : float
	quantity_on_hand : int = 0
	
	def total_cost(self) -> float: 
		return self.unit_price * self.quantity_on_hand
```

The `@dataclass` aspect will add something along the lines of: 
```
def __init__(self, name: str, unit_price: float, quantity_on_hand: int = 0): 
	self.name = name
	self.unit_price = unit_price
	self.quantity_on_hand = quantity_on_hand
```

`@dataclass` does this for us. 

See [[OOP, Decorators, and Iterators]]

Therefore a continuation: 
```
import pickle
from dataclasses import dataclassf

@dataclass
class Person: 
	first_name : str
	last_name  : str
	id         : int
	 
	def greet(self): 
		print(
			f"Hi, Iam {self.first_anem} {self.last_name}"
			f" and my Id is {self.id}"
		)

people = [ 
	Person("Obi-wan", "Kenobi", 123), 
	Perons("Anakin", "Skywalker", 456), 
]

with open("data.pickle", "wb") as stream:  # we have to open for writing binary "wb"
	pickle.dump(people, stream) # only engages with a binar file

with open("data.pickle", "rb") as stream:  # getting it back from binary
	peeps = pickle.load(stream)

for person in peeps: # go through the translated data, python will create from binary 
	person.greet()
```

Again, `dump` to put binary into the file, then `load` in order to get it back out, this was done at ALevel should be quite simple 

### Saving Data with `shelve`
`shelf` is a persistent dictionary object. 
This is rarely used practice, so this will be quick. 

```
import shelve

class Person: 
	def __init__(self, name, id): 
		self.name = name 
		self.id = id

with shelve.open("shelf1.shelve") as db: 
	db["obi1"] = Person("Obi-wan", 123)
	db["ani1"] = Person("Anakin", 456)
	db["a_list"] = [2, 3, 5]
	db["delete_me"] = "we will have to delete this one..."
	print(
		list(db.keys())
	) # ['ani', 'delete_me', 'a_list', 'obi1']

	del db["delete_me"] # gone

	print(list(db.keys()))
```
There is more here, however, it not being a major part, again, we will have a look when we need to.


### Saving Data to a Database
We are going to work in an in-memory database, which will make things simpler for us. 

IMDB - In Memory Data Base, this relies on RAM for data storage, rather than disk-based storage. 
Very fast access, there are trade off in persistence and volatility. 

Redis, H2 (in-memory mode), MemSQL (now known as SingleStore). 
Caching, real time analytics, temporary session data. 

Hybrid - RAM for active data, persistence of full data to disk in the background. 
SQLLite (in-memory mode with disk backup), PostgreSQL with RAM chaching, SAP HANA. 

In-Memory Caches (Not Full Databases)
Designed for speed, not full database features. 

And more. 

### Configuration Files
Crucial components of many Python applications. 

Separate the main app, from settings and parameters. 

Helpful for maintaining, managing, and distributing software. 
Especially if the app needs to run in different environments. 

We can change behaviour of an app, without modifying source code. 

This is crucial and a meme that we've seen a lot, that we find API keys on replit and github. 
These need to be kept separate. They should never be published of course, however, lots of people don't even think about this. 

##### Common Formats
There are several formats, each with its own syntax and features. 

`INI, JSON, YAML, TOML`  and `.evn` files. 

We've gone through JSON and some YAML, there are modules for each. 

##### The INI Config Format
Simple text file, divided into sections. 
Each section contains properties expressed in the form of key/value pairs. 
![[Pasted image 20250724131224.png]]
In order to read this into an application, we can use `configparser` from standard lib. 

It will create an object similar to that of a dictionary. 

`import configparser`

![[Pasted image 20250724131553.png]]

Could do more research here. 

#### The TOML Format
A richer set of features, compared to the INI one. 

Honestly, both just need more research if we are going to use it. 





