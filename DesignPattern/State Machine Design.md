#statedesign
Finite state machines: 
Where the state of the machine allows for certain input to do different things. 

#### Using Enums and Switches (switch statements)
```
enum State  // weakly typed, underlying int
{ 
	STATE_STANDING, 
	// etc.....
}
```

Then we can have that which uses the states: 
```
class Heroine::handleInput(Input input)
{ 
// so the Heroine has a state, then we have a switch on the state, changing what we do for player input
	switch(state_)
	{ 
		case STATE_STANDING:
			if(input == PRESS_B)
			{ 
				state_ = STATE_JUMPING;
				// everything needed for jump here
			} else if(input == PRESS_DOWN)
			{ 
				state = STATE_DUCKING;
				// do everything needed for ducking
			}
			break;
		
	default: // whatever;
	}
}
```

[[The Update Method Design]]
Then the `update()` method can look something like this: 
```
void Heroine::update()
{ 
	if(state_ == STATE_DUCKING)
	{ 
		chargeTime++;
		if(chargeTime_ > MAX_CHARGE)
		{ 
			superBomb(); // the book just says this
		}
	}
}
```

### Classes for States
```
class HeroineState
{ 
public: 
	virtual ~HeroineState();
	virtual void handleInput(Heroine& heroine, 
			Input input) {}
	virtual void update(Heroine& heroine) {}
}
```

We have a class for each state: 

```
class DuckingState : public HeroineState
{
public: 
	DuckingState() : chargeTime_(0) {}

	virtual void handleInput(Heroine& heroine, 
			Input input) 
	{ 
		if(input == RELEASE_DOWN)
		{ 
			// change heroine to standing state !!! explained later
			heroine.setGraphics(IMAGE_STAND);
		}
	}
	// the states themselves have their own update() for the heroine that is passed in
	virtual void update(Heroine& heroine)
	{ 
		chargeTime_++;
		if(chargeTime_ > MAX_CHARGE){ 
			superBomb();
		}
	}

private: 
	int chargeTime_;
}
```

To note, if we see something taking in a another object in terms of composition/aggregation. So heroine has a state, therefore when we are in the state implementation, and we have the outer class in the arg list like `Heroine& heroine`, the likelihood is that in the owning class ie. `Heroine`, we are passing into it something like this `state_->whatever(*this)`. 
You'll see this below:
Therefore in Heroine: update will go from Heroine to the state: 
meaning we don't have to add the states to the list of objects to update every loop
```
class Heroine
{ 
public: 
	virtual void handleInput(Input input)
	{ 
		state_->handleInput(*this, input);
	}

	virtual void update()
	{ 
		state->update(*this);
	}
};
```

We can also set this up as static states of a class that holds all the states that a particular thing can have: 
```
class HeroineState
{ 
public: 
	static StandingState standing;
	static DuckingState ducking;
	static JumpingState jumping;
	static DivingState diving;
	// other code...
}
```

If we wanted the heroine to jump, then the standing state might do something like this: 
The issue earlier is that we didn't really have an instance of the class that the Heroine could point to, to each one. This is just a wrapper for instantiating states, without having to explicitly. 
```
// within the standing state;
if(input == PRESS_B)
{ 
	heroine.state_ = &HeroineState::jumping;
	heroine.setGraphics(IMAGE_JUMP);
}
```

Instantiating like this can sometimes go wrong: 
As they are static, there is only one of them, therefore if we have two heroines, eg. in coop. 
We could have the `handleInput()` in `HeroineState` to optionally return a state. 
`Heroine` will delete the old one and make a new one. 

```
void Heroine::handleInput(Input input)
{ 
	HeroineState* state = state_->handleInput(
	*this, input);
	if(state != NULL)
	{ 
		delete state_;
		state_ = state;
	}
}
```
Just swapping out states. 



#garbagecollectors
just a note on garbage collectors, they will find, behind the scenes, unreachable pointers and delete them for us, therefore, we don't have to worry about them. 


*****

Here is the finalised code, consisting of the: 
- header // including implementation for main
- source

###### Header
```
#ifndef STATE_MACHINE_H
#define STATE_MACHINE_H

#include <string>

 
/* Into Main: USAGE
Entity nE("Hugo", 50);

	std::string temp;
	Input i = PRESS_A;

	while (true) {
		nE.handleInput(i);
		nE.update();

		std::cin >> temp;

		if (temp == "D") {
			break;
		}
		else if (temp == "B" || temp == "b") {
			i = PRESS_B;
		}
		else if (temp == "A" || temp == "a") {
			i = PRESS_A;
		}
		// DEBUG : std::cout << i << std::endl;
	}

*/


enum Input{ PRESS_A, PRESS_B };

class Entity;
class DuckingState; class StandingState; class JumpingState; // hate this

// see below for Entity, that points to an EntityState

class EntityState {
public: 
	static DuckingState ducking;
	static StandingState standing;
	static JumpingState jumping;
	EntityState() = default;
	virtual ~EntityState() = default;
	virtual void handleInput(Entity&, Input);
	virtual void update(Entity&);
};

class DuckingState : public EntityState {
public:
	DuckingState() = default;
	~DuckingState() = default;

	void handleInput(Entity&, Input) override;
	void update(Entity&) override;
};

class StandingState : public EntityState {
public:
	StandingState() = default;
	~StandingState() = default;

	void handleInput(Entity&, Input) override;
	void update(Entity&) override;
};

class JumpingState : public EntityState {
public:
	JumpingState() = default;
	~JumpingState() = default;

	void handleInput(Entity&, Input) override;
	void update(Entity&) override;
};


class Entity {
public:
	EntityState* e_State;

public: 
	Entity() = delete;
	Entity(const std::string&, int);
	void update();
	void handleInput(Input);
	void outStat();
	std::string getName();

private: 
	std::string e_Name;
	int e_Health;
};

#endif
```

###### Source
```
#include "StateMachine.h"

#include <string>
#include <iostream>



// ENTITY STATES
/*
	Required forward declaration of child states eg. DuckingState in StateMachine.h
*/

// Necessary?
DuckingState EntityState::ducking;
StandingState EntityState::standing;
JumpingState EntityState::jumping;


void EntityState::handleInput(Entity& entity, Input input)
{
	std::cout << "Handling input" << std::endl;
}

void EntityState::update(Entity& entity) {
	std::cout << "UPDATING" << std::endl;
}



//DUCKING
void DuckingState::handleInput (Entity& entity, Input input)
{ 

	if (input == PRESS_A)
	{
		entity.e_State = &EntityState::standing;
	}
	else if (input == PRESS_B)
	{
		std::cout << "----- Cannot Duck Further -----" << std::endl;
	}
}

void DuckingState::update(Entity& entity)
{
	std::cout << "--DuckingState Update: " << entity.getName() << std::endl;
}


// STANDING
void StandingState::handleInput(Entity& entity, Input input)
{
	if (input == PRESS_A)
	{
		entity.e_State = &EntityState::jumping;
	}
	else if (input == PRESS_B)
	{
		entity.e_State = &EntityState::ducking;
	}

}

void StandingState::update(Entity& entity)
{
	std::cout << "--StandingState Update: " << entity.getName() << std::endl;
}


//JUMPING
void JumpingState::handleInput(Entity& entity, Input input)
{
	if (input == PRESS_A) {
		std::cout << "----- Already Jumping in the Air ------" << std::endl;
	}
	else if (input == PRESS_B)
	{
		entity.e_State = &EntityState::standing;
	}
}

void JumpingState::update(Entity& entity)
{
	std::cout << "--Jumpingstate Update: " << entity.getName() << std::endl;
}




// Entity 

Entity::Entity(const std::string& s, int a) : e_Name(), e_Health(a) {
	e_State = &EntityState::standing;
	std:: cout << "-- Created Entity Object --" << std::endl; }

void Entity::handleInput(Input input)
{
	e_State->handleInput(*this, input);
}

void Entity::update()
{
	e_State->update(*this);
}


void Entity::outStat()
{
	std::cout << "Name : " << e_Name << "   Health : " << e_Health << std::endl;
}

std::string Entity::getName()
{
	return e_Name;
}


```