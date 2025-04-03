[Lambdas From Scratch Video](https://www.youtube.com/watch?v=3jCOwajNch0&t=1070s)
#lambdacapturelist
Taking certain notes from the talk, super useful all the way through: 
[[BtB]]
#btb


```
#include <stdio.h> 

int g = 10;
auto kitten = [=]() { return g+1; }; // capture by value
auto cat = [g=g]() { return g+1;}; // capture by value again

int main(){ 
	g = 20;
	printf("%d : %d\n", kitten(), cat());
}
```
we get the output `21 : 11`;
It appears that kitten has used `g = 20`;

The important thing to remember here is that `[=]` will capture everything it NEEDS. 
However, the compiler is clever enough to know that it doesn't need to initialize, capture, and set a data member to 10, as it knows that g is global. It knows that kitten will be able to get g wherever it goes, whenever it is used, therefore it doesn't need to grab it at that point. 
With cat, we are explicitly saying, "yo capture g now, I want it to be captured at 10 before it changes". 
Now what's even more strange, is if we did this: 
`auto cat = [g]() { return g + 1; };`
This will cause the compiler to warn us: 
capturing the variable `g` with non-automatic storage duration: 
Then it will show us where g is declared, here it is at `int g =10`. 

Remember that you can show the return type if you wanted to, it is inferred by the return type in the braces however: 
`auto kitten = [=]() -> int { return g + 1; };`

Cat is saying here that we want to capture g, whether we feel we need it or not. 



### Some more features
#lambdareturntype 
We can convert to raw function pointer (if captureless): 
`int (*fp)(int) = [](int x) { return x + 1; };`

We can use the unary + operator, forcing any expression to `scalar` types: these are types that represent single values. They are fundamental types, and don't have internal structures. 
They are the simplest of types, meaning that they don't hold multiple values at a time. 

```
template<class T> void fn(T t);
fn(+[](int x){ return x + 1; }); // calls fn<int(*)(int)>
```

so here + is promoting the lambda to a function pointer type, we don't have to declare `fp` here as we did earlier. 
This will save on template instantiations. 

#### Per-lambda Mutable State (wrong!)
```
auto counter = [](){ static int i; return ++i;};
// essentially creating a class 
auto c1 = counter; 
auto c2 = counter; // making two copies here

cout << c1() << c1() << c1() << "\n"; // 123
cout << c2() << c2() << c2() << "\n"; // 456 they share
```

They share the same state. 

Two instances of the same lambda type. 

That closure type behaves just like the following class type: 
```
class Counter { 
// no captured data members
public: 
	int operator() const { 
		static in i; return ++i; 
	}
};
```
So `i` will be shared by **all** callers of `Counter::operator`!

The best way to do this then we just have a data member, therefore we just capture it. 
The capture list will just create data_members of the Callable object that the lambda is.
**However** if we did that here, we would get an error, and it's not obvious. 
![[Pasted image 20240327185858.png]]
Now we didn't write const anywhere, so where did it come from. The idea here is that the object of the callable object is a const, because we don't want to change the object itself. And using the capture list, we create a data member, so we can't change that member, as the callable object here is a const. 
So the default we get here is a const. 
Operator `[]` will be `const` anyways. 

Unfortunately, here, we wouldn't want the const operator, we want to modify the callable object itself. In C++ that word is mutable. 

`[i=0]() mutable { return ++i; };`

That does mean that we can now modify all the data members. We can only go **all or nothing** here. 


#### Capturing `this`
![[Pasted image 20240328140711.png]]


Here are a few examples and most of them are really important: 
Whenever we capture something into a lambda, remember that lambdas are constant callable objects, therefore sometimes you might need to use the word mutable to change what is going on inside: 
```
class Test{
    int data;
public:
    void Work(){ printf("Working\n");}
	
    Test() = default;

    void doSomething() {
        auto f = [=]() {
            this->Work();
        };
        f();
    }

    void doSomethingElse(){
        auto g = [this] {
            this->Work();
        };
        g();
    }

    void alsoSomething(){
        auto h = [ptr = this] {
            ptr->Work();
        };
        h();
    }

    void anotherOne(){ // special case just for this
        auto i = [obj = *this] () mutable {
            obj.Work();
        };
        i();
    }

    void similarOne() {
        auto j = [obj = std::move(*this)] () mutable {
            obj.Work();
        };
        j();
    }
};

int main(){
    Test newtest;
    newtest.doSomething(); // works just fine
    newtest.doSomethingElse();
    newtest.alsoSomething();
    newtest.anotherOne();
    newtest.similarOne();
```

**HOWEVER** still the best option that you will use the most, and the compiler will help you here and find the best way to optimise etc. is this one here: 
```
void LastOne(){ 
	auto f = [&]() { this->Work();};
}
```
This is exquisite code right here, but not from me. 

This can be hard to understand, however, remember that the idea of callable objects, you won't be changing the object itself, so it's defaults to const. Therefore we need to write mutable in it if we want to change the variables that we have captured into it. 

It's the same idea as this: 
```
int i = 0; 
int e = 0;
auto f = [i]() -> int { return ++i; }; // error as read-only variable i

auto g = [n=0]() -> int { return ++n; }; // error for same reason 

auto h = [&e]() -> int { return ++e; }; // FINE as reference keeps mutability of outside scope `e`

cout << f() << endl;
```

If I also do something like this: 
```
int x = 0; 
auto f = [=]() -> int { return ++x; }; // Error, x is not a modifiable value

auto h = [&]() -> int { return ++x; }; // Fine, reference to a nonconst value, mutable 
```

```
int x = 0;
auto f = [&x]() -> int { return ++x; };
for (int i = 0; i < 10; ++i){
	cout << f() << " : ";
}
cout << endl; // 1 : 2 : 3 : 4 : 5 : 6 : 7 : 8 : 9 : 10 : 
```

##### Variadic Generic Lambda
```
auto plus = [] (auto... args) { 
	return sum(args...);
};
```

Please review over this bit again. Really tough to understand so far. 


MyCode: 
using namespace std;
#define ARRAYSIZE(arr) (sizeof(arr)/sizeof(arr[0]))

int main(){ 
	int arr[5] = {1,2,3,4,5};
	auto printele = [arr=arr](int i) -> void { printf("%d", i[arr]);};
	for(int i = 0; i < ARRAYSIZE(arr); ++i){ 
		printele(i);
	}
}
```
[[Weird SubScript Interaction]] in the lambda here. 
