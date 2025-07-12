Game world holds a collection of objects. 
Each object implements an update method, simulating one frame of the object's behaviour. 
The one frame thing is pretty important. 

Then, each frame, every object in that collection will be updated. 

This will work wonders more for objects that are independent of each other. 

You might have to store states in order to "remember" what that object is doing. 
Therefore you might have to implement with the state method. 

The order of the update is really important, for most objects (?). 

Sequentially updating can be really useful, however, keep in mind that one might update and one behind it can see, but the ones before won't be able to see until the next frame. 

A really really good idea, is to cache the number of objects in the list before iterating over it, means that new objects added to the list in that frame, will not act within that frame, and possibly screw things up. 

```
int numObjectsThisTurn = numObjects_;
for(int i = 0; i < numObjectsThisTurn; i++)
{
	objects_[i]->update;
}
```

Now a bigger issue is when something is removed during the iterating over. 

One way to solve, is to change that object in the list with a "dead" object, it won't do anything, it will just add a harmless object to that index, it won't do anything but will keep the numbers the same. 

----- 
##### Sample Code: 
```
class Entity{ 
public: 
	Entity()
	 : x_(0), y_(0) {}


	virtual ~Entity() {}
	virtual void update() = 0;

	double x() const { return x_; }
	double y() const { return y_; }

	void set .... 

private: 
	double x_, y_;
};
```

The abstract update method is important here!
The game will maintain a collection of these. 
Here we use a class to represent the World: 
```
class World{ 
public: 
	World()
	: numEntities_(0) {}

	void gameLoop(); 

private:
	Entity* entities_[MAX_ENTITIES];
	int numEntities_;
};
```

Using the game loop patter here: 
```
void Wordl::gameLoop()
{
	while(true)
	{ 
		// Handle User input...
		// Update each entity
		for(int i = 0; i < numEntities_; ++i)
		{ 
			entities_[i]->update();
		}
		// Physics and rendering...
	}
}
```
We have to hold the idea of "object composition over class inheritance". 
Meaning that we should simplify, using contained classes rather than multiple class inherited objects. 
Remember that composition is the "has-a" relationship.

The Component Pattern is super useful here, and will be shown later. 

Back to the skeleton class;
```
class Skeleton : public Entity
{ 
public: 
	Skeleton()
	: patrollingLeft_(false) {}

	virtual void update()
	{ 
		if(patrollingLeft_)
		{ 
			// go left
		} else{ 
			// go right
		}
	}
};
```

Same with statue: 
```
class Statue : public Entity
{ 
public: 
	Statue(int delay)
	: frames_(0), delay_(delay) {}

	virtual void update(){ 
		if(++frames_ == delay_)
		{ 
			shootLightning();
			frames_ = 0;
		}
	}
private: 
	int frames_; 
	int delay_;
	
	void shootLightning()
	{ 
		// do the thing
	}
};
```


Remember the hollow arrow in UML 

![[Pasted image 20240529204033.png]]

[[Aggregation]]
Aggregation is a "use-a" relationship. The lifetimes are not linked, they are independent. 

Composition  is a "has-a" relationship. The lifetime of the object found in another will have a linked lifetime to the "owner". 



The hollow aggregation arrow, means that the lifetimes are not controlled by the parent object, the filled in arrow is where the lifetime of the child object, is controlled by the parent. 


Many games use a **variable time step**. 
If there are dormant objects/entities, then you might have to have a separate list. 

```
class Entity;
class TestEntity;


class Entity {
public:
	Entity() = default;

	virtual ~Entity() {}
	virtual void update() = 0;
};

class TestEntity : public Entity {
public: 
	TestEntity(string n) : name(n) {}
	TestEntity() : name("Default Name") {}

	void update() override {
		cout << "TestEntity update called" << endl;
	}

private: 
	string name;
};

class World {
public: 
	vector<shared_ptr<Entity>> worldEntities;
	
	void UpdateAll()
	{
		for (auto& entity : worldEntities)
		{
			entity->update();
		}
	}

};

int main(){
	World w1;
	TestEntity t1;
	TestEntity t2;
	auto s1 = make_shared<TestEntity>();
	auto s2 = make_shared<TestEntity>();
	w1.worldEntities.push_back(s1);
	w1.worldEntities.push_back(s2);
	w1.UpdateAll();
```

The upcasting and downcasting will work with vectors too. 
Meaning that we can have a derived class, shared pointers, and add them to a vector of the base class shared pointers.

This means that as long as the function in the base class is virtual, then we can dynamically dispatch and call the correct function.

The general idea: 
```
int main() 
{ 
	vector<shared_ptr<Entity>> eV;
	auto te1 = make_shared<TestEntity>(); 
	// remember that this is just a function of shrd-ptr
	// we have to use the () at the end
	// not like the most vexing parse

	eV.push_back(te1); // fine
	eV.[0]->print(); 

	// this is provided that print is a virtual function, otherwise, dynamic dispatch won't work
	// print is defined in the derived classes
}
```

It's just nice to know that this works with vectors, and pointers in general. I guess shared_ptrs are just ptrs in general. 
Yet I believe it was the idea of a container and the type added to that, which confused me. 

If print here was not virtual, then the dynamic dispatch would not occur, meaning that we are actually just pointing statically to the Entity type, which doesn't have a print function, or any idea of that in the derived classes. 

Something to remember is that when we do something like this: 
```
class Derived : Base
// !!!! THIS WILL INHERIT PRIVATELY !!!!
```


#### Concurrent State Machines 
Having two separate state machines. This means that when we have a pair of things eg. carrying a gun, along with standing, ducking, jumping etc. then we don't have to have a state for every pair etc. 

```
class Heroine
{ 
	// Other code...

private: 
	HeroineState* state_;
	HeroineState* equipment_; 
}
```
Then when we take in input for our states, then we just pass that on to the two that we could possibly have. 

```
void Heroine::handleInput(Input input)
{ 
	state_->handleInput(*this, input);
	equipment_->handleInput(*this, input);
}
```
Remember that we are passing in the Heroine here. 

### Hierarchical States
```
// superstate
class OnGroundState : public HeroineState
{ 
public: 
	virtual void handleInput(Heroine& heroine, 
				Input input)
	{ 
		if(input == PRESS_B) // jump...
		else if(input == PRESS_DOWN) // duck...
	}
};

// substate
class DuckingState : public OnGroundState
{ 
public: 
	virtual void handleInput(Heroine& heroine, Input input)
	{ 
		if(input == RELEASE_DOWN)
		{ 
			// stand up ...
		} else { 
			// Didn't handle input, so walk up hierarchy
			OnGroundState::handleInput(heroine, input);
		}
	}
}
```