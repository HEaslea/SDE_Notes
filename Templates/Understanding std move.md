#stdmove
Allowing us to get the rvalue reference bound to an lvalue. 
`move` can take arguments from literally any type, it should not be surprising that `move` is a template therefore. 

##### How `std::move` is Defined
The standard will define `move` as follows: 
```
template <typename T> 
typename remove_reference<T>::type&& move(T&& t)
{ 
	// static_cast was covered earlier
	return static_cast<typename remove_reference<T>::type&&>(t);
}
```
Remember the difference between static and dynamic types.

`move`'s function parameter, `T&&` is an rvalue reference to a template parameter type. 
Remember with reference collapsing, "universal reference", this parameter casn match arguments of any type. 
We can pass either an lvalue or rvalue to `move`: 
```
string s1("hi!"), s2;
s2 = std::move(string("Bye!")); // ok moving from an rvalue 
s2 = std::move(s1); // ok, s1 will have indeterminate value
```

### How does `std::move` work? 
#stdmoveworking
In the call `std::move(string("bye"));`
- The deduced type of `T` is `string`.
- Therefore, `remove_reference` is instantiated with `string`. 
- The `type` member of `remove_reference<string>` is string. 
- The return type of `move` is `string&&`. 
- `move`'s function parameter, `t`, has type `string&&`. 

Accordingly, this call instantiates `move<string>`, which is function `string&& move(string&& t)`. 

The body of this returns `static_cast<string&&>(t)`. 
The cast here does nothing as the type of `t` is already `string&&`. 
The result of this call is the rvalue reference it was given. 

Consider the second assignment we used, `std::move(s1)` in which argue and lvalue. 
- The deduced type of `T` is `string&` (reference to `string`, not plain `string` like in the rvalue argument). 
- Therefore, `remove_reference` is instantiated with `string&`. 
- The `type` member of `remove_reference<string&>` is `string`. 
- The return type of `move` is still `string&&`. 
- `move`'s function parameter, `t`, instantiates as `string& &&`, which collapses to `string&`. 

This call instantiates `move<string&>`, which is `string&& move(string& t)`

##### `static_cast` from an Lvalue to an Rvalue Reference is Permitted
There is a special dispensation for rvalue references : Even though we can't implicitly convert an lvalue to an rvalue reference, we can **explicitly** cast an lvalue to an rvalue reference using `static_cast`. 
If we bind and lvalue to an rvalue, we are giving permission to clobber the lvalue. 
There are times when we know that it is safe to clobber and lvalue (to leave it undefined). `std::move`. 
Just remember that `std::move` will clobber the lvalue that we pass into it. 

## Forwarding
#forwarding
If we don't want the type to be unchanged when it is passed in. It is essentially when we want perfect forwarding. When we want to preserve everything about the forwarded arguments, including whether or not the argument is type `const` or not. 

Let's write a func that takes a callable expression, and two additional arguments. 

Our function will call the given callable with the other tow arguments in reverse order . 
```
// template that takes a callable and two params
// and calls the given callable with the parameters "flipped"
// flip1 is an incomplete implementation: top-level const and references are lost
template<typename F, typename T1, typename T2> 
void flip1(F f, T1 t1, T2 t2)
{ 
	f(t2, t1);
}
```

This works fine, until we want to use it to call a function that has a reference parameter: 
```
void f(int v1, int &v2) // note v2 is a reference
{ 
	cout << v1 << " " << ++V2 << endl;
}
```

Imagine now that we call `f` using `flip1`, therefore the reference will not change the value of the original argument: 
```
f(42, i); // f changes its argument i due to reference
flip1(f, j, 42); // f called through flip1 leaves j unchanged
```
The problem here is that the parameters in `flip1` are not references. 
The instantiation here, of `flip1` is this; 
`void flip1(void(*fcn)(int, int&), int t1, int t2);`
The value of `j` is just copied into `t1`, at no point will we change that value of `j` itself, we just create at copy and a reference to `t1`, which is just the local object of `flip1`. 

##### Retaining Type Information
Pass a reference through our flip function, we need to rewrite our function, the parameters preserve the lvalueness of its given args. 
Thinking ahead, we want to preserve the `const`ness of the arguments as well. 

Using a reference parameter will let us preserve `const`ness, the `const` here will be low-level. 
Through reference collapsing, defining the parameters as `T1&&, T2&&,` we can preserve the lvalue and rvalue property of flip's arguments. 
```
template <typename F,typename T1, typename T2>
void flip2(F f, T1&& t1, T2&& t2)
{ 
	f(t2, t1);
}
```
When we call `flip2(f, j, 42)` the value `j` is passed to the parameter `t1`. 
The type deduced here now is `int&`, not just `int`. 

The type collapsing where will just be `int&`.
Therefore `t1` is finally a reference to `j`. 
When `flip2` calls `f` is bound to `t1`, which in turn is bound to `j`, they sort of lead on to each other. 
When `f` increments `v2`, it is actually changing the value of `j`, which can be exactly what we want. 

> **A function parameter that is an rvalue reference to a template type parameter (ie. T&&) preserves the `const`ness and lvalue/rvalue property of its corresponding argument**


This will solve only half the issues. What if I want to pass in rvalue references. 
`void g(int&& i, int& j)`
If we try to call this through `flip2`, we are passing the parameter `t2` to `g's` rvalues reference parameter. 
The idea is that we can't initialize int&& from an lvalue. 
When we pass an rvalue to a function, it sort of becomes an lvalue. 


Just some notes on function parameter/argument: 
```
void foo(int x){ 
	// x is an lvalue in the scope of foo
	x = 5;
}

void foo2(int& x){ 
	// x is an lvalue refernce, within the scope foo
	x = 5; // will modify the original variable
}

void foo3(int&& x){ 
	// x is an lvalue within the scope of foo
	x = 5; // will modify the original variable
}

void foo4()

int main(){ 
	foo(10); // 10 is an rvalue
	
	foo2(10); // this will be an error;
	// cannot bind an rvalue to an lvalue reference

	foo3(10); // fine to bind to rvalue reference
}
```
The idea is that when we pass an argument to a function, regardless of whether the argument is an lvalue or an rvalue, it becomes an lvalue within the scope of the function. 
This is just because, the parameter name is a named variable, and named variables are always lvalues. 


#### Using `std::forward` to Preserve Type Information in a Call
`foward` has to be called with an explicit template argument.
`forward` returns an rvalue reference to that explicit argument type. 
The return type of `forward<T>` is `T&&`. 

Normally, we use `forward` to pass a function parameter that is defined as an rvalue reference to a template type parameter. 
Through reference collapsing, on its return type, `forward` will preserve the lvalue/rvalue nature of its given argument. 

```
template <typename T> intermediary(Type&& arg)
{ 
	finalFcn(std::foward<Type>(arg));
}
```

`arg` here is an rvalue reference to to a template type parameter, `Type` will represent all the type information in the argument passed to `arg`. 
If that `arg` was an rvalue, then `Type` is an ordinary (nonreference) type and `foward<Type>` will return `Type&&`. 
This is all due to reference collapsing. 

> **When used with a function parameter, that is an rvalue reference to template type parameter (T&&), `forward` preserves all the details about an argument's type **

Using `forward`, let's do some rewriting: 
```
template <typename F, typename T1, typename T2>
void flip(F f, T1 && t1, T2 && t2)
{ 
	f(std::forward<T>(t2), std::foward<T1>(t1));
}
```

If we call `flip(g, i, 42)`, `i` will be passed to `g` as an `int&` and `42` will be passed as an `int&&` retaining it's rvalueness. 
Now this is perfect forwarding. 

> As with `std::move` it's a good idea not to provide a `using` declaration for `std::foward`. 



My own notes on this sort of thing: 
```
void process(int& x){ 
	// lvalue reference
}

void process(int&& x){ 
	// x is an lvalue in this scope
}

template <typename T> 
void foo(T&& param){
	// param is still an lvalue
	process(std::foward<T>(param));// will pass an rvalue, even though param will make it an lvalue in the scope
}
```
The rvalue concept is held within the template. 
ChatGPT even understands the idea of universal references. 
Remember these two facts: 
`template<typename T> void foo(T&& param)`
- if you pass an lvalue, `T` is deduced as an lvalue reference type(`T&`). 
- If you pass an rvalue, `T` is deduced as a non-reference type (`T`)

If we pass an lvalue into `std::foward<T>(param)` where param is an lvalue reference. 
There we would be passing on a lvalue reference. 

If we pass in an rvalue eg. `param` is of type `int&&`. `param` is a named type, therefore param is an lvalue of type `int&&`, however, forwarding it will call the new function with the rvalue that we once had. 

