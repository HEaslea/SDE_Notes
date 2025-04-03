#staticclassmembers #classes #definingabstractdatatypes 
Classes sometimes need members that are associated with the class, rather than with the individual objects of the class. 
A bank account class might need a data member to represent the current prime interest rate. 
Class associate, not object associated. 

#### Declaring `static` Members
`static` members can be public or private: 
The data member can be `const`, reference, array, class, and so forth. 
Here we define a class to represent an account record at a bank: 
```
class Account{ 
public: 
	void calculate() { amount += amount * interestRate;}
	static double rate() { return interestRate; }
	static void rate(double);
private: 
	std::string owner;
	double amount;
	static double interestRate;
	static double initRate();
};
```

`static` member functions mean that they still belong to the class and not to an object, therefore they can be called without having an instance of the class (an object). 
`static` members of a class exist outside any object (its creation). Objects therefore, do not contain data associated with `static` data members. 
Each `Account` object will contain two data members - `owner` and `amount`. 
There is only one `ineterestRate` object that will be shared by all the `Account` objects. 

They will not have a `this` pointer. 
`static` member functions may not be declared as `const`, and we may not refer to `this` in the body of the a `static` member. 

If we wanted to access `rate` member without using an object, then we obviously just use the scope access: 
```
double r; 
r = Account::rate();
```
Also: 
```
Accoutn ac1; 
Account *ac2 = &ac1;
// equivalent ways to casll the static membear rate function
r = ac1.rate(); // we can access it through an object
r = ac2->rate();
```

Member functions can use static members directly, without the scope operating or the accessing etc. 
```
class Account{ 
public: 
	void calculate() { amount += amount * interestRate;}
private: 
	static double interestRate;
	// remaining members as before
};
```

### Defining `static` Members
#definingstaticmembers
We do not need to repeat the static keyword if we define the function outside of the class. 
It will appear only with the declaration on the inside of the class. 
```
void Account::rate(double newRate){ 
	interestRate = newRate;
}
```

Members using static are not defined by the class' constructors. 
We may not initialize a `static` member inside the class. 
Instead we must define and initialize each `static` data member outside the class body. 
Moreover, if we do initialize a static data member, then we must use const. 
They may be defined only once. 
Like global objects, `static` data members are defined outside any function. 
Once they are defined, they continue to exist until the program ends. 

We would define one similarly to how we would define class member functions outside the class. 
```
// defining and initializing a static class member
double Account::interestRate = initRate(); 
```
Once we have seen the class name `Account`, then we can access the private `initRate`, using it to initialize. 
As with any other member definition, a static data member definition may access the private members of its class. 

The best way to ensure that the object is defined exactly once is to put the definition of `static` data members in the same file that contains the definition of the class noninline member functions. 

#### In-Class Initialization of `static` Data Members
Ordinarily, these members may not be initialized in the class body. 
We can provide in-class initializers for `static` members that have `const` integral type and must do so for `static` members that are `constexpr`s of literal type. 
The initializers must contain constant expressions. 

Such members are themselves constant expression; they can be used where a constant expression is required. eg. 
```
class Account{ 
public: 
	static double rate() { return interestRate; }
	static void rate(double);
private: 
	static cosntexpr int period = 30; // period is a constant expression 
};
```

You can't have a nonconst member initialized in class, that would mean that for each object, we are re-initializing and returning it back to the value that is in the class. 

There is this sort of thinking for classes, that we might re-initialize every time that we call an object, thinking about it. As a static member, we can't do that. 
So think of it as associated with the class, but due to it's non-object-only-reference, we can't initialize it in the class, unless it is a const, therefore it doesn't really matter if it is re-initialized. 

The idea mainly is that we should define a static member outside the class body, just the idea that we don't re-initialize, even if the value is constant anyways. 

Another idea is that if the compiler can substitute the member's value, then an initialized `const` or `constexpr static` need not be separately defined. 
If we use the member in a context in which the value cannot be substituted, then there must be a definition for that member. 

Static definition outside the class; 
```
class Test{ 
public: 
	// constructors and all that here
	
	Test() = default;
private: 
	int tAge; 
	static int stCnt;
};

int Test::stCnt = 0;// int is required here as it describes the type of variable we are defining from inside the class
```


```
class Account{ 
public: 
	static double rate() { return interestRate; }
	static void rate(double);

private: 
	static constexpr int period = 30; // period is a constant expression here
	double daily_tbl[period];
};
```

The idea with the constant expression is that it is a compile time constant and will remain the same throughout all of run-time, so we can solidify it at compile time, not run-time. 
If the member is used only in contexts where the compiler can substitute the member's value, then an initialized `const` or `constexpr static` need not be separately defined. 
However, if we cannot substitute in the same way, then there must be a definition for that member. 

If the only use we make of `period` is to define the dimension of `daily_tbl`, there is no need to define `period` outside the class. 
If we omit the definition, it is possible that even seemingly trivial changes to the program might cause the program to fail to compile because of the missing definition. 

If we pass `Account::period` to a function that takes a `const int&`, then period must be defined. 
Now, if an initializer is provided inside the class, the member's definition must not specify an initial value. 
```
//definition of a static member with no initializer
constexpr in Account::period; // initializer provided in class definition
```

Good practice would be to define it outside the class. 

### `static` Members an be Used in Ways Ordinary Members Can't
We know they exist independently of objects. 
They can therefore be used in ways that are illegal for `nonstatic` members. 
In particular, a `static` data member can have the same type as a the class type of which it is a member. 

![[Pasted image 20240311134137.png]]
```
class Bar{ 
public: 
	//.... 

private: 
	static Bar mem1; // ok: static member can have incomplete type
	Bar *mem2; // ok pointer member casn have incomplete type
	Bar mem3; // Error; data members cannot be of incomplete type
};
```

Remember than an incomplete type refers to a type that has been declared but not fully defined. 
We don't need to define it quite yet as it might just be a pointer. 

We can also use `static` members as a default argument: 
```
class Screen{ 
public: 
	//bkground refers to a the static member
	// declared later in the class definition
	Screen& clear(char = bkground);
private: 
	static const char bkground;
};
```
A non-static data member may not be used as a default argument because its value is part of the object of which it is a member. 
Using a `nonstatic` data member as a default argument provides no object from which to obtain the member's value and so is an error. 

#### My Try:
```
class Test{

public:

    Test() { stCnt += 1; tAge = 0; }
    Test(int a ) : tAge(a) { stCnt += 1; }

    static void PrintStat();
    static void ResetStat();
    static void ReadOutPast();
    static void ChangeCnt(int);

private:
    int tAge;
    static int stCnt;
    static vector<int> stPast;
};

// static variable definition
int Test::stCnt = 0;
vector<int> Test::stPast; // this part is necessary
  
void Test::PrintStat(){
    cout << "Object count: " << stCnt << endl;
}

void Test::ResetStat(){
    stPast.push_back(stCnt);
    stCnt = 0;
}

void Test::ReadOutPast(){
    if(stPast.size() == 1){ // if only one element
        cout << stPast[0];
    }
    else{
        for(auto i : stPast){ // > 1 element
            cout << i << " ::: ";
        }
    }
    cout << endl;
}

void Test::ChangeCnt(int a ){
    cout << "Changing Cnt to: " << a << endl;
    stCnt = a;
}

int main(){  
    Test::PrintStat();
    Test newobj;
    Test::PrintStat();
    Test newobj2(2);
    Test::PrintStat();
    Test::ResetStat();
    Test::ChangeCnt(43);
    Test::ResetStat();
    Test::ReadOutPast();
```


Fairly simple, gives a good idea of how it all works. 
![[Pasted image 20240311121221.png]]
