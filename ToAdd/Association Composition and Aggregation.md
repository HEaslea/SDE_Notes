## Association
Essentially the idea that two objects are in some way related to each other: One - to - one, 
Many - to - one, Many - to - Many. 
Both composition and aggregation are a type of association, so this is not to be worried about too much. 

### Composition
Think of it as being composed of, an object being a container for other objects. 
When the containing object is created, the others are too, and when that containing object dies/deleted, then the "contained" objects are too. 
Like creating a House object, then creating a new Kitchen and new Bedroom within that object. 
When the House object is deleted, the others are too. 
In this sense, the child class cannot exist independently of the parent. 
In UML, the diamond would be filled in, the idea that it exists due to the parent class. 
### Aggregation
Think of it as a class containing a list of child objects. 
Like a van; a van is an object that can hold a bunch of objects that exist in their own right. 
If the parent object is deleted, then the child objects still knock around. 
Shown in UML with a hollow diamond, to show that they can be independent. 

