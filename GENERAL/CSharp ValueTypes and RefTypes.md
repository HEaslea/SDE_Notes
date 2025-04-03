Unlike C++, C# doesn't explicitly have references that we declare: 
`int& ir = i;`

Here `ir` is a reference, however that doesn't exist in C#. 

Think about it like, C# wants to automatically do a lot for us, find the best way to handle data, it wants to do as much for us as possible, even if it means that we have to think differently about it from C++ to C#. 
C# will automatically handle whether a type is a reference or a value type based on the type definition itself. 

In C#, types are implicitly (it is inherent to the type it seems) that some types are value types and some are reference types: 
- **Value Types** : `int, struct, float, enum` etc. which are of course copied when assigned or passed to methods. They are typically just stored on the stack. 
- **Reference Types** : `classes, arrays, strings, delegates, interfaces` etc. 
 We lose a level of control over how memory is managed, however, think of all the retards that really need that, especially when they just wanna make another to do list. 

Remember that in C++, the default behaviour is pass-by-value, C# wants to manage this for you, by allowing references to be implicit to the type itself. 

### Passing by Value / Reference
In C#, we can use the ref keyword, to pass a value type by reference (so that changes do in fact change the original). 

Something slightly odd here, is that reference types are passed by reference automatically, now that makes sense right, it's a little annoying however, it works. Meaning that changes are made to the original when we pass them in, however to modify the reference itself, you still need the `ref` keyword. 

```
class Person
{ 
	public string Name;
}

void ModifyPerson(Person p)
{ 
	p.Name = "Alice"; // modifies the same object that p references
	p = new Person(); // Reassigns p to a new object, thsi change won't affect the caller
	p.Name = "Bob";
}

Person person1 = new Person { Name = "David" };
ModifyPerson(person1);

Console.WriteLine(person1.Name); // Ouputs; Alice 
```

Then what if we pass with `ref`: 
```
void ModifyPerson(ref Person p)
{ 
	p.Name = "Alice"; // modifies the object
	p = new Person(); // Reassigns 'p' to a new object, 
	// THIS WILL AFFECT THE CALLER 
	// it's like we are taking in the reference itself
	p.Name = "Bob";
}

Person person1 = new Person { Name = "David"};
ModifyPerson(ref Person1); // ref has to be used here as well

Console.WriteLine(person1.Name);
```

This is really cool, as C# subtracts that layer away by saying that it will assign value and reference types, and then gives us the ability to actually change the reference itself. 
Remember that we cannot reassign or reseat references in C++, meaning that once it has been passed in somewhere, you can't change that. 

However, in C#, we can rebind, as we didn't explicitly say that something is a reference, it was sort of forced upon us, therefore, we should be able to rebind it, fuck it. Not only that but we can rebind the reference that is outside of the scope of the function to something new inside the function, like changing a pointer to something new. 

I kind of like this, however, the difference should be kept in mind. 



### **C++ References vs. C# `ref` (Key Differences)**

#### **C++ References (`T&`)**:

- **Fixed once bound**: When a reference is initialized in C++, it becomes permanently bound to the object it refers to.
- **Cannot be reassigned**: You cannot rebind the reference to point to a different object after initialization.
- **Modifies the original object**: Changes to the object via the reference affect the original object, but you cannot change what the reference refers to.
- **Rebinding requires pointers**: To change what a reference points to, use a pointer (`T*`) and pass it by reference (`T*&`).

#### **C# `ref`**:

- **Can be reassigned**: Using `ref`, you can modify both the object itself and the reference to make it point to a new object.
- **Changes visible outside the function**: If a reference is changed (rebound) inside a method, that change is visible outside the method.
- **Direct equivalent to C++ pointers by reference (`T*&`)**: The `ref` keyword in C# allows both modification of the object's contents and reassigning the reference, similar to passing a pointer by reference in C++.

#### **Summary**:

- **C++**: References (`T&`) cannot be reassigned. To rebind a reference, use pointers (`T*`).
- **C#**: `ref` allows both modification of the object's state and reassignment of the reference itself.

---

You can append this to your notes to keep the differences clear. Let me know if you'd like any tweaks!

Here's something, we can do something similar to C++: 
```
class Person 
{ 
public: 
	std::string name;
};

void ModifyPerson(Person*& p)
{ 
	p->name = "Alice"; // modifying the original
	p = new Person(); // Rebind the pointer to a new object
	p->name = "Bob";
}

int main() 
{ 
	Person* p1 = new Person();
	p1->name = "David";

	ModifyPerson(p1);

	std::cout << p1->name ; // outputs "Bob" 
	// The most important thing is that we are rebinding what our pointer is pointing to
	return 0;
}
```

`Person*&` is a reference to a pointer: 



# `in`, `out` and `ref`

## `ref` 
Allowing a method to read and modify the original variable (both the reference and its value). 
```
void Modify(ref int x)
{ 
	x = 20; // modifies the original value
}

int num = 10; 
Modify(ref num); // need ref here
Console.WriteLien(num)
```
Allowing both reading and writing of the original value: 

## `in`
Allows a method to read, but not modify, the argument. It's a read-only reference. 
Sort of like const. The variable, like a const, must be initialized before being sent in. 
```
void ReadOnly(in int x)
{ 
	Console.Writeline(x); // can read the value, not modify it
}

int num = 10; 
ReadOnly(in num); // 
```
When we want to pass in large structs or object by reference, however, to ensure that we don't modify the object. 

## `out`
This will allow for multiple values to be returned, by modifying arguments. 
The main thing about this is that is doesn't require anything to be initialized. 

```
void GetValues(out int x, out in y)
{ 
	x = 10;
	y = 20;
}

int a, b;
GetValues(out a, out b);
```
YOU HAVE TO ASSIGN IN HERE, it's like a return value: you couldn't have : 
```
int iR()
{ 
// what if we had no return here
}
```



### **Comparison of `in`, `out`, and `ref` in C#:**

| Feature            | `ref`                                                      | `in`                                          | `out`                                          |
| ------------------ | ---------------------------------------------------------- | --------------------------------------------- | ---------------------------------------------- |
| **Initialization** | Must be initialized before passing                         | Must be initialized before passing            | Does not need to be initialized before passing |
| **Modification**   | Can be modified inside the method                          | Cannot be modified inside the method          | Must be assigned a value inside the method     |
| **Read/Write**     | Read and Write                                             | Read-only                                     | Write-only (output)                            |
| **Purpose**        | When the method should read and modify the original object | When the method should only read the argument | When the method should output a new value      |
If you don't put the keyword in the call, then you will get a compile error. 

### ValueType
`ValueType` is a class that acts as the base class for **all value types** (such as `int, double, bool`). 
Like all types in C#, `ValueType` is a subclass of `Object`. 

`Object` is the mother of all classes, the ultimate base class, the root of the type hierarchy. 
[Microsoft on Object Class](https://learn.microsoft.com/en-us/dotnet/api/system.object?view=net-8.0)

Even when we write something like: 
```
using System;

class Point
{ 
	public int x, y; 

	public Point(int x, int y)
	{ 
		this.x = x;
		this.y = y;
	}

	// Object has some Equals that we can override
	// and we take in some Object
	// i think we have to have object in here for overriding
	poublic override bool Equals(object obj)
	{ 
		// seems that if we have a type mismatch to Point, then we return false anyways
		if(obj.GetType() != this.GetType()) return false;

		// then return true if x and y fields match up
		var other = (Point) obj; // then we cast to Point
		return (this.x == other.x) && (this.y == other.y);
	}

	public override int GetHashCode() 
	{ 
		return x ^ y;
	}

	public override String ToString() 
	 {
		 return $"({x}, {y}");
	 }

	public Point Copy()
	{ 
		return (Point) this.MemberwiseClone();
	}

}
```

A sealed class is one that cannot be inherited from, a `final` from C++. 


### Value Containing a Reference
Here's another case: When we have a value that is containing a reference. 
When we have an assignment along these lines, we get behaviour that is a mixture of the two. 
The behaviour of value type fields is different than that of reference type fields. 
This seems super odd, and annoying tbh. 
However, the way to think about it, is that we are shallow copying all the time. 
This means that we are copying the reference, not the object on the heap that we are referencing. 
And when we copy the value, that is just a copy, we are copying the value. 
Consider: 
```
struct myStruct 
{ 
	public int Value; // value type field (int)
	public string Name; // Reference type field (string)
}

MyStruct a = new MyStruct { Value = 10, Name = "Original" };
MyStruct b = a;

// changing value and references here
a.Value = 20;
a.Name = "Changed";

Console.WriteLine(b.Value); // outputs as 10, working as value type
Console.WriteLine(b.Name); // Outputs "Changed"
```

