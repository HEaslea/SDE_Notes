Exceptions are run-time anomalies - losing db connection, or strange input. 

- Throw expressions - which the detecting part uses to indicate that it encountered something it can't handle. We would say that throw raises an exception. 
- Try blocks - the handling part uses to deal with an exceptions. Starts with try and ends with one or more catch clauses. Exceptions thrown are handled by one of the catch clauses. They are known as exception handlers. 
- A set of exception clauses to show and pass information about what happened between a throw and an associated catch. 

### A Throw Expression
Using a throw expression to raise an exception. Keyword - `throw`. Type of expression will determine what kind of exception is thrown. 

```
if (item1.isbn() != item2.isbn())
	throw runtime_error("Data must refer to the same ISBN");
cout << item1 + item2 << endl;
```
Throwing an exception will terminate the current function and transfer control to a handler that will know what to do. 

`runtime_error` is part of the standard library exception types and is defined in the `stdexcept` header. 

### The `try` Block
```
try{ program-statements
} catch(exception-declaration) { 
	handler-statements
} catch(exception-declaration){ 
	handler-statements
}//... here
```
When a catch is selected to handle an exception, the associate block is executed. After that statement is processed, we move onto the statement immediately after all the catch statements (here).

### Writing a Handler
```
while (cin >> item1 >> item2){
	try{ 
		// execute code that will add the two Sales_Items together
	} catch (runtime_error){ 
		// remind the user that the ISBNs must match and prompt for anothe rpair
		cour << err.what() << "\n Try Again? Enter y or n" << endl;
		char c; 
		cin >> c;
		if (!cin || cin == 'n')
			break; // break out of the while loop
	}
}
```
`err.what()` will be a function of the `run_time` class. It returns a c-style string that is used to initialize the particular object. 
Here it would return `"Data must refer to same ISBN"`. 

#### Functions are exited during the Search for a Handler
The idea of a try block that contains a try block that calls a function that contains a try block etc. etc.

The search for a handler will reverse the call chain. 

If no matching *catch* is found then we terminate that function, we move back through and see if the function that called the other has a catch in it as well. If not, then we move through that. Then that function's caller is searched, and so on back up the execution path until a *catch* of an appropriate type is found. 

If NO catch is found, execution is transferred to a library function named terminate. The behaviour of that is system dependent but is guaranteed to stop further execution of the program. 

Exceptions that do not define any try blocks are handled in the same manner; if there are no try blocks, there can be no handlers. 
If there is an exception, and no try blocks, then terminate is called again. 

### Standard Exceptions
Four headers that include classes: 
- `exception` header, the most general kind of exceptions class named `exception`. Communicating only that an exception occurred but provides no additional information. 
- The `stdexcept` header defining several general-purpose exception classes, ![[Pasted image 20240220163115.png]]
- The `new` header defines the `bad_alloc` exception type, which we will cover later. 
- The `type_info` defines the `bad_cast`exception type, which we will cover later as well. 

They only have a few operations. You cannot provide an initializer for objects of these exception types. 
They only have a single operation named `what`. ``



There is a weird thing that we can do here: 
```
void ThrowingInt(){ 
	throw 1;
}

int main(){ 
	try{ 
		ThrowingInt(); // even though int is not an error in of itself
		// because we throw one, then we will go into catch
	}catch (int& i){ 
		cout << "Error" << endl;
	}
}
```
```
void ReturnInt(){ 
	return 1;
}
```
If we used `ReturnInt()` instead, then we aren't throwing it and we are just bypassing the catch entirely. 