### Liskov Substitution Principle

> **"Objects of a super class should be replaceable with objects of its subclasses without breaking the application"**. 

Is what it boils down to. 

### Covariance
Allowing a subtype to be substituted where a supertype is expected. 
When a subclass overrides a method from its superclass, the return type of the overridden method can be a substype of the original return type. Allowing for more specific behaviour within the subclass, while maintaining compatibility. 
### Contravariance
Allowing a supertype to be substituted where a subtype is expected. 
When a subclass overrides a method from its superclass, the parameter types of the overridden method can be supertypes of the original parameter types. Allowing the subclass to handle a broader range of input types, enhancing their substitutability. 
### Invariance
Meaning no substitution is allowed: the types must match exactly. 

#### Covariance Example
```
class Animal 
{ 
public: 
	virtual Animal* clone() const 
	{ 
		cout << "CLONING ANIMAL" << endl;
		return new Animal(*this); // this used as a prototype almost
	}
};

class Dog : public Animal 
{ 
public: 
	// Covariant return type : Dog* is a subtype of Animal*
	virtual Dog* clone() const 
	{ 
		cout << "CLONING DOG" << endl;
		return new Dog(*this);
	}
};

int main() 
{ 
	Animal* a = new Dog();
	Animal* b = a->clone(); // Dog::clone()
}
```

#### Contravariance Example
This is a little trickier in C++, as it does not support different parameter types for the same function in order to override. The parameters have to be the same in order for them to override. 
```
class Animal
{ 
public:
	virtual void feed(Animal* a)
	{ 
		cout << "FEEDING ANIMAL" << endl;
	}
};

class Dog : public Animal 
{ 
public: 
	// here Animal::feed is hidden, not overidden as the parameters are not the same
	virtual void feed(Dog* d) // note the lack of override
	{ 
		cout << "FEEDING DOG" << endl;
	}
};
```

### Covariance and Contravariance in Templates
C++ templates are generally invariant, meaning if `Dog` is a subtype of `Animal`, 
`std::vector<Dog>` is not a subtype of `std::vector<Animal>`. 

The lack of Contravariance in C++, means that we do have to put a bit more thought into the parameter types that go into the supertypes, that the subtypes are able to follow that method and override such a thing. 


