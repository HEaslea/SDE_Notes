#lambdaexpressions

The predicates that we have passed in the last bit [[Customizing Algorithm Operations]], we would have to pass a unary or a binary predicate, depending on the algorithm. 

The name lambda comes Lambda calculus, a mathematical system that was used to explore the fundamentals of math in order to reduce the complexity of large maths sums, into one "simple" function.

We will revise our program to report how many words are of a given size or greater. 
We'll also change the output so that it prints only the words of a given length or greater. 

The sketch of the function, named `biggies` might look like so: 
```
void biggies(vector<string> &words, vector<string>::size_type sz){ 
	elimDups(words); // put words in alphabetical order and remove duplicates
	// resort by length, maintaining an alphabetical order among words of the same length
	stable_sort(words.begin(), words.end(), isShorter);
	// get an iterator to the first element whoe size() is >=sz
	// comput the number of eleents with size >= sz
	// print words of the given size or longer, each one followed by a space
}
```

Our first issue is finding the first element in the `vector`, that has the given size. 
Once we know that element, we can use its position to compute how many elements have that given size or greater.
We can use the `find_if` to find an element that has a particular size. 
The third argument here is a predicate, which is called one each element. 
Returning the first element for which the predicate returns a nonzero value, or its end iterator if no such element is found. 

`find_if` takes a unary predicate - any function that we pass to `find_if` must have exactly one paramter that can be called with an element from the input sequence. 
There is no way to pass a second argument representing the size. 


### Introducing Lambdas
#introtolambdas
We can pass any kind **callable object** to an algorithm. 
An object or an expression is callable if we can apply the call operator to it. 
That is if `e` is a callable expression we can write `e(args)` where `args` is a comma, separated list of zero or more arguments. 

The only callables we have found so far are functions and function pointers. 
Classes that overload the function-call operator, covered much later on and **lamdba expression**, will also fulfil this list. 

It represents a callable unit of code. 
It can be thought of as: 
> **unnamed inline function**

It has a return type, a parameter list, and an function body. 
Unlike functions, lamdbas can be defined inside a function. 

They have the form: 
```
[captuire list] (parameter list) -> return type { function body };
```
look at that, no name. Also have the same idea of
```
auto FunctionName(paramter list) -> return type
```
This here is an actual function, nothing to do with lambdas, just presenting the idea that you can represent the return type of a function with the `->`. 

Capture list, is often empty, is a list of local variables that are defined in the enclosing function; return type, parameter list and function body are the same as in any ordinary function.  They must use a trailing return to specify its return type. 

We can omit either or both of the parameter list and return type but must always include the capture list of and the function body. 

```
auto f = [] {return 42};
```
So whatever we do we have to have the capture list and the function body. 
This could also have been : 
`auto f = []() -> int { return 42; };`

The capture list is a little odd, it's like taking a snapshot of the variable in it at the time:
```
int x = 10; 
auto f = [x]() -> int { return x * 10;};
cout << f() << endl; //== 100
x = 5;
cout << f() << endl; // == 100 still as x was captured at 10
```

This might seem a little odd, however, capturing can be pretty useful when we pair it with certain expressions: 
```
void asyncOperation(std::function<void()> callback) { 
	// simulating an asynchoronous operation
	// I/O operation or a network request
	cout << "Performing async operation... \n";
	std::this_thread::sleep_for(std::chrono::seconds(1));
	callback();
}

int main { 
	int value = 5;
	// catpure value by value in the lambda
	auto lambda = [value] () { 
	std::cout << "Value capture by lambda: " << value << "\n";};

	asyncOperation(lambda);

	cout << "Continuing with program... \n";
}
```
The very least is the capture list and the function body; 

#### Passing Arguments to a Lambda
```
[](const string &a, const string &b) { return a.size() < b.size(); }
```

The empty capture list indicates that this lambda will not use any local variables from the surrounding function. 
```
auto isShorter = [] (const string& a, const string& b) { 
	return a.size() < b.size(); };
```
Will return true if a is shorter than b. 

We can write our `stable_sort` to use this lambda as follows: 
```
// sort words by size, but maintain the alphabetical order for words of the same size
stable_sort(words.begin(), words.end()
	[](const string& a, const string& b) -> bool {
		return a.size() < b.size();});
```
The lambda function will be called on every single element. 

#### Using the Capture List
Although a lambda might appear in a function, it can use variables local to that function `only` if it specifies which variables it intends to use. 
A lambda specifies the variables it will use by including those local variables in its capture list. 
This list direct the lambda to including information needed to access those variables within the lambda itself. 
In this case, our lambda will capture `sz` and will have a single `string` parameter. 
The body of our lambda will compare the given `string`'s size with the captured value of `sz`: 
```
[sz](const string& a)
	{ return a.size() >= sz;};
```
Inside the capture list, is a comma separate list of names defined in the surrounding function. 
As we have captured `sz` the body of the lambda may use `sz`. 
The lambda does not capture `words`, and so has no access to the variable. Had we given our lambda an empty capture list, our code would not compile. 
I think because the main idea is that it's a function, and unless we have access to the variable, by passing it in, or if the variable is global, then we cannot use it at all. 

![[Pasted image 20240325005528.png]]
#### Calling `find_if`
Using this lambda, we can find the first element whose size is at least as big as `sz`: 
```
// get an iterator to the first element whose size() is >= sz;
auto wc = find_if(words.begin(), words.end(), 
	[sz](const string& a) -> bool
		{ return a.size() >= sz;});
```

Compare the number of elements with size >= sz
```
auto count = words.end()-wc;
```
This will give us the number of elements with size >= sz;

### The `for_each` algorithm
This will just go through each element and call a function on them. 

```
// print words fo the given size or longer, each one followed by a space
for_each(wc, words.end(), 
	[](const string& a){cout << s << "";});
cout << endl;
```
This will print all of the words that are above a certain length in the container. 
We use it only for nonstatic variables defined in the surrounding area. 

![[Pasted image 20240325011354.png]]
```
void ForEach(const std::vector<int>& values, void(*func)(int)){ // pointer to function
	for(int value: values){ 
		func(values);}
}

int main() { 
	std::vector<int> values = {1,5,4,3,2};
	ForEach(values, [](int value) -> void { std::cout << "Value: " << value << endl;});
}
```

[Cherno Video about Lambdas](https://www.youtube.com/watch?v=mWgmBBz0y8c&t=97s)

What if we wanted to put outside variables into the instructions of the lambda, as we are constructing a function that will then get called later, if we use variables that are outside of our lambda. 

I think the idea of them is that the variables that are not global etc. might not be accessible to the lambda function when it is used. We might pass that lambda function to another function and use it there, like in the example above, which incidentally does not have a capture list: 
We can also use references if we want to use those values directly. 

```
int x = 5, y = 10;

auto add = [=]() { // pass in eveqrything by value
	return x + y;
};

auto multiply = [&]() { // pass everything by reference
	return x * y;
};

x = 7; 
y = 15;

cout << "Addition Results: " << add() << endl;
cout << "Multiplication Result: " << multiply() <<endl;
```
Then the output will be:
![[Pasted image 20240325013251.png]]

#### Putting it All Together
The program as a whole: 
```
void biggies(vector<string>& words, vector<string>::size_type sz){ 
	elimDupes(words); // put words in alphabetical order and remove duplicates
	// sorts words bys ize, but maintain alphabetical order for words of the same size 
	stable_sort(words.begin(), words.end(), 
		[](const string& a, const string& b)-> bool { 
			return a.size() < b.size();});
	// get an iterator to the first element whose size is >= sz
	auto wc = find_if(words.begin(), words.end(), 
		[sz](const string& a) { return a.size() >= sz;});
	// compute the nubmer of elements wwith size >= sz
	auto count = words.end() - wc;
	cout << count << " " << make_plural(count, "word", "s") << " of length " << sz << " or longer " << endl;
	for_each(wc, words.end(), 
		[](const string& s) { cout << s << "";});
	cout << endl;
}
```

### Lambdas Captures And Returns
#lambdacaptureandreturn

When we are defining a lambda, the compiler generates a new (unnamed) class type that corresponds to that lambda. 
We'll see how they are generated later on. 
What is useful for our understanding now is that when we pass a lambda to a function, we are defining both a new type and an object of that type; The argument is an unnamed object of this compiler-generated class type. 
When we use auto to define a variable initialized by a lambda, we are defining an object of the type generated from that lambda. 

By default, the class genereated from a lambda contains a data member corresponding to the variables captured by the lambda. 
Like the data members of a class, the data members of a lambda are initialized when a lambda object is created.


### Capture by Value
Similar to parameter passing, we can capture variables by value or by reference. 
So far, we have only captured by value. 
As with a parameter passed by value, it must be possible to copy such variables. 
**THE VALUE OF A CAPTURED VARIABLE IS COPIED WHEN THE LAMBDA IS CREATED, NOT WHEN IT IS CALLED**. Which makes a lot of sense as to how the capture system works. 

```
void fcn1() 
{ 
	size_t v1 = 42; // local variable
	// copies v1 into the callable object named f
	auto f = [v1] { return v1; };
	v1 = 0; 
	auto j = f(); // j is 42 f stored a copy of v1 when we create it
}
```

It seems that lambda is more like a callable object, more than a function as a function as a function if that make sense. 

### Lambda Capture List
#lambdacapturelist
![[Pasted image 20240325015843.png]]

Because the value is copied when we create the lambda, subsequent changes to a captured variable will do nothing to the value that we have inside the (class) lambda. 

#### Capture By Reference
Lambdas that are capturing by reference: 
```
void fcn2(){ 
	size_t v1 = 42; // local variable
	// the object f2 contains a reference to v1
	auto f2 = [&v1]{ return v1; };
	v1 = 0;
	auto j = f2(); // j is 0; f2 refers to v1; it doesn't store it as such
}
```

We must be sure that the referenced object will be real when we use the lambda function, which is not always easier. 

The variables that are captured by a lambda are local variables, in the same way that we wouldn't return a reference to a local variable, we wouldn't return a lambda (callable) object when it has a reference to a local variable in it. 

They might be necessary sometimes: 
```
void biggies(vector<string> &words,
        vector<string>::size_type sz,
        ostream &os = cout, char c)
{
    // code to reorder words as before
    // statement to print count revised to print to os
    for_each(words.begin(), words.end(), [&os, c](const string& s){ os << s << c;});
}
```

Remember that we cannot copy an `ostream`; the only way to capture `os` is then to use reference (or through a pointer);

We can always return a lambda from a function: 
The function might directly return a callable object or the function might return an object of a class that has a callable object as a data member. 
So just be careful when you are capturing by reference and then you might return that lambda, which uses the reference away from the local scope, destroying the variables that the references are pointing to. 

A lambda capture stores information between the time the lambda is created, and the time the lambda itself is executed, it is our, the programmer's responsibility, to ensure that the intended meaning each time the lambda is executed. 

When we capture a string, an int etc., by value, then we just have to make sure that we capture when they are at a value that we actually0 want. 

When we capture a pointer, and iterator, or a reference, then we need to make sure that the thing they are pointing to is still existing every time that we call a lambda function. 

Just minimize the data that we capture, making it a lot easier. 

#### Implicit Captures
#implicitcaptures
Rather than stating the values and variables that we want, we can let the compiler infer, and make a decision for what we want. 
To let the compiler take control, we use the `&` or =. 
The `&` tells the compiler to capture by reference, and the `=` says the values are captured by value. 
```
// sz implicitly captured by value
wc = find_if(words.begin(), words.end(), 
	[=](const string& s) { return s.size() >= sz;});
```
If we want to mix between references and values then we can say so like this: 
```
void biggies(vector<string>& words, vector<string>::size_type sz, ostream &os = cout, char c = '')
{ 
	// other processing as before
	// os implicitly captured by reference; c explicitly captured by value
	for_each(words.begin(), words.end(), 
		[&, c](const string& s){ os << s << c;});
	// os explicitly captured by reference; c implicitly captured by value
	for_each(words.begin(), words.end(), [=, &os](const string& s){os << s << c;});
}
```


### Mutable Lambdas
#mutablelambdas
By default, a lambda cannot change the value of a variable that it copies by value. 
If we want to be able to change the value of a captured variable, we must follow the paramter list with the keyword `mutable`. 
Lambdas that are `mutable` may not omit the parameter list. 
```
void fcn3()
{ 
	size_t v1 = 42; // local variable
	// f can change the value of the variable it captures
	auto f = [v1]() mutable { return ++v1;};
	v1 = 0;
	auto j = f(); // j is 43
}
```

Whether a variable captured by reference can be changed (as usual) depends only on whether that reference refers to a `const` or a `nonconst` type: 
```
void fcn4()
{ 
	size_t v1 = 42; // local variable
	// v1 is a reference to a non const variable
	// we can change that variable through the reference inside f2
	auto f2 = [&v1]{ return ++v1; };
	v1 = 0; 
	auto j = f2(); // j is 1
}
```



#### Specifying Lambda Return Type
#lambdareturntype
Most of the lambdas we have written so far, have only a single return statement. 
We haven't had to specify a return type. 
If a lambda body contains any statement other than a `return`, that lambda is assumed to return `void`. 
We might use the transform alg in order to change all negative values to their abs value: 
```
transform(vi.begin(), vi.end(), vi.begin(), [](int i){return i < 0? -i : i;});
```
The function takes three iterators and a callable. 
The third iterator is a destination. 
The alg calls the given callable on each element in the input sequence. 
The idea of the algorithm above, is that when the iterators are == then the algorithm will start to do its thing.

Defining a return type, we would have to use a trailing return type; 

```
transform(vi.begin(), vi.end(), vi.begin(), 
	[](int i) -> int {if (i < 0)return -i; else return i;});
```
In this case, the lambda to `transform` has an empty capture list, taking a single apramter of type `int` and returns a value of type `int`. 

### Binding Arguments
If we wanted to define an action in a lot more areas, then we might want to use a function, rather than writing the same lambda over and over. 
If an operation requires many statements, ordinarily better to use a function. 





My Examples: 
Hopefully a mixture of both algorithms and lambdas: 
```
vector<int> vi = {0, 1, 2, 3, 4, 5}; 
for_each(vi.begin(), vi.end(), [](int& i)-> void {if(i != 4)i += -1;});
```

![[Pasted image 20240325115617.png]]

Can even do something like this: 
```
vector<int> vi = {0,1,2,3,4,5,6};
auto f = [](const vector<int>& vi){for(auto i : vi) cout << i << " ; "; cout << endl;};
f(vi);
```
will result as you can imagine, just the contents of the vector. 

It is not easy to write a function that would also capture the local variables. 
The lambda we used in the call to `find_if` compared a `string` with a given size `sz`. 
We can easily write a function that would do the same thing: 
```
bool check_size(const string& s, string::size_type sz){ 
	return s.size()>=sz;
}
```
However, we wouldn't be able to use this, as `find_if` takes  a unary predicate (the function part that can be used as a callable object). 
In order to find a way to use this predicate in the algorithm, we have to figure out how to pass an argument to the `sz` paramter> 


#### Library `bind` Function
#bindfunction
We can pass a size argument to `check_size` by using a new library function named `bind`. 
Which is defined in the `functional` header, which cherno was using in his video. 

It can be thought of as a general purpose function adaptor (like with the container stack and queue adaptor).
It will take a callable object and generates a new callable that "adapts" the parameter list of the original object: 
The general form of it is as so: 
`auto newCallable = bind(callable, arg_list);`
The idea here is that when we call `newCallable` it will still call `callable` however, it will pass into it the `arg_list`. 
The arguments in `arg_list` may include names of the form `_n`, where n is an integer. These arguments are "placeholders" representing the parameters of `newCallable`. They stand "in place of" the arguments that will be passed to `newCallable`. 
The number `n` is the position of the parameter in the generated callable: `_1` is the first parameter in `newCallable`, `_2` is the second etc. etc. 

### Binding `sz` Parameter of `check_size`
We're going to use `bind` to generate a new callable object that calls `check_size` with a fixed value for it size parameter as follows: 
```
// check6 is a callable object that takes one argument of type string
// and calls check_size on its given string and the value 6
auto check6 = bind(check_size, _1, 6);
```

This means that `check6` takes a single argument. 
The placeholder appears first in `arg_list`, which means that the parameter in `check6` corresponds to the first parameter of `check_size`. 
Whatever this type was in `check_size`, that will have to be the same in `check6`. 

The placeholders are just to say that the arguments will be supplied later, by something else. 
```
void print(int a, int b, int c){ 
	cout << a << " " << b << " " << c << endl;
}

int main() { 
	//biding the first two arguments of the print function
	auto func = std::bind(&print, 100, std::placeholders::-1, std::placeholders::_2);

	func(200, 300);
}
```


The idea here is that the placeholders will be given later: and the bound parameter will be part of the function: 
```
auto f = [](int a, int b, int c) -> void { cout << a << " " <<  b << " " <<  c << endl; };
auto nC = std::bind(f, std::placeholders::_1, std::placeholders::_2, 3);
auto nW = std::bind(f, std::placeholders::_1, 2, 3);
f(1, 2, 3);
nC(5, 10);
nW(20);
```

The placeholders just represent what we will pass in later. They will be overwritten when we call the object. 

```
string s = "hello";
bool b1 = check6(s); // check6(s) calls check_size(s, 6);
```

Using `bind` we can replace our original lambda_based call to `find_if`: 
```
auto wc = find_if(words.begin(), words.end(), 
	[sz](const string &a))
// with the version that uses check_size
auto wc = find_if(words.begin(), words.end(), bind(check_size, _1, sz));
```

This would mean that our callable bind object will only take on argument when we call it, the other will be captured from the surrounding scope. 

### Using Placeholders Names
 #placeholders
 The `_n` names are defined in a namespace named `placeholders`. 
 This is defined in the `std` namespace itself. 
 To use them, we have to use `std::placeholders::_n` is a declaration for n in that sense. 

Instead of stating each one, it's a lot of namespaces and scopes to enter for each placeholder, 
we can use a different type of `using` than we did before.
`using namespace namespace_name;`
Says that we make all the names from `namespace_name` accessible to our program. 
`using namespace std::placeholders;`

### Arguments to `bind` 
So we can use `bind` to fix the value of a parameter.
We can also rearrange the parameters in the given callable. 

Assuming if `f` is a callable object that has five parameters, the following call to `bind`: 
```
// g is a callable object that takes two arguments
auto g = bind(f, a, b, _2, c, _1);
```
This will generate a new callable that will only take two arguments, the placeholders, remember that the others are all bound to it. 
The new callable will pass its own arguments as the third and fifth arguments to f. 
The first, second, fourth arguments to `f` are bound to the given values, a, b, c respectively. 

The arguments to `g` are bound positionally to the placeholders, that is the first argument to g is bound to `_1`, and the second argument is bound to `_2`. 
When we call `g`, the first argument to `g` will be passed as the last argument to f. 
The second argument to `g` will be passed as the last argument to f, the second argument to g will be passed as f's third argument. 
This will mean that we have something like this positionally. 
```
g(_1, _2)
to
f(a, b, _2, c _1)
```

That is, calling `g` calls `f` (remember that when we call the bound object, it will just call f with the bound arguments), for the placeholders along with the bound arguments, a, b, and c. 
Calling `g(X, Y)` calls f `f(a, b, Y, c, X)`. 

### Using bind to reorder Parameters
#reorderingparameters

As a more concrete example, of using `bind` to reorder arguments, we can use `bind` to invert the meaning of `isShorter` by writing 
```
// sort on word length, shortest to longest
sort(words.begin(), words.end(), isShorter);
// sort on wordl length, longest to shortest
sort(words.begin(), words.end(), bind(isShorter, _2, _1));
```

In the first call, when `sort` needs to compare wo elements, A and B it will call `isShorter(A, B);`
So the numbers of the placeholders are really important. 

#### Binding Reference Parameters
By default, values are copied into the callable object that `bind` returns. 
```
// os is a local variable referring to an output stream
// c is a local variable of type char
for_each(words.begin(), words.end(), [&os, c](const string& s){ os << s << c;});
```
We can easily write a function do to the same job: 
```
ostream& print(ostream &os, const string& s, char c)
{
	return os << s << c;
}
```

However, we can't use `bind` directly to replace the capture of `os`: 
```
// error :: cannot copy os
for_eacH(words.begin(), words.end(), bind(print, os, _1, ''));
```
It's basic, bind will copy the values, we cannot copy an ostream, as the copy constructor is probably private in the class itself: 
Therefore here, instead of using &, we have to use the library `ref` function: 
```
for_each(words.begin(), words.end(), 
	bind(print, ref(os), _1,''));
```


The `ref` function returns an object that contains the given reference an that is itself copyable.; 
There is also `cref` that generates a class that holds a reference to `const`. 
`bind`, `ref` and `cref` functions are defined in the `functional` header. 
