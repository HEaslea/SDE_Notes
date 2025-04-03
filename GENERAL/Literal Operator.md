AKA **User Defined Literals**. 
Defining custom behaviour for literal values. 
Eg. you can define `_km` suffix to represent kilometres, automatically converting the literal value into a specific unit or type. 

### Syntax
```
ReturnType operator"" _suffix(unsigned long long);

// string literal operator
ReturnType operator"" _suffix(const char*, std::size_t);

// template literal operator
template<char...> operator"" _suffix();
```

Defining a Kilometre Literal: 
```
#include <iostream> 


struct Kilometres 
{ 
	double value;
};

Kilometres operator"" _km(long double km)
{ 
	return Kilometers{static_cast<double>(km)};
}
// The basics here, just returning the Kilometre type, having to cast long double just to double (not sure why it would give that as an example). 

int main() 
{ 
	Kilometres distance = 10.5_km; // essentially just casting on the fly? 
	cout << "Distance: " << distance.value << " km/n";
}
```


```
constexpr unsigned long long operator"" _bin(const char* str, std::size_t)
{ 
	unsigned long long value = 0;
	for(std::size_t i = 0; i < str[i] != "/0"; ++i)
	{ 
		value <<= 1; // value is what value is shifted one to the left
		if(str[i] == '1')
			value |= 1;
	}
	return value;
}

int main() 
{ 
	auto number = 1011_bin; // should be 11 in decimal
}
```

In this one, the compiler sees `1011_bin`, we interpret the `1011` as a string literal.
Therefore the string literal will correspond to `const char* ` and we need the length as well `std::size_t`.

##### Summary of Parameters in UDLs
**UDL**: - **User Defined Literals**.

**String Literal**: `ReturnType operator"" _suffix(const char* str, std::size_t len);` -> with the usage of `"1011"_bin`

**Numeric Literal**: `ReturnType operator"" _suffic(unsigned long long);` -> with the usage of `1011_bin`. 

**Floating Literal**: `ReturnType operator"" _suffix(long double);` -> with the usage of `10.5_suffix`

**Character Literal**: `ReturnType operator"" _suffix(char);` -> with usage of `'a'_suffix`. 


```
long double operator "" _w(long double);
std::string operator "" _w(const char16_t*, size_t);
unsigned operator ""_w(const char*); 

int main() 
{ 
	1.2_w; // calls operator ""_w(1.2L);
	u"one"_w; // calls operator ""_w(u"one", 3
	12_w; // calls operator ""_w("12")
	"two"_w; // error
}
```

#### Another Good Example
From the book no less: 
```
template <char ... cs> 
int operator "" _B7() // the UDL itself
{ 
	std::array<char, sizeof...(cs)> chars{cs...}; // unpack chars into array
	
	for(char c : chars)
	{ 
		cout << "'" << c << "'" << endl;	
	}
	cout << endl;
	return ...; // placeholder for return value
	// ... here makes no sense
}
```
In template UDL's, the template is used to capture the characters of the literal directly. 
I think the general idea here is that if we want to change a number, say we have a number and it's in base 7, like the example above, then if we do something like `int a = 121_B7;`, then we want `a` to be whatever 121 is in human terms. 

Say if we have one for hex: 
```
template <char ... cs> 
constexpr unsigned int operator "" _hex()
{ 
	constexpr std::array<char, sizeof...(cs)> chars { cs... };

	unsigned int value = 0;

	for(char c : chars)
	{ 
		cout << "'" << c << "'";

		unsigned int digit;
		if(std::isdigit(c))
			digit = c - '0';
		else if(std::isxdigit(c))
			digit = std::toupper(c) - 'A' + 10;
		else
			throw std::invalid_argument("Invalid Char in Hexadecimal Literal");
		value = value * 16 + digit;
	}
	cout << endl;
	return value;
}
```

```
int main() 
{ 
	try 
	{ 
		unsigned int num = 1A3_hex;
	} catch (const std::exception& e)
	{ 
		cerr << "Error: " << e.what() << endl;
	}
}
```



