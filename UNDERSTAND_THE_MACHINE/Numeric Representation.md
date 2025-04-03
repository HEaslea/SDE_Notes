A number is abstract, intangible concept. Intellectual device to denote quantity.
"One Hundred" is an abstraction. 
100 - this is not 100, the real one is an abstraction. 

![[Pasted image 20250318220633.png]]
There is the number somewhere in the universe - then there is the representation of it. 

Representation is a sequence of symbols, in this case of 1's and 0's. 

### Numbering Systems
Base10 (denary) - Base2(binary) 

Positional Notation - so not only having the symbols - but their positions have some meaning as well. 

##### Decimal Positional Numbering System
Our system. 
Then the decimal point to separate whole and fractional portions of the number representation. 
Then just between 0-9. 

![[Pasted image 20250318221222.png]]

![[Pasted image 20250318221234.png]]

Just basic maths stuff - however this is some stuff that is really difficult in the Donald Knuth thing that is hard. 

Think about the space that is required to write out a number. 

### Radix (Base) Values
The base because of the maths above. 

![[Pasted image 20250318221726.png]]

To make `base-n` numbering system, we just need `n` digits, note that base 10, doesn't go to ten, therefore, we need 0 as well. 

So `0 - n - 1`

Most of the time in machines, we are working in 2, 8, 16. 

8 Was popular with older systems. 

### Binary Yay
$11001010_2$
![[Pasted image 20250318222127.png]]


Converting to binary from denary, here's a new way that we haven't thought about : 
![[Pasted image 20250318222307.png]]Binary not being as compact as denary: we need some way to make them easier to read. 
Putting underscores under every `n` points will that do for us. 


#### Converting Binary and Hexadecimal
![[Pasted image 20250318222917.png]]

Just remember this A is 10 - 15 is F. 

Each hexadecimal is a nibble - half a byte - 4 bits etc . etc.

Sometimes for older systems, we need the octal numbering system: 
#### Converting Octal and Binary

![[Pasted image 20250318223328.png]]
See that it's 3 bits. 

It's one in triads. 

### Numeric/String Conversions
Imagine this: 
Initialize with 0 : holding the the final value. 
Get the next digit -> convert from ASCII to integer. 
Multiple variable by 10, to move it then add the converted digit. 

Then integer to string: 
Get empty string : 
Edge case : 0 then output "0" : 
Get % 10 : to get the first digit
Then get rid of it with `n // 10` (in python at least, we get the truncated value). 
Pretty simple stuff: 

```
printf("%d", i);
cout << i;
```


Use these conversions as little as possible. 

Signed integers require a lot more

(Linter - checks source code for potential errors, stylistic issues, and violations of coding standards.)

Floating-point values, however, are far more difficult to convert, between string and numeric form. 
So keep that in mind. 

### Internal Numeric Representation
##### Bits
![[Pasted image 20250318225300.png]]

`Bit Strings` Forming a sequence of multiple bits

##### Bit Strings
Nibble - 4 bits - most systems don't provide efficient access to nibbles in memory. 

A Byte - 8 bits - smallest addressable data item on many CPUs. 
CPU can efficiently retrieve  - therefore the smallest data type that many languages support is that of 1 byte. 
How would you denote individual bits within a byte. 

`Bit Numbers` - Bit 0 Is `Low Order (LO)` or least significant
Bit 7 `High-Order (HO)` Most significant bit - (of the byte). 

![[Pasted image 20250318231215.png]]


**Word:** A word is defined per CPU : it may be 16 bit, 32 or even 64 bit. 

Taking the 80x86 - defining a word with 16 bits. 
![[Pasted image 20250318231456.png]]

Then we can have the whole 2 Bytes with HO and LO idea. 

In this case there are two bytes in a word for this system. 

**Double Word**: just two words long - for this system - 16 bits. 

![[Pasted image 20250318234919.png]]

Working with objects that are larger than word - is less efficient. 

Typically won't be seeing programs handling numeric objects much larger than about 128 or 256 bits. 

Imagine that we have a word size of 16, then we have a 64 bit floating point - that's a quad word. 

### Signed and Unsigned Numbers 
Remember the whole **Two's Complement:** -> with 16 bit word 2^15 - 1 - meaning that we can have –32,768 to +32,767 range. 
The range is $-2^{n - 1}$ and $+2^{n - 1} - 1$. 

Then we use the HO bit as a the **sign bit**: 
Then if 0, then non-negative. 

![[Pasted image 20250319001415.png]]

In order to do that: 
1. Invert all the bits in the number. 
2. Add 1 to the inverted result (ignoring overflow)

Same for going from a negative to a normal. 
You can think of these numbers as the HO bit is negative whatever it is, then the rest are adding to it, to make the negative number smaller. 

![[Pasted image 20250319003019.png]]

### Properties Of Binary Numbers
- Always, the LO bit is 1, then the number will be odd
- Oddly - LO n bit all being 0, then the number is even divisible by $2^n$. 
- If a binary value contains a 1 bit in position `n` , and 0's everywhere else, then we know the number to be $2^n$ (0 index). 
- Shifting all the bits in a number to the left, multiplies it by 2. 
- Then shifting to the right, divides by 2, **odd numbers are rounded up**. 
- Multiplying two n-bit values together may requires as many as $2 \times n$. Which seems obvious, but really isn't, but think about $99 \times 99 = 9801$ 2 digits, to 4. 
- Adding or subtracting two n bit binary values, will never require more than n + 1 bits to hold the result. Thinking again about something like 99 + 99 = 198, requiring only one more bit. 
- Inverting all the bits in a binary number, same thing as negating, of the value and then subtracting 1 from the result (as seen earlier). 
- Decrementing from 0, will always give the highest value, all 1's. 
- n bit value provides 2<sup>n</sup> unique combinations of those bits. 
- The value $2^n-1$ contains n bits, each containing the value 1. 


#### Sign Extension, Zero Extension, and Contraction
Single negative values are represented differently depending on the size of the representation. 
8 bit signed values must be converted and for use in an expression involving a 16 bit number. 
This would be sign extension, then the inverse, moving a 16bit to 8, is sign contraction. 
Considering -64. 
The 8Bit for this is `$C0` Remembering that C is 12 as bits and therefore `1100`. The 16 bit equivalent is `$FFC0`, they are not the same bit patterns, they couldn't be. 
Now consider +64, the 8 and 16 bit versions are basically the same : `$40` and `$0040`. 
We extend the sign of negative values different than positive values. 
To sign-extend a value, copy the sign bit into the additional HO bits. 
Therefore for 8 bit, copy bit 7 (starting from 0) into the rest of the HO bits. 
This is for any size really, this is because for negative numbers, whatever we are minussing from that new HO bit, we are adding, with all the new 1 bits. 
Before we do any mathematics with any of this, we would need to extend or contract. 

![[Pasted image 20250319112550.png]]
Zero-Extension converts smaller unsigned values to larger unsigned values. 
This is super fucking easy right. 
![[Pasted image 20250319112622.png]]
Many language compilers will automatically handle sign and zero extension. 

```
signed char sbyte; // chars in C are byte values. 
short int sword; // short integers in C are *usually* 16 bit values. 
long int sdword; // Long integers are *usually* 32 bits in C

sword = sbyte; // auto sign-extends the 8 to the 16 bit
sdword = sbyte; // auto sign extends the 8 bit to the 32 bit
sdword = sword; // auto sign extends the 16 bit to the 32
```

This will require more machine instructions, usually, therefore, for arithmetic , try and keep everything the same size. 

Sign contraction is very difficult : 
Consider -448 : 16 bit hex is `$FE40` - meaning all `1111 1110 0100 0000`, the magnitude for thsi number is too large for one byte, 8, so we can't accurately assign that. 
In order to do this, look at HO byte(s) that you want to discard. In order for this too work, like in reverse of the sign extension, we need either all 0's or all F's at the beginning. 
Then it becomes very obvious what we need to do. 
![[Pasted image 20250319113642.png]]C will sometimes just store the LO portion of the expression into a smaller variable, discard the HO component - at best, C will give you a warning about the loss of precision that may occur. 

```
signed char sbyte; // chars are still a byte
short int sword; // shorts usually 16 bits
long int sdword; // Longs are usually 32 bit
... 
sbyte = (signed char) sword;
sbyte = (signed char) sdword;
sword = (short int) sdword;
```
The only safe option here is to compare the result of the expression to an upper and lower bound values before attempting to store the value into a smaller variable, just ot make sure that it will fit in after contraction. 

```
if(sword >= -128 && sword <= 127)
{ 
	sbyte = (signed char) sword; // is in range for 8 bit
} else 
{ 
	// report appropriate error
}

// Another way with assertions
assert( sword >= -128 && sword <= 127)
sbyte = (signed char) sword;

assert( sdword >= -32768 && sdword <= 32767 )
sword = (short int) sdword;
```
We would probably want to turn this into a `#define`or a function so that the code is hidden and everything else is a bit more readable. 


#### Saturation
Reduce the size of an integer through *saturation*
We do this when we don't mind losing some precision. 
You copy the LO bits of the larger object into the smaller object. 
If outside the range, then we just clip the larger value by setting the smaller object to the largest (or smallest) value within the smaller value's range: 
Eventually saturating all the bits that we have. 
When converting 16 to 8, if the value is not in range -128 and 127, then we are screwed, we lose some precision. Otherwise we just copy the LO byte into the 8 bit object. 
If outside the range, then we just clip appropriately. 

Sometimes clipping is better than raising an exception or just rejecting the calculation. 
Sometimes in audio or video, that result is just fine, and the user will not really notice, might just me a minor clip as well. 

Many CPUs support saturation, MultiMedia Extension instruction set - MMX/SSE/AVX on 80x86

MMX - Multimedia Extension - the first SIMD
SSE - Streaming SIMD Extension - Improved SIMD operations
AVX - Advanced Vector Extensions - for parallel processing

These are instruction sets for processors - defining commands (operations) that the processor - they are used in ASM or machine code (binary, as in the machine only language) - 

Most HL Languages do not support, the technique is not difficult to do on our own: 

### Binary-Coded Decimal Representation
BCD - sequence of nibbles, each nibble representing 0-9 - that's one way of doing it - A byte also for the decimal itself.

![[Pasted image 20250320001613.png]]
Just remember for BCD - Binary Coded Decimal (great for really simple stuff like digital clocks). 
Just a nibble for each value from 0 - 9. 
16 bit can so values from 0000- 9999. 
This is super inefficient storage. 
The calcs also tend to be slower as well. 

These are more likely in embedded systems - toaster ovens and alarm clocks. 

FPU - floating point unit - does actually support BCD in the 80x86. 
FPU can convert BCD to binary. 
Apparently producing more accurate results - not sure about that one. 




### Fixed-Point Representation
There is also floating point - remember A level (:peepoYes:). 
When it was just CPUs less overhead with this over floating point (less calculation I think?). 

Then we added the FPU - Now you rarely see this one, I guess without an FPU, this is and BCD are the only ways. 

In certain niche situations, some amazing ASM code will run fixed point faster, but it's rare. 

Some 3D gaming stuff will produce faster computations using 16:16 Fixed over 32 bit floating. 

The idea, choose a bit, before that, is the decimal. Therefore everything after that will be 2^-n away from that decimal. Starting at 1/2, then 1/4, then 1/8. 

Just thinking about the range for each part, if we need a higher range for the whole number, then we need more bits before the decimal. 

They are not completely expressive, there are numbers that cannot be represented by the number of bits that we have. There are infinite numbers between two integers, we could not possible represent them all with any given range of bits below infinity, therefore, we may just lose out on some accuracy. 
That could be with numbers that don't seem obvious at all. Given 8 bits, 0.3 - yeah that's not happening at all. 

Reduce the error by adding more bits. 

1.3 is a rough one as well apparently (meaning 0.3 also is). 

HOWEVER in BCD we can actually represent that number really easily with just a byte, two nibbles. 
`$0130` Where there is an implicit decimal point in the middle there. 
Generally though, more often than not, the fixed point binary representation will be the more useful one. 
Depending on what we need it for, either the binary format or the decimal format will let us do that. 


### Scaled Numeric Formats
I think it's a form of fixed point arithmetic, number is an integer that is scaled to represent fractional values. 
Here is a combination - We can choose any base - say that we are working with base 3 fractions, allowing us to represent 1/3, 1/9/, 1/27. 
For fractional values - multiply original value by some value that converts the fractional component to a whole number. 
Maintaining two decimal digits, tot he right of the decimal point. Multiply your values by 100 upon input. 
This will translate 1.3 to 130, which can be represented using an integer value. 
Imagine that we have 1.50 and 1.30, then convert that to 150 and 130, then we get 280. When we need to output these values, you divide them by 100, emit the quotient as the integer portion of the value and the remainder, and remainder as the fractional component. 
Needing specialized input and output routines. 

Represent fractional numbers using only integer values. 
Floating point operations are too slow (say on the FPU) or hardware resources are limited. 
Choose a scaling factor, usually a power of 2 or 10. 
Working with SF at 100, means that we are working with 2 decimal places. 
With 1024 working with 10 bits of precision as 2^10 = 1024. 
When we have a number with the fractional part, we scale by the SF to eliminate the fractional part. Then we have an integer. Perform any calcs as integers. 

Integer ops are usually faster, especially on hardware with limited FP support. 
There is limited precision, potential for overflow as well. 
The number has to start with a fixed-point number. 

Limited Range - using some bits for the fractional, that we actually just don't use. 

Slight difference in multiplication and division: 
Here's the issue : say 0.25 x 0.01 we know this to be 0.0025: however, if we multiply by 100, then 25 x 1 = 25, then 25/100, that's wrongly 0.25, therefore, we need SF^2, in order to get 1000, then 25 / SF^2 = 0.0025. 

This is true due to commutative laws: 
Where: 
$$(0.25 \times (100)) \times (0.01 \times (100))$$
Division suffers with the same problem: 
Suppose that we have 5 / 2.5, that we move to 500 and 250, clearly 2. 
We have scaled them by 100, the result that we would need then is 200, so we get 100 scaled less than what we want. If we just /100, then we get 0.02, which is just wrong. 
When we look at: 
$$(5\times100) / (2.5 \times 100)$$ 
Then we see that the scaling actually cancels each other out. 

Multiplication = Requiring / SF^2 at the end. 
Division = Requiring closer look to see if SF will cancel each other out. 

#### Rational Representation
With close approximations, apart from long BCD's. Not being able to do 1/3 is pretty bad, that's like a third of fractions right there. 
If we moved to base 3, then we wouldn't be able to do 1/2 or 1/10. 
We need a number system that could do any of this. 

Rational representation uses pairs of integers to represent fractional values. 
One integer representing the numerator (n) and the other represents the denominator (d). 
The actual value is n/d. 
As long as n and d are **relatively prime** (that is, not both evenly divisible by the same value). 

The arithmetic is quite easy; you can use the same algs to add, sub, multiple and divide that we learned in grade schools. 
Just be careful of overflow for either. 
I believe this is what `Fraction` does in python. 

###### More Reading
Donald Knuth Volume 2




