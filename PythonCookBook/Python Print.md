I noticed that there are some occurrences with `print()` where the elements don't print, but rather the `type` of the object itself. 

#### What does `print()` do? 
When we do `print(thing)` what python is actually doing is `print(str(thing))`. 

We are actually seeing the result of `thing.__str__()` or `__repr__()` if `__str__` is not defined. 

The reason that we can print a `list` to brazenly is that it does define the `__str__()`. 

There is an idea of eager containers - however, I have not seen this anywhere else : however, the idea is that they know all their elements at the time of print, and know their length as well. Which raises the idea 


#### Why Some `things` Don't Print as nicely
`map` and `generator` objects are lazy iterators. 
**THEY DON'T STORE THE ELEMENTS THE SAME WAY THAT A LIST MIGHT, THEY COMPUTE THEM ONE BY ONE AND THEY CAN BE EXHAUSTED**. 

Therefore - they won't print - lists know how many elements and which elements are in there, therefore they can print, any generator etc. doesn't and we don't want to exhaust. 
That's why their string `__str__()` has to look like this: 
`"<map object at 0x...>"`

In order to actually get the actual elements, it would have to exhaust the generator/iterator - which is not what we want at all right. 

`list(generator)` will exhaust - so be careful with that - and generate the values, so that they are known in the `list` - this means that we can print them, using list's `__str__()`. 

In my own personal work, I get this: 
```
F:\C++>git status
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)
```
Which is very explicit in the way that it is expressed. 

