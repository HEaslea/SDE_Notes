## Arithmetic Operations on Binary and Hexadecimal Numbers
Hexadecimal is kind of annoying to do by hand. 

### Adding Binary Values
There are only eight rules to learn: 
The way that I imagine this is either 0 or 5(1), then our denary system makes this make sense as well:
```
 1. 0 + 0 = 0
 2. 0 + 1 = 1
 3. 1 + 0 = 1
 4. 1 + 1 = 0 with carry
 5. Carry + 0 + 0 = 1
 6. Carry + 0 + 1 = 0 with carry
 7. Carry + 1 + 0 = 0 with carry 
 8. Carry + 1 + 1 = 1 with carry 
```
Then everything is really simple. 

```
0101 + 0011: 
1 + 1 = 0 carry 1
then 1 + carry = 0 + carry
carry + 1 = 0 + carry 
Then 0 + carry = 1
so 1000
```

Then we'll do: 
```
1100_1101 + 0011_1011
so we have 1 + 1 = 0 + carry 1 // imagine 5 + 5 carry "5" 1
1 + 0 + carry = 0 + carry // again 5 + 5 = 0 + 1
1 + 0 + carry = 0 + carry // 000
Then we have 1 + 1 + carry = 1 + carry // 5 + 5 + 5 = carry + 5
// LO byte is 1000  + carry
then we have 0 + 1 + carry = 0 + carry // 5 + 5 = 0 + carry
same again = 0 + carry
same again X 2 + the carry at the end to get 1_000
Therefore we get 1_0000_1000
```

```
1001_1111 + 
0001_0001

1 + 1 = 0 + carry // the first casn never be 1 + carry unless carry from elsewhere
1 + 0 + carry = 0 + carry // 
same x 2 = 0 carry to HO byte // so then 0000 - boring
then 1 + 1 + carry = 1 + carry // 5 + 5 + 5 = carry + 5
0 + 0 + carry = 1 // so far 11 
0 + 0 // 011
then 1 // so 1011_0000 // very good
```

#### Subtracting Binary Values
There are eight rules and again it might be helpful to think of these in terms of denary 5's (although this doesn't work as well): Remember with all of this that order really matters
```
1. 0 - 0 = 0
2. 0 - 1 = 1 with a borrow // similar to to when we have 10 - 5 we just get rid of the first one
3. 1 - 0 = 1
4. 1 - 1 = 0
5. 0 - 0 - borrow = 1 with a borrow // in that 10 - 5 is still 5
6. 0 - 1 - borrow = 0 with a borrow // 10 - 5 - 5 = 0
7. 1 - 0 - borrow = 0 // you know that 1 - 0 = 1, so this is 1 - borrow = 0
8. 1 - 1 - borrow = 1 with a borrow // 15 - 5 - 5 = 5 with take away the 1, so borrow
```
```
basic one to start with: 
 0101
-0011
Start with the LO bits Remember that the order really matters as well
1 - 1 = 0
0 - 1 = 1 with a borrow // so then 01 
then we have 1 - 0 - b = 0 // 010
then we have 0 - 0 // therefore 0010 // just look to see that the number is smaller (without two's complement)
```
```
 0111_0111 // this is fairly obvious that this will just result in a smaller number
-0000-1001

1 - 1 = 0
1 - 0 = 1 x 2 // 110
0 - 1 = 1 with b // 1110
1 - 0 - b = 0 // 0_1110
then 1 - 0 x 2 then 0
0000_1110
```

### Multiplying Binary Values
There are fewer rules here: they are really really simple: 
```
1. 0 x 0 = 0
2. 0 x 1 = 1
3. 1 x 0 = 0
4. 1 x 1 = 1
```
Then we do it just like normal , that we would with normal numbers

```
	 1010
	x0101
	----- 
	 1010 # the LO of the lower one by the top nibble
    0000  # See that this moves to the left one just like in our own multiplication
	----- # let's do a partial sum
   001010 # previous partial sum
   1010   # (1 x 1010)
   ------
   110010 # partial sum
   ------ 
  0000    # then the last on the bottom with the top 
  0110010 # (product) the final sum
```

### Dividing Binary Values
We just use the same longhand division algorithm as decimal division. 

![[Pasted image 20250321213851.png]]

Exactly the same as polynomial division: 

This is actually easier in binary: 
![[Pasted image 20250321214354.png]]

This is all very obvious when you see it like this: 

## Logical Operations on Bits
There are the four main operations: 
AND, OR, XOR, NOT. 

These are everything that you know. 

![[Pasted image 20250321214837.png]]![[Pasted image 20250321215000.png]]

### Logical Operations on Binary Numbers and Bit Strings
Most programming languages manipulate groups of 8, 16, 32, 64. 
Then we need to go beyond the bit to the bit-by-bit(bitwise) basis. 

```
%1011_0101
%1110_1110
----------
%1010_0100
```

The ability to force bits to 0 or 1 using AND and OR operations, inverting bits using XOR, very important when working with strings of bits (such as binary numbers). 
If you have an 8 bit binary value X and you want to guarantee that bits 4 through 7 contain 0s, then AND the value X with the binary value $0000_1111. 
This will force the HO 4 bits to 0, and all the rest will just stay the same. 
We can force a bit to 1 by using OR: 
Then we can invert with the XOR. 

Doing this is known as **masking** : #masking
To "mask out" or "mask in" certain bits in an operand while forcing other bits o 0,1, or their inverse. 

The C++ example: 
```
i = j & k; // Bitwise AND
i = j | k; // Bitwise OR 
i = j ^ k; // Bitwise XOR
i = ~j;    // Bitwise NOT
```
ASM `AND, OR, NOT, XOR` Instructions

### Useful Bit Operations
#### Testing Bits in a String Using AND
We can use AND to test if certain bits in the string to see if they are 0 or 1. 
`IsOdd = (ValueToTest & 1) != 0`
```
xxxx_xxxx_xxxx_xxxx_xxxx_xxxx_xxxx_xxxx_xxxx // Assuming ValueToTest is 32 bits
0000_0000_0000_0000_0000_0000_0000_0000_0001 // Bitwise AND with the value 1
-----------
0000_0000_0000_0000_0000_0000_0000_0000_000x // Result of bitwise AND
```
We just ignore all the other bits that we have going on. 

#### Testing a Set of Bits For Zero/Not Zero using AND
This is a really cool idea : in order to check if a number is divisible by 16 : then we need the first four bits to be 0's. 
That has never ever occurred to me before. 
`IsDivisibleBy16 = (ValueToTest & $f) == 0`
`$f` being `1111`in the LO nibble. Therefore if it's purely divisible by 16, we get all 0's there, therefore, the other bits being 0 and AND'ing them, we will overall get 0. 
Such a great way to check if something is divisible. 
![[Pasted image 20250321222948.png]]

#### Comparing a set of Bits Within a Binary String
Say that we are comparing the 0, 1, 10, 16, 24, 31 and 32 bit values of two bit strings. 
`%1xxxxxx0xxxxxxx1xxxxx0xxxxxxxx10`
Assume that we are interested in comparing, and we only want to compare these two bits: 

```
// C++ Example
if((value1 & 0x81010403) == (value2 & 0x81010403)) 
{ 
	// do something if bits 31, 24, 16, 10, 1 and 0 of them are equal
}

if((value1 & 0x81010403) != (value2 & 0x81010403))  etc. 
```

```
// HLA/x86 Assm Example: 
mov( value1, eax); // EAX = value 1
and( $8101_0403, eax); // Mask out unwanted bits in 
mov( value2, edx);
and( $8101_0403, edx); // Mask out bits in EDX 
if ( eax = edx ) then // see fi the remaining bits match
// do something
endif
```

#### Creating Modulo-n Counters Using AND
The idea being: `counter = (counter + 1) mod N`.  There is the idea of cycling
The idea is: 
```
int main() 
{ 
	const int N = 5; // Modulo-5 Counter (cycles 0 to 4)
	int counter = 0; 
	
	for(int i = 0; i < 10; ++i)
	{ 
		std::cout << "Counter: " << counter << std::endl;
		counter = (counter + 1) % N;
	}
}
// Will just output 0 1 2 3 4 0 1 2 3 4 0 1 2 3 4
```

Remember that the number we get to is `n-1`

Division is an expensive operation, MORE time needed than addition. 
You would do well to do something like this: 
```
cntr += 1;
if( cntr >= n)
	cntr = 0;
```


For certain cases, when N is a power of 2, then we can do this with AND operation. 
Increment the counter, then logically AND it with the value $x = 2^{m} - 1$ (This will contain 1s in the bit positions 0 ... m - 1 and 0's every else). AND is much faster than division, these are much more efficient than those using the remainder operator. 
On most CPUs, using AND is way more efficient than `if`. 
Say we want to counter around `n = 32` using the AND operation: 
```
Note that 0x1f = 31 = 2^5 - 1, so n = 32 and m = 5
// therefore everything before the 5th bit is going to be 1, and the 5th bit and gove will 
cntr = (cntr + 1) & 0x1f; // C / C++ / Java / Swift
```
The assembly is pretty nice here: 

```
inc( eax );
and( $1f, eax )
```

### Shifts and Rotations
Shift left, rotate left, and the same for right. 

When we shift left, the LO bit will be 0, and the HO will be the carry bit. 

Always remember that shifting to the left is the same as multiplying the value by 2. 
And /2  for shifting to the right. 

![[Pasted image 20250322140429.png]]

Remember that if we do this with two's complement, then that's not going to be the value that we want, as the 1 at the beginning is going to be replaced with a 0, therefore if we `SHR EAX, 1;` Shift Right EAX by 1. Without doing this to cover the signed bit (replacing the front with the sign bit), -2 will become 127, which is an issue. 
That's why we have the **Arithmetic Shift Right**, doesn't modify the HO bit. 
![[Pasted image 20250322140841.png]]
This will always round to the nearest integer, that is < = to the actual result. 

Most integer division will round towards 0, `//` in python, and integer division in C++. 

C++ and python don't have a arithmetic shift right, therefore, we should only use this when we know that the new HO, being 0 with shift right, 
In order to make sure arithmetic, we would need to handle that manually with OR, or just drop into ASM. 

Remember this, bit strings that are 2^n in value will just have 1 1. 

Remember this as well, that one hexadecimal digit represents all the representations of a nibble, which didn't exactly click the first time. 

To check that a number is a power of 4, for the power of 2, the 1 had to be only one 1 and could be in any position. For the power of four, the 1 has to be only in the odd positions. 
`0x5` gives us `0101`. 
Remember that in a 32 bit integer, that we start at bit 0, therefore the HO bit is 31, the sign bit.
```
// written in C/C++, assuming 32 bit integers, logical shift right
// Compute bit 30 // bit 31 is HO bit
// making sure that the HO Bit is 1 before doing this
Bit30 = ((ShiftThisValue & 0x80000000) != 0) ? 0x40000000 : 0;
// Shift bits 0..30;
ShiftThisValue = (ShiftThisValue & 0x7fffffff) >> 1;  // 7ff.... is all 1's apart from HO bit
// merge in Bit #30
ShiftThisValue = ShiftThisValue | Bit30; // | is basically just adding the 1 at bit 30


// Arithmetic Shift Right Operation// Remember the HO Bit
Bits3031 = ((ShiftThisValue & 0x80000000) != 0) ? 0xC0000000 : 0;  
// 0x800.... -> is just 1 in the HO bit
// 0XC00.... -> c is 12 which is 1 1 0 0, therefore the top two bits
// Shifts bits 0..30, everything but the last bit
ShiftThisValue = (ShiftThisValue & 0x7fffffff) >> 1; // everythinb but the HO bit
// remember that this will just put 0 at the front
// Merg bits 30/31
ShiftThisValue = ShifThisValue | Bits3031; 
```

Assembly languages do a rotate instruction as well, that circulate bits through an operand by taking the bits shifted out of one end of the operand and shifting them into the other end. 
Few HLL's do this: fortunately, we won't need it that often. 

### `x - 1`
This is a big one : `x - 1`, **flips all bits after and including, the right most set bit(1)**: 
Given `8` : `1000 - 0001` Remember our rules: `0111` is our answer
12 : `1100 - 0001`  = `1011` flip the last 1 and everything after it. 
19 : `10011 - 0001`  = `10010` very simple, and this one makes the most sense. 

Think about it like with 8, when we have that number, then the last 1, we have to full up to that 1, which is just 1'ing the rest of the bit strings. 


### Bit Fields and Packed Data
CPUs work most efficiently on byte, word, double word and quad word data types, occasionally, we need to work with some other size. 
Remember these things, that memory is addressed in binary, and is typically built in bytes. 
CPUs use byte-addressable memory, meaning each memory address points to a single byte. 
And remember this, the term **word** matches the CPU's native register size, and memory bus width. 

The Byte is used, as it most efficiently stores the ASCII character, they will still be addressed in this manner, 1, 2, 3, etc. etc. 
Remember as well, that if it is little-endian - the lower byte is stored first, and the other way for big-endian. 

When we have rough memory layouts, we would do well by **packing** different strings of bits together as compactly as possible, without wasting bits to align to a particular data field. 
Consider this date `04/02/01`. Three numeric values, month day year. 
1 through 12 for month. Day needs 1 through 31, needing 5 bits. Then year will need 99, requiring 7 bits. 
This will be a good 16 bytes. 
This will save one byte of memory, instead of just doing 3 bytes, 1 for every value. 
If we are storing 1 m dates, that's one mil bytes, that's a megabyte right there, which doesn't sound like a lot, but it is to me and to embedded systems. 

![[Pasted image 20250323013924.png]]
Then we can do this:
```
0100     00010     0000001 = %0100_0001_0000_0001 or $4101
04       02        01
```
Packed values are space efficient, they are actually quite computationally inefficient and that's quite obvious, as we need a method to go through a byte or 2, just awkward. 
Extra instructions to unpack what we are doing here. 
That takes time to execute. 
(and even more bytes to hold those instructions). 
We need to consider whether this idea will actually do anything for us. 

This could be the HLA/x86 code packing and unpacking in this example: 
```
program dateDemo; 

#include ( "stdlib.hhf" )

static

day: uns8;
month: uns8;
year: uns8;
packedDate: word;

begin dateDemo; 


	stdout.put("Enter the current month, day, and year: ");
	stdin.get(month, day, year);
	// Pack the data into the following bits: 
	// 
	// 15 14 13 12 11 10 9 8 7 6 5 4 3 2 1 0
	//  m  m  m  m  d  d d d d y y y y y y y 

	mov( 0, ax );
	mov( ax, packeDate); // Just in case there is an error
	if( month > 12) then 
		stdout.pout( "Month value is too large", nl);
	elseif( month = 0 ) then 
		stdoout.put( "Month value must be in the range 1..12", nl ); 
		... other checks


	else
		mov( month, al );
		shl( 5, ax );
		or( day, al );
		shl( 7, ax );
		or(year, al );
		mov( ax, packedDate);
	endif; 
	
	// Unpack the Date
	mov( packedDate, ax ); // Retrieve the year value. 
	and( $7f, al ); 
	mo( al, year );

	mov( packedDate, ax); // Retrieve the day value. 
	shr( 7, ax );
	and( %1_1111, al );
	mov( al, day );
	
	mov( packedDate, ax ); // Retrieve the month value
	rol( 4, ax );
	and( %1111, al );
	mov( al, month );
	
end dateDemo;
```

![[Pasted image 20250323113836.png]]

This might be a better way of doing this, so that we don't have any awkward packing or unpacking. 
Realistically, one byte for day, one for month, then a whole 2 bytes for years. 

We can argue this is not packed at all. 

A good thing about this is that year is first, and it's its own 2 byte format, that we can use compare with. 
Consider this HLA/assembly cod: 

```
mov( Date1, eax ); // Assume Date1 and Date2 are double-word variables
if( eax > Date2 ) then // using the long packed date format
	<< do something if Date1 > Date2 >> 
endif; 
```
Had we kept the different date fields in separate variables or organized the fields differently, you wouldn't have been able to compare `Date1` and `Date2` in such a straightforward way. WoW, that is really fucking cool. 

Some C : packed data: 

```
// this is using bits field
struct 
{ 
	unsigned bits0_3 : 4; // 4 bit field
	unsigned bits4_11 : 8; // 8 bit field
	unsigned bits12_15 : 4; // another 4 bit
	unsigned bits16_23 : 8; // another 8 bit
	unsigned bits24_31 : 8; // another 8 bit
}
```
`bit fields` are the same in C++ I believe.
Remember tho `:n` means that `n` is the minimum number of bits that the compiler will assign to that thing if it can. 

C/C++ compilers will implement in any ways that they see fit in honesty. 

The arrangement is also arbitrary which is weird, `bits0_3` here could be bits 28 through 31 of the ultimate object. 

The compiler may also put padding in between which is not nice of it. However, most compilers will try to minimize all that padding, however, on different CPUs will differ, which is cool. 

In C/C++ struct bit fields declarations are almost guaranteed to be **non-portable**, as you cannot guarantee what the compiler is going to do with those bits and fields. 

```
#include <iostream>

struct PackedData {
    unsigned int a : 4;  // 4 bits
    unsigned int b : 8;  // 8 bits
    unsigned int c : 4;  // 4 bits
    unsigned int d : 8;  // 8 bits
    unsigned int e : 8;  // 8 bits
};

int main() {
    PackedData data = {0b1111, 0b10101010, 0b0001, 0b11001100, 0b11110000};

    std::cout << "Size of PackedData: " << sizeof(PackedData) << " bytes\n";
    std::cout << "a: " << data.a << "\n";
    std::cout << "b: " << data.b << "\n";
    std::cout << "c: " << data.c << "\n";
    std::cout << "d: " << data.d << "\n";
    std::cout << "e: " << data.e << "\n";
}
```

```
// Packing
| a (4) | b (8) | c (4) | d (8) | e (8) |
-----------------------------------------
|  1111 |10101010| 0001 |11001100|11110000|
```

```
// Unpacking
data.b -> (memory_value >> 4) & 0xFF  // Extracts 8 bits starting at bit 4
```

Using the compiler's built in data packing capabilities, the compiler will auto pack and unpack that data for us. 

The compiler always handles packing and unpacking automatically, whether we use bit fields or not. 

So Given: 
```
struct NoBitFields
{ 
	char a; // 1 byte
	int b; // 4 bytes
	short c; // 2 bytes
};
```
The compiler, although that we cannot be sure : might look like: 
- `a` (1 byte) is stored first
- Then there may be (3 extra bytes) for padding, to align with `b`. 
- `b` (4 bytes) is stored next
- `c` (short being 2 bytes) is stored next
- Padding (2 extra bytes) to be aligned with the 4 or 8 bytes. 

Using bit fields could make this smaller. 

```
unsigned int rawData = 0b11111010101000011100110011110000;

// Extract 'b' (bits 4-11, 8 bits)
unsigned int b = (rawData >> 4) & 0xFF;  // 0xFF = 0b11111111 (8-bit mask)

// Extract 'c' (bits 12-15, 4 bits)
unsigned int c = (rawData >> 12) & 0xF;  // 0xF = 0b1111 (4-bit mask)

// Extract 'd' (bits 16-23, 8 bits)
unsigned int d = (rawData >> 16) & 0xFF;

std::cout << "b: " << b << "\n";  // Outputs: 170 (10101010)
std::cout << "c: " << c << "\n";  // Outputs: 1 (0001)
std::cout << "d: " << d << "\n";  // Outputs: 204 (11001100)
```


The compiler will do this for us behind the scenes for us when we use bit fields. 
Without the bit fields, there will be padding, and the compiler doesn't really need to mask and shift etc. 


### Packing and Unpacking Data
The advantaged of Packed Data Types (PDTs) is efficient memory usage. Consider the Social Security Number (SSN). 
Nine digits. 
`XXX-XX-XXXX`
Think about this, 32 bit integers for each one, that would be 12 bytes (4 x 3). 
The real better solutions is using 16-bit integers. 
Now that would obviously only be 6 bytes. 
The middle number will always be 00-99. So then the middle part can just be a byte. 

Pascal/Delphi Does this: 
```
SSN :record 
	FirstField: smallint; // smallints are 16 bits in Free Pascal/Delphi
	SecondField: byte;
	ThirdField: smallint;
end;
```
The first and third field, first : 000-999, 2^10 i believe, and then the third is : 0000-9999, 2^14 (as log2(9999) = 13.24234256 .... ). 


Remember that in order to find the number of bits that we have to represent a number, it will be $\lceil{log_2(x)}\rceil$  , where `x` is the number that we are looking to represent. 

For representation, this packing method can work. 
Software might need to operator on individual bits, this would mean expensive division, modulo and multiplication operators in order to extract the fields from a SSN, that we have packed into a 32-bit integer format. 

![[Pasted image 20250323203543.png]]Here is a more simplified view, where we are looking at using a separate string for each bit field. 

0 Bit can be accessed more efficiently, so we should arrange fields in a packed data type, such that the field you access most often begins at bit 0. This is more due to ISA design, and hardware. 
Most instructions operator on LSB first, so a lot of instructions are set to get that bit first. 

![[Pasted image 20250323204309.png]]
If that's the case, then we would hope for this sort of layout. 

This means, however, that we can't sort, with an intuitive system, comparing 32 bit unsigned integers. If that's not important, then this is perfect. 
The `ThirdField` will start on a byte boundary, this means that the compiler has more work to do in order to get the thing that we actually want. 
If the value is byte aligned, the compiler has less to do, and the computer therefore has less tod o. 
If performance is most important - then use more space - make sure everything is byte aligned. 
If space is more important - 

In order to unpack : 
1. Retrieve, **extract** the packed fields
2. Insert data into these fields. 
The **AND, OR, SHIFT** will pretty much do that for us. 

When working on these fields, obviously it's more helpful to work with the three separate fields, rather than try and work with the whole thing, and work out how to add and remove from it with all the bits not byte aligned. 

Let's use the bits from the last figure. 
Then we need to get that representation of the `SecondField` to the variable. (Remember that in reality a variable is just a memory address). 
So we just need the first 7 bits. 
This means that we need 0 through to bit 6: 
Assuming that `packedValue` is a 32-bit packed SSN. 
`SecondField = packedValue & 0x7f; // 0x7f = %0111_1111`
So this is why this is easier to get `0 bit` values, as we don't have to do any shifting, I think. 

Extracting those bits that are not at 0, a little more work. 
In order to get the other parts, we need to shift in order to get the value that we are working with to the 0bit. 

`ThirdField = (packedValue & $3fff00) >> 8;`  That's how many bits we need to shift it right in order to keep it going. 
You could also shift and then AND it. 
`ThirdField = (packedValue >> 8) & 0x3FFF;`

To get the part that is aligned against the HO bit. I don't believe that we need to mask, I think we can just shift. No masking with these bits, bit shift will just add the 0's to the front. 

`FirstField = PackedValue >> 22;` 

```
HLA
mov( packedValue, eax );
shr( 22, eax ); // shift by 22 bits
```

Inserting a field into a packed object requires three operations. 
Shift the field's data to the left so it's in alignment with the corresponding in the packed object. 
Clear the corresponding bits in the packed structure. 
Then logically OR. 

![[Pasted image 20250323221312.png]]![[Pasted image 20250323221323.png]]
Masking out the bits means that we are actually getting them to 0. 

Here's the C++ code: 

`packedVlaue = (packedValue & 0xFFc000FF) | (ThirdField << 8);`

Again, Read: 
Art of Assembly Language - No Starch
Art of Computer Programming Volume 2 : Donald Knuth


