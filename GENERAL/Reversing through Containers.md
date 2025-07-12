This was literally devised for this. 

[ReverseIterator](https://en.cppreference.com/w/cpp/iterator/reverse_iterator)

Defined in header `#include <iterator>`;
 ```
vector<int> v = {0,1,2,3,4,5,6,7,8,9,10};
 
    for (vector<int>::reverse_iterator riter = v.rbegin() ; riter != v.rend(); ++riter){
        cout << *riter << "   ";
    }
    cout << endl;
```


Array: 
```
int arr[] = {0,1,2,3,4,5,6};
    for (auto aEnd = (end(arr)-1); aEnd != (begin(arr)-1); aEnd--){
        cout << *aEnd << "  :  ";
    }
    cout << E;
```
This is a terrible way of doing it lol, but you get the concept.

[Reverse Algorithm](https://en.cppreference.com/w/cpp/algorithm/reverse)
Requires the `#include <algorithm>`
This has the same time complexity for temp swapping all the variables in the array. 
```
int arr[] = {0,1,2,3,4,5,6,7,8};
std::reverse(std::begin(arr), std::end(arr));
for(auto c : arr){
    cout << c << "   ";
}
cout << E;
```


```
nlist = [x for x in range(10)]
rlist = list(reversed(nlist))
print(rlist)
```
This is it in python.


This is another way: 
```
std::array<std::size_t, 10> a = {0,1,2,3,4,5,6,7,8,9};
    for (std::size_t i = a.size(); i--;){
        cout << a[i] << "  ";
    }
    cout << endl;
```
The loop condition looks a little weird, however, it works just fine; 
The initializer section is simple, however, the conditional aspect of it looks odd. However, think of it like testing i, then decrementing i. 

So we test to see whether i is valid, valid to the type that we have initialized it to be, which is int. It's a cool illustration of code. 

You can reverse iterate through list as well. 
