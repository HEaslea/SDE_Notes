Strongly-typed `enum class`. 
```
enum A : int;

enum class B; // scoped enum defaults to int but

enum class C : short; // integral underlying type defined here
```

`enums` are visible throughout the scope in which the `enum` is declared. 

By using `class`, we are specifying that the `enum` is scoped, and an `identifier` must be used.
You can also use `struct`. 

```
namespace CardGame_Scoped
{ 
	enum class Suit { Diamonds, Hearts, Clubs, Spades};

	void PlayCard(Suit suit)
	{ 
		if (suit == Suit::Clubs) // enum must be qualified by enum type
		{ /*...*/}
	}
}
```

```
namespace CardGame_NonScoped
{ 
	enum Suit { Diamonds, Hearts, Clubs, Spades };

	void PlayCard(Suit suit)
	{ 
		if( suit == Clubs) // visible without qualifier
		{ /*...*/}
	}
}
```

In these, the same integral type can be used twice: 
```
enum Suit { Diamonds = 5, Hearts, Clubs = 4, Spades};
```

`Spades, Diamonds` are both 5. 

So the `enum` type can be converted to `int` due to the underlying integral type. However, an `int` cannot be converted to the `enum` type .

eg.
```
int account_num = 135692; 
Suit hand;
hand = account_num; // error
```
A cast would be required here. 
You can promote an **un-scoped** enumerator to an integer value without a cast. 

Promoting an **un-scoped** enumerator to an `int` value without having to cast. 
```
int account_num = Hearts; // OK if Hearts is in an unscoped enum
```
Implicitly conversions of this nature, can be unhelpful. 

Scoped enums must be qualified by the enum type name (identifier) and can't be implicitly converted, as  in following example: 

```
namespace ScopedEnumConversions
{
	enum class Suit { Diamonds, Hearts, Clubs, Spades };

	void AttempConversions()
	{ 
		Suit Hand; 
		hand = Clubs; // error, no identifier
		hand = Suit::Clubs; // Fine
		int account_num = 135962; 
		hand = account_num; // Error
		// Cannot convert from `int` to `Suit`
		hand = static_cast<Suit>(account_num);
		// This is weirdly fine, but probably a bug


		account_num = Suit::Hearts; // Error, cannot
		// cast strongly typed to `int`

		account_num = static_cast<int>(Suit::Hearts); // OK
	}
}
```

#### Enums with no enumerators
By defining an enum, with an explicit underlying type and no enumerators, we are essentially introducing a new integral type that has no implicit conversion to any other type. 

eg. 
```
enum class byte : unsigned char { };
```

By using this type, instead of its built in underlying type, we are eliminating the potential subtle errors caused by inadvertent implicit conversions. 

The new type is an exact copy of the underlying type, has the same calling convention, can be used across ABIs (Application Binary Interface). 

```
enum class byte : unsigned char { };

enum class E : int { }; 
E e1 {0};
E e2 = E{0};

struct X
{
	E e{0};
	X() : e{0} {} // default ctor
}

E* p = new E{0};

void f(E e) {};

int main()
{
	f(E{0});
	byte i{42};
	byte j = byte{42};


	// unsigned char c = j; 
	// Error as cannot convert byte to unsigned char
}
```

This just means that our `type` cannot be implicitly converted. 


You might want to think about enums in global scope as a bunch of declarations of the underlying type : eg: 
```
enum Colours : int { Blue, Red, Orange };
// This is what it looks like
int Blue = 0; 
int Red = 1; 
int Orange = 2;
// they are equivalent 
```
Using an unscoped enum is exactly that, unscoped, and the underlying ints are defined globally. 

When we scope it, we are enclosing in a "class" scope. 

It's like scoping what we have above in a defined "class": 
```
enum class Colors : int { 
	int Blue = 0; 
	int Red = 1; 
	int Orange = 2;
};
```

This second one looks a look like what the first one does, but it does not.
