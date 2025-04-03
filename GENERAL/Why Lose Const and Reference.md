Why do we lose `const` or the reference when we copy from these types of variables. 
This is because these things apply to the variable itself, not to the underlying type, so when we copy, of course we are going to lose those top level features. 

Similarly, when we copy a reference, then we are copying the value itself, not the variable. 
Think about it like this, that references are not first class objects, that when we copy one, we have to go the first class object that it refers to in order to copy anything. 

When we copy a `const` object, we are copying the object, not whether we can access it or not. This new object may have different access rights. 

Since a reference cannot exist without being tied to an object, it has to be initialized. Therefore, when we copy a reference, we really want to copy that thing that the reference is pointing to. 

```
    std::string f("hello There");
    std::string const& rF = f;
    auto n = rF; // std::string n; no const no reference
```