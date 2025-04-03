#RVO #NRVO
## Return Value Optimization
## Name Return Value Optimization

They both essentially do the same thing. 
The idea here is: 
```
set<int> s1 = randomSet(10, 100);
```
Where `randomSet(10, 100)` will return, by value, a `set<int>`, holding random numbers in a range, denoted by the args handed into it. 
Instead of creating a local return object and then moving/copying it in place of the function call, this optimization instantly creates it in the right place. 
Something to think about when we do what we have done above. 
You might think that our `randomSet` set, that it returned, will be an rvalue and then we use a rvalue copy constructor, or mostly just the copy initialization (copy constructor) will be used. 
```
Set(Set& other) : data(other.data) {};
```
Just a mock implementation. 

This was implemented into C++17, under the notion of copy/move elision, meaning that under some circumstances, you do not have to have a copy or a move constructor for certain classes and types that you have constructed. 

[Stack Overflow](https://stackoverflow.com/questions/38043319/how-does-guaranteed-copy-elision-work)
Without non-guaranteed copy elision rules, this will create a temp, then move from the temp into the functions' return value. 
```
T func() { return T();}
```

The issue here, especially when it comes to `T`, this means, that T will have to have a suitable `move` constructor, even if it's not needed, and not moved. 

Some refinement on the `prvalue`, the issue here is that we think of it as a temporary, however, since C++17, the thing is that they are only defined as with the ability to materialize a temporary, but isn't one yet. 
Say i f you use a prvalue to init an object of the prvalue's type, then no temp is materialized. 
Then `return T();` this initializes the return value fo the function via a prvalue. 

Since we are returning `T`, no temp is created; the init of the prvalue simply directly initializes the return value. 
Therefore, no temp, that value is just used to go straight into the construction of that variable. 

Therefore when we write something like this: 
`T t = Func();`, the prvalue of the return value directly initializes the object `t`;
There is no create a temporary copy/move stage. 