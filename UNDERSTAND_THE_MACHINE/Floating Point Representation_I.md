When we cannot represent a real number exactly, as there are infinite even between 0..1, then we just use the the closest number that we can represent exactly. 

Integers cannot represent fractional parts. Then, fixed point, loses the range that integers have. 


The thing to remember through all of this: when we have a number in denary, we can times to be a factor of 10 in order to "move" the numbers up through each column of units. So 2 x 10 = 20, then 2 x 10^3 = 2000. That all makes sense, it's the same in binary, where instead of 10, we just have 2. 
So if we have a binary number, we might do something like this number here: 
170 : `10101010` then we an say x by 2 so : ` x 10` (2 in binary); 
Then that is just, very simple, adding a single 0, remember that `<<` is just timesing by 2. 
so then more clearly 2^3 = `1000 (in binary)`which looks familiar to the 10's right. 
The number here: 
`170 x 8` : 
`10101010 x 1000 (or 2^3)` = `010101010000` notice the three extra 0's at the end. 
More clearly we might have. 
`1111 x 100 = 0111100`  which is just 15 x 4 = 60. This is going to be very useful, as we are going to see `2^n` a lot with floating point representation. 
It's exactly the same way in binary, now that is very very cool and something that I haven't thought about before. 
Then, the same in hexadecimal is pretty obvious. 
If we have a number: `0x1A x 16 = 0x1A0`
Just by any multiple of 16, numbers have to move to the left by 1. 

![[Pasted image 20250324114244.png]]

Think about this, when we have 8 bits for the fractional component, and 8 bits for the integer component. The integer part can be between 0 ... 255, and the fractional component, between 2^-8 - and 1. 
Now imagine that we had a number, where, we only needed to bits in the fractional part, however, we have 6 more bits, that are just going to go to waste, wouldn't it be nice to add range to the integer part, without messing up the fractional part too much. 

Floating point, radix (binary point) can float between digits in the number as needed. 
In the case above, the point can float so that we only have to fractional points. 
Leaving 15 bits for the integer. 

So we have bits for the mantissa, a base value that falls within the limited range. 
Exponent bits - the multiplier applied to the mantissa. 
If the different between the smallest and largest exponent is greater thant the number of significant digits in the mantissa. 

![[Pasted image 20250324120824.png]]
Remembering that `en` (where `n` is a number) is just $\times10^{n}$  . 


This particular floating representation can approximate, 0.00 -> 9.99 e 99. 

Careful, however, that hte value like, 9_876_543_210, would have to be approximated with, 9.88 e 9. The range on this badboy is huge. 

Floating point can have multiple representations, for instance, 1.00e+1 and 0.10e+2. 
For every value that has multiple representations, that means that are fewer values that we can actually represent right. 
Say if one value has 2 ways of presenting, then that is one less value that we can represent. 

When we are adding or subtracting two numbers that are in scientific notation, we must make sure that they are in the same exponents. 
1.23e1 and 4.56e0, you would need 0.456e1, in order to do anything with it. 

The lack of precision (number of digits or bits maintained in a computation) means that the accuracy will be less. 

For more accuracy, we would need more bits - they are known as **guard digits**. 

The accuracy lost can build up over a sequence of computations can be wild. 

> The order of evaluation can affect the accuracy of the result

> Whenever subtracting two numbers with the same signs or adding two numbers with different signs, the accuracy of the result may be less than the precisions available in the floating point format. 

**Catastrophic Cancellation** in floating point arithmetic : Imagine that we are subtracting two numbers (having the same sign, say two positives, two negatives, where we will get the difference between the two numbers)(the difference will be smaller than either of the two numbers). 

When subtracting nearly equal number, the most significant digits cancel each other out, leaving only the least significant bits, which may just be affected by prior rounding errors. 
```
double x = 1.0000001;
double y = 1.0000000;

double result = x - y; // the different is tiny, precision loss my occur
// the different is very tiny indeed. 
```
That's one problem that might occur: 

Adding two numbers with different signs (one large and one very small). 
Take (1e10 + (-1e-5)) : the smaller number may be lost due to limited precision in floating-point representation. Due to **floating point rounding**. 


**Accuracy** : how close a value is to the true or exact mathematical value. A result with low accuracy has significant error compared to the correct value. 
**Precision**: refers to how many significant digits a floating-point number can represent. Higher precision (usually) more bits used to store the value, reducing rounding errors. 

Multiplication and division does not suffer form these problems, there is no adjusting of the operations before the computation, with multiplications - just add the exponents together, and multiply the mantissas (or subtract the exponents and divide the mantissas for division). 

They may exacerbate the accuracy error that already exists in a value. 
This means: 
> Whenever we have a chain of calculations, always do the multiplication or/and division first. 

Given that algebra exists, then we just rearrange a calculation so the multiplication and division operations occur first. 

`x * (y + z)` Then just `x * y + x * z`. 

When there is multiplying two very large or very small numbers, there is *overflow* and *underflow*. 

The same will just occur when we have a small number and divide it by a large, or by a large number that is divided by a very very small number. 

Whenever we do the calculations, we would do well to put the numbers with the same magnitudes first. 

The limited precision : there are only finite bits used to represent numbers : 
32 for single precision, 64 for double precision. 
If we cannot represent the number within available bits, imagine that we have `1/3`, hard enough in denary, very hard in binary, the number will be rounded. 

There will be some issue in rounding issues, due to the precision of the bytes, this will accumulate over a period of time, therefore we should use the rules that we are given above, and just be careful and think about the numbers and their magnitudes as we go ahead. 

*Underflow* Very small numbers - that may just be rounded to 0. 

There are numbers that are not accurately represented in binary (0.1, 0.2, 1/3), their binary equiv is non-terminating fraction. This has to be truncated or rounded when stored. 

0.1 - no exact in binary - so we have to approximate it in floating point storage. 

This means that some languages will have this error: Especially in python: `0.1 + 0.2 == 0.3` this sometimes be false, even though mathematically they should be true. This is because they are approximated. 

Remember the whole, subtracting of nearly equal numbers - 1.00000001 - 1.00000000 this could be catastrophic. 

- **Limited precision:** Only a fixed number of digits can be represented.
    
- **Rounding errors:** Small differences arise due to rounding.
    
- **Precision loss with large/small numbers:** Underflow and overflow.
    
- **Non-terminating binary representations:** Many decimal fractions cannot be exactly represented.
    
- **Comparison issues:** Floating-point numbers may not compare exactly.
    
- **Catastrophic cancellation:** Subtracting nearly equal numbers can lead to significant loss of precision.
    
- **Negative number representation:** Precision can be lost in subtraction or other operations with negative values.
- **Precision** refers to the **number of bits** used to represent a number, which determines how many digits can be stored. More bits mean higher precision.
    
- **Accuracy** refers to how **close** the stored number is to the **true value**. It's about how well the floating-point number approximates the actual real number.

> RULE : **you should never compare two floating-point values**

Different computations, that produce the same (mathematical) result may differ in the LO bit - This is due to the rounding and the inaccuracies of the bits. 

There could be 1.31e0 and 1.69e0, that should result in 3.00e0. 
Then adding 1.50e0 and 1.50e0, that should result in the same thing. 

However, they are not equal `==`. 

So then how do we compare floating points: 
First to find out how much tolerance we show allow in a comparison: Then we need to check the range there: 
`if (( value1 >= (value2 - error)) and (value1 <= (value2 + error))`
 
Another way : more efficient way: 
`if (abs(value1 - value2) <= error)` where `error` is the tolerance. 

The final rule is: 
> When comparing two floating point numbers, for equality `==`, always compare the values to see if the difference between the two values is less than some small error value

This is a very famous problem. 
Less than or greater than, this is less problematic. 

Imagine that we have a sequence of floating point calculations : then we will get a result with $\pm  \text{error}$. 

Something cool, in python: 
```
print(0.1 + 0.2)
# prints out 3.00000000000000004
```

For `>` and `<` there could be this. 
Say that we 1.25 which is `error` of $\pm 0.05$ meaning that that value could be 1.20, or 1.30. 
This means that value might be erroneous, due to the inaccuracy. 
So then if we have another value is has no error : say 1.27 and we say `value1 < value2` that would be true, however, in this case, we don't actually know that, and it could actually be false. 

Remember as well that `(B + error)` would still have inaccuracies, depending on the relative magnitudes of the values B and `error`. 

The real trick is understanding floating points in the language that you are going to be using. 


### IEEE Floating-Point Formats
FPU - intel first said yeah, for the 8086 - however, they needed it improved for the 8087 FPU. 

Kahan, Coonen, Stone - they came up with the KCS Floating Point Standard 
They did so well, the IEEE used this format as the basis for the IEEE Std 754 floating point format. 

There are three floating point formats : single, double and extended. 

Single and double are C's `float` and `double`. 

Extended precision, contains 16 extra bits, that can be used as guard bits before rounding down to a `double`. 

**Guard Bits** - additional bits in floating point calculations, enhancing precision during intermediate calculations, to prevent rounding errors or performing operations. 

When performing - temporarily store the intermediate result in a **larger-than-usual register**. 

After the result is computed, the system rounds the value to the available precision (32 or 64 for single or double). 
These registers are in the CPU, or the FPUs if there is one. 

#### Single Precision Floating Point Format
24 bit mantissa, 8 bit exponent. 
The mantissa in this form will represent a number between 1 and 2. 
The HO bit of the mantissa is always 1 and represents a value just to the left of the binary point. 
The 23 other bits, to the right of the binary point, and represent the value: 
`1.mmmmmmm mmmmmmmm mmmmmmmm`
This means that the mantissa is always greater than or equal to 1, because of the implied 1 bit. 
If the other bits are 0, then the value is 1.
Everything after that will represent a value times a successive negative power of 2, even if infinite bits after the 1, it would never add up to 2. 
Some examples as this is not abundantly clear: 

![[Pasted image 20250327164402.png]]

|Step|Fraction|Multiply by 2|Integer Part (Binary Digit)|New Fraction|
|---|---|---|---|---|
|1|0.7997|**1.5994**|**1** (keep 1, subtract 1)|0.5994|
|2|0.5994|**1.1988**|**1** (keep 1, subtract 1)|0.1988|
|3|0.1988|**0.3976**|**0** (keep 0)|0.3976|
|4|0.3976|**0.7952**|**0** (keep 0)|0.7952|
|5|0.7952|**1.5904**|**1** (keep 1, subtract 1)|0.5904|
|6|0.5904|**1.1808**|**1** (keep 1, subtract 1)|0.1808|
|7|0.1808|**0.3616**|**0** (keep 0)|0.3616|
|8|0.3616|**0.7232**|**0** (keep 0)|0.7232|
|9|0.7232|**1.4464**|**1** (keep 1, subtract 1)|0.4464|
|10|0.4464|**0.8928**|**0** (keep 0)|0.8928|
|11|0.8928|**1.7856**|**1** (keep 1, subtract 1)|0.7856|
|12|0.7856|**1.5712**|**1** (keep 1, subtract 1)|0.5712|
|13|0.5712|**1.1424**|**1** (keep 1, subtract 1)|0.1424|
|14|0.1424|**0.2848**|**0** (keep 0)|0.2848|
That's how you get the 1's and 0's. 

There are 23 bits - only 8 million values that we can represent, 23 bits of precision. 

The mantissa uses the 24-bit, meaning an unsigned binary number. 
The sign bit in 31, determines whether that value is positive or negative. 

This means that there are 2 ways to represent 0 (with the sign bit set or clear). 
![[Pasted image 20250327165604.png]]

The layout : 1 bit for the sign
8 for the exponents
23 for the fraction (mantissa). 

The key idea here is Floating Point as Scientific Notation: 
123,000 is just $1.23\times10^{5}$. 
0.00456 is just $4.56\times10{-3}$.

So anything outside the range of the mantissa, we need the mantissa * the 2 ^{exponent}. 

The exponent use an excess-127 format, to get the value : to convert an exponent to an excess 127 format, add 127 to the exponent value. AKA bias representation. 
Avoids negative exponent bit handling. 
As the exponent is an unsigned integer - this means that we can compare much much easier. 
So given this representation : 
00000000 = -127

Then 
01111110  = -1

Then +127 would have to be 11111110. 

Here's the thing: 
2^3 = 8, how to store that in this format. 

Exponent = 3. 
We need the value + 127, so we need 130, this format starts
130 = 10000010

This  just means that the exponent can have a range of 127 to -127 without needing a separate sign bit. 

Bit patterns can really just mean anything, any pattern can represent anything given that we have the right implementation, and we are consistent across a format. 

Then in the mantissa  :  the first 1 bit is assumed. So we don't need it in the representation. 

The idea being that we convert the decimal number into binary. Normalize, so that there is a 1 at the front. 
How much  did you move to get there, that's the exponent inverse. 
Then drop the leading 1, as it's assumed. 

We know several things to speed up the process: 
If the sign bit is 1, then we now that will be less than those values that have 0 sign bit. 

There is the idea that integer comparison is much faster than floating point number comparison. 

Remember the leading bit is implicitly 1 due to the fact that we normalize every single number, which is not so clear in the book. 

When we look at scientific notation : `3.14159 x 10^2` the mantissa is the 3.141... 

Storing numbers in `(sign bit) x mantissa x 2 ^{ exponent }` 

**Decimal Digits**: how many digits that we can accurately represent. The digits (0-9) after the decimal point, representing the fractional parts of a whole number. 

If a system has 6.5 decimal digits of precision, a number like this `123.456` is fine, but something like this `123.456789` would lose precision and be rounded. 

In IEEE 754, 1 bit sign -> 8 bit exponent -> 23 bit mantissa. 

In normalized form, there is the implicit leading 1, giving us effectively 24 bits of precision. 

Each binary bit contributes to precision, but in a different way than decimal digits. 

Decimal digits = $log_{10}(2^n) = n \times log_{10}(2)$  giving us about 7.22 decimal digits. 

Consider `123456789` in binary is roughly `111010110111100110100010101`
Due to rounding some of the least significant bits are lost, therefore with 24 bits of precision the number might be stored as 123456792. 

The more bits in the mantissa, the more decimal digits of precision. 

Simple rule of thumb is 3.32 bits $\approx$ 1 decimal digit -> the number of meaningful digits a floating-point number can hold before rounding messes it up. 

For 32 bits, 32/3.32 (around 7) $\approx$ 7 digits before things started to get rounded. 

The limited accuracy may not be right for scientific apps etc. 

### Double-Precision Floating-Point Format (64 bit)
11-bit excess-1,023 exponent, a 53 bit mantissa (implied HO bit of 1). 
Roughly 15 to 16 bits of precision, sufficient for most things. 

![[Pasted image 20250328114019.png]]
64 bit. 


### Extended-Precision Floating-Point Format
Ensuring accuracy in long chains of computations involving double-precision floating point, Intel made this. 
Using 80 bits, 10 bytes. 
When you store a single or double-precision to memory : the FPU automatically rounds the value down to the appropriate size before storing it. 
![[Pasted image 20250329112846.png]]

## Quad-Precision Floating-Point Format
The proper extension should be from 64 bit to 128 right.
This was too expensive to implement in the hardware. 
Some CPUs today can do it. 
![[Pasted image 20250329113042.png]]


### Maintaining Accuracy
1. **Order of Operations**: Sometimes, due to precision and accuracy, there's this idea that is really only true in computing, $(a + b) + c \neq a + (b + c)$. Small numbers added to large number can be lost. Summing smaller numbers before adding them to larger ones (Kahan Summation). Inaccuracies will crop in when we don't have the precision to represent a number perfectly and that compounds.
2. **Avoiding Catastrophic Cancellation**: Subtracting numbers that are very close together, may mean that we have underflow. Reformulate when you can to avoid these Catastrophic Cancellations. 
3. **Minimizing Rounding Errors**: Each floating-point operation introduces a small rounding error. 
4. **Using Higher Precision**: If we are just accumulating errors, `double` instead of `float`. 

#### Kahan Summation
Compensated summation : summation with the carry algorithm, minimize the loss of significance. 
Maintaining **correction term** tracking lost precision from previous additions. 
```
sum = 0
c = 0 (correction term)
# for each number that we are summing, x
y = x - c # adjust for lost precision
sum += y
#update c
c = (sum - temp) - y # temp is the previous sum
```
```
double sum = 0.0, c = 0.0; 
for(double x : {1.0, 1e-10, 1e-10, -1.0})
{ 
	double y = x - c;
	double t = sum + y; 
	c = (t - sum) - y; 
	sum = t; 
}
```

[Great Paper on It](https://www.validlab.com/goldberg/paper.pdf)


## Normalization and Denormalized Values
Normalized with the HO bit starting at 1. 
This means that there are more bits in the mantissa for actual numbers, without there being a whole load of leading 0's. 
Meaning it will be more accurate. 
Move to the left and decrement the exponents as we go. 
There are important cases where the number cannot be normalized, 0 is one of them. 

Biased exponent - offset to the value, excess127 has a bias of 127. 

## Biased Representation
If we have a range of -8 and +8, then we if we just add 8 to any of this: then we have 0 to +16. Just like converting Celsius to Kelvin. 
Excess - k, offset binary or biased representation, uses a fixed value K as a biasing value. 
Thus 0 is represented by K, and -K is all 0 bit patterns. 

Giving excess-3 for instance: the stored value that we have is 8, however, this will be the actual value of 5. 
Meaning that we can have integers, that with the excess, will represent negative numbers. 
This means that excess-127, whatever the binary value, -127 from it and get the actual value. 

Meaning that we can do the integer calculations and then remove the excess that we have. 

So the binary value in be in excess. 
$$E_{result} = (E_a + E_b) - 127$$

Do the computations before subtracting the excess. 


## Rounding
Calculations may produce a result with greater precision. The Guard Bits my help with this extra precision. 
When the calc is done, put back into floating point variable, what to do with the extra bits? 
1. Truncation
2. Rounding Up

**Guard Bit (G)** - The first extra bit after the least significant store bit
**Round Bit (R)** - The second extra bit
**Sticky Bit (S)** - A bit that tracks where any nonzero bits were shifted out. 

Rounding doesn't occur if guard bits are all 0, we have not had any "overflow" (not in that sense) that we would need to adjust for. 

Truncation - rounding towards 0. +ve numbers -> rounding down and truncation are the same thing

I would come back to this and do some more reading. 

Come back to page 73