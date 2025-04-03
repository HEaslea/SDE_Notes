So we have wondered why it is that class member functions are implicitly inline when they are defined in the header file: 
Well shouldn't this be obvious: **Redefinition Errors**. 
If we declare in the header, then define in the CPP file, well that's fine. 
Remember, `inline` allows our compiler to have multiple definitions of the same function, in multiple translation units.

Although we don't write it, if we have a member function, and define it in the header file, then we need it to be inline, the compiler doesn't want to rely on your dumbasses to do it, therefore, it will do it itself. 

```
class MyClass 
{ 
public: 
	void myFunction() { /* ... */ } // implicitly inline 
};
```

Now here's the tricky part, and this is demonstrated more-so in template functions: 
```
// in Hpp file
class MyClass 
{ 
	void myFunction();
};

inline void MyClass:myFunction()  // need the inline 
{ 
	// function body 
}
```