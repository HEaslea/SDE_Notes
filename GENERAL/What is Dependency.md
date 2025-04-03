From the OReilly C++ Software Design Book: 

The general idea is that: 
> **A Dependency:** is when there is a relationship between two components, where one component relies on the other to work properly. 

Say if we have a component that depends on a library in order to query a database, then clearly the component will be depending on the library. This is fine as long as the library is stable, it's interface doesn't change, and we can continue to use it the same way for as long as possible, however, if that library changes in any significant manner, then we run into a shit ton of trouble. 

#### How we might form Dependencies
Say we have an abstract class, it will be forming the interface, that we will later implement. Say that this class is that class that will be using the library that will query a database. Now, does the library depend on the class, no, not at all, we might see, or not see, that it doesn't mention the our class in any way. Our class, will certainly have to mention the library at some point, in order to use it, therefore we have a dependency on the library. At some point the class will mention this library. 

Think of dependencies as: does at any point, class X use, even by way of having an instance of, class Y. If so, then X depends on Y. Y may never mention class X, therefore, it doesn't depend on it to function at all. 

1. **The Type is in The Interface**: If `X` has `Y` as part of its interface, this means that `X` either: 
	- Uses an instance of Y (eg. as a parameter of a return type) . 
	- Holds a pointer or a reference to `Y` in its public or private members. 
	- Expects `Y`as a template parameter. 
2. **Type in the Implementation**: Even if `Y` is not of `X's` public interface, if `X` uses `Y` internally (eg. instantiates Y or call's Y's methods in its private implementation), this also forms a dependency. `X` depends on `Y` being available and functioning correctly. 


### Why Does THIS Matter
##### Coupling
When one component depends on another, the two become coupled. High coupling will form tethers that will restrict us later on, making things much harder to change and more issues if at any point Y is changed, think about what the knock on effect will be. 

##### Architectural Boundaries
A good design, minimizes dependencies between high level and low level components, ensuring that high level modules are not tightly coupled to low level details. 
As long as the dependencies flow from low to high, it's not bad at all. 
