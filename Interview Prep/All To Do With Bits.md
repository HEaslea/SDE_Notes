[[Compiler Intrinsics]]
The `#include <intrin.h>` library has a couple of really good functions that look at bit counting, that are specific to the compilers (?). 

### Swapping Bits
Remembering that `x & (x - 1)` will **flip the lowest set bit in x**. 
Another is we can extract the lowest set bit of x with `x & ~(x - 1)`. 
The idea here is to think about what -1 does to the number, ie, we are flipping the final one and everything after it. Then when we get the not, we are flipping the final one again, (and everything after it). The everything after it, doesn't matter as they will be 0's anyways, however, the final one is important. 
Since it has been swapped twice, it will be a 1 again, duh, everything in front of it will be flipped once, everything after it will be a 0, (before and after flipped twice). Then when we & it with the original, we just get the last 1 in the word. 

There is the brute force idea, however, what we should be thinking about is that there are only 2 states for a bit to be in. If we think about a integer as an array of bits. 
To exchange the bit at i and j, we can first look at whether they differ, if they do not differ, which is actually half of the time, then we need not do anything. 
Then if they do differ, then all we need do is flip each one, Remember from [[Parity]], that XOR with 1, will flip a bit. or we can just not the bit. 

In an interview, you will ask what it is the function will be taking as args. 
Here we take the index `i` and `j` for the bit that we are flipping. 
One thing to remember here, is the idea of bit extracting, getting the bits we want and then doing something with 1, which is always `0x00001` however many zeroes before it. 

```
long long SwapBits(long long x, int i, int j)
{ 
	// extract and see if the two bits differ
	if(((x >> i) & 1) != ((x >> j) & 1))
	{
		// if they aren't the same
		// we have to flip them
		// here we use XOR in order to flip the bits
		// here we are creating the mask to swap 
		// so that we have 1 in the bits that we are swapping
		unsigned long long bit_mask = (1L << i) | (1L << j);
		x ^= bit_mask;
	}
	return x;
}
```

This is about as simple as it gets, but uses the very cool `(1L << i)` and the OR in order to add them together. 

### Reversing Bits
Similar to the [[Parity]] problem, we can use a look up table. 

Then we use `|` OR in ORder to add the sections of bits together: 
```
unsigned long long ReverseIbts(unsigned long long x)
{ 
	const int kMaskSize = 16; // same as the other
	const int kBitMask = 0xFFF;
	return precomputed_reverse[x & kBitMask] << (3 * kMaskSize) |
		precomputed_reverse [(x >> kMaskSize) & kBitMask] << (2  * kMaskSize) |
		precomputed_reverse [ x >> (kMaskSize * 2) & kBitMask] << (3 * kMaskSize) |
		precomputed_reverse [ x >> ( 3 * kMaskSize) & kBitMask];
}
```

Here again we are using an array based lookup table. You might think that "actually there must be a faster way", however, you have to think about how the other ways may include brute forcing through the entire word, then scale it in your mind. For just the first word, that's fine and fast, however, imagine if we had 200000 words, what are you going to do then. 

The thing to remember here however, that is different than parity, is that the order does matter, the MSB will become the LSB  in the reverse order: 

Imagine this was with an 8 bit integer, and we are separating into 2 bit integers. 

If the input is `(10010011)` then the output needs to be `rev(11), rev(00), rev(01), rev(10)` therefore we get `(11001001)`. Here the order is reversed as well, unlike parity that is associative, and indeed, commutative. 



#### Finding the Closest Integer with the Same Weight
The weight of an integer here, is said to be the the number of 1's in its binary form. 

`92` is `1011100`, the weight of 92 is therefore 4. 

The object here is to get the closest, same weight integer. We take in `x` and return `|y - x|` (ie. the smallest difference). ie. if `x = 6` you should return `x = 5`. 

The brute force will look at `x - 1, x + 1, x - 2, x + 2`. Think about if we took in 8, then the only other ones are going to be the powers of 2, therefore 4 would be the closest. Here we are trying $2^{3-1} - 1$ numbers before we arrive at the one that we want. 
Therefore if we were to look at $2^{30}$, we would have to look at $2^{29} - 1$, which is a shit ton of integers. 

Let's think about what constitutes a number that is closer to another, and we'll start smaller and try and find a rule that we can use for the larger numbers. 

Let's look at 4 bit integers: 
The LSB has the smallest change of 1: So in general can we say that just moving the least significant bit down by one denotes the smallest change?: 
`(1010)` and `(1001)`.  Where we have 10 and 9. 
`(0100)` 4 and `(0010)` 2 This seems to work, we are just moving the LSB down one space. 
Thinking about how all of these numbers are smaller than `x`. 
What if we have `(0111)` the closest will `(1011)` which is 11, which is higher. 
Here notice that we have flipped the $2^2$ bit so ( 7 - 4 ) and have flipped the $2^3$ bit, so (7 - 4 + 8). 
In order to minimize the difference here, I believe we just have to minimize the distance of the flip. 
`(01110)` `(x - 2^i) + 2^j`. The closer i and j are, the closer our results will be. 
The summary here is that we are swapping the two right most bits that differ. 

`(001110)` so here the right most if obviously bits at 0 and 1. 
`(0111)` the right most **different** bits are those at 3 and 2.

```
unsigned long long ClosestIntSameWeight(unsigned long long x)
{ 
	const static int kNumUnsignedBits = 64;
	for(int i = 0; i < kNumUnsignedBits - 1; ++i)
	{ 
		if(((x >> i & 1) != ((x >> (i + 1)) & 1))
		{ 
			x ^= (1 UL << 1) | (1UL << (i + 1)); // 
			return x;
		}
	}
	return invalid_argument("All bits are 0 or 1");
}
```

Here's a cool thing: 
With this number: `(001100)` How many other integers are there that I can make: 
Think about this, every one can be flipped into another zero, for the first one, there are 4 zeroes that we can pick (4 c 1): remember that choices like that, you can flip them, how many ways can we pick 1 from 4 = how many ways can you pick a set of 4 from 4. 
Then the second one is 4 c 1 as well. Now it's always going to be c 1, therefore, we can say, n(k), where K is number of zeroes, and n is number of 1. 
Therefore here we have `(001100)`, we have 2 1's and 4 0's, therefore we have 8 other integers that we have the weight for. 

### Computing Product without Arithmetical Operation
Find the multiplication without using the product operation (`*`).
Here, we would ask with the restrictions are, to the interviewer. 
They will be non-negative. They will also say that you cannot use decrement or increment. 

Well, what is multiplication: 3(5), that's just 0 + 5 + 5 + 5 = 15. If we were to do this, then we would run into a huge problem with time. 

The thing to think about with this, is that if we shift an integer, `x`, by 3, so `x <<= 3;` `x` is shifted to the left 3 times. That is the same as `x *` $2^{3}$. 

Very simple code to demonstrate: 
```
#include <math.h>

int a = 5;
a <<= 3;
cout << a << endl; // outs 40

int b = 5;
cout << b * (pow(2, 3)) << endl; // also outs 40;
```

What do we notice here, well, when we shift by `k`, we are multiplying by $2^{k}$. 
Right so what do we do when we multiply base 10 numbers in the special way we use to in primary school: 
```
	 21
	*34
   ----
	 84
   +630 // where we shifted left, so up by a tens
   ----
	714 // this is where we had to carry the 1 as well
```


So then the trick here is that we will iterate through the bits of `x`, when we find a 1, then we add to what is an initiated at 0 result. 
Go to A in the notes that should be with the book. 

What you will see is that if we use the same method as above with binary, we will notice that 0 is more often found. Therefore, whenever we see a 0, there will just be a null period, I mean, that whole row for addition will be 0. 
And also what you will notice, which is also really nice, is that the only other number there can be is 1, which means that the top number (or bottom) depending on how you multiply. 
Two great numbers when we think about multiplication. 
Remember as well, that when you go down a rung in the addition, then you will shift by 1 towards the left, which is exactly what our useful << operator does. 
What we arrive at is the idea that we are going to add `y`, when `x` has a 1 bit, and that `y` will be shifted to the left by however far through that `x` bit is, ie if the bit is `0100`, then we will shift by 2. 

We will add, just using the | operator. 

```
unsigned long long Multiply(unsigned long long x, unsigned long long y)
{ 
	unsigned long long = 0;
	while(x)
	{ 
		// examining each bit of x. 
		while(x & 1) // while the LSB is 1
		{ 
			sum = Add(sum, y)
		}
		x >>= 1, y << = 1;
	}
	return sum;
}

// then we need a helper function to add them together
unsigned long long Add(unsigned long long a, unsigned long long b)
{ 
	while(b)
	{ 
		unsigned long long carry = a & b;
		a = a ^ b;
		b = carry << 1; // this is for the carry bit 
	}
}
```

The only issue I have here is that $O(n^{2})$ Time Complexity; However this is much better than the $O(2^n)$, like a massive massive improvement. 

You can begin to see the pattern with these sort of questions, that we utilise shifting and bitwise operations in order to mimic addition and multiplication. 

The next question, you'll need to know that $12 \div 2 = 6$  12 is the dividend, 2 is the divisor, and 6 is the quotient. 

The key to understanding the next bit, is that we know that binary base, base 2, is a number system, it just works, our number system of denary, doesn't only work in denary, it all works at all bases, however, we just have to remember that the base is. 
Other than that, the addition, subtraction etc. are universal among all bases. 
However, we tend to talk about binary, in terms of denary, sort of like how we might look at a coordinate system in terms of another, when we look at linear algebra. 

#### Finding the Quotient using Arithmetic Operators
Read the above. 
Think about how we divide numbers, and how all that works. 

So for instance, when we have $12 \div 3 = 4$. Really all we are looking at, is how many times you can take/subtract 3 from 12 in order to reach 0. 
If we cannot subtract 3, that would take us below 0, we take the remainder

#### Computing pow(X, Y)
Take in `x` and `y` and return $x^y$. 
Again, we ask about what are the assumptions, what the function should take in, and return etc. 
Is x an int, a double etc. Do we assume that `y` is non-negative. 
Remember when we think about ever iterating over something, if there is a lot to iterate over, are we able to do more per iteration. 
How do we look at exponents? Well, if we have $x^4 = x \times x \times x \times x$. 
You can think about this: $2^{8}$ this is the same as $(2^2)^4$. Instead of multiplying 
This one can be reduced further, as $2^{8}$ is the same as $((2^2)^2)^2$. 

If we have $1.1^{21}$ we are taking 1.1 and multiplying it by itself 20 times. 
Or we could do $(1.1^2)$ and then multiply that by 10.
Notice the first bit, we could do better, and have something as close to $1.1^3$ then multiply it by 7 times. We just have to remember this type of thing: 
$x^{y_0 + y_1} = x^{y_0} \times x^{y_1}$. 
Take something like this: This is for even numbers
$x^{(1010)_2} = x^{(101)_2 + (101)_2} = x^{(101)_2} \times x^{(101)_2}$  
Similarly:  for odd numbers, notice the $x$ at the back here.
$x^{(101)_2} = x^{(100)_2 + (1)_2} = x^{(100)_2} \times x = x^{(10)_2} \times  x^{(10)_2} \times x$.  
The main idea here, being that if we are taking (1010), which is 12, then we can have $x^6 \times x^6$. 
This is fine for every bit pattern that is even, meaning that if the LSB is 1, then this won't work, and we have to do something slightly different for that. 
When we have an odd number, then we just take the 1, from the back of the bit pattern, which forms $\times x$. at the end. 
Generalizing, we can do this for when y is even $(x^{y/2})^2$. Otherwise, for odd we do something like this: $x \times (x^{y/2})^2$. 
There is the idea of recursion here. 
This is fine for non negative. 

The easiest thing to do when `y` is negative, is just to take `1/x` and invert `y`, so `-y`. 
This is just a simple invert at the beginning, nothing too clever here. 
First without recursion, to avoid function calling overhead. 
```
double Power(double x, int y)
{ 
	double result = 1.0;
	long long power = y;
	if (y < 0)
	{ 
		power = -power, x = 1.0 / x;
	}
	while (power) // instead of recursion for now
	{  
		if(power & 1) // for oddity
		{ 
			result *= x; // if power is odd
		} 
		x *= x, power >>= 1; // here we are halving the power
	}
	return result;
}
```
Another thing to note is that we are gonna lose some accuracy here. 
(Precision is how close are readings are together).
![[Pasted image 20241016232906.png]]
The idea is that we are iterating over all the bits of `y`. 
We continue as long as y is not zero. 
We are taking into account, how many times can we halve the exponent, we can square x. 
The whole thing boils down to, how many times can we square x, reducing the exponent. 
We aren't really taking y/2, as we are shifting to the right. Say if we right shift `5`  , then we get 2. 

Going through some implementation: 
Say that we have`x = 2.0, y = 5, therefore power = 5 (binary 101) and result always 1.0`
5 is odd, therefore we have `result *= x` therefore `result = 2.0`.  
This is taking the `x` part out of the equation due to the oddness. 
Then we square `x` and half the power. 
Then the next one is not odd, therefore we square x and half the power. 
Eventually we get down to the power = 1. definitely odd there. 
I feel as though, we are taking the power, and then just halving, and squaring in the brackets, until we reach 1, putting any x's outside the result. 
Don't think of it as an equation, but rather, what we have to do to result, what we can do to result in order to get the answer that we want. 
The thing to remember here is that x will grow and then when we multiply result by that x, x might be a lot larger than it was. 
We haven't done any input checking here, that's something that we might include in order to improve.
Now this can be done recursively: 
```
double PowerRecursive(double x, int y)
{ 
	if(y == 0)
		return 1.0
	if(y < 0)
	{ 
		if(x == 0)
			throw std::invalid_argument("Undefined Result .....");
		return 1.0 / PowerRecursive(x, -y); // the inversion is done at the end
	}

	if( y % 2 == 0)
	{ 
		double halfPower = PowerRecursive(x, y/2);
		return halfPower * halfPower;
	} else 
	{ 
		return x * PowerRecursive(x, y - 1);
	}
}
```

#### Reverse Digits
The brute force approach would be to convert to a string, and then traverse over the string. 
I believe that we have already done this one, what we want to do here is take the increasing mod of the number, meaning that we want to isolate each number, and create a new number with it. 
if we have 1342, and we want to grab the 2, then we use `% 10`. 
Then we can just use /10 in order to reduce the number down. 
So really we take `%10, /10, %10, /10` etc. 
This one is really easy: 
```
int ReverseInt(int x)
{ 
	int result = 0; 
	while(x)
	{ 
		result = result * 10 + x % 10;
		x /= 10;
	}
	return result;
}
```

#### Check if a Decimal Integer is a Palindrome
Negative numbers cannot be palindromes. 
Here's something that we haven't thought about before, but really should have. 
When we have a number, and we say $log_2(x)$ we are asking, how many times, can I divide by 2 before the result falls below one. We are asking 2 to the power of something = the number in brackets, we are asking how many times we double what we have before we reach the number. 
Therefore when we say $log_2(150)$ we are really asking, how many bits it will take in order to produce this number in binary, of course we have to ceil this in order to get the right answer as here we cannot have 7 and a bit bits. 
$log_2(150) = 7.2288...$ Therefore we need 8 bits, as on the 8th /2, we fall below 1. 
We can do the same for any base system, especially denary.

Having a look at denary: our numbers are already in denary, but humour me for a moment. 
How many digits does it take to represent 150, well, we have to use $log_{10}(150)$ and we get something like 2.17...., so it takes 2.17 digits to represent 150, ceil that, or floor that + 1. in order to get the answer. It definitely takes 3 digits. 
Not something we think about, as we can see it, but if x is arbitrary and we weren't to know, then we would have to use this technique to see how long our number is in ints.

The brutish approach would be to convert to string, then two iterators, from the middle, move out, see if they match etc. 
However, remember the simple expression to extract the LSB, which is `x & ~(x - 1)`

Remember Always, that in order to represent a denary number in binary, to know the number of bits that this would require is `auto numBits = floor(log2(x)) + 1`. 

In order to get the most significant digit, here we have to divide the `integer` by the 10 ^ number of digits - 1.

This is fucking beautiful, never need to convert to a string. we just move up the least significant bit and the most significant, we make the int smaller and smaller. 

```
bool IsPalindrome(int x)
{ 
	if(x <= 0) // negative numbers cannot be palindromic
		return x == 0; // don't really like this due to error numbers

	int const num_digits = static_cast<int>(floor(log10(x))) + 1;
	int msd_mask = static_cast<int>(pow(10, num_digits - 1));
	for(int i = 0; i < (num_digits / 2); ++i)
	{ 
		if (x / masd_mask != x % 10)
		{ 
			return false;
		}
		x %= msd_mask; // remove the most significant digit of x
		x /= 10;       // remove the least significant digit of x
		msd_mask /= 100;
	}
	return true;
}
```

#### Generate Uniform Random Numbers
Here's a great question: *How would you mimic a three sided coin with a two sided coin*. You just flip it twice, you can ignore one, act like it doesn't exist and it doesn't and then we really have only three outcomes that are equally as likely. 

The trick with this one is that if we want to have a random number between `upper_bound` and `lower_bound`, then we have `[lb, ub]`. One thing that you can do is simple get a random number between `[0, ub - lb]` and then add `lb`. 
This is super fucking obvious when we see it like this `[lb , ub] = [0 , ub - lb] + lb`. 
Another thing to note is when we look at how many bits that we will require: 
$floor(log_2(x)) + 1 $ will give us that idea, and then we know this: 
$2^n - 1 < x \leq 2^{n+1}-1$. Where $n = floor(log_2(x))=<$
The above is important, as we have a number generator, but it only does 1 or 0, essentially a bit random generator for a single bit
This is the same thing with the coin from earlier, if you have one that is out of range, then we just discard that and try again. This is rough however, as we are going to be going through a few loops in order to do this checking. Every time we discard, we are going to have to go through n times, where n is the number of bits denoting the integer above the ub. 

Whenever we have a random number generator and we look at time complexity, we will have to look at probability. 

The general idea then, is we ask the number of outcomes that there could be: 
take `[lb , ub]` to be `[4 , 10]`. 
Therefore the number of outcomes : will lie in the range of : `[0 , 10 - 4 + 1]` giving us `[0 , 7]`. 
Then we have a look to say how many bits are required in order to show that, well: 
$2^2 - 1 < number of outcomes \leq 2^ 3 - 1$. which is actually exactly true with the =. 
Therefore we require 3 bits: 
The really nice thing about knowing that $x \leq 2^n -1$ means that if we have `1 << (n+1)` and that will be greater than any number of binary of n bits. 
I mean this is true of denary numbers as well, but you just haven't thought about it yet, and wouldn't need to. 
In that the smallest three digit number, is greater than any 2 digit number. 
What is true of one base system, will be true of another base system. 
Any number of 5 bits, will be greater than any number of 4 bits, plain and simple: 

```
int UniformRandom(int lower_bound, int upper_bound)
{
	int number_of_outcomes = upper_bound - lower_bound + 1;
	do // lol haven't seen one of these for a long time
	{ 
		result = 0; 
		for(int i = 0; (1 << i) < number_of_outcomes; ++i)
		{ 
			result = (result << 1) | ZeroOneRandom();
		} while(result >= number_of_outcomes);
		return reuslt + lower_bound;
	}
}
```

Here one of the problems that we have here, is that if we get say all 1's, the number keeps going an might go above the UB. 
The time complexity is a little weird. 

