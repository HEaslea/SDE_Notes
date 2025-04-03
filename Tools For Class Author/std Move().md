Will allow the efficient transfer of resources from `T` to another object. 
#rvalues #lvalues
#moveconstructor

[Cherno Video 1](https://www.youtube.com/watch?v=ehMg6zvXuMY) Watch first

[Cherno Video 2](https://www.youtube.com/watch?v=OWNeCTd7yQE)

Allow us to move objects around. We don't always want to copy and object or another. 

Passing object into a function, I have no choice but to copy if. 

The idea is that most of the time we are constructing objects, that we might not need to necessarily. 

If we have a heavy object, that has a lot of strings etc. 
Then we don't want to copy that object a whole bunch of times. 

```
class String
{
public:
    String() = default;
    String(const char* string) // basic constructor takes in string
    // allocates memory
    {
        printf("Created!\n");

        m_Size = strlen(string);
        m_Data = new char[m_Size];
        memcpy(m_Data, string, m_Size);
    }

    ~String()
    {
		printf("Destroyed!\n");
        delete m_Data;
    }

    String(const String& other)
    {
        printf("Copied!\n");
        m_Size = other.m_Size;
        m_Data = new char[m_Size];
        memcpy(m_Data, other.m_Data, m_Size);
    }

    void Print()
    {
        for(uint32_t i = 0; i < m_Size; i++)
            printf("%c", m_Data[i]);
        printf("\n");
    }

private:
    char* m_Data;
    uint32_t m_Size;
};

class Entity
{
public:
    Entity(const String& name)
        : m_Name(name)
    {
    }

    void PrintName()
    {
        m_Name.Print();
    }

private:
    String m_Name;
};

int main(){
    Entity entity(String("Cherno")); // will copy this into entity
    entity.PrintName();
```
![[Pasted image 20240327143645.png]]
First Destroyed is for the temp string that we had make when we did the whole `String("Cherno")`. then the second one is for the destruction of `Entity entity`.

Now, this doesn't seem like an issue, however, if we look at our code, in order to copy, we have to allocate memory on the heap. 
The copying takes place in the `Entity` constructor we were are saying `m_Name(name)` it will have to allocate memory and copy it etc. etc. 

WHY CAN'T WE JUST MOVE IT :) 

Therefore we have to write a move constructor, which is super similar to the copy constructor, except that it takes an rvalue reference, which in this case would be the `"Cherno"` in our entity definition in main.

```
String(String&& other) noexcept // noexcept just so we don't get exceptions
{ 
}
```

In order for this to work we have to make sure that the constructor for our `Entity` will take in a temporary as well:

```
Entity(String&& name)
	: m_Name(name) {}
```

They do the same thing, but that's the first step. 
So all we want to do is make this new `String`'s char pointer, point to the same block of data that the temporary object that we have created using `String("Cherno)`. 


```
String(String&& other) noexcept
{ 
	printf("Moved!\n");
	m_Size = other.m_Size;
}
```










Here is my version of the code: 
```
class inTest{
public:
    inTest() : inTest("Default") {}
    inTest(const string& n) : m_name(n) { printf("inTest Created\n");}
	// able to take in an rvalue too, rather thank copying, will just reference
    // that's why we need the const string& n, rather than just string& n
	
    inTest(const inTest& other) // copy constuctor using lvalue or rvalue
    {
        printf("inTest copied!\n");
        m_name = other.m_name;
    }

    inTest(inTest&& other) // copy constructor using only rvalue, preferred over the other copy constructor
    {
        printf("inTest Moved\n");
        m_name = other.m_name;
    }

private:
    string m_name;
};

class Test{
public:  
    Test(const inTest& iT, int a) : m_inTest(iT), age(a) { printf("Test Created using lValue inTest\n");}
    
    // this is where the m_inTest is copied right here

    Test(inTest&& iT, int a) : m_inTest((inTest&&)iT), age(a) { printf("Test Created Using Rvalue inTest\n"); }
    
    // in this version we move the rvalue inTest
    // if we didn't have the (inTest&&) cast here, then we would implicitly get a
    // cast to a lvalue version of the object, which requires a copy
    // however, having a cast here will make sure that iT stays an rvalue
    // in order to call the correct move "copy" constructor here
    // The cast here is for now necessary, however we will use move
	
private:
    inTest m_inTest;
    int age; // keep it really simple
};

int main(){
    // Test newTest(inTest("Hugo"), 3);
    // creating a temporary type of inTest
    // inTest() here is an rvalue therefore we need a way to bind that rvalue
    // to something in Test, we need a constructor in Test
    // that will accept an rvalue inTest()
    // the way i have done it here is by adding the const inTest& n to the
    // constructor

    Test newTest(inTest("Hugo"), 3);
    // using rvalue move, so that we don't have to copy the temp object we have created here

	inTest temp("Hugo");
	Test tempTest(temp, 3); // using lvalue inTest here
}
```


This is in the `MoveSemantics.cpp`
So if I wrote
```
Test newTest(inTest("Hugo"), 3);
```
We are creating a temp object of inTest so that we can copy that into Test's `newTest`.



This is a pretty good example, and all it is, is looking and constructing based of whether we provide an lvalue or an rvalue. 

I like this one more than cherno's here tbh. 


Here's another example using a struct: 
```
struct MemberForTest{
    string m_Data;

    MemberForTest(const string& n) : m_Data(n) { printf("MFT Created\n"); }
    MemberForTest() : MemberForTest("Default") {}
    // This is fine as the first constructor takes a const string& n, which
    // can refer to rvalues

    MemberForTest(const MemberForTest& other) : m_Data(other.m_Data) { printf("MFT Copied using a Copy\n"); }

    MemberForTest(MemberForTest&& other){
        printf("MFT Moved using the rvalue\n");
        m_Data = other.m_Data;
    }
};

class Test{
public:
    Test(const MemberForTest& mftl) : m_MFT(mftl) { printf("Created using mostly lvalue\n");}
    // the m_MFT(mftl) will call a copy constructor

    Test(MemberForTest&& mftr) : m_MFT((MemberForTest&&) mftr) { printf("Created using rvalue only\n");}

private:
    MemberForTest m_MFT;
};
  
int main(){
    Test newTest(MemberForTest("Hugo"));
}
```

The biggest thing here is that we can pass an rvalue and use a `const type& t` in order to grab that rvalue. 
Creating lvalue const ref to an rvalue is absolutely fine. 

[[Move Constructors]]

Here is another example: 
```
#include <cstring> 
#include <algorithm>

class string 
{ 
	char* data;
public: 

	string(const char* p)
	{ 
		size_t size = std::strlen(p) + 1;
		data = new char[size]; 
		std::memcpy(data, p, size);
	}
	// now that we have allocated to heap memory, we have to remember the rule of three, the destructor, copy constructor and copy assignment
	// we defer writing the assignment operator for now, but you should if you allocate anything
	
	~string()
	{ 
		delete[] data;
	}

	string(const string& that)
	{ 
		size_t = std::strlen(that.data) + 1; 
		data = new char[size];
		std::memcpy(data, that.data, size);
	}
};
```

```
string a(x);                                // line 1
string b(x + y);                            // line 2
string c(some_function_returning_string()); // line 3
```

To note here that only in the first line, where we copy `x` is this deep copy really necessary, because we might want to inspect `x` later and would be very surprised if `x` had changed somehow. 
`x` here is an lvalue. 

However, when we pass for lines 2 and 3, they are not lvalues, they are rvalues, we cannot refer to the data here with either of the two variables that we use in the constructing brackets. 
The client has no way to inspect them again at a later point in time. 
"rvalues" denote temporary objects which are destroyed at the next semicolon ( to be more precise; at the end of the full expression that lexically contains the rvalue ). 

Now we have rvalue references, allows us to detect rvalue arguments via function overloading. 
All we have to do is write a constructor with an rvalue reference parameter. 
We can do anything that we want to do to the source here, it's temporary, we will never have a way to reference it again, move it, shove it, change it completely, make it into something cool, we just have to leave it in "some" valid state:

```
string(string&& that) // string&& is an rvalue reference to a string
{ 
	data = that.data;
	that.data = nullptr;
}
```

Instead of deeply copying the heap data, we have just copied the pointer and then set the original pointer to null (to prevent `delete[]` from source object's destructor from releasing our "just stolen data"). 
We have "stolen" the data that originally belonged to the source string. 

There should also be: 
```
string& operator=(string that)
{ 
	std::swap(data, that.data); // or say using std::swap; then swap()
	return *this;
}
```












```
class Simple{
public:
    int value;
    Simple(int a) : value(a) { cout << "Constructor used \n";}
    Simple() : Simple(0) {}

    ~Simple() = default;

    Simple(Simple & other){
        value = other.value;
        cout << "Copy constructor used\n";
    }

    Simple& operator=(Simple& other)
    {
        value = other.value;
		cout << "Operator= used\n";
    }
};

int main(){
    Simple a(1); // Constructor used
    Simple b(a); // Copy constructor used
	Simple c = b; // Copy constructor used
	a = b; // Operator= used
```









