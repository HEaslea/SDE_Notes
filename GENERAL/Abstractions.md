Abstractions are models that simplify complex systems, by modelling them at a higher level. 
We focus on essential features, relevant to a particular perspective while hiding the intricate implementation details. 

1. **Data Abstraction** : defining data structures, without exposing their internal representations. eg. `list, stack, queue` ADTs in general. 
2. **Control Abstraction** : abstracting control flow mechanisms, such as loops, conditionals and function calls, using higher level functions like `map` or `filter`. 
3. **Procedural Abstraction** : Defining functions/procedures that perform specific tasks, hiding the detail `sort()`. 
4. **Object Abstraction** : defining classes and objects that encapsulates data and behaviour. eg. `Car` class that provides methods like `start(), stop()` without exposing the engine's internal workings. 


### High Level and Low Level
This is early on in the move recent book on C++ software design. 
Think of it as an extension of low and high level languages. 
**High Level**: what the system or component does such as the interfaces, APIs, system architecture designs. 
**Low Level**: how the system does it, we look at the implementation of it. We look at algorithms, data structures, function implementations, hardware interactions, memory managements etc. 
Think of the high level as the zooming out, what the large system does, not how it does it, the overview, the class as itself. 
The low level, we zoom in, we don't care so much about the class a whole, but how a function works, what it does on a micro level. 

### Higher Level Abstractions
We look at the purpose and functionality of an abstraction, what it does, not how it does it. 
For instance, when we use a `std::vector`, when we use it, we are looking at what it does, we are not concerned at the `HOW` is it doing that. We are not so concerned with how it's managing memory etc.  
We look at **simplifying**, what does our abstraction provide here. 
We look at **interfaces**, rather than implementations. 
We work on our **encapsulation**, we don't let users look at the inner workings, or god forbid implement it themselves, we hide those "features", and worry more about what, as a whole, the abstraction will be able to provide, and hide. 
At a higher level, we are thinking about **flexibility, extensibility and reusability**. 

```
// Abstract class defining the interface
// This is a high-level abstraction
class Logger
{ 
public: 
	virtual void log(const std::string& message) = 0; // requiring implementation in all inheriting classes

	virtual ~Logger() = default;
};
```

```
// Log Level Implementation
class ConsoleLogger : public Logger
{ 
	// ... the implementation of this particular logger
};
```

---- 

## Postconditions and Preconditions

**Preconditions**: conditions that must be true before a function or method is executed. The caller's obligations. We as the caller create the environment that allow the method or function to work. Say the caller of a function that calculates the square root, should have the obligation, not to send in a negative number. 

**Postconditions**: conditions that must be true after a function or a method has executed. The functions guarantee to the caller. Really, we are looking for the function to have done the task correctly, and leaving its composites in a useable and prescribed manner. Say if we were to sort a list, then the postcondition is that the list is actually sorted. 

**Invariants**: Conditions that must always hold true for a class or a data structure. 

#### Design By Contract (DbC)
We define formal, precise and testable (verifiable) interface specifications for software components. 
- Preconditions: obligations of the caller. 
- Postconditions: guarantees provided by the callee. 
- Invariants: Conditions that must always hold true for a class or data structure. 

A narrow precondition, might be considered a "**narrow contract**", in which, there may be undefined behaviour if we fail to meet this precondition. 
Naturally a "**wide contract**" is one that accepts a wide range of preconditions, handling invalid inputs. 



