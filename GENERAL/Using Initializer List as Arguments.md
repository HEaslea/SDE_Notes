#initializer_list
You will have to use `#include <initializer_list>`. 
Passing an arbitrary number of arguments of the same type to a function or a constructor. 

```
void printList(std::intiailizer_list<int> list){ 
	for(auto it = list.begin(); it != list.end(); ++it){ 
		std::cout << *it << " ";
	}
	std::cout << std::endl;
}

class MyClass{ 
public: 
	MyClass(std::initializer_list<int> list){ 
		for(auto it = list.begin(); it != list.end(); ++it){ 
			data.push_back(*it);
		}
	}

	void printData(){ 
		std::cout << elem << " ";
	}
	std::cout << std::endl;

private: 
	std::vector<int> data;
};

int main(){ 
	printList({0,1,2,3,4,5});
}


```




#iteratorofthelast
```
template<typename Iter>
Iter Next(Iter iter){ 
	return ++iter;
}

if((itr != List.end()) && (Next(itr) == List.end()))
{ 
	// points to last element;
}

// wrapping it up a little
template<typename Iter, typename Cont> 
bool is_last(Iter iter, const Cont& cont)
{ 
	return (iter != cont.end()) && (next(iter) == const.end())
}


// Then we just argument the iterator that we are checking if it is last, and the list that it is a part in
```

`if((itr != list.end()) && (itr + 1 == list.end())`

`if((itr != list.end()) && (itr == --list.end())`

```
template<typename Iter> 
Iter Next(Iter iter){ 
	return ++iter;
}

template<typename Iter, typename Cont> 
bool is_last(Iter iter, Cont cont){ 
	return (iter != cont.end()) && (Next(iter) == cont.end());
}

void PrintList(std::initializer_list<int> List){ 
	for(auto it = List.begin(); it != List.end(); ++it){ 
		if(is_last(it, List)){ 
			cout << *it << endl;
			break;
		}
		cout << *it << " : ";
	}
	cout << endl;
}

int main(){ 
	PrintList({0,1,2,3,4,5,6,7});
}
```

All of that just to see if the iterator is on the last element lol. 
But at least we're using templates fairly efficiently here. 

