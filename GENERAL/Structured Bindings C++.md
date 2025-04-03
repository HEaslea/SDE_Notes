What feels like the equivalent of Decomposition in C#. 

The C++ template book is saying that we should really be using `auto` (I think you have to), in order to get these to work. 

`auto [var1, var2, var3] = someTupleOrPair;`

`attr cv-auto ref-qualifier [identifier-list] = expression`
`attr cv-auto ref-qualifier [identifier-list]{expression};`
`attr cv-auto ref-qualifier [identifier-list](expression);`

`attr` - sequence of any number of attributes. 
`cv-auto` - possibly cv-qualified type specifier auto (const, volatile). Possibly including static as well. 
`ref-qualifier` - either `&` or `&&`. 
`identifier-list` - list of comma separated identifiers introduced by this declaration, each identifier may be followed by an attribute specifier sequence. 
`expression` - does not have the comma operator at the top level, either an array or non-union class type. Should not be referring to any of the names in the `identifier-list`. 

#### Binding Process
Introducing a uniquely named variable, we will use `e`. Expression having array type A and no ref qualifier is present, then `e` has type `cv A` where `cv` is the `cv` qualifiers in the `cv` auto. `cv` carries over for the type of `e`. Each element of `e` will be copy initialized or direct initialized depending on the situation, from the corresponding element in the expression. 
Say we use `E` to denote the type of `e` in that `E` is equivalent to `std::remove_reference_t<decltype((e))>`. 
Then the types will use the various binding protocols that we have below. 
##### Array Binding: 
```
int a[2] = {1, 2};
auto [x, y] = a; // creates e[2] copies a into e, then x refers to e[0]
// and y refers to e[1]

auto& [xr, yr] = a; // xr refers to a[0] and yr refers to a[1] 
```
#### Type Implementing the Tuple Operations
The expression creates `e`, which is of the same type. 
Similarly, will use `e.get<i>()`. 
```
float x{};
char y{};
int z{};

std::tuple<float&, char&&, int> tpl(x, std::move(y), z);
const auto& [a, b, c] = tpl; 

/* 
using Tpl = const std::tupel<float&, char&&, int>; 
a names a structured binding that refers to x (init'd from get<0>(tpl))
decltypes(a) is std::tuple_element<0, Tpl>::type, i.e float&

b names a structured bind that refers to y (init'd from get<1>(tpl))
decltype(b) is std::tuple_element<1, Tpl>::type, char&&

c names a structured binding that refers to the third componet of tpl
get<2>(tpl)
decltype(c) is std::tuple_element<2, Tpl>::type ie. const int. 
*/ 
```

#### Binding to Data Members
Every non-static data member of E, must be a direct member of E or the same base class of EA, well formed. No union members. 
The number of identifiers must be equal to the number of non-static data members. 
```
struct S
{ 
	mutable int x1 : 2; // bitfield
	volatile double y1;
};

S f() { return S{1, 2.3};}  // some aggregate initialization here

int main() 
{ 
	const auto [x, y] = f(); 
	// x is an int lvalue identifying the 2 bit bit field
}
```


#### Bit Fields
These are only acceptable for integral data types. 
```
struct StructName 
{ 
	dataType fieldName : width;
};

class Classname
{ 
public: 
	dataType fieldName : width;
};

struct Loan2
{ 
	// maximum number 2 ^ 20; 1048575
	unsigned int principal : 20;

	// maximum interest rate of 63
	unsigned int interestRate : 6;

	// maximum period of 63 months
	unsigned int period : 6;
};
```
Reducing the number of bytes that our structs are considerably. 

Apparently, you can define bits that are larger than the default type: 
```
struct values 
{ 
	int num : 520; 
};
```
Remember, that there is still the idea of padding, to ensure that classes fit nicely into our memory. 

We can not create pointers that point to bit fields because they may not start at a byte boundary. 

```
strut BitField
{ 
	unsigned int num : 1;
};
// obviously here, bitset<1> which does allow for pointers
// and of course having that level of abstraction is really nice too
int main() 
{ 
	BitField var;
	var.num = 1;

	// you cannot do either of these
	unsigned int* ptr = &var.num; 
	int& ref = var.num; // remember that references are just pointers/addresses
}
```

1. It is majorly used for memory optimization as it allows us to use only the necessary number of bits to represent a member variable which reduces memory overhead.
2. It can be used to compress the data so that it can take less time to transfer data from one point to another over the network.  
3. It can be used to design hardware registers that have specific bit structures.
4. In graphics applications, color components, pixel formats, and image data often have specific bit requirements which can be customized using bit fields.