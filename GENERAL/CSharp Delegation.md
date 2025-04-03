**THIS IS NOT DELEGATE, WHICH ARE COMPLETELY DIFFERENT**

The idea here is that a class will hand off (delegate) responsibility for some of its behaviour to another class. 
The containing class might not implement all methods directly but exposes the methods of it contained class to the outside world. 

```
public class Engine
{ 
	// i think the ctor is defaulted here
	// much like in C++
	
	public void Start() 
	{ 
		 // any implementation here
	}

	public void Stop()
	{ 
		//....
	}
}
```
```
public class Car
{ 
	private Engine _engine;


	public Car() 
	{ 
		_engine = new Engine;
	}

	// delegating Engine methods
	public void StartEngine()
	{ 
		_engine.Start();
	}

	public void StopEngine()
	{ 
		_engine.Stop();
	}
}
```
Here `Car` is delegating to the implementation found in the `Engine` class here. 

#### Advantages
1. Separation of Concerns: we can just delegate to the class that holds the behaviour that we want, rather than rewriting in the classes that, really, have little to do with the implementation of it. 
2. Composition over Inheritance: Creating new classes for new operations and uses, means that we are separating, Single Responsibility Principle etc.
3. Encapsulation.
4. Extensibility.

### Delegating with Interfaces

```
public interface IEngine 
{ 
	void Start();
	void Stop();
}

public class ElectricEngine : IEngine
{ 
	public void Start() 
	{ 
		//...	
	}

	public void Stop()
	{ 
		//... 
	}
}

public class GasEngine : IEngine 
{ 
	public void Start() 
	{ 
		//...	
	}

	public void Stop()
	{ 
		//... 
	}
}

public class car 
{ 
	private IEngine _engine;

	public Car(IEngine engine)
	{ 
		_engine = engine;
	}

	// here we are delegating to which engine that we have
	public void StartEngine()
	{ 
		_engine.Start();
	}
	
	public void StopEngine()
	{ 
		_engine.Stop();
	}
}

Car electricCar = new Car(new ElectricEngine());
electricCar.StartEngine();
electricCar.StopEngine();
```

Similar with the `gasEngine`. 
