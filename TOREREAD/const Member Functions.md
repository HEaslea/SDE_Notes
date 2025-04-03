A member function that may not change an object's ordinary data members. The `this` pointer in a `const` member is a pointer to `const`. A member function may be overloaded depending on whether the function is `const`. 

```
// pseudo code illustration of how the implicit this pointer is used
// this code is illegal, we may not explicilty define the this pointer ourselves
// note that this is a pointer to a const because isbn is a const member

std::string Sales_data::isbn(const Sales_data *const this)
{ return this->isbn;}
```

