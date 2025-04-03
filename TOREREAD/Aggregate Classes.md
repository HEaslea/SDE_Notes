#aggregateclasses
#classes #definingabstractdatatypes

They give you direct access to member and has special initialization syntax. 
A class is an aggregate if: 
- All of its data members are `public`
- It does not define any constructors
- It has no in-class initializers
- It has no base classes or virtual functions which will be covered later on

This class is an aggregate: 
```
struct Data{ 
	int ival;
	string s;
};
```
We can initialize the data members by providing a braced list of member initializers: 
```
// val1.ival = 0; val1.s = string("Anna");
Data val1 = {0, "Anna"};
```

It's like initializing an array's elements, if the list of initializers has fewer elements than the class has members, the trailing members are value initialized. The list cannot contain more elements than the class has members. 

There are drawbacks to explicitly initializing the members of an object of class type: 
- It requires that all the data members of the class be public:
- It puts the burden on the user of the class (rather than on the class author) to correctly initialize every member of the object. This is tedious and error-prone, easy to forget an initializer or to supply an appropriate initializer. 
- If a member is added or remove, all initializations have to be updated. 


They seem like the most basic of classes, no protected, non private non-static members. 
 Super basic; 
```
struct Point{ 
	int x; 
	int y;
};
```




