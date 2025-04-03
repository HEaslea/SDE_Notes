#copyassignmentdestruciton
#constructors [[Constructors]]
#definingabstractdatatypes [[Defining Abstract Data Types]]

Classes also control what happens when we copy, assign, or destroy (essentially the whole thing can be controlled by the "user"). 

Objects are assigned when we use the assignment operator. 
They are destroyed when they cease to exist, such as when a local object is destroyed on exit from the block in which it was created. 
Ie. when a vector is destroyed, the objects in it are destroyed as well. 

If not defined, the compiler will synthesize them for us. 

Earlier when we `total = trans; // process the book`, it will execute as if we had written: 
```
// default assignment for Sales_data is equivalent to: 
total.bookNo = trans.bookNo;
total.units_sold = trans.units_sold;
total.revenue = trans.revenue;
```
Yet we can define our own versions of usage like above. 

Many classes that will require dynamic memory, generally these classes cannot rely on synthesized class copies, deletes etc. 

