[CppRef for this](https://en.cppreference.com/w/cpp/language/cv)
## Const Object
A nonmutable subobject of a const object too. 
Non-modifiable, will result in a compile time error. 

## Volatile Object
Again, a subobject object of a volatile object will be volatile as well. 
This too, a mutable subobject of a const-volatile object. 
Accessing volatile objects cannot be optimized out or reordered with another visible side effect that is sequence before or sequenced-after the volatile access. 
Good for communication with a signal handler, but not with another threawd. 
