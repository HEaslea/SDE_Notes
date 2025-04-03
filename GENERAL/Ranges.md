`#include <ranges>`
Is an extension and generalization of the algos and iterator libraries that makes them more poweful by making them composable and less error prone. 
Ranges are a further abstraction on top of: 
- `[begin, end)` - iterator pairs, all algs that take iterator pairs, will notw take ranges (eg. `ranges::sort`). 
- `begin + [0, size)` - counted sequences, range returned by `views::counted`
- `[begin, predicate)` - conditionally terminated sequences . eg ranges returned by `views::take_while`
- `[begin, ..)` - unbounded sequences, eg. ranges returned by `views::iota`. 


Didn't even realize that we can use namespace alias as well: 
```
namespace views = ranges::views;
```

![[Pasted image 20241117203930.png]]

Given a `const` container, then the iterators will obviously be `const` as well. 

