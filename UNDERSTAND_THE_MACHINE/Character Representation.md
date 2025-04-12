Human or machine-readable symbol that is typically a non-numeric entity. 

We have to have for control characters and special characters too. 


# The Data
Using a 1-byte, multibyte sequence to **encode** the chars. 

ASCII - Unicode, members all be represented with 1/multibyte binary sequences. 

#### ASCII Char Set
128 chars, that's 8 bits, that's beautiful right there. 
($0 -> $7F)

There is a standardized mapping, especially if we have peripheral devices. 

Nearly everyone uses it, need that standard so that everything knows that 65 is `A`. 

ASCII is the standard for data interchange across computer systems and programs. 

For ASCII - 4 groups of 32 chars. 
The first, $0->$1F (0 through 31) -> the control characters -> carriage return. 

Second Group of 32 -> various punctuation symbols, special chars, and the numeric digits. 
The space char being one of those. 

The Third Group of 32 -> uppercase alphabetic char. A through Z range $41 through to $5A. 

There are 26 different alphabetic chars- duh, therefore, the remaining 6 are various special symbols, now this is important. 

Lower case is the fourth group, $61  - $7A. 
**The difference between the lower and upper case, are a difference in 1 bit position** 

![[Pasted image 20250330142840.png]]
A difference of 2^5, $32_{10}$. 
Therefore just set the 5th bit to 1. 

```
char original = 'A'; // 'A' is 65 (0b01000001)
char modified = original | (1 << 5);
```

You can just add 32 to this .

The interesting thing here is that 5 and 6 denote the character's group. 
![[Pasted image 20250330143559.png]]
This is because the 5th bit is 32, there are groups of 32. 
3rd group is going to be at the beginning of 64, all the way up to 96. 
The hexadecimal grouping is very important. 
The LO nibble is the binary equivalent of the represented number. 
2^5  (32) is just $20. 
So the first group is $00, the second is $20, the third is $40, then $60 for the fourth. 

There will be differences between machines. 

Exchanging just simple text between two different systems can be an exercise in frustration lol. 

### The EBCDIC Char Set
IBM used EBCDIC Set. 
Rarely seen outside of that. 

There's also BCDIC, that is the unextended version. 
Born on the old machines. 
It's a family of character sets. 
There is a common core, different versions, known as `code pages`, where different encodings for different things. 
Many algs that work well with ASCII do not work with EBCDIC as it's a little more weird. 

### Double-Byte Character Sets
DBCSs represent using 16 bits. 
Single byte for most, however, some are double byte (not all double byte is the take away). 

Using ASCII with, some additional bits from `$80 -$FF`. 

I think things like punctuation is still in one byte. 
Chinese, Japanese, there are more chars, therefore, we need more values (range). 

- **Shift-JIS** (used in Japan)
    
- **GB2312** (used for Simplified Chinese)
    
- **Big5** (used for Traditional Chinese)
    
- **EUC-KR** (used for Korean)

For instance, in **Shift-JIS** encoding (used for Japanese):

- **One-byte characters** (like English letters or punctuation) use values from **0x00 to 0x7F**.
    
- **Two-byte characters** (used for Japanese kanji) use values from **0x81 to 0x9F** and **0xE0 to 0xFC** in the first byte, with the second byte completing the character.

More chars - Very good for East Asian Languages. 

However, we would need to distinguish between single and double byte chars. 
There are compatibility issues as well. 

Each extension byte allows another set of chars to be explored. 

Bitmapped displays with software character generators became prevalent.
meaning that each pixel on the screen corresponds to a bit (or group of bits) in memory. 

Then there are different levels of bits, eg a byte for one pixel. 

Software Character Generators: using the bitmapped memory to draw character or symbols on the screen. 
Instead of relying on a fixed set of pre-defined hardware characters, this can create characters on the fly. 

Imagine that we wanted to display the letter A on the screen, the software character generator would look up the bitmap that represents the letter A, find the corresponding pattern in mem, instruct the bitmapped display to light up the appropriate pixels on the screen to form the letter "A". 

**Fonts**: 
Fonts are usually vector graphics (not bitmaps), meaning that they are scalable. 
Requiring a `.ttf` (TrueType Fonts) or `.otf` (OpenType Fonts). 
There is still the idea of encoding for chars `A` is a specific code point. 
However, when we come to render that char, we use the font that we want, and that will make it look different. 
*The Rendering Process:* each char is associated with a glyph. Visual representation of the char ("A", "B"). Stored in font file, containing info about how to draw the char (shapes, curves, spacing etc.). 
*Kerning*: Spacing between specific chars, AV Tends to be closer than TV, which is very cool indeed. 
This all has to be scalable, that's why we use vector graphics. Scale without losing quality. 

A common C++ trick to go through a string using expression like `++ptrChar` or `--ptrChar`. **THIS WOULDN'T WORK WITH DEBCs**. 

There is a lack of standard, therefore, if you need more than 256 chars, then you're better of using Unicode. 

## The Unicode Character Set
Before this there was only DBCSs, however, non-standardized and would need a special library in order to make it work. 

That's why we have Unicode, 2-byte character size. 
This would <u>still</u> require some special library to change from 1byte code to this. 
This means that we can show 65,536 chars, huge over the 256 1byte of ASCII. 

If the HO 9 bits of Unicode, all contain 0, then the LO 7 bits will be the ASCII. 
If they do contain a 1, then we know that it's a Unicode specific char. 

TO NOTE: There are some asian languages that have more than 4096, which is sadly, a little more than ASCII. 

Apparently half of the char codes are taken up, meaning there are more codes that we can use if Aliens give us there language. 

There is a standard, by the Unicode Consortium, for potability across systems. 

#### Unicode Code Points
There are emojis, arrows, pointers, wide variety of stuff. 
There are so many of those things, that aren't chars used in languages, that there are 1,112,064 (>) code points. That is far over the 2-byte. 
A **Code Point**: is simply an integer value that Unicode Associates with a particular character symbol : The idea is hexadecimal with `U+` prefix: `U+0041`, that is the letter A. 
(Remember this, very important, that if you see two hexadecimal numbers, that's a byte). 

##### Unicode Code Planes 
There are blocks of 2 byte chars. 
They are *multilingual plane*. 
`U+000000` to `U+00FFFF` ::= the original, the BMP, the Basic Multilingual Plane. 
Planes `U+010000 -> U+01FFFF`, 2`U+020000 -> U+02FFFF` , the pattern is clear, therefore, plane 14 is `U+0EFFFF`. 
Note that `U+10FFFF` is 1,114,111. 
There's the idea of *surrogates*, they are Unicode extensions. 
The HO two hexadecimal digits of the 6 digit code, value specify multilingual. 
Remember that the first plane is the BMP (Basic Multilingual Plane). 

## Surrogate Code Points
It became obvious, even 2 bytes were not enough. 
Storing chars outside the BMP. 
We have just seen the BMP  is just `U+0000` to `U+FFFF`: 

The Supplementary: `U+10000` to `U+10FFFF` (rarer chars, emojis, ancient scripts)> 

The Surrogate Code Points: `U+D800` to `U+DFFF` -> Surrogate Code Point Range. 

They are not valid Unicode Characters. 
They are used internally in UTF-16 encoding

![[Pasted image 20250401234536.png]]
The Emoji 😊 (`U+1F60A`) is outside the BMP, stored using two surrogate code points in UTF-16 .

Gaming analogy: 
Spells in a book, only basic spells (1-slot spells). 
Then we need a higher level spell, however, we only have basics. 
Therefore the system is: 
- First Rune (`U+D800 - U+DBFF`) = High Surrogate (like the setup spell)
- Second Rune (`U+DC00 - U+DFFF`) = Low Surrogate (activation spell). 

In Unicode : Anything beyond `U+FFFF` (such as `U+1F60A`) : This has to be broken into UTF-16 : High Surrogate here would be `0xD83D` and low is `0xDE0A`. 
![[Pasted image 20250402000110.png]]

**Code Point** - Atomic unit of information. 

I was missing the point, those chars can't represent anything, as they are two bytes in the 4 byte surrogate, if they represented something, they would just output that char. 
The range for us to know that it's a surrogate (used in a combination). Therefore, we just have a combination, where neither UTF-16 Code Point is going above the larger value. 
Therefore we need a range, that by themselves, do not represent something, so that we know that there is a high and a low and we can use that. 

The idea being that `A` exists in conceptual space, and there are infinite ways to encode that (turning it into bytes). 
![[Pasted image 20250402001059.png]]
Surrogates only appear in the BMP, they are not in the other planes. 

### Something Interesting with BitMasks
This is something I've not thought about before. 
Take the idea of the number 8, `0b1000`, then think about how if we go from 8 to 15 (in this case), we are going through every single combination of the LO 3 bits. 
8   →  1000  
9   →  1001  
10  →  1010  
11  →  1011  
12  →  1100  
13  →  1101  
14  →  1110  
15  →  1111  

So that is 2^n -> 2^n+1 - 1, all the combinations of the first `n` bits. 

### Glyphs, Characters, Grapheme Clusters
Each Unicode code point, has a unique name. 

`U+0045` -> "LATIN CAPITAL LETTER A" : A is not the name of the char -> `A` is the glyph. 

Time Roman Letter A and Times Roman Italic Letter A have different Glyphs (different shapes). 
However, Unicode doesn't differentiate -> `A` remains `U+0045` regard-less of the font or style you use to draw it. 

**Unicode Code Point**: Numerical value assigned to each char in Unicode, ranging from `U+0000` to `U+10FFFF`

Remember the surrogate code points. 

So the Unicode scalar value is any code point except those in the surrogate range: 
In the ranges: 
- `U+0000` to `U+D7FF`
- `U+E0000` to `U+10FFFF`
![[Pasted image 20250402114631.png]]

Imagine the letter é, this may take up some bytes, on screen however, just one char right. 
This is a single char for us. 

A character is largely equivalent to a code point. This is not what people think of as a char, and this is important. 
The terminology, a **grapheme cluster** is what people normally think of as a character. 

So the Unicode code point represents a single unit of Unicode. A **grapheme cluster** accounts for combinations of multiple code points that together form what appears as one character. 
![[Pasted image 20250402115135.png]]
The grapheme cluster represents what a user perceives as a single character. 
Consisting of one or more Unicode code points -> it's the code that represents the final product that we see. 

They combine to make a single language element (that is, WHAT WE SEE, a single character). 

The scalar value is one value that can make up a grapheme cluster. 

So **Grapheme Cluster** is made up of **Scalar Values**. 

![[Pasted image 20250402115521.png]]

### Unicode Normals and Canonical Equivalence
We can have the same char on the screen, but different values behind the scenes. 
If we compared them, visually they are the same, but the scalar values will be different. 

"\{E9}", "e\{301}", and "e\{301}\{301}" all produce the same output. 

Therefore we need **normal forms** for Unicode Strings. 

**Canonical Equivalence**: meaning that two Unicode representations of the same char, should be treated the same way. 

![[Pasted image 20250402120046.png]]
The normalization, ensures that the text is kept in a consistent format. 

There are four main forms of normalization : 
![[Pasted image 20250402120219.png]]NFC will save space as well, by using fewer code points. 

Before moving on
**Code Point**: A numerical value assigned to a char in Unicode (this includes surrogates).  
**Scalar Value**: Same as a code point, however, without the surrogate code points. 
**Grapheme Cluster**: Can be one or multiple code points, that represent a single character. 

### Unicode Encodings
There are certain encodings - UTF-32, 16 and 8 of course, each with pros and cons. 
Unicode v2.0, is 21 bits - over a million chars (most reserved for future use). 

UTF-32 : doesn't need surrogate pairs - every code point fits in the 4 bytes given to us. 

UTF-16 need the surrogate pairs. 

UTF-8 - uses variable length encoding (anything above the range will take multiple bytes). 

The fact that we don't have variable length encoding or surrogate pairs with UTF-32 - this means that we have random access. 
The drawback being that that's a lot of memory, compared to 8, that could just be representing the same thing. 
This is not so bad for disk, but for cache, this is really rough. 


On 64 bit machines - registers and memory addresses are typically (8 bytes) 64 bits. 
The data we fetch into the cache, depends on the cache line size, not just the word size of the CPU. 
- Cache line : Bigger than 8 Bytes -> Even if we just get 1 byte, we fetch a full cache line (64 bits) from RAM to cache. 
- Spacial Locality : Preloads nearby memory into the cache to optimize performance. 
- GPR (general purpose registers) - Hold word size. 
For example: If we have `int array[N];` then we get `array[0]`, which is `int` (4 bytes). The CPU might fetch the entire 64 byte cache line (from `array[0] -> array[15]`), therefore `array[1]` and `array[2]` -> this will be much faster. 
Given a 64 bit word and cache line size, that's only two chars at a time, not 4 or 8. 

UTF-16 -> surrogate pairs -> The vast majority fit into the 16 bits, therefore, it's rare that we would need the surrogate pairs, this would mean 32 bits - and cause a stir, as the characters are not of consistent size. 

UTF-8 Forward compatible with ASCII character set. 
**If UTF-8 HO bit is 1, then the char requires between 1 and 3 additional bytes to represent the Unicode Code Point**. 

![[Pasted image 20250402122543.png]]

### Unicode Combining Characters
The CPU overhead when dealing with UTF-16-8, with multi-byte (multiword), complicates their use a little, there can be bugs and performance issues. 

Several grapheme clusters require concatenated Unicode Code Points. 
![[Pasted image 20250403115502.png]]
Each of these produces an identical character: an ó, it's showing that there a different ways of combining to form this character. 
![[Pasted image 20250403115620.png]]
See how they all compare to be true. 

There is no one scalar value that will produce this character. 
The very least, we have to combine two. 
Even with UTF-32 encoding, still require two (32-bit) scalars to produce this particular output. 

Consider : `U+1F471`, prints an emoji of a person with blond hair to the screen. 
Then if we add a skin colour modifier to this, 
`U+1F471 + U+1F3FF`, produces a person with dark skin tone (and blond hair still). 
There is still a single char on the screen. 
Apparently there is no way to encode this with a single UTF-32 value. 

The bottom line being that there are certain grapheme clusters that require >1 scalar values. No matter how many bits that we use for the scalar. 
It's possible to combine 30 to 40 scalar values into a single grapheme cluster. 

Regardless of how hard we try to avoid it, sometimes we are going to have to use >1 scalar values. 

This is partly why UTF-32 has never really taken off. 

Not solving the problem of random access into a string. 

There's a dealing with the normalizing and combining Unicode Scalars, therefore, it's more efficient to use UTF-16 and 8. 

Most languages and OSs already deal with the issue of having multibyte -> rather than just using ASCII for it. 

Swift doesn't really even have ASCII support. 

## Character Strings 
A sequence of characters with two main attributes : Length and Character Data. 

#### Formats
Different languages will use different structures to represent strings. 
Some using less memory -> faster processing, more convenient -> more functionality. 

#### Zero-Terminated Strings
Most common -> native string format for C, C++, and others (some ASM). 
0 > more 8 bit char codes, ending with a byte containing 0 (would also be true to 16 bit codes as well right). 

In C/C++: ASCII `"abc"` requires 4 bytes: 1 for each of the chars, then the 0 byte at the end. 

There are a few advantages over other string formats: 
- Represent of any practical length, with only one byte overhead (2 bytes in UTF-16, 4 in 32). 
- C/C++ has high performance libs, that process strings like these amazingly well. 
- Easy to implement. In C/C++ strings are just sequences of chars, therefore they wouldn't have to clutter the language with the string format (`std::string` came later on). 
- Easily used in any language that has arrays and a 0, you just have to write that implementation. 

There are the disadvantages: 
- When needing to know the length of the string, the only way is linear. Not great with long strings. 
- There is the reason why in C/C++ it's `\0` and `0`, can sometimes cause some confusion. 
- The string won't tell you how long it can go after the `\0`, so overflow can be an issue.

##### C/C++ `std::string` and `char [];`
Obviously in C, `char []` is a `string` (a string being this idea of a sequence of chars moreso than an abstraction). 
`printf("%s", str)` and `strlen(str)` all work by scanning the the chars before the `\0` right. 
C is beautiful, however, this is error prone for "the noobs" - George Hotz. 
There is also a lack of built-in functionality of course (concatenating, searching). 

`std::string` was born to provide safer, more convenient way to handle strings: 
`.substr()` `.find()` `.replace()` were all added for convenience. 

C-Style strings are very much needed to be understood and used possibly with lol-level and embedded systems. 

### Length-Prefixed Strings
A byte containing the length of the string. `"abc"` -> 4 bytes -> The length byte `$03`, then followed by `abc` . 
The only thing to remember is that the first char will be at index 1 : 0 index will be the length. 
This can be much more convenient then having a NUL termination : Having the length up-front. 

Obviously the only issue here is the length of the length byte, so can only be 255 without moving on to having two bytes of length. The overhead increases when we go over 255. 

### Seven-Bit Strings 
Great for 7 bit encodings like ASCII. 
The HO bit is normally unused. 
Then we use the HO bit to indicate the end of the string. 
All but the last char, the HO bit is clear - the last char, the HO bit is set. 

There are quite a few disadvantages: 
- Have to scan the whole string in order to determine the length of the string. 
- No zero length strings - which sounds weird, but obviously very important. 
- Few languages provide literal string constants for 7-bit strings. 
- Maximum of 128 char codes, find when using ASCII, but not that great for anythign else. 

Advantage is that there are no need for bytes to encode the length. 
ASM -> probably the best language to use 7 bit strings with. 
ASM programmers -> tend to be the ones that worry about compactness the most. 
HLA macro that converts a literal string to a 7-bit string: 
```
#macro sbs(s);

	// grab all but the last char of the string: 
	(@substr( s, 0, @lenghts(s) - 1) + 
		// Concatenate the last character with its HO bit set: 
		char(uns8( char( @substr(s, @length(s) - 1, 1))) | & 80) )
#endmacro
... 

byte sbs("Hello World");
```

### HLA Strings
A few extra bytes of overhead per string, create a string format that combines the advantages of both length-prefixed and zero-terminated strings without their respective disadvantages. 
The High-Level Assembly language, does this with their native string format. 

A lot of overhead per string. 
Memory constraints here -> something we have to think about. 
HLA uses 4-byte length prefix, allowing char strings to be just over four billion chars long (far more than what is feasible). 
Then it will append a 0 byte to the char string data. 
Then at the front, 4 bytes for the maximum legal length for that string. 
Immediately checking for string overflow. 
![[Pasted image 20250404213408.png]]

HLA string vars are pointer that contain the byte address of the first char in the string. 
To get the length fields, you load the value of the string pointer into the 32-bit register, then get the `Length` with the offset of -4, then `MaxLength` is -8. 

```
static 
	s :string := "Hello World";
	... 
	mov( s, esi ); // Move the adress of 'H' in "Hello World"
					// into esi
	mov ( [esi-4], ecx ); // Puts the length of the string (11 for "Hello World")
						// int ecx
	... 
	mov( s, esi);
	cmp( eax, [esi - 8] ) // see if value in EAX exceeds the maximum 
	ja StringOverflow;
```

##### Descriptor-Based Strings
All so far have attribute information (that is, the lengths and terminating bytes) : 
Keeping the information in a record structure, *descriptor*, containing a pointer to the character Data (starting to sound an awful lot like a `string`)
Pascal/Delphi data structure: 
```
type 
	dString :record 
		curLength :integer;
		strDatas  :^char;
	end;
```
Note that this data structure does not hold the actual char data. 
Contains address of the first char of the string - can lazy evaluate, somewhat like a Proxy Design. 

```
struct StringDescriptor
{ 
	char* data;
	size_t length;
	size_t capacity; 
};
```

Here we are interacting with an *interface*/*handle* for the `string`. 

This means that the char array can be bare bones - no length bytes or terminating bytes at all. 

This means that we can get "overlapping strings", same char array just different descriptors. 

![[Pasted image 20250407182650.png]]
There are as many substrings as there are: 
This means that we just point to the part of the char array that we want. 
This means something like `substring()` is super efficient, just different pointers. 

Just make sure that you don't write over anything without all the other descriptors knowing, if that's what you want, perhaps they have to make a new `string` when one is written over and the other uses that part of the written over string. 

### Java Strings
Java uses this descriptor-based string form. In Java they are opaque, meaning that you aren't supposed to know about or mess with it. 
Don't manipulate them other than the String API. 
A pointer to an array of 16-bit (original) Unicode characters (no extension beyond 16 bits), a count field, an offset field, and a hash code field.

### C# Strings
UTF-16 encoding for characters in its strings. 
C#'s `string` -> opaque as well. 

### Python Strings
Support for UTF-16 and 32 encodings. 
Nowadays, Python does something special with string formats that tracks the characters in strings and stores them in ASCII, UTF-8, 16, 32, etc. based on the most compact representation. 


### Static / Pseudo-Dynamic / Dynamic
##### Static Strings
Maximum size, programmer chooses when writing the program. 
Arrays of chars in C/C++, with Null Terminated, fall into this category. 

```
// const
char cString[256]; // Constant Expression
```

Run-time, now way to increase the maximum sizes of the static strings. 
Nor is there a way to reduce the storage they will use; these string objects. 
The compiler can determine their maximum length at compile time and implicitly pass this information to a string function so it can test for bounds violations at runtime. 

##### Pseudo-Dynamic Strings
Length at run time, by calling some memory management function like `malloc()` : Once this is allocated, the maximum length of that string is fixed. 
HLA strings generally fall into this category. 
HLA programmers typically calls the `stralloc()` function to allocate storage for a string variable, now that length cannot change. 

##### Dynamic Strings
Typically using a descriptor-based format, automatically allocating sufficient storage for a string object whenever you create a new string or otherwise do something that affects an existing string. 
If we just use the Descriptor, then a lot of these functions are super fast. 
Copy-On-Write -> COW -> memory optimization technique used for things like strings where multiple instances can share the same data until one them tires to modify it. 

Instead of copying a string's data every time you assign or pass a string : 
- Multiple strings point to the same underlying character buffer.
- When one string is modified, a real copy of the data is made - so the original shared data isn't affected. 

```
struct CowString
{ 
	char* data;
	int* refCount;

	vod write(char newChar, int pos)
	{ 
		if(*refcount > 1) // there is some change, therefore we need a deep copy
		{ 
			char *newData = copy(data);
			--(*refCount);
			data = newData; 
			refcount = new int(1);
		}
		data[pos] = newChar;
	}
};
```

Was actually removed from `std::string` in C++11 due to multithreading issues. 
Studies show that there are more functions for `substr()` than there are for actually changing chars - therefore, using this COW technique - Lazy deep copy is the only way. 

The only downside is that we have the issue of floating string data in heap data -> just need to make sure that we don't have any memory leaks. 
There should be some form of *garbage collection* -> scanning the string heap area looking for `stale` character data in order to recover that memory for other purposes. 
Garbage collection can be quite slow. 

#### Reference Counting for Strings 
Like a `std::shared_ptr<char>`. 
Handling all of this through a function -> everything has to go through a function here

- **Utility function** – a common term for reusable, generic helpers.
    
- **Private function** – when it's used internally within a class/module.
    
- **Support function** – sometimes used to mean the same as helper.
    
- **Subroutine** – more old-school or in some languages.
    
- **Callback** – if it’s passed into another function to be called later (context-specific).

`std::string` in C++ and `std::string` in Rust, just a wrapper around heap-allocated character buffer that resizes when needed. 

```
struct DynamicString
{ 
	char* data; //pointer to heap memory
	size_t size; // number of chars used 
	size_t capacity; // total allocated space
};

if (size + new_data > capacity); // reallocation

May use SSO (Short String Optimization)
```
In Rust
```
let mut s = String::from("hello");
s.push_str(" world");
```

In python 
```
s = "hello"
s += " world"
// strings are immutable, so the += creates a new string each time
```

## Character Set Data Types
Mathematical set of characters. 
![[Pasted image 20250409113230.png]]

Data types that store characters, often with specific encoding or language support. 
Pops up a lot of databases, programming languages, and character encodings. 

##### Powerset Representation of Character Sets
Boolean vectors - value for whether the corresponding character is true or fals. 
1 bit per character. 
This is known as a `powerset`. 
![[Pasted image 20250409113808.png]]Bit 0, the most LO bit, is ASCII 0 (the NUL char), if this is 1, then this character set does contain the NUL char. 

This is true to competitive programming as well, when we have numbers that are represented in a set, then we can use bitwise operators to simulate functions on those sets. 

Bit Vectors very easy to perform set operations, union, intersection, difference comparison, and membership tests. 

##### Like Representation of Character Sets
Could just use character string to represent a list of characters. 
If a set is small enough, then linear search will be fine to go through it all. 
Powerset of a huge amount of chars (could be sparse) meaning that we have a lot of empty spaces, so we aren't using memory efficiently there.
For those sparse sets, we just put in the characters that are present into the set. 


### Designing Own Character Set
ASCII and EBCDIC are antiquated.
Easy to translate between char sets using a lookup table. 
Loss of efficiency in I/O, is fine if efficiency of using other parts is better for us.
How many chars - 256 say, some power of 2, easier to represent bytes. 
Think about transmitting this as well. 

### Designing an Efficient Character Set
Think about how we are implementing over our strings, say with 0/null terminated, then we have to have that char included in the set. 
Take note that most functions that work on strings will not work with our representation. 
We will have to write a lot of those functions ourselves. 
![[Pasted image 20250409120051.png]]![[Pasted image 20250409120059.png]]
```
Think about this:  in python

nChar = "." # any char 

if nChar in [".", ",", "/", "[", ...]  # etc. etc. 
	# do something if in
# super costly right
# however, if our implementation was just looking at the range that "punctuation" lands in

# So given
Punc_Range = (32, 64) # just an example 
if nChar >= 32 and nChar <= 64: # see inclusivity
	# do something if in
```


### Grouping Alphabetic Characters
ASCII char set, just not well designed for alphabetic character tests and operations. 
Here are some problems with ASCII that we could solve with HyCode: 
- Alphabetic chars lie in two disjoint ranges. Therefore these tests require 4 comparisons. 
- Not super intuitive - meaning that lower case letters should be lower in numbers. 
- `a` > `B`
![[Pasted image 20250412102457.png]]

In ASM: 
```
cmp ( al, 76 ); 
jnae NotAlphabetic;
// Execute these statements if it's alphabetic

NotAlphabetic: 
```

Say that we interleave the lower and upper. 
![[Pasted image 20250412102746.png]]
Notice that all the Uppercase is an odd number. 

Still less work in ACSII. 
```
if(( c >= 76 ) && ( c & 1 ))
{ 
	// char is uppercase
}

if(( c >= 76 ) && !(c & 1))
{ 
	// if lowercase
}
```
This in 80x86 ASM: 
```
// Note ROR(a, AL) maps lowercase to the range $26 .. $3F (38 to 63)
// Uppercase to $A6 ... $BF (166 ... 191) 

ror( 1, al );
cmp( al, $26 );
jnae NotLower; // Note : must be an unsigned branch

	// Code that deals with a lowercase character
	
NotLower: 
// For Uppercase, note that ROR creates code in the range $A8 ... $BF
// negative (8-bit) values. 

ror( 1, al );
cmp( al, $a6 );
jge NotUpper; // Must be a signed branch

// Code that deals with an uppercase character

NotUpper:
```

### Comparing Alphabetic Characters

![[Pasted image 20250412103342.png]]
This is what we want in a lexicographical ordering, also the most intuitive. 
Thinking about case-insensitive comparison, mask out the LO bits (or force them both to 1), as the difference between upper and lower is just a difference in 1 (odd and even). 

```
if(toupper(c) == toupper(d))
{ 
	// do code that handles c==d using a case-insensitive comparison
}
```
`#define toupper(ch) ( (ch >= 'a' && ch <= 'z' ) ? ch & 0x5f : ch)`

This expands to: 
```
if (
	( ( c >= 'a' && c <= 'z') ? c & 0x5F : c)
	== ( ( d >= 'a' && d <= 'z') ? d & 0x5F : d)
)
{ 
	// do code that handles c  == d using a case-insensitive comparison
}
```
There's some 80x86 code expansion here: 
```
// assume c is in cl and d is in dl
cmp( cl, 'a'); // see if C is in the range 'a'..'z'
jb NotLower; 
cmp( cl, 'z'); // 
ja NotLower; 
and( $5F, cl); // convert lowercase char in cl to uppercase

NotLower: 
cmp( dl, 'a'); // see if d is in the range 'a'..'z'
jb NotLower2;
cmp( dl, 'z'); 
ja NotLower2;
and( $5F, dl); // convert lowercase char in dl to uppercase

NotLower2:
cmp( cl, dl); // Compare the (now uppercase if alphabetic chars
jne NotEqual; // Skip the code that handles c == d if they're not equal
```
In HyCode ( our char ) the comparisons are much simpler: 
```
// check to see if CL is alphabetic.
cmp(cl , 76); // If CL < 76 ('a'), then not alphabetic, now way that two chars are equal
jb TestEqual; 

or( 1, cl); // CL is alpha, force it to uppercase
or( 1, dl); // DL may or may not be alpha, force to uppercase if it is

TestEqual: 
cmp( cl, dl ); // Compare the uppercase versions
jne NotEqual;  // bail out if they're equal

TheyreEqual: 
// do the code that handles C==d using case-insensitive comparison

NotEqual:
```

Then there's the idea that we group certain types of chars together - meaning that we just have to look at the range. 

