This is the idea that we pass in a rvalue temp object for use to construct another object of that type. 
Rather than creating that object, and copying all of it into the new object. 
They are part of move semantics, which is a whole topic behind the scenes. 
[[std Move()]]

Here is some code from that same note page in order to explain what is going on. 
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

This is the simpler of the two. 
The reason that we have move and rvalue references here, is so that every time we copy an object, we just move things over, rather than actually copying. 
So really we just wanna move, this is case for larger heavier objects, it's really important so that we don't take up a ton of memory on the stack. 

Remember that a main thing here is that I can say: 
```
const int& iRef = 100;
// however i cannot do
int& error = 100;
```

If I did something like this: 
```
void PrintInt(int&& f){ // has to take in lvalue
	printf("%d", f);
}

int main(){ 
	int i = 10; 
	PrintInt(i); // not allowed, as can't an rvalue reference cannot be bound to a lvalue
	PrintInt(std::move(i)); // all move just does here is convert lvalue thing to rvalue
}
```



```
#include <iostream> 
#include <utility>

using namespace std;

struct MyClass{ 
	MyClass() { printf("Constructor\n"); }
	MyClass(const MyClass&) { printf("Copy Constructor\n"); }
	MyClass(const MyClass&&) noexcept {}
};

int main(){ 
	mClass newclass; // Output: Constructor
	mClass copyclass = newclass; // Output: Copy Constructor
	mClass copyclass2 = std::move(copyclas); // Output: Test Copy with const&&
}
```

```
class MyClass{ 
public: 
	int* data; 

	//Constructor
	MyClass() : data(new int[100]) {}

	//Move Constructor
	MyClass(MyClass&& other) noexcept : data(other.data) { 
		other.data = nullptr;
	}

	//Destructor
	~MyClass(){ 
		delete[] data;
	}
};


int main(){ 
	MyClass temp;/ // temp obj
	MyClass obj(std::move(temp)); // Move Constructor Invoked her

	cout << "Temp.data" << temp.data << endl; // this will print 0
}

// here we have sort of gotten rid of the the temp object, it's data doesn't point to anything now
```

