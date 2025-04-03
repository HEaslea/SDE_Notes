#assignmentandswap
[[Containers]]
[[Sequential Containers]]

Assignment-related operators, act on the entire container. 

The assignment operator replaces the entire range of elements in the left-hand container with copies of the elements from the right-hand operand: 
```
c1 = c2; // replace the contents of c1 witha copy of the elements in c2
c1 = {a,b,c}; // after the assignment c1 has size 3
```
![[Pasted image 20240315175851.png]]```


```
	intarray a1 = {0,1,2,3,4,5,6,7,8,9};
    intarray a2 = {0};
    a1 = a2;
	
    for(int f : a1){
        cout << f << endl;
    }
```

Then a1 is just 10 0's.

IF the containers are of unequal size, after the assignment, both would be of the same size after the operation. 

You wouldn't be able to do something like this.
```
a2 = {0}; // from above
// error as cannot assign to an array from a braced list
```
Cannot assign to arrays of different sizes: 
```
array<int, 10> lArray = {0,1,2,3,4,5,6,7,8,9};
array<int, 3> sArray = {0,1,2};
lArray = sArray;
cout << lArray.size() << endl;
```

### Sequential Containers only, using `assign`
#usingassign
The operator for assign, requires that the left hand and right hand operands have the same type. 
It copies all elements from the right-hand operand into the left-hand operand. 
THis is because in their classes they have that operator defined too, that depicts what that container should do in that instance. 
```
_Vector_profile_pre&
      operator=(const _Vector_profile_pre&)
      { _M_conjure()._M_profile_destruct(); }
```
it makes no sense, but that is where the = operator is defined. IT is defined somewhere, even if the compiler does it for us at some point. 

Most containers, have a member named `assign` that lets us assign from a different but compatible type, or assign from a subsequence of a container. 
We can use `assign` to assign a range of `char*` values from a vector into a `list` of `string`: 

```
list<string> names; 
vector<const char*> oldstyle;
names = oldstyle; // obvious error
// next is ok, as can convert from const char* to string
names.assign(oldstyle.begin(), oldstyle.end());
```
It doesn't work the other way around: 
`oldstyle.assign(names.cbegin(), names.cend());`
As you can go from `const char*` to `string` but not the other way around. 

So `assign` will put all the elements in the arguments into the operand on the left hand side. 

You cannot `assign` from the container that we are `assign`ing to. 

Another version of `assign` takes an integral value and an element value. 
It replaces the elements in the container with the specified number of elements, each of which has the specified element value: 
Just like the assignment constructor. 

```
// equivalent to slist1.clear();
// followed by slist1.insert(slist1.begin(), 10, "Hiya!");
list<string> slist1(1); // one element, which is the empty string
slist1.assign(10, "Hiya!"); // ten elements; each one is Hiya!
```


### Using `swap`
#usingswap
The `swap` operation exchanges the contents of two containers of the same type. 
The elements will be interchanged, meaning that they are completely swapped. 


Here's the idea: 
```
void PrintVect(vector<array<int,5>>& thisvect){
    for(array<int,5> a : thisvect){
        for(int i : a){
            cout << i << " :: ";
        }
        cout << endl;
    }
}

int main(){
    vector<array<int,5> > arrayvect;
    array<int,5> odd = {1,3,5,7,9};
    array<int,5> even = {0,2,4,6,8};
    arrayvect.push_back(odd);
    arrayvect.push_back(even);

    PrintVect(arrayvect);
    cout << endl;
    swap(arrayvect[0], arrayvect[1]);
    PrintVect(arrayvect);
```

And the output will be:
![[Pasted image 20240315194426.png]]
So you can see that the arrays have swapped. 
Really really simple. 

Now something we noticed from this is: when we want to swap two containers, we cannot use the whole `container.swap(newcontainer)`. Because you can't see what you need to swap. `swap()` has to be a function on its own, not a member function in a class. Otherwise how would the swap work, how would you assign etc. As you can imagine `swap()` is a void function, it just swaps in place using references. 

```
vector<string> svec1(10); // vector with ten elements
vector<string> svec2(24); // vector with 24 elements
swap(svec1, svec2);
```

```
vector<int> sv(10);
    vector<int> sb(24);
    cout << sv.size() << "   " << sb.size() << endl;
    swap(sv, sb); // swap size too essentially
    cout << sv.size() << "   " << sb.size() << endl;
```
output will be 
```
10   24 
24   10
```


With the exception of `array`, swapping two containers is guaranteed to be fast - the elements themselves are not swapped, internal data structures are swapped. 
The fact that elements are not moved, means that, witht he exception of `string`, iterators, references, and pointers into the containers are not invalidated. As in the elements are in the same place in memory. 


Here is a very good way of looking at it, noticing that the memory addresses don't change: 
```
#define AS 10
using namespace std;
typedef array<int, AS> nA;

void OutAdd(nA& one, nA& two){
    size_t cnt = 0;
    while(cnt < one.size()){
        cout << one[cnt] << " " << &one[cnt] << "   :   " << two[cnt] << " " << &two[cnt] << endl;
        ++cnt;
    }
}

int main(){
    nA bar, sar;
    size_t mCnt = 0;
    while(mCnt != AS){
        bar[mCnt] = mCnt;
        sar[mCnt] = mCnt + AS;
        ++mCnt;
    }

    OutAdd(bar, sar);
    swap(bar, sar);
    cout << "\nSWAPPED: SWAPPED: SWAPPED:" << endl;
    OutAdd(bar, sar);
}
```

The output will look something like this; 
![[Pasted image 20240315203457.png]]
You might also want to add the idea that if you point to an element in `bar` our first array, with numbers 0-9. After the swap it will still point to the same memory address, therefore whatever it is in `sar` out second array. 
Say we have a pointer to int, with the address `0x61fef8` which is 5 in `bar` when they are swapped, the value will be 15. 

Pretty cool idea for pointers, iterators and what not lol. 

This means that the time to perform this is based on how many elements are in the container.

You could easily write the function for this. 

```
void OutAdd(nA& one, nA& two){
    size_t cnt = 0;
    while(cnt < one.size()){
        cout << one[cnt] << " " << &one[cnt] << "   :   " << two[cnt] << " " << &two[cnt] << endl;
        ++cnt;
    }
}

int main(){
    nA bar, sar;
    size_t mCnt = 0;
    while(mCnt != AS){
        bar[mCnt] = mCnt;
        sar[mCnt] = mCnt + AS;
        ++mCnt;
    }
	
    //OutAdd(bar, sar);
    int* bar3ptr = &bar[3];
    cout << bar3ptr << "  " << *bar3ptr << endl;
    swap(bar, sar);
    cout << bar3ptr << "  " << *bar3ptr << endl;
    //OutAdd(bar, sar);
```

