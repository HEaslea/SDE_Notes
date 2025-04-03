I've been through the desert on a struct with no name. 
Allowing you to declare variables directly within the scope in which the struct is defined, making the struct's members directly accessible without having to declare an instance of the struct explicitly. 
Commonly used in C, in order to group variables without having to introduce another name. 

```
struct 
{ 
	int x;
	int y;
} point; // point here si a variable, not a struct type
// we are defining point with a struct type, without that struct having to have its own name. 

int main() 
{ 
	point.x = 5;  // here we see that point is a variable and we don't have to 
	// instantiate a struct of another name to use it
	point.y = 10;
}
```
It's mostly that we are defining a type before the variable. 

### Nameless Namespaces
#namelessnamespaces

All these have internal linkage, they are tied to the TU that they are declared in> 
Giving us a modern replacement for `static`, remember that if they are found in header files, then each TU will have this namespace added to it. 

![[Pasted image 20240925190857.png]]

If this nameless namespace is defined in a header file, then it will add to all the nameless namespaces of all the translation units that it is defined in. There will not even be an redefinition issues across the translation units. 

