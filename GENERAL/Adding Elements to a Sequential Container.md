#addingelementstocontainers

Most containers provide flexible memory management. 
Adding and removing elements: 
![[Pasted image 20240316120136.png]]

`emplace_back` creates an object from the `args` given. 

```
struct Q{ 
	int year; 
	string name;
	Q(int y, string n) : year(y), name(n) {}
};

void PrintVelct(vector<Q>& v){ 
	for(auto i : v){ 
		cout << i.name < "  " << i.year << " : ";
	}
	cout << endl;
}

int main(){ 
	vector<Q> vQQ(10, Q()); // error as no default constructor in Q 
	
}
```

If we had
```
Q() = default;
```
Then `vector<Q> vQQ(10, Q()); // would be fine`
This would initialize `year` and `name` to the default value of both int and string. 
If we had `int year = 3; string name = "Default";` then it would initialize to that too.
Or we could do `Q(int year = 3, string n = "Default") etc.` Giving our two arg constructor values to work with as default here. 

Then we want to insert something, we use a `CONTAINER::const_iterator` + whatever index we want, then it will insert BEFORE this element. 


Just want to show this as well: 
#vectorofobjectpointers
```
struct Q
{ 
	int year; 
	string name;

	Q(int y, string n) : year(y), name(n) { cout << "Default Q C called" << endl;}
};

void PrintVect(vector<Q*>& v){ 
	for(auto i : v){ 
		cout << i << " : ";
	}
	cout << endl;
}

int main(){ 
	Q* qp = new Q(4, "Hugo");
	vector<decltype(qp)> vQ(10);
	PrintVect(vQ);
}
```
This will result in ![[Pasted image 20240316130721.png]]

Something like this: 
```
vector<Q> vQQ(10, Q());
PrintVect(VQQ);
vQQ.insert(vQQ.cbegin()) // can you vQQ.begin()
PrintVect(vQQ);
```
![[Pasted image 20240316133336.png]]
If the default values are `"Defaul"` and `0`.


### Using `push_back`
#usingpushback
Every container bar `array and forward_list` will support pushback. 
This will read one word at a time into `word`;
```
// read from standard input, appeneding each word
string word; 
while(cin >> word)
	container.push_back(word);
```
Ofc this will increase the size of the container by 1. 
Because `string` is just a container of characters.
Therefore we can do something like this: 
```
void pluralize(size_t cnt, string& word){ 

	if(cnt > 1)
		world.push_back('s'); // same as word += 's';
}
```

You can also do something like this: 
```
#include <sstream>

void Pluralize(stringstream&);

void Pluralize(stringstream& s){ 
	s << 's';
}

int main() { 
	stringstream uS; 
	uS << "cow";
	Pluralize3(uS);
	cout << uS.str() << endl; // which does the same thing lol
}
```
another way: 
```
void Plural(string &w){ 
	w += 's';
}

int main() { 
	string c("Cow");
	Plural(c);
	cout << c << endl;
}
```

That might be used one too many container tbh. 
The `word += 's'; ` is just so simple, doesn't overcomplicate. 


Container element  are copies. When using an object to initialize a container, or insert an object into a container, a copy of that object's value is placed in the container, not the object itself. 
When we pass an object to a nonreference parameter, there is no relationship between the element in the container and the object from which that value orginiated. 
Changes to the element in the container have no effect on the original object, and vice versa. 
So basically, when you add an element to a container, eg. an object with name data parameter, then that object will be copied and placed in. 

### Using `push_front`
#usingpushfront

```
list<int> ilist;
// add elements to the start of ilist
for(size_t ix = 0; ix != 4; ++ix){ 
	ilist.push_front(ix);
}
``` 

This would result in 3,2,1,0 being added at the front of the list, so not in ascending order!. 

`Vector` does not offer this functionality. `deque` will guarantee constant-time insert and delete of elements at the beginning and end of the container. 

### Adding to a specified Point in the Container
Remember that it will take an iterator as the first argument here. 
Then will insert just before that iterator: 

Here is an interesting concept: 
```
int main(){ 
	vector<strring> sV(5, "Default");
	sV.push_back("Last Element");
	vector<string>::iterator sLast = sV.end() - 1; 
	cout << *sLast << endl;
}
```

output will indeed be: `"Last Element"`

For containers that do not have a `push_front` function, you can just use `insert`. 

```
vector<string> svec; 
list<string> slist;
slist.insert(slist.cbegin(), "Hello!");
svec.insert(svec.cbegin(), "Hello!");
```
Both are fine, can use instead of `push_front`.
Careful that inserting anywhere but at the end of the vector might be really slow. 
`svec.insert(svec.end(), 10, "Anna");`

Pretty obvious. 

```
vector<string> v = {"quasi", "sima", "frollo", "scar"};
// insert the last two elements of v at the beginning of the slist
slist.insert(slist.begin(), v.end() - 2, v.end());
```

```
template<typename T>
void pVect(vector<T> v){
    for(auto s : v){
        cout << s << " : ";
    }
    cout << endl;
}

template<typename T>
void pList(list<T> l){
    for(auto s : l){
        cout << s << " : ";
    }
}


int main(){  
    list<string> slist = {"Test"};
    vector<string> v = {"quasi", "sima", "frollo", "scar"};
    slist.insert(slist.begin(), v.end() - 2, v.end());
    pList(slist);
```
![[Pasted image 20240316152249.png]]
as our output. 
We cannot refer to the same container as the one we are changing, as in, we cannot do: 
`slist.insert(slist.begin(), slist.begin(), slist.end())`

### Using the Return from `insert`
We can use the value return by insert to repeatedly insert elements at a specified location in the container: 
```
list<string> lst;
auto iter = lst.begin(); 
while(cin >> word){ 
	iter =  lst.insert(iter, word)
}
```


### Using Emplace Operations 
#usingemplace

There are three new `emplace_front, empalce, emplace_back`. 
They correspond to `push_front, insert, push_back`.

When we call a push or insert member, we pass objects of the element type and those objects are copied into the container. 
When we call an `emplace` member, we pass arguments to a constructor for the element type. 
The emplace members use those arguments to construct an element directly in space managed by the container, we so cut out the middle man. 
Emplace will use the type that is found in the container
```
// construct a Sales_data object at the end of c
// uses the three-argument Sales_data constructor
c.emplace_back("978-0590353403", 25, 15.99); // fine 
c.push_back("978-0590353403", 25, 15.99); // error, push_back doesn't take three arguments
// if we used push_back, we might have to make a temporary object, using extra memory
c.push_back(Sales_data("978-0590353403", 25, 15.99));
```

`emplace` creates immediately into the container. 
The `push_back` will create a temporary local object. 

Arguments have to match a constructor in the class type: 
```
// iter referes to an element in c, which holds Sales_data elements
c.emplace_back(); // uses the Sales_data default constructor
c.emplace(iter, "999-99999999"); // uses Sales_data(string)
// uses the Sales_data constructor that takes the ISBN, a count, and a price
c.emplace_front("978-234246534", 25, 15.99);
```
