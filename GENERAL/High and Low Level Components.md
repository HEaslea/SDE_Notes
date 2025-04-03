### High - Level Components
**THE WHAT IT DOES**
Usually more abstract, and close to the overall system design and architecture. 

- High stability: they change less frequently, as they represent core aspects of the design. 
- More general: Defining broader behaviours, policies, eg. interfaces, abstract classes, or overarching design patterns. 
- Dependencies : High level components should not depend on low level components, however, we should use abstraction that low level components will implement, local inversion of dependencies etc. 

### Low - Level Components
**THE HOW IT DOES**
These are more concrete and implementation specific. They are often classes and functions that fulfil the behaviour defined by the high level components. 

- Less Stable: They can change more frequently, due to changing requirements or implementation details. 
- More Detailed: Looking closely at how something should behave, and **HOW** to get it to work. 
- Client Code: in the general context, we might say that client code is low level, as they are usually the ones to implement the how. 

```
class IDataProcessor 
{ 
public: 
	virtual void processData() = 0; // defines the *what*
};

// low level component
class FileDataProcess : public IDataProcessor
{ 
public: 
	void processData() override
	{ 
		// implementation specific details (the *how*)
	}
}
```

And when the book says that we are introducing an abstraction, that actual means that we are introducing an abstract class, that looks more closely to the interface and the what, meaning that we leave the low level code, the how. 
We have to make sure that our low level depends on the high level and not the other way around. 

#### Adhere to the Expected Behaviour of Abstractions
When we introduce an abstraction, we are introducing a set of requirements and expectations, not just an interface. 
When we come to implement that abstraction, hopefully somewhere, in the documentation, we are thinking about what the abstraction actually wants us to do, whether it be pay closer attention to the post and pre conditions etc. (eg. linear search doesn't require (precondition) or being sorted being `sort()`). 

##### The Liskov Principle 
> Objects of superclass should be replaceable with objects of a subclass without altering the correctness of the program. 

The idea of covariance (that we replace the supertype with the subtype). 

All we are really talking about here is that the implemented behaviour of the subclass is going to work in the context of the base class. 

Here's one that doesn't work like that: Here is an LSP Violation: 
```
class Bird
{ 
public: 
	virtual void fly() = 0; // abstract class method
};

class Sparrow : public Bird
{
public: 
	void fly() override
	{ 
		// sparrow's implementation of flying
	}
};

class Ostrich : public Bird
{ 
public: 
	void fly() override
	{ 
		throw std::runtime_error("Ostriches Can't Fly");
	}
};
```
This is super obvious, and needs no explanation. 

Here, the issue is clearly architectural, how would you expect ostrich to just fit in with such a wide net as `Bird`. 
So let's abstract, separate things out, and see if we can have another WHAT (higher level abstraction) that will better fit ostriches: 
```
class Bird 
{ 
public: 
	virtual void eat() = 0;
};

class FlyingBird : public Bird
{
public: 
	virtual void fly(); = 0// 
};

class Sparrow : public FlyingBird 
{ 
public:
	void eat() override { /*  ..... */ }
	void fly() override { /*  ..... */ } 
};

class Ostrich : public Bird
{ 
public: 
	void eat() override { /*  ..... */ }
};
```
Here, bird is a more general abstraction, that oddly doesn't require all of its subtypes to be able to fly. We may have another abstraction for that (which we do) which is `FlyingBird`. 

### Some Guidelines
1. **Avoid Strengthening Preconditions**: The derived class should not be imposing greater restrictions onto the preconditions than the base class. Say if the base class accepts all integers, then the derived class should not be limiting which integers that it will be able to accept. 
2. **Avoid Weakening Postconditions**: What are the guarantees of the base class? If a biproduct of a function found within the abstract class is that the returned list be sorted, then of course, the derived class should be defining that too. 
3. **Preserve Invariants**: Invariants are things that throughout the implementation and usage/abstraction of, should never change, for instance: if the base class ensures that our `size` member is always non-negative, our derived classes should not be allowing any `size`that becomes negative. 
4. **Avoid Side Effects**: this just seems obvious, we have to adhere to expectations of the base class. 


This is true of all concepts, not just run time dynamic polymorphism. We should be able to find these principles even through template code as well. 

### DIP 
#### Dependency Inversion Principle
Bring Abstractions to a higher level. 

> **High level modules should not depend on low level modules. Both should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions**

High level modules (that define logic or core application behaviour) should not be tightly coupled to low level modules (which handle specific details like file handling or data base operations etc.). 
Both high and low level should rely on a shared abstraction such as an interface or abstract class. 

#### Bringing Abstractions to a Higher Level
1. Introduce an Abstract Interface or Base Class : create an abstract interface or class that defines the contract or behaviour expected by the high level module. 
2. Make High Level Modules Depend on Abstractions : The high level module (controller or service class) should interact with the abstraction, not wit a specific low level implementation. 
3. Have Low Level Modules Implement the Abstraction : This will put in the dependence that we want in the right order. 

##### DIP without Abstraction (Tight Coupling)
```
class CreditCardPayment
{ 
public: 
	void processCreditCardPayment() 
	{ 
		// Low level implementation for credit card processing
	}
};

class PaymentProcessor
{ 
	CreditCardPayment ccPayment; // high level depends on low level
public: 
	void process()
	{ 
		ccPayment.processCreditCardPayment();
	}
};
```
The issue here is that the high level `PaymentProcessor` is tightly coupled to `CreditCardPayement`. If we wanted to add more payment methods, such as `PayPalPayment`, we would need to modify `PaymentProcess`, which also violates OCP (open to extension, close to modification). 

The way to navigate this is to introduce an abstraction: `IPayment`
```
// High level Abstraction (interface of base class)
class IPayment
{ 
public: 
	// pure virtuals
	virtual void processPayment() = 0; // abstract function (the contract)
	virtual ~IPayment() = default;
};

class CreditCardPayemtn : public IPayment
{ 
public: 
	void processPayment() override
	{ 
		// Concrete Card processing implementation
	}
};

class PayPalPayment : public IPayment
{ 
public: 
	void processPayment() override
	{ 
		// Concrete PayPal Processing Implementation
	}
};
```
Now we see that our lower level modules are depending on our `IPayment`. 
Now the payment idea and the implementation are a layer removed in the abstraction. 
