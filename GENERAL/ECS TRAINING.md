#### Entities 
Entities have become just `uintx_t`  where `x` scales dependent on how many entities we are likely to have. Say 10000, then we want a larger `x`. `x` being the number of bits that we are allowing for this value. 
`INT32_MAX` = 2147483647. So there you go, that's how many entities that we can have. My GPU is quaking in its boots.

```
using Entity = std::uint32_t;

const Entity MAX_ENTITIES = x; // x is semi arbitrary number
```

#### Component
At its core we want the components to be just structs, aggregates basically. With a small chunk of functionally related data. 
```
struct Transform 
{ 
	Vec3 d_Position; 
	Quat d_Rotation;
	Vec3 d_Scale; 
};
```

Then each component is fairly simple from here. 

#### Read About Mixins: 


#### Signatures
Tracking which components an entity has, easiest way is a bunch of ones and zeroes, basically a bit set. Then to see if that entity has that component, we need only look at the bit in that entity's signature. 
Basically `std::bitset<x>` where `x` is the number of types of components that you are choosing to have. 
32 Types of components, then `std::bitset<32>`
If an entity had components of type 2, 3 and 5 then their signature might look something like this: 
`0b10110`, then finding which components an entity has is very simple with bit-wise operations. 
Perhaps there is the idea of predication here, where instead of `if-branching`, we use bitwise operations to see if the bit is on/off. 


#### Entity Manager
The guy uses a container for all the possible numbers. I'm not so sure that this is necessary. 
What are we looking for: 
His code is kind of all together, maybe you could SRP it a bit more, however, it works, so there. 
I think the queue is just so that we cycle through the available entities, we an add a recently surrendered one to the back of the queue. 


```
class EntityManager 
{ 
	std::queue<Entity> mAvailableEntities{};
	std::array<Signature, MAX_ENTITIESF> mSignatures{};
	uint32_t mLivingEntityCount();
	
public:
	EntityManager()
	{ 
		for(Entity entity = 0; entity < MAX_ENTITIES; ++entity)
			mAvailableEntities.push(Entity);
	}

	Entity CreateEntity()
	{ 
		// this should never happen though
		assert(mLivingEntityCount < MAX_ENTITIES, "Too many entities in existence");
		Entity id = mAvailableEntities.front();
		mAvailableEntities.pop()
		++mLivingEntityCount;
		
		return id;
	}

	void DestroyEntity(Entity e)
	{ 
		assert(e < MAX_ENTITIES, "Entity out of range");

		mSignatures[e].reset();

		mAvailableEntities.push(e);
		--mLivingEntityCount;
	}

	void SetSignature(Entity e, Signature s)
	{ 
		 // another assert to check
		 mSignatures[entity] = signature;
	}

	Signature GetSignature(Entity e)
	{ 
		// another assert
		// returning
		return mSignatures[e];
	}
}
```

#### The Component Array
If entity is really just an index into an array of components. Then grabbing the relevant components for the entity is trivial. 
The entire point of ECS is cache-friendly data. 
We also want to be branch friendly as well, meaning that we don't want too many `if(something)` checks either. 

Keeping an array packed is a great way over this. 
Here we have a map to know which Entity's components are in which slot. 
Then to keep the arrays packed, when we delete something, say something in the middle, we move the last element in the array to that slot, then we have to update the map. 
The way he has laid it out is to have abstract Component Array, so that we can use polymorphism later. 
For each component `T`. 

```
class IComponentArray
{ 
public: 
	virtual ~IComponentArray() = default;
	virtual void EntityDestroyed(Entity) = 0;
};

// simple template here
template <typename T> 
class ComponentArray : public IComponentArray
{ 
	// the array holding compont of type T
	std::array<T, MAX_ENTITIES> mComponentArray;

	// Map from an entity id to an array index of this type
	std::unordered_map<Entity, size_t> mEntityToIndexMap

	// Map from an array index to an entity ID
	std::unordered_map<size_t, Entity> mIndexToEntityMap;
	
	// total size of valid entries in the array
	size_t mSize;

public: 
	void InsertData(Entity e, T component)
	{ 
		assert(mEntityToIndexMap.find(entity) == mEntityToIndexMap.end()) etc.

		size_t newIndex = mSize;	
		// Where does this entities component lie in the array
		// not sure about these two these just seem reverse of eawch other
		mEntityToIndexMap[entity] = newIndex;
		mEntityToIndexMap[newIndex] = entity;

		mComponentArray[newIndex] = component;

		++mSize;
	}

	void RemoveData(Entity e)
	{ 
		size_t indexOfremovedEntity = mEntityToIndexMap[e];
		size_t indexOfLastElement = mSize - 1;
		// moving component from back to removed
		mComponentArray[indexOfRemovedEntity] = mComponentArray[indexOfLastElement];

		// updating maps after the move
		Entity entityOfLastElement = mIndexToEntityMap[indexOfLastElement];
		mEntityToIndexMap[entityOfLastElement] = indexOfRemovedEntity;
		mIndexToEntityMap[indexOfRemovedEntity] = entityOfLastElement;

		mEntityToindexMap.erase(e);
		mIndexToEntityMap.erase(indexOfLastElement);
		
		--mSize;
	}


	T& GetData(Entity e)
	{ 
		// returning the reference to the component
		return mComponentArray[mEntityToIndexMap[e]];
	}

	void EntityDestroyed(Entity entity) override
	{ 
		if(mentityToIndexMap.find(entity) != mEntityToIndexMap.end(0))
			RemoveData(entity);
	}
};
```

The other idea here is that we have observer, then when we see the entity is destroyed, then we destroy all the corresponding Components. 

#### The Component Manager
