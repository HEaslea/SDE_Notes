Whenever we include a h file in C++, think about the include graph. 
In a larger project this will be ridiculously huge, and it will grow and grow. 
There is the use of `#pragma once` which will do essentially the same thing as 
```
#ifndef __HEADER_A_HPP__
#define __HEADER_A_HPP__

... 

#endif
```

### The Double Guard
There is an idea that in order to reduce cyclical include paths, we can speed up our code as well, by using this idea. 
>Whenever we include this header, along with its header guard, we will essentially write another guard

```
#ifndef __HEADER_A_HPP__
#include "header_a.hpp"
#endif
```

So this is wherever that we would want to include this header file. 

What does this actually do. It would seem obvious that this would prevent further inclusion into a file that was already included, however, there is more. 

There is an argument to be made that this greatly reduces compilation speed. 

The reason for this is that when we include, we still have to open that file in order to see whether we have that definition or not, however, having this "redundant" include guard here, we don't have to open the file in order to see whether we should include it or not. 
Back in the day, files were much slower to open than normal. 
However, we don't really need them in this day and age, as the compiler, which most are, pretty good at remember where and what they've seen. 
That extra layer is not really needed anymore. 

