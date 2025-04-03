#override #final 
`override` declare, expressing that this function will be overriding a function in the base class. 

A `final` declared, expresses that his function will override a virtual if it can, however, it will be the last override, as it itself cannot be overridden. 
These are useful to use, as `override` will allow the compiler to tell us if we get any errors. 
`final` can also be used for some optimization too. 

Both of these variants are equivalent:
```
void func() final; // but please only use this
virtual void func() final override;
```

The same is true for classes as well: 
```
class Base{ 
};

class Derived Final : public Base{ 
};

// the following would cause an error in compilation: 
// class AnotherDerived : public Derived{ 
};
```

Final doesn't make too much sense, Python is a late binding only language, however in C++ doesn't make too much sense because : [[Template Method]]
