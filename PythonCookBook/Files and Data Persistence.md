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

