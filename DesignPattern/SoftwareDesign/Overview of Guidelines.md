#### Guideline One : Importance of Software Design

Features are not software design. 
Software design looks at the overall structure. 

The art of managing dependencies and Abstractions. 
Software must be malleable. 

There will always be some form of dependencies, there will therefore always be some form of coupling. 
We must keep artificial dependencies to a minimum. 
We must be good at the art of managing interdependencies between software components. 

**Abstraction**: The organization of functionality and data items into data types and functions. Also the modelling of common behaviour and the representation of a set of requirements and expectations. 

There are really three levels of design: 
1. Software Architecture - client-server, microservices - the overarching, the physical etc.
2. Software Design - Design patterns (Visitor, Observer, External Polymorphism)
3. Code Level - Implementation Details. 
Really, we should be looking at the big picture. 

There is less of a focus on C++ details, rather an overarching look at the software, how everything will connect together etc. 
The boundary between architecture and design is quite blurry. 


#### Guideline Two : Design For Change
This is the constant, that there will be change to our developments; there will be additions, changes of implementation etc. 
**Separation of Concerns**: 
Making the system more modular. 
Separating orthogonal aspects of software. 
>Cohesion is a measure of strength of association of the elements inside a module. High cohesion is found in a module when it is constituted by a collection of statements and data items that should be treated as a whole, as they are so closely related. Dividing up that module would actually ruin the system a little more, increase coupling and decreased readability. 

The S in SOLID is for SINGLE RESPONSIBILITY PRINCIPLE.

Logical and Physical Coupling: 
We draw diagrams in UML (Unified Modeling Language). 

There is the idea of **HIGH and LOW Level**
High Level is that of low dependency on other things, they are the most stable parts of the whole design mostly. They are the larger ideas, or overarching functionalities. They focus on what the software should be doing, not how it does it. 
Low level are the implementation details, how we do it. 

High : what
Low : how

Imagine the high level is the Hero's Quest, it's the what and the why of the game ("Defeat the Dark Wizard" like in Breath of the Wild, where the quest is to just kill Ganandorf). 
Then the Low level is that of going to the blacksmith, it provides the how of the quest, how we are going to beat the quest. 
However, the high should not depend on the low, if the forge were to blow up due to some gnasty goblins, then that shouldn't stop the whole game, it's just one of the hows. 
The hero will depend on the "weapon provider" abstraction, however, this could be a blacksmith, magic enchanter or a treasure chest. 

Lower level will have more frequently changing aspects to it. 
The overall quest doesn't change, but parts of the blacksmith might be changing all the time. 
We should always have low depending on the high. 
If the main quest depended on the blacksmith being a certain way, then we would be out of luck. 

Think of in a game, where the only way we can progress is through one very situational moment in the blacksmith, this would really suck if not done perfectly. However, if there are a thousand ways that we can progress with the main story, every low level part is depending on the high level, however, the high level does not depend on any of them. 

Imagine this too, if all the low level stuff didn't work out, then we wouldn't want the main quest to dissolve. However, if we didn't have the main quest, then the low level stuff doesn't matter at all. 

The less coupling, the more flexible the system will be. 

Physical coupling is that of having the files, and the libraries etc. 

Imagine our hero depends on the blacksmith a lot, then if he moves or gets sick, then we are out of luck. 
Transitive Coupling: if the hero relies on merchants for supplies, and the merchant relies on a farmer. Although the hero doesn't directly rely on the farmer, he will indirectly rely on the farmer. 
Instead of dealing with them specifically (the blacksmith and the merchant (singular of each)), he could rely on the Guild of Suppliers. The guild will handle all of the dependencies internally. 

This is where design patterns come in: where we want to lessen dependencies, coupling etc.

**DRY: DONT REPEAT YOURSELF**
Every responsibility should only exist in one place. 

Avoid premature separation of Concerns. 
Don't try to achieve SOLID, use SOLID to achieve maintainability. 

YAGNI : You aren't going to need it. 

Regularly using unit testing (testing on specific thing in the code) to make sure it works as expected. 

**DFC : Design For Change**


#### Guideline Three : Separate Interfaces To Avoid Artificial Coupling
The I in SOLID: Interface Segregation Principle (ISP). 
A class shouldn't be forced to implement interfaces it does not use. 
We shouldn't be, ever, implementing methods that are not going to be used. 
Breaking up larger, less specific into smaller ones. 

Imagine that we have a party and we are recruiting for that party: however, the requirement is that every single person in the party has to be good at fighting, casting spells and healing: 
The warrior can't really spell cast, the mage cannot wield the sword, and the healer is useless in combat. 

However, if we were to ask only one thing of each, then we can achieve a greater party, meaning that they only do what they are strong at. 
Imagine a worker having to do loads of things at the office, after a while they just end up being cluttered, overworked and harder to move around etc. 

The SRP for Interfaces. 
Changing an entire interface later on will be really difficult. 
This will help for both inheritance hierarchies and templates. 


#### Guideline Four : Design for Testability
There is the idea of white-box testing, and that we should avoid it. 
The tester will have full visibility into how the system works. 
If the test is written with the knowledge of the function in mind, then perhaps the test won't work if we change anything about it. 
The test is based on the implementation: 
The tests will become tightly coupled to the internal workings of the code. 
This leads to fragile testing environments. 
They should be used, when we want to test how something does something, really looking into the internals of something. 
But more often than not, we should be looking at what the function will do, and now how it does it, therefore should be tested on what it does. 
There is black and gray box testing as well. 

This is like testing every single aspect of the potion making process of an alchemist, however, we want to see what the potion does, if he changes the recipe, yet the potion still does the same thing, then that's fine. 
If it doesn't, then we are kind of fucked. 
We shouldn't be looking at every aspect of how the potion is made, or how it works, as that might change, however, let's look at what it does, if it does it well , then that's what we want overall, unless we are super specific into the tuning of that function. 

Inheritance is rarely the answer. 

The answer to testing when the class doesn't lend itself to testing, is **separating concerns**. 
The idea being that we have a free function that will do the majority of the testing. 
