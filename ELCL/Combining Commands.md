Command Line, supplies lots of little commands, that we can chain together in order to do bigger stuff. 

There's the idea of pipes, a feature that connects the output of one command to the input of another. 

#### Input, output, Pipes
Linux has fancy names for input and output, just like C++. 

`stdin` - the stream of input that linux reads from keyboard. Whenever we type, we are supplying data on stdin. 
`stdout`- out to the display, say that we use `ls` - the will use `stdout`. 

We can connect the `stdin`of one to the `stdout` of another. 

If you use `ls -l /bin` then you might get a load of files, so we may need less, however, there is already a command for that `less`, this means that we can scroll through the files slower, then press q to quit. 

Therefore we can combine the two using the pipe idea, as `ls` outs to `stdout` and `less` takes from `stdin`. 

`ls -l /bin | less` just the first, as we give the command into less. 

Any command line with pipes is called a **pipeline**. 

The commands are not aware that they are writing to each other, they just know that they are writing to `stdout` and from `stdin`. 

##### What is a Command

A Program - a single executable program named and executed by a single word, such as `ls` or a similar feature built into the shell, such as `cd` (shell builtin). 

A Command - a program name, optionally followed by arguments, such as `ls -l /bin`
Here `-l` is the idea of long listing. 
`/bin` listing from the `bin` dir. 

![[Pasted image 20250720234347.png]]

### Six Commands to Start
`wc, head, cut, grep, sort, uniq`

There are options and modes for each one. 

Just quickly, if you wrote a program in C++: 
```
int main(int argc, char* arg[])
```

Then say that we read: `./myprog -v --file=data.txt`
We might have something like: 
```
argv[0] = "./myprog"
argv[1] = "-v"
argv[1] = "--file=data.txt"
```

Then we can just iterate through the small number of options given, in that, even with the highest number of options that we could use, iterating through is very quick. 

```
for(int i = 1; i < argc; ++i)
{ 
	bool verbose = false;
	
	if(std::strcmp(argv[i], "-v") == 0)
	{ 
		verbose = true; 
	}
}
```


Back to it. 

If you need the manual about anything just use `man wc`. 
This will work on the system that you use, Ubuntu. 

----
`wc` - prints the number of lines, words, and chars in a file. 
`-l` - for lines. `-w` - for words. `-c` - for chars. 

`wc` for word count. 

To ask and answer the question about how many files are in this dir. 
Then we can do. 

`ls -1` list files in a single column. 

Then we can pipe that, given that they are going to be single words, into `wc -l`. 
The thing to remember here is that we are not piping `stdin` as the contents of the files, but rather the output of `ls -1` which is just going to be a single column of single "words" that are the file names. 

That is a recurring theme, that we have to remember that w are only piping what would actually come out of the command, not some hidden, or something in the files etc. 

We can even pipe the `wc` into itself. 

`wc animals.txt | wc -w`

----- 

Weirdly, `ls` is the only command that does know whether `stdout` is the screen of redirected using a pipe or otherwise. 

This is for user-friendliness. 

When it knows that it is the screen, it will organise in multiple columns for convenient reading. 

When redirected, `ls` produces a single column. 

Say `ls /bin | cat`. 
Will give a column. 

![[Pasted image 20250721000347.png]]

The first, gives a column list like this, then the second will give a single column, as if you did `ls -1`. 

----

`head` -  will print the first lines of a file. 

`head -n3 animals.txt` will print the first 3 lines. 

Omitting `-n`,it will default to `-n10`. 

`head -n3 animals.txt | wc -w`
word count for the first 3 lines of `animals.txt`. 

Remember as well, that `stdin` of the next command is the `stdout` of the first. 

`ls /bin | head -n5` 
Get the first 5 lines of `stdout` of `ls` - list - of the `bin` folder. 

---- 
`cut`

Similar, but will print a column from a file. 

eg. Printing all book tiles from `animals.txt`, that appear in the second column. 
`cut -f2 animals.txt`. 

Then this can be shorted using `head` as well. 

![[Pasted image 20250721203707.png]]


Or by a numeric range
`cut -f2-4 animals.txt | head -n3`

`f` - for field

`c` - for character. 

---- 

If you are tired to repeating a lot of command, just use the up and down array to scroll through the recent commands. 

---- 

`grep`

Printing lines that match a given string: 
Global Regular Expression Print
If we wanted to see the lines in `animals.txt` that contain `Nutshell` - then we would do
`grep Nutshell animals.txt`

We can also print lines that do not match a given string with the `-v` option
The powershell version of thsi is `Select-String`. 

For any file that ends in `.txt` we just use: 
`grep someSearchTerm *.txt`

Again, we use the idea of `stdin` and `stdout` in order to use `grep` with more complicated search terms. 

`ls -l /usr/lib`

![[Pasted image 20250721205151.png]]Remember that `-l` stands for long-format - this gives us the letter at the beginning. 

Then we can use `cut` to isolate what might or might not be a `d` at the first char, ie. at the beginning. 
Using: 
`ls -l /usr/lib | cut -c1`
Get all the first chars of the list, using the long format. 

Then we could use grep to keep only the lines that contain `d` 

`ls -l /usr/lib | cut -c1 | grep d | wc -l` 
Then clearly just `wc` the number of lines in there. 
This will just give a number, which is key. 

---- 
`sort`
Does exactly what you were hoping for. 

Reverse the order `-r`. 

We can use `cut` in order to get a field, then we can sort that too. 

`cut -f3 animals.txt`
This will sort everything in the 3rd field across. 

`-n` option, sorting numerically, not alphabetically. 

Then we can seven use head, to get the first line out of a sort. 

`cut -f3 animals.txt | sort -nr | head -n1`

Just in general, we use, `... | sort -nr | head -n1`, to get the maximum value

Then `... | sort -n | head -n1` to get the maximum. 

![[Pasted image 20250721210904.png]]
This will be increasing useful. 

---- 
`uniq` - detecting repeated, adjacent lines in a file. 

By default, it will remove the files. 

![[Pasted image 20250722000637.png]]

`cat` - just gets the contents of a file.
In that we might do, 
`cat file1.txt file2.txt > combined.txt` so get the two, and combine them into a `combined.txt` file. 
`cat` stands for concatenate. 

So imagine that we have grades throughout a list, and we want the grades at the beginning. 
`cut -f1 grades | sort` that will sort them properly, then we can use `uniq -c` in order to count the occurrences of each one. 

so then we could do a sort on top of that as well and get the most abundant grades.

`cut -f1 grades | sort | uniq -c | sort -nr | head -n1`

This one is pretty crazy here, however, we got there. 

### Detecting Duplicate Files
In order to do this, we can use `md5sum` that will create a check sum, a 32 char string from the file. 

`md5sum imagine001.jpg`.

If you have two files that are the same, then their checksums are gonna be the same. 

If there are no files that are the same, then `uniq -c` will give all 1s. 

Give this a shot: 
`md5sum *jpg | cut -c1-32 | sort | uniq -c | sort -nr | grep -v "      1"`