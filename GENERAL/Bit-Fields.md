Declaring a class data member with explicit size. 
Other members may be packed to share and straddle the individual bytes. 

```
struct S
{ 
	// three-bit unsigned field, meaning that allowed vlalues are 0...7
	unsigned int b : 3;
};
```
In my code I do the same and then just go through some numbers: 
```
struct Test{ 
	int b : 3;
};

int main(){ 
	Test newT; 
	newT.b = 8;
	cout << newT.b << endl; // outs 0, as the number is cut off at 3, and 8 is a 1 and 3 zeroes 
	newT.b = 12;
	cout << newT.b << endl; // outs -4, as twelve is 8 and 4,  but that 4 bit is the signed bit of our int, therefore -4, in two's complement, not just a signed bit

	// if here, b was unsigned int, then b = 12 will out 4, makes sense

	newT.b = 11;
	cout << newT.b << endl; // outs 3, as 8 + 2 + 1, the signed bit is not triggered, therefore, its just 3;
}
```

The idea is that we can use the last bits of a byte, if they are not fully used up, in order to pack efficiently, we could use them as flags or something. 

You can use Visual Studio Memory Layout as a way to double check what you bitfield , however, the general idea I think is that if you have normal types at the beginning and then try and bitfield the padding, that won't really work. 
However, let's say that you use a short, which is two bytes usually, however, you know that you will only use 0-10, then rather, you might use `unsigned` in order to make sure that none of the argued numbers will be signed. 
It's like my own version of `super super short short`. 
`short short` is not a thing for some reason. 


#usingbitflags
```
class MyClass { 
public: 
	// changing underlying value 
	enum Flags : std::uin32_t
	{ 
		FLAG_ONE = 1 << 0; // 0001
		FLAG_TWO = 1 << 1; // 0010
		FLAG_THREE = 1 << 2; // 0100
		FLAG_FOUR = 1 << 3; // 1000
	};
	
	MyClass() : flags(0) {}

	void setFlag(Flags flag)
	{ 
		flags |= flag;
	}


private: 
	std::uint32_t flags;
};
```

