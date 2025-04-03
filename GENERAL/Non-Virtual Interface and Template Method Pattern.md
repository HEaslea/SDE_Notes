Is a design pattern in C++ where the interface of a base class is a non virtual, but internally it delegates functionality to protected virtual functions that derived classes can override. 

**Ensuring a consistent interface, while giving derived classes the flexibility to delegate to and customize the behaviour**. 

```
class Base
{ 
public: 
	void Interface() 
	{ 
		std::cout << "Base: Pre_processing" << std::endl;
		DoWork();
		std::cout << "Base: Post_processing" << std::endl;
	}

protected: 
	virtual void DoWork() = 0; // Force derived class to implement
};

class Derived : public Base
{ 
protected: 
	void DoWork() override
	{ 
		std::cout << "DERIVED: doing work" << std::endl;
	}
};

int main() 
{ 
	Derived d;
	d.Interface(); // Calls the non-virtual interface
	// that calls the Virtual Function
	return 0;
}
```

![[Pasted image 20241204200705.png]]

## Template Method Pattern
Allowing derived classes to override certain steps of the algorithm without changing its structure. 

The Base Class will define the template method (non-virtual) that outlines the algorithm, it will contain some default behaviour and delegates specifics to virtual methods. 

Derived Class will implement the virtual methods to provide specific behaviour. 

```
class AbstractProcess 
{ 
public: 
	void Execute() 
	{ 
		Step1(); 
		Step2(); 
		Step3(); 
	}

protected: 
	void Step1()  // common step (non virtual)
	{ 
		std::cout << "STEP 1 : Common Logic\n";
	}

	void Step3() 
	{ 
		std::cout << "STEP 3 : Common Logic\n";
	}
};

class ConcreteProcessA : public AbstractProcess
{ 
protected: 
	void Step2() override // Custom Implementation
	{ 
		std::cout << "STEP 2 : Process A Specific Logic\n";
	}
};

class ConcreteProcessB : public AbstractProcess
{ 
protected: 
	void Step2() override
	{ 
		std::cout << "STEP 2 : Process B Specific Logic\n";
	}
};

int main() 
{ 
	ConcreteProcessA processA; 
	processA.execute();


	ConcreteProcessB processB; 
	processB.execute(); // notice the common interface
}
```


### Insert and Extract without `heapify`
This really nice idea of bubbling up bubbling down in order to maintain heap order.

```
class SimpleHeap 
{ 
private:
	std::vector<int> heap;

	// Bubble down to maintain heap property
	void bubbleDown(int i)
	{ 
		int n = heap.size(); 
		while(true)
		{ 
			int largest = i; 
			int left = 2 * i + 1; // meaning i can be 0
			int right = 2 * i + 2; 
			
			if(left < n && heap[left] > heap[largest])
				largest = left;
			if (right < n && heap[right] > heap[largest])
				largest = right;
				
			if (largest == i) break; // if no swaps, bubbling down

			std::swap(heap[i], heap[largest]);
			i = largest; // then go through again
		}
	}
	
public: 
	//insert into heap
	void insert(int val)
	{ 
		heap.push_back(val); // put new val at end
		// then bubble up to maintain heap order
		while(i > 0 && heap[(i - 1)/2] < heap[i]) // while the parent is less than heap
		{ 
			std::swap(heap[i], hea[(i - 1/ 2)];)
			i = (i - 1) / 2;
		}
	}

	// extract tthe maximum element
		int extractMax() 
		{ 
			if(heap.empty()) throw std::runtime_error("Heap is empty!:");
		
			int maxVal = heap[0];
			heap[0] = heap.back();
			heap.pop_back();

			if(!heap.empty()) // recheck
			{
				bubbleDown(0); // from the root
			}
			
			return maxVal;
		}
};
```



#### `heapify`
As Heaps are usually put onto arrays due to their complete nature. 
That means that have to know how to `heapify` one: 
```
void heapify(int arr[], int N, int i) // where N is size of arr, starting at i
{ 
	int largest = i; 
	int l = 2 * i + 1; // meaning that i can be 0; left 
	int r = 2 * i + 2; // right 

	
}
```
