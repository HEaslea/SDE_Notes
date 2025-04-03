When you write an implementation file (`.cpp`, `.cxx`, etc) your compiler generates a **translation unit**. This is the source file from your implementation plus all the headers you `#include`d in it.

_Internal linkage_ refers to everything only **in scope of a translation unit**.

_External linkage_ refers to things that exist beyond a particular translation unit. In other words, **accessible through the whole program**, which is the combination of all translation units (or object files).
Remember that if we do not explicitly say what linkage we are using, then by default: 

`extern` - for non-`const` symbols
`static` - for `const` symbols. 

Get this tattooed on your forehead. 


