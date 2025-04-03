[[Rule of Five]]
# PRVALUE
#### PURE R VALUE

An expression that evaluates to temporary value (a value that is not associated with any object at all) therefore does not have identity. 
The thing on screen (the expression) has a value, it is not an object, and cannot be bound to a non-const lvalue. 

```
int x = 5;
int y = x + 10;
MyClass obj = MyClass(); // prvalue as well, temp object with no identifier
```

Common ones are: 
- Literal Values (5, 3.14 etc.)
- Temp objects created via constructors like `MyClass()`
# GLVALUE
#### GENERALIZED L VALUE
An expression that refers to an object or a location in memory. 
It has an identity but may or may not have a value at the current point in time, that's the key, MAY OR MAY NOT HAVE A VALUE AT THIS CURRENT MOMENT IN TIME. 
These are inclusive of both **xvalues** and **lvalues**. 
They are useful when we want to access an identity of an object. 

```
int a = 10; // a is a glvalue (referring to an object in memory)
int* p = &a; // &a is a glvalue here too, refering to a's address
```
# XVALUE
#### EXPIRING VALUE
A special type of glvalue that is also part of an rvalue, see the x as the intersection almost. 
Referring to an object that is about to be destroyed. You can see this is not just  PRVALUE as they have no object reference at all, they are just a value. 

Xvalues are associated with objects whose resources can be moved away, via move constructor or move assignment etc.. 
They represent objects that can be safely moved from. 
```
std::move(x); // where x is obviously an xvalue 
MyClass&& obj = MyClass(); // MyClass() is an xvalue, that csan be moved from
```

# LVALUE
#### LEFT VALUE

Type of glvalue that refers to an object with persistent identity, meaning it has a stable address and can be on the the left hand side of an assignment: 
```
int a = 5;  // here a is an lvalue 
a = 10; // again 
```

They represent objects that have stable memory, and can appear on the lhs of an assignment. 

# RVALUE
#### RIGHT VALUE
Essentially they are a PRvalue or an Xvalue, meaning it represents a temporary value or an object that is about to expire. 

They can't be assigned to directly (they can't be on th left hand side duh). They are typically used for inputs for operations, not as assignable objects.
They are really either temporary objects (PRVALUES) or Expiring Objects (XVALUES). 


```
#include "BasicH.hpp"

#include <any>
#include <variant>

#define LOG(expr) std::cout << expr << std::endl;
struct A
{
    A() : data() { LOG("DEFAULT CTOR ");};
    A(const std::string& s )  : data(s) {LOG("A CTOR"); }
    A(const A& a) : data(a.data) { LOG("A COPIED CTOR");}

  
    A(A&& a) noexcept : data(std::move(a.data))
    {
        LOG("MOVE A USED");
    }

    A& operator=(const A& a)
    {
        LOG("Copy Assignment of A");
        data = a.data;
        return *this;
    }


    A& operator=(A&& a) noexcept
    {
        LOG("Move Assignment of A");
        if(&a != this)
        {
            data = std::move(a.data);
        }
        return *this;
    }

    ~A() {LOG("A DELETED");}

    void out() const
    { LOG(data); }

  
    std::string data;
};

void outA(const A a)
{
    a.out();
}

int main() {
    A b;
    outA(b);
	
    return 0;
}
```

![[Pasted image 20240910154208.png]]
```
// MORE EXAMPLES 

int x = 5 + 10; // 5 + 10 is a prvalue, no identity just the values

int y = x; // x is an lvalue (refers to a named object, can be on the right or the left, rvalues can only be on the right)

std::move(x); // x is an xvalue , meaning it will expire here

MyClass objj = MyClass(); // MyClass is an rvalue (temporary)


void foo(const MyClass&); // mostly for lvalue, can take in rvalue too
void foo(MyClass&&); // for rvalues (including xvalues)

MyClass a; // a is an lvalue
MyClass b = std::move(a); // sudedenly becomes an xvalue
```

![[Pasted image 20240914165623.png]]
