![[Pasted image 20240615202901.png]]

![[Pasted image 20240615202949.png]]```
```
class Sort { 
public: 
	virtual void processData() final { 
		readData();
		sortData();
		writeData();
	}
private: 
	virtual void readData() {};
	virtual void sortdata() = 0; // pure virtual
	vritual void writeData() {}
};
// the pure virtual, the derived should only implement sort data


// therefore
class QuickSort : public Sort { 
private: // inheriting public processData();
	void readData() override { 
		cout << "readData" << endl;
	}
	void sortData() override{ 
		cout << "sortData" << endl;
	}
	void writeData() override { 
		cout << "writeData" << endl;
	}
};

class BubbleSort : public Sort(){ 
private: 
	void sortData() override {  // this has to be overridden
		cout << "sortData" << endl;
	}
};

int main(){ 
	Sort* sort = new QuickSort;
	sort->processData();
	// output : 
	// readData
	// sortData
	// writeData


	sort = new BubbleSort;
	sort->processData();
	// output : 
	// sortData
}
```

Final does not mean that it is not inherited, it does mean that we can't override, but we do inherit still. 

