Base classes typically act as an interface: 
Where a hierarchy represents a set of hierarchically organized concepts. 

- **Interface Inheritance** will usually use `public` inheritance. Separating users from implementations to allows derived classes to be added and changed without affecting the users of base class, functions are available outside the class. 
- **Implementation Inheritance** often uses `private` inheritance. Typically, the derived class provides its functionality by adapting functionality from base classes. 

#### Adapter Pattern
The alternative is **composition** where our object holds the object inheritance. 
![[Pasted image 20240615205048.png]]Privately deriving from the "legacy" implementation, and publicly inheriting from an interface, therefore we can redirect: say if we want to use `Draw()` however, our old rectangle design uses `oldDraw()`: We want to use `oldDraw()` for `Draw()`, however we just want to change the name that is calling old Draw. 
```
class RectangleAdapter : public Rectangle, private LegacyRectangle { 
	void Draw() override{ 
		oldDraw();
	}
}
```

See, now the interface has changed, however, we have used old, now derived classes in order to do that. 

