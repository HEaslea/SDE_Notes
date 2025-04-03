The idea is that every time an object, or an instance of an object is destroyed, whether it be at the end of a function, or just the end of its lifetime, the destructor is called. 

```
class dArray{ 
private: 
	int *array;
	int size;

public: 
	dArray(int size) : size(size){
		array = new int[size]; // dynamic array created
	}

	~dArray() { 
		delete[] array;
	}

	void setValue(int index, int value){ 
		if (index >= 0 && index < size){ 
			array[index] = value;
		}
	}

	int getValue (int index) const{ 
		if (index >= 0 && index < size; ){ 
			return arra[index];
		}
		return -1; // return if index not in range of array
	}
};
int main(){ 
	const size_t SIZE = 5; 
	dArray* arr = new dArray(SIZE); // both the object and the array of the object are dynamic here

	//setting values
	for (int i = 0; i < SIZE; ++i){ 
		arr->setValue(i, i*2); 
	}
	for (int j = 0; j < SIZE; ++j){ 
		cout << arr->getValue(j) << endl;
	}

	delete arr; // calling the destructor
}
```
