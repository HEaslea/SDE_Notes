#readonlyalgorithms
The `find` function would be one of these, as is the `count` function. 
Another is `accumulate`, which is defined in the `numeric` header. 
The `accumulate` function takes three arguments. 
The first two specify the range to sum. 
The third is an initial value for the sum. 

```
// sum the lemeents in vec starting the summationw with the value of 0 
int sum = accumlate(vec.cbegin(), vec.cend(), 0);
```

The type of the third argument to `accumulate` determines which addition operator is used and is the type that `accumulate` returns. 

### Algorithms and Element Types
The elements in the container must match or be convertible to the type of the third argument. 
Elements in `vec` might be `ints`, or they might be `double, long long` etc. or any other type that can be added to an `int`.

`string` has a + operator too, we can concatenate elements of a vector of `strings` by calling `accumulate`. 
```
string sum = accumulate(v.cbegin(), v.cend(), string(""));
```
Important to note here that we explicitly pass a string into the third parameter. 
Passing the empty string as a string literal would be a compile-time error:
As the string literal is a const char*, and they don't have a + I don't think. 

If I look at this: 
```
auto s = "hello "; // s is a const char*
auto f = "world"; // also a const char*
auto n = s + f; // compile time error as const char* doesn't have a +
// error : no + on const char*
```


Ordinarily, it is best to use `cbegin()` and `cend()` for  read only algorithms. But if you plan to use the iterator returned by the algorithm to change an element's value, then you need to pass `begin()` and `end()`. 

### Algorithms that Operate on Two Sequences
Another read-only one is `equal`, letting us determine whether two sequences hold the same value. 
Comparing elements will require us to use the `==` here. 
Therefore we could have `roster1` be of `vector<string>` and `roster2` and `list<const char*>`.
To do something like this: 
```
// roster2 should have at least as many elements as roster 1
equal(roster.cbegin(), roster1.cend(), roster2.cbegin());
```
You might notice that the third arg is a little weird in that it doesn't relate to the end of `roster2`. The algorithm will presume that there is a corresponding element for each of those elements in the second sequence. 
Algorithms that take a single iterator denoting a second sequence *assume* that the second sequence is at least as large as the first. 

