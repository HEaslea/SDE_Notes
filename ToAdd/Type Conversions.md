Two types are related if there can be a conversion between them. 

`int ival = 3.541 + 3; // compiler may warn about loss of precision`

Rather than add immediately, we will convert and then add. 
Remember that we convert to the LHS of the expression. 

`double i = 3.4561 + 3;`
This would finalise to 6.4561, as the LHS is a double. 
`int i = 3.4561 + 3;`
Readout is 6;

Conversions that happen without us having to manually convert stuff, that would be considered an **implicit conversion**.  
Implicit conversions among arithmetic types are for preserving as much precision as we can. 
So on the right, precision is preserved by implicitly converting the RHS to a double. 

However, when the init happens, the type of the object we are initializing dominates. 
Then our double is converted to object's type. 
`double -> int`, then this int is used to init our `ival`, truncating our decimals. 

When do implicit conversions occur: 
> The compiler automatically converts operands int he following circumstances: 
> - in most expression, values of integrals types smaller than int are first promoted to an appropriate larger integral type. 
> - in conditions, `nonbool` expression are converted to bool. 
> - Init is converted to type of the variable; in assignment, the right hand operand is converted to the type of the LH. 
> - Common type in mixed type arithmetic and relational expression. 
> - Also during function calls. 

## Arithmetic Conversions
We must think about the widest type here, operands to an operator are converted. 
If one type is a long double, then the other operand is converted to type long double regardless of what the second type is, as this is likely wider than the other. 


#### Integral Promotions
Converting the small integral types to a larger integral type. 
`bool, char, signed char, unsigned char, short, unsigned short`, are promoted to `int` if all possible values of that type fit in an int. Otherwise, `unsigned int`.

False - 0 
True - 1

Integral promotions are done first, if the resulting type matches the type needed, no further conversion is needed. 

If the operands are `long` and `unsigned int`, and `int and long` (on this machine), have the same size, the long will be converted to `unsigned int`. If the `long` type has more bits, then the `unsigned int`, will be converted to long. 

Understanding arithmetic conversions is a matter of finding lots of examples. 

`bool flag; char cval;`
![[Pasted image 20240219114709.png]]

#### Other Implicit Conversions
**Array to pointer conversions**. 
```
int ia[10]; // array of ten ints
int *ip = ia; // convert ia to a pointer to the first element
```

This is **not** performed when we use `decltype` of the array. Also when we use a reference to an array. 

**Pointer Conversions**: int value of 0 and nullptr can be converted to any pointer type; a pointer to any `nonconst` type can be converted to `void*`, and a pointer of any type can be converted to a `const void*`. 

**Conversion to `bool`**: Arithmetic or pointer types to bool. 
If 0, then false, any other result yields a `true`. 
```
char *cp = get_string();
if (cp){ etc } // true if the pointer cp is not zero
whlie (*cp) // true while *cp is not the null character due to dereferencing
```

**Conversion to `const`**:
Obviously we can convert a pointer to a non const type, to a pointer of a `const` type, and similarly for references. 
If `T` is a type, we can convert a pointer or a ref to T into a pointer or ref to `const T`. 
```
int i; 
const int &j = i; convert a nonconst to a ref to const int
const int *p = &i; // convert address of a nonconst to the address of a const
```
`itn &r = j, *q = p; // error as conversion from const to non-const not allowed` 
Remember what Tom said about const being explicit. 

The first examples, we can change i using i, however the other two are read-only, meaning that we cannot use either to change i. 

#### Conversions Defined by Class Types: 
Class Types can define conversion so that the compiler will apply automatically. 

Applying a one class-type conversion at a time.

```
string s, t = "a value"; character string literal converted to type string
while (cin >> s) // while condition converts cin to bool
```
We read  `cin` as its result. 
The type here is `istream`, however, we would require type bool. The IO library defines a conversion from `istream to bool`. 
The conversion depends on the last state of the type. 


## Explicit Conversions
Explicitly force an object to be converted to a different type. 
#### Named Casts: 
`cast-name<type>(expression);`
Where `type` is the target type of the conversion; and expression is the value to be cast. If `type` is a reference, then the result is an lvalue. 
Cast name can be any of: 
```
- static_cast
- dynamic_cast
- const_cast
- reinterpret_cast
```
Depending on what type of conversion you want to be performed. 

#### Static_Cast
NOT for low-level const ()
Remember that our top level and low level const are like this:
```
const int i = 0;  
int f;
int const* p = &i; // low level const 
// p is a pointer to a constant int, here i can change, but p can't change i

int *const cp = &f; // p is a constant pointer to an int that isn't constant. 

cont int *const ccp = &i; // both top and low level  
```

![[Pasted image 20240219124612.png]]

This is correct. 

`double slope = static_cast<double>(j)/i;` 
Static is often used when the larger arithmetic type is assigned to a smaller one. 
It informs the reader and the compiler that we are not concerned about the loss of precision, as the compiler would promote the smaller to a larger otherwise. 
The warning message will be turned off here, if the compiler were to do so. 

```
int i = 0; 
bool flag = static_cast<bool>(i);
```

Also for conversion that wouldn't explicitly happen. 
Using `static_cast` to retrieve a pointer value that was stored in a `void*`. 
```
void* p = &d; // meaning the pointer can point to any type
double *dp = static_cast<double*>(p);
```
A void pointer CANNOT be dereferenced unless it is cast to another type. 
This guarantees that the pointer value is preserved. 

![[Pasted image 20240219125758.png]]

`p` and `dp` have the same address. 


#### Const_Cast

This will change only a low level const in its operand: 
```
const char* pc; 
char *p = const_cast<char*>(pc); // ok but writing through p is undefined
```

Casting a const object to a non-const object, casts away the const. 

Only a const_cast can be used to change the const-ness of an expression. Trying to change a const any other way would be compile-time error. 

cannot use a const_cast to change the type of an expression: 

```
const char *cp; 
char *q = static_cast<char*>(cp); 
// error as cp is a const_char
static_cast<string>(cp); // ok; converts string literal to string
const_cast<string>(cp); // const cast only changes constness
```
Output: char* cast to string.
â─↑├1█δ☻ë├ìC☺ï¶àα<@
Very odd. 

#### Reinterpret_Cast
Performing a low-level reinterpretation of the bit pattern of its operands. 

```
int *ip;
char *pc = reinterpret_cast<char*>(ip);
```
Don't forget that the object addressed by `pc` is an `int` not a character. 
If we then did `string str(pc);` this would produce a strange fail at run time. 
I have no idea why you would ever use this, nor what it is for. 

A huge use is to reinterpret the type that pointers point to:
Converting pointers/references: 
`int *ptr = reinterpret_cast<int*>(0x1234);`

Type Punning: (I think this is what the book was trying to get at): 
Where we redefine the type of a block of memory. 
```
float floatValue = 3.14f;
int * intptr = reinterpret_cast<int*>(&floatValue);
```

Bit Manipulation as well: 
Manipulating individual bits of data, this is very tricky here. 
```
int value = 10; 
unsigned char* bytePtr = reinterpret_cast<unsigned char*>(&value);
```

Unsigned char, equivalent to a byte of memory. 
```
unsigned char buffer[100]; 
unsigned char* ptr = buffer; 
*ptr = 0xFF; setting first byte to 0xFF
```
```
unsigned char data[1024]; 
file.read(reinterpret_cast<char*>(data), sizeof(data));
```

#### Old_Style Casts
Then the book tells you to avoid casts, particularly reinterpret_casts. const_cast often indicates a design flaw. 
Old style casts are literally just: 
```
type(expr); // function-style cast notation
(type)expr; // C-Language-style cast notation
```
This will default to static_cast and const_cast, however will do reinterpret_cast if neither of those are legal. 

`char *pc = (char*)ip; // ip is a pointer to int`
More difficult to see. 