We know about compression, just taking a file and making it smaller, however, archiving is taking those files and turning them into one single file. 

Doesn't necessarily make the file/s smaller.

#### Linux
`tar` - short for `tape archiver`, originally made for writing files to magnetic tape. 

Collects many files/dirs into one continuous stream of data. 

Preserves the file system metadata (permissions, ownership, symbolic links, modification times etc.). 

Keeps the structure intact as well.

There will be no compression by default. 

`tar -h` to get all the details

We can combine the tow together so that we have an archive and a compression at the same time: 
```
tar -czf backup.tar.gz myfolder/  # gzip  compression
tar -czf backup.tar.bz2 myfolder/ # bzip2 compression
tar -czf backup.tar.xz myfolder/  # xz    compression
```

Then use `-x` to unpack it. 

### Compressed Archives
They're weird to look at first. These files are usually written as `.tar.gz`. 

Just like with most code, we have to work from the right side to the left. 
We get rid of the `.gz` first and then the `.tar`. 

```
gunzip file.tar.gz
tar xvf file.tar
```

Then to get to that `.tar.gz` it's clear that we `tar` first and then `gz`, or just use the commands that we have earlier. 

 `gzip` to compress.

## `zcat`
This is the best way of doing it, that puts it into one, without using up too much time. 

`zcat file.tar.gz | tar xvf -` remember that `x` for `tar` is to unarchive. 

Or just use the two that we have earlier: 
`tar ztvf file.tar.gz`

A `.tgz` is the same as a `.tar.gz` file. 
