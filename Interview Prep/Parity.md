Parity of a word is the oddity of ones in the word, if there are odd ones, then parity is 1, if there are even ones, then the parity is 0. 

The first thought is to cycle through the word, and & for ones: 
```
short Parity(unsigned long long word)
{ 
	short num_bits = 0;
	while(word)
	{ 
		num_bits += x & 1; returning the number of ones 
		x >> = 1; // shift to the right one 
	}
	return num_bits;
}
```

Then we look at parity;
```
short Parity(unsigned long long x)
{ 
	short result = 0; 
	while(x)
	{ 
		result ^= (x & 1);
		x >> = 1;
	}
}
```
 
The key thing to remember is that if we have 1 `0001` with any amount of zeroes in front, then XOR'ing with 1, means that we are flipping that bit: 
`1 ^ 1 = 0` `0 ^ 1 = 1` that's really cool, and it's a bit trick that will really help us here. 

Now here's the really cool thing: when we have a word, and we `w - 1`, we are flipping all the bits after the final 1, including that one. 
eg. 
```
18 = 10010
17 = 10001 // notice we flip the final one and then all the 1's and 0's after that

5748 = 1011001110100
5747 = 1011001110011 // flipped the final one and the everything after that

x = x & (x - 1);
x = 5472; however the number doesn't matter here
```

Then the idea is that we take the that answer, and we & then together, therefore we are taking the final one of x ie. 5748and getting rid of it, we are removing the final one, we are removing the least significant bit. 
The idea is: `x & (x - 1)` removes the least significant bit: 
The number doesn't matter, however, the fact that we removed the final one of the word, means if we do that for every one, until the number = 0; then we are going to count how many ones, based on the iterations to reach 0. Then every time remove a 1, we flip the result, from 1 at first, to 0 etc. etc. 

```
short Parity(unsigned long long x)
{ 
	short result = 0; // 
	while(x)
	{ 
		result ^= 1;
		x = x & (x - 1);
	}
	return result;
}
```

There is another way, for if we have some really really long words, take 64 bit words. 
How many ints are there, $2^{64}$ ints, you want to store that amount, nah, neither do I. 
What can we store, 16 bit words, that will do. There are much fewer of those. 
The thing we want to remember, is that we want to cache results, therefore we don't work them out, rather, we just look for them in storage. 
The idea with parity is that it is associative, meaning that if you have the parity for each 2 bit word of a longer word, then we can XOR them all together in order to get the final parity. 

Therefore, let's store the 16 bit words, cache them, and them work through the 16 bit words of a longer 64 bit word, meaning here we will have 4 chunks, then work out the XOR parity of the 16 bit phrases. 
We are breaking apart the word and working on the parity of the phrases. 

We will shift bits in order to get that part and then use a mask in order to check just that section, they will be pre computed 16 bit phrases: 
```
short Parity(unsigned long long)
{ 
	const int kMaskSize = 16; // size of the phrases
	const int kBitMask = 0xFFFF; // the last 16 bits are all 1's 
	return kPreComputedParity[x >> (3 * kMaskSize)] ^ 
			kPreComputedParity[x >> (2 * kMaskSize) & kBitMask] ^
			kPreComputedParity[x >>  kMaskSize & kBitMask] ^
			kPreComputedParity[x & kBitMask];
}
```
The first phrase will need to be shifted by 16 * 3 over, in order to get the first 16 bits to the end, we won't need a mask here, as the leading 0's will be the other 48. 

Now time complexity wise, think about the length of the words that we are using. 
Let that be $L$ (the width of the words for which we are caching the results, in this case 16) and $n$ is the size of the words themselves, here they are 64 bits, here we have 64/4 ($n/L$). 
So then here, assuming that each of the bit operations are $O(1)$, then we have $O(n / L)$. 

XOR is associative, meaning that we can do the XOR operations in any order and you will find the same answer. 

Clearly, as we are essentially "counting" the numbers of 1s and 0s, the parity of a word is the same, no matter which order that we represent the bits. 
Remember the idea that the parity of `11010111` is the same as the parity of `1101` and `0111` XOR'd together. 
The idea here is that if we XOR the words together, then the parity will be the same. 
Take `11010111` and split it up into two words, `1101 ^ 0111 = 1010` The parity of the first is 0, the parity of `1101` = 1 and `0111` = 1, the then XOR 1 1 = 0. 
Essentially, we get the XOR all the way of each shortening phrase. 

The idea is that we take the first 32 bits and XOR with the last, then we take the 32 bit remainder, XOR the first 16 with the last 16 etc., until we are down to the last two. 
This will look really simple like this: 
```
short Parity (unsigned long long x)
{ 
	x ^= x >> 32;
	x ^= x >> 16;
	x ^= x >> 8;
	x ^= x >> 4;
	x ^= x >> 2;
	x ^= x >> 1;
	return x & 0x1; // then just mask for the last bit. 
}
```

Here, there are how many times we divide by 2 of the word length to get to 1 operations, therefore $O(log n)$ where n is the word length. 

