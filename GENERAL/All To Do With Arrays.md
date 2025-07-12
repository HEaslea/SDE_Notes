Retrieving and updating arrays : O(1)

Take array A : `A[i]` : retrieves `i + 1`<sup>th</sup> element/object. 

They are static arrays (as vectors can be considered dynamic arrays). The size of constexpr. 
If we want to resize, then we must relocate, causing some issues. 

We use the library more than built in arrays, nicer to use, more operations and cleaner code, not decays etc. 

If we want to delete an element in there, we have to declare that element as 0, and then shift all to the right of it to the left by 1, meaning that it takes `O(n - i)` all the elements to the right of `i`. 
The same would be true of inserting a new element, given that there is space for insertion (making the array more like a stack). 

##### Boot Camp
Let's have a look a little better at how arrays work, where we restrict some of the possibilities that would make it easier to solve. 

The idea is that we have an array, we cannot make another, we have limited space, but we want to store incoming numbers (coming in as a vector it seems), in a pattern, we don't really want to use sort. 
We want to store the array, so that the even numbers are first. 

The king about arrays, is that we can work efficiently on both ends, and retrieval is `O(1)`. 
We can partition the array into 3 subarrays, Even, Unclassified, Odd. 
Everything will be unclassified at first, then we move each number to the corresponding edges. 

It's like we have two pointers, that move in from the edges, and we swap from the unclassified into the edges: 
```
void EvenOdd(std::vector<int>* A_ptr)
{ 
	vector<int>& A = *A_ptr;
	int next_even = 0; 
	int next_odd = size(A) - 1; 
	// prevent overlapping of indexes
	while (next_even < next_odd)
	{ 
		// if even, then we can leave where it is
		if(A[next_even] % 2 == 0){
			++next_even;
		} else {
			swap(A[next_even], A[next_odd--]);
		}
	}
}
```

I like the idea as well, of using templates with these: 

```
// where type must have a state resembling an integer
template <typename Inner> 
void EvenOdd(std::vector<Inner>* A_ptr)
{ 
	// Allowing for ADL
	using std::swap;

	int next_even = 0; int next_odd = size(A) - 1;
	
	vector<Inner>& A = *A_ptr;
	while (next_even < next_odd)
	{ 
		if(A[next_even] % 2 == 0) // requiring mod operator overload for Inner
		{ 
			++next_even;
		} else 
		{ 
			swap(A[next_even], A[next_odd--]);
		}
	}
}


// Where swap with type T is a free function.
```

#### Know Your Array Libraries
Array library is fixed size (static) and Vectors are dynamic (dynamic arrays). 

```
std::array<int, 3> , std::array<T, size_t>. 
std::array<int, 10> a{10, 20, 30}; // the rest are zeroes

std::vector<T> v{// whatever length that you want};
```

Then we can subarray: 
```
std::vector<int> vi{10 , 20 , 30 , 40 , 50 , 60 , 70 , 80 , 90};
std::vector<int> bi(vi.begin() + 1, vi.begin() + 5); // 5 - 1 = 4 elements
// 20 <-> 50
```

This is aggregate initialization. 
`std::array<int, 3> arA = {10, 20...}`
This is because `array` is an aggregate type, meaning that is has no user provided constructors. 
Here, no constructor can be called. Aggregate initialization just directly initializes the members directly. 

However, it's not the same with `vector` as they do have constructors provided by the user. 
They have a constructors with initializer list. 
`std::vector<int> vec = {1, 2, 3};` 
`std::vector(std::initializer_list<T> init);`


###### 2D Arrays
Remember that in C# there are jagged and rectangular arrays. 
`std::array<std::array<int, 2>, 3> A = {{{1, 2}, {3, 4}, {5, 6}}}`
Again with the aggregate initialization, therefore we need the outer {}. 

`std::vector<std::vector<int>> A = {{1, 2}, {3, 4}, {5, 6}};`
Remember that we are always doing the rows first, then the columns, the same way that we look at matrices. 

###### Emplace_back 
Remember to use that for objects to avoid extra copies that might occur. `push_back` for simple primitive types


### Copying Vectors
They are copied via  deep copy: 
Every element will be copied into another location in memory. 
There is a separate ownership of data between the two vectors. 
The copy constructor for the elements will be called in that case. 

[[Shallow and Deep Copy]]

##### Key Algorithms
```
binary_search(A.begin(), A.end(), 42);
lower_bound(A.begin(), A.end(), 42);
upper_bound(A.begin(), A.end(), 42);
fill(A.begin(), A.end(), 42);
min_element(A.begin(), A.end(), 42);
max_element(A.begin(), A.end(), 42);
reverse(A.begin(), A.end());
rotate(A.begin(), A.begin() + shift, A.end());
sort(A.begin(), A.end());
```
There was the idea that we use `begin(A)` for the majority of unknown containers. 

[[Quicksort]]
A divide and conquer sort algorithm. 


#### What to Look out For With Arrays

When it comes to using space, a lot of space complexity comes from allocating more space `O(n)`, however, if we use the array itself, then we will arrive at `O(1)`. 

Filling from the front is really slow, as we have to move everything after the front `O(n)` `n` objects. 
If you have to add, we try and add to the back so that none of the other elements have to be rearranged. 

Instead of deleting, requiring moving all the elements to the right by delete amount, consider just overwriting (seems obvious, but isn't always). 

When we encode integers within an array: consider little and big endian. 

Be comfortable working with subarrays. 

One of the biggest errors that we find is **one-of-errors**. Meaning that we go one beyond, probably the biggest cause in errors with arrays. 

When we are working on an array, it's fine to jumble it up, and only the state and integrity has to be considered just before the return. 

#### The Dutch National Flag Problem
Where we look at the `quicksort`. 
In general `quicksort` has large run times and deep function call stacks on arrays, as the subarrays that we find with the pivot, can differ so greatly. 

One general idea: the precondition of quicksort is nada, doesn't require anything, however, what if we impose the precondition that we somewhat organise values. 
I would call it a **Flexible Precondition**: isn't mandatory, however, would speed up run time of a specific implementation that we have. 
The **Flexible Precondition**: is that we should probably have a pivot, and then arrange, all < before, = in the middle, and > after that. See page 51, and that will create the Dutch national flag lol. 
We have an idea of the pivot before hand, then we are handing quicksort a beautifully wired array that it can blast through. Why not not use a quick sort? 
The = is really applicable if we have an array of very similar numbers. 
eg. `A = <0,1,2,0,2,1,1>`
The pivot index here will be 3 (0). 
Our pivot value = 0; 
Then a valid partitioning along the lines of (< , = , >). 
The valid partitioning is this: `<0,0,1,2,2,1,1>` Everything = to 0, and then everything > 0. 
If the pivot value was 2, then `<0,1,0,1,1,2,2>` and also `<0,0,1,1,1,2,2>` are both valid partitions. 

Our task is to write a program that takes an array A, and an index i into A, and rearrange the elements such that all elements less than A[i] (the pivot) appear first, followed by the equals to, followed by the greater than. 

The brute force, with extra allocated space is obvious, just write to a new list, while iterating through the given array. 

Remember that we should look towards a solution that uses the array in place, rather than creating new lists every time. 

Essentially we are writing the partitioning part to the quicksort algorithm: 
This might be a first pass at an alg to solve this.

```
typedef enum { kRed, kWhite, kBlue} Color;

void DutchFlagPartition(int pivot_index, vector<Color>& A)
{ 
	Color pivot = A[pivot_index]; // geting our value 
	for(int i = 0; i < size(A); ++i)
	{ 
		for(int j = i + 1; j < size(A); ++j)
		{ 
			if(A[j] < pivot)
			{ 
				swap(A[i], A[j]);
			}
		}
	}
}


```



```
template <typename T> 
void testImp(T&& A, int pI)
{ 
	auto pivotVal = A[pI]; // getting the pivot value
	for(int i = 0; i < size(A); ++i)
	{
		for(int j = i + 1; j < size(A); ++j)
		{ 
			if(A[j] < pivot)
			{ 
				swap(A[i], A[j]);
			}
		}
	}
}
```

The cool thing about this is that we have two for loops, now this is not the end goal, this is not where we want to be at `O(n^2)`, however, the cool thing about this, is how we look at the for loops. 
The first for loop is going to go through every element of the array: 
Immediately on to the second for loop: 
This is going to go through every single element that is one index further than i. So then the subarray that excludes elements <= i. 
Whenever we have for loops over arrays like this, we have to think about the subarray that we are going to iterate over. This makes it much easier to visualize what we are going over. 

Just visualizing that is very important. 
This means that we can visualize, that j, being the inner for loop iterator, we move through the subarray that is greater than i, and we swap with `A[j] for A[i]`, there seems to be a lot of wasted swaps here. And it takes all the iterations in order to get it sorted. 
Then, everything below the pivot is where it needs to be, what about above the pivot. 
We have to have a whole secondary system for that. 
We go over this in reverse, and we look for numbers that are greater than the pivot.
```
for(int i = size(A) - 1; i >= 0; --i)
{ 
	for(int j = i - 1; j >= 0; --j)
	{ 
		if(A[j] > pivot)
		{ 
			swap(A[i], A[j]);
			break;
		}
	}
}
```


This will not mean that they are ordered, however, we do get the greater numbers on the right of the pivot, and the lesser numbers on the left, and the equal numbers, naturally fall in the middle.

Here we have a space complexity of `O(1)`. 
The time complexity is bad, as we are iterating over a couple of times. 
The trouble is that with j, we are starting from i + 1, every time, we are swapping back and forth far more than we need to. 

Here is a slight variation: 
```
void DutchFlagPartition(int pivot_index, vector<int>& A)
{ 
	int pivot = A[pivot_index];
	// First pass for grouping elements smaller than
	int smaller = 0; 
	for(int i = 0; i < size(A); ++i)
	{ 
		if(A[i] < pivot)
			swap(A[i], A[smaller++]);
	}

	int larger = size(A) - 1;
	for(int i = size(A) - 1; i >= 0; --i)
	{ 
		if(A[i] > pivot)
			swap(A[i], A[larger--]);
	}
}
```

Then we have to think about how we can do this just in one pass. Can we reduce passes, by have a more tricky implementation in just a single pass. 

I suppose a way to think about it, is to subarray, and classify each smaller subsection of the array as `less, equal, unclassified, greater`. 
We can iterate through the `unclassified` and sort them to where they need to go. 
As we go through that, if the number is < pivot, we exchange it with the first in the unclassified list, if it is > then we exchange it with the last of the unclassified. If the same, we don't need to move it, as it is equal. 

```
void DutchFlagPartition(int pivot_index, vector<int>& A)
{ 
	int pivot = A[pivot_index]; // getting pivot value
	int smaller = 0, equal = 0, larger = size(A);
	/* 
	 * Keeping the following invariants during the iterations'
	 * bottom A[0, smaller - 1] 
	 * middle A[smaller, equal - 1]
	 * unclass A[equal, larger - 1] 
	 * top A[larger, size(A) - 1]
	*/
	while(equal < larger)// while we still have unclassified items
	{ 
		if(A[equal] < pivot)
		{ 
			swap(A[smaller++], A[equal++]);
		} else if (A[equal] == pivot)
		{ 
			++equal; // essentially just move on
		} else // A[equal] > pivot
		{ 
			swap(A[equal], A[larger--]);
		}
	}
}
```
The time complexity `O(n)` we are spending `O(1)` within each iteration. We go through n unclassified, and the whole array is unclassified at the beginning, therefore `O(n)`. 
Space complexity is clearly `O(1)`. 

There are some amazing variants to consider: 
Let's say there are three numbers, the groups are very clear here : They only have to appear together, and in no way have to be sorted. 

#### Incrementing an Arbitrary-Precision Integer
Taking in an integer, as an array of digits, non negative, decimal integer, and we want to update that array in order to show `D + 1`. 
If the input is `<1, 2, 9>` then our output will be `<1, 3, 0>`. 

Go from the back of the array, add 1. 
It's an integer therefore we will have to check that it has two digits, ie. >= 10. 
As we are only adding 1. Then we will have to roll through the number, and add 1 to the more significant digit. 

This is my one go: 
```
void AddingOne(std::vector<int>& A)
{ 
	int iE = A.size() - 1;
	for(;;)
	{ 
		A[iE]++;
		if(A[iE] == 10)
		{ 
			A[iE] = 0;
			iE--;
			if(iE < 0)
			{ 
				A.insert(A.begin(), 1);
				break;
			} else 
				break;
		}
	}
}
```

This does work, it's gross af, in an interview we would probably have to tidy it up, it's unclear. 
We can move a lot of the ifs into the for loop conditional: 
```
void AddingOne(std::vector<int>& A)
{ 
	++A.back();
	for(int i = A.size() - 1; i > 0 && A[i] == 10; --i)
	{ 
		A[i] = 0, ++A[i - 1]; // just like mine
	}
	if(A[0] == 10)
	{ 
		A[0] = 1; 
		A.emplace_back(0); // reducing one copy of one int
	}
}
```

Variant: Getting two strings of "01010111" etc. and then adding them together. 

#### Multiply Two Arbitrary-Precision Integers
It's common for Cryptographic algs to operate on very large integers. 
eg. 1024 or 4096 bit integers. 
A primitive operation of these is the multiplication. 
We can have big-endian arrays, to represent the number. With the first number negative, if the whole number negative. 

Like we did with the bits, under binary rules, what do we do in primary school. 
The issue that we have here is that we don't want to convert to an int, due to the issue that we might have overflow. 
Overflow if we have something like: 
`std::vector<int> vI = {0, 1, 2, 3, 4, 3, 4 ,2, 1, 2, 3, 1, 4, 1, 2, 3, ,1 2, 3, 4, 2, 3};` Which, if we converted to int, would be an error. 

Think about how we do it at school. Well we take the LSD (least significant digit) multiply that with every digit in the other number. 

I would like to say that, as well as time complexity, we have to think about space complexity as well. 
We would rather do everything all in one place. 

The largest number that can come from the multiplication of two other numbers, is `m + n` where `m`is the number of digits of A, and `n` is the number of digits of B. 
The size of the return array will therefore be `m + n`. 

The general way we do multiplication when we were younger, is such: 
```
123 * 678 

3 * 678 = 2034
2 * 678 * 10 = 13560
1 * 678 * 100 = 67800

Then add them all together and we get 83394
```

I was correct in assuming that we want to look at whether the whole number is going to be negative first. 
Doing the whole thing without any multiplication at all:
`const int sign = (num1.front() < 0) ^ (num2.front() < 0)? -1 : 1;`
The `^` here is very important.
With that sorted, we need to take out any negative numbers in the the passed numbers. 
Taking the abs value of both: 
`num1.front() = abs(num1.front()), num2.front()` 
Create our return vector: 
`std::vector<int> result(size(num1) + size(num2), 0);`
Initialize to all 0's for size of highest possible return. 
With the notion that this is the max number of digits of return, therefore, we might have to remove leading zeroes, if both numbers are on the lower scale of their possible ranges, based on number of digits. 

Remember the ways in which we can move integers and their digits around: 
Obviously we will have to start at the back of an array in order to get the lowest possible number. 
Where do we put the result number, based on i and j (i being index of the first number, j being the index of the second number), well that would be `i + j + 1`. 
So then: 
```
for(int i = size(num1) - 1; i >= 0; --i){
	for(int j = size(num2) - 1; j >= 0; --j)
	{ 
		result[i + j + 1] += num1[i] * num2[j]; 
		result[i + j] += result[i + j + 1]/10;
		// take the last number of that
		result[i + j + 1] %= 10;
	}
}
```

Then we will have to remove the leading zeroes: 
```
result = { 
	find_if_not(begin(result), end(result) [](int a){ return a == 0;}, end(result), end(result)
}; // changing result by assigning vector based on 
if(empty(result))
	return {0};
result.front() *= sign;
return result;
```
This works to find the first none `x` thing, `x` given in the lambda, that returns a `bool`. 

Very nicely done. 

### Advancing Through An Array 
This is where we begin to get a little different. 
The explanation was confusing here: 
The idea is that the array is full of nonnegative integers. The integer represents how many steps ahead you can go from that position. 
Ie. 3 is letting move 3 steps ahead. 
Therefore, we have to see if the array is traversable at all, we return `i`, which is the `i`th position that we can maximally traverse to. 
`A = <3, 3, 1, 0, 2, 0, 1>`
It is possible, but not if you take 3 steps from the get go, all 0's are traps and we want to avoid landing on those. 
We take one step, then 3 steps, then 2 steps, and the whole thing is traversable. 
If `A = <3, 2, 0, 0, 2, 0, 1>`. 
This one is not doable. 

Therefore, we are to write a function that takes in an array of `n` integers, and we are to return `i`, which is the index of the furthest point that we can get to in the array. 

We can analyse each point in the array. 
The way that I'm immediately thinking about it is, keeping a track of where we are, and analysing each of the "runs" that we make, essentially, we have a position, and we query by taking various "paths" from that point, and then saving maximum `i` that we achieve at that point. 

Obviously taking the largest jump every single time is not going to get us to the end every single time, that's just plain obvious. 
It might benefit us to analyze completely each position that we have.
Imagine it like a tree that builds chess moves, we have our current position, then we look at a move, then we analyse that as far as we can. 

The furthest that we can advance for each index `i` is `i + A[i]`. 
We keep track of `i`, being the furthest that we can go so far. 

```
bool CanReachEnd(std::vector<int> const& max_advance_steps)
{ 
	int furthest_so_far = 0, last_index = size(max_advance_steps) - 1;
	for(int i = 0;
		i <= furthest_so_far && furthest_so_far
		&& furthest_so_far < last_index; 
		++i)
	{ 
		furthest_so_far = max(furthest_so_far, max_advance_steps[i] + i);
	}
	return furthest_so_far >= last_index;
}
```
Generally a part of this is just seeing if we can get past the 0's in a row, if we can, then we are fine, however, if we can't and we find that `i` > `furthest_so_far`, then we know that we can't make it. 

Taking a fairly simple Array: 
`A = {3, 2, 2, 0, 2, 0, 1};` This is clearly true.
We set our `furthest` to 0, and the `last_index` to 6. 
Then we iterate through every single one of the numbers in the array, until we can get out of the array, as soon as we know it's possible. 
Essentially the algorithm looks at whether we can pass every single 0 in the array, if we can, then we know that we are golden. 
Starting at one, the furthest that we can reach here as an index is index 3; 
Then we move onto two, we are at index one, and the furthest that we can reach here is `max(index, i + A[i])`, which is 1 + 2, which is the same as index, therefore, we are still at 3 here. 
Then we move up one. 
We know that index here will be 4, as that is higher than 3, and we have 2 + 2; 
Then we move to i = 4, then our max is still going to be 4. 
The key here is this, if we have another zero, our i will be 5, and the highest we can go is 5. The index won't change as the next steps we can take is 0, therefore i will go to 6, which is > index. Therefore we would return false. 
The idea is that we look at the furthest that we can go so far, if we iterate through, and we are further than what we can possibly get to, then we have to return false. 
`O(n)`  for time, and `O(1)` for space. 
Take `A = {3, 2, 1, 0, 2, 0, 1}`
Index = 0. Iterate through, can get to 3, look at i = 2, can get to 4. 
Then at i = 3, we can also get to 4, still the furthest that we can get to. 
Then we get to i = 4, however, we can't move any more, meaning that index will stay at 4. 
Then i = 5, meaning the iteration over the array has gone further than we will be able to. 

I think that's the main thing here, is that we can move through the array, always advancing and we have to see if the person can also get there. 
The key here is that any position is accessible that is <= index. 
So if index = 5. All positions <= 5 are accessible. 
I don't think the book explains it very well at all: 
Think about the problem that we have, at it's core, define it, refine it etc. 
The issue here is that we have to get past the zeroes, we jump over them, or we just can't avoid them. 
The issue here is that we have a series of 0s, and if all the numbers preceding them are not able to get over, then we can't make it. 
Therefore, if we take at each point, the max amount we can go over, that means that we are also using <= the max that we can move. 

#### Deleting Duplicates From A Sorted Array 
I imagine the sorted aspect here is very important. 
There is an `O(n)` method for this. 
We don't want to use an auxiliary data structure for this either. We do want to use `O(1)` for space complexity as well. 
There are quite a few ideas in here, where we take another number and use it as an index into the array: 
The main idea here is that we rewrite over the array, and we just rewrite to the next space of the write index, that gets incremented whenever we see a unique number in that sequence (not using a map). 
Using a for loop that does go over every single point, in order to make sure that the numbers are each unique etc.
Then `O(n)`time complexity, `O(1)` in space as we are just going over, ignoring added variables.

```
void DeleteDupes(std::vector<int> const& A) 
{ 
	int write_index = 1;
	for(int i = 1; i < size(A); ++i)
	{ 
		if(A[i] != A[i - 1])
		{ 
			A[++lrite_index] = A[i];
		}
	}
}
```
Notice here that we start on one, we have to start on one, but we can, as we know that anything before the second digit will have to be unique, that seems really stupid, however, that number change. 
Hold on, there is another way of doing. 
My way of doing it is slightly different, and tbh, it means that you have to add one to the write_index at the end, however, that's minimal, and I believe mine reads easier. 
I've done pretty extensive testing. 

Usually if we have the idea that the precondition that the array is sorted. 

Then there is the variant of this, where we pass in a key as well. 
I had a good idea for this, not sure if the this is the best way of doing it at all. 

```
template <typename T, template <typename> typename Cont>
void deleteOfKey(Cont<T>& A, T key)
{ 
	int wi = 0; 
	for(int i = 0; i < A.size(); ++i)
	{ 
		if(A[i] != key) 
			A[wi++] =A[i];
	}
	for(; wi < A.size(); ++wi)
		A[wi] = 0;
}
```

I think the key for both of these, is the recognition, that the number we are on, is either unique, or is not the key. 
The one above does work, however, we have to go through the entire thing, then we might have to go through the entire thing again. 
There must be a way of shortening that. 
I forgot about swap, omgness. 

#### Buy and Sell Stock Once
Go onto page 59 for the graph. 
We are given the price of stock throughout the day, and we have to find the best time to buy and sell. 
Therefore, `A = <310, 315, 275, 295, 260, 270, 290, 230, 255, 250>`. 
We have to buy and sell in that order. 

Therefore the best on here is 260, and 290., therefore, the best we can get is 30. 
To note, that 250, is not the lowest price, nor the highest price. 

Therefore what we are finding, is rather, the range providing the greatest difference. 
The general idea is if the minimum occurs after the maximum. 

The general idea is this: 
`S[j] - S[i]`, where `j > i`, then we need to update the highest difference.
The logical step after that is that we go from i, into the subarray, and go through, meaning that we are essentially creating `O(n^2)`. 

That's how I would do it first, however, there has to be a better way. 

Dividing and Conquering:
Into two subarrays:
$S[0, \text{floor}(\frac{n}{2})]$  $S[\text{floor}(\frac{n}{2}) + 1, n - 1]$. 
The idea here is rather odd, we take the first subarray and see what the best result is there, then the second, then we combine the result, taking the better of the two results. 
Considering the possibility that we have the buy in the first subarray, then the sell in the second, which is a possibility, nevertheless, then it becomes obvious that we just take the minimum in the first subarray, and the maximum of the second. 

These are computable in `O(n)` time, with a single pass over each subarray. 
The time complexity here is `T(n) = 2T(n/2) + O(n)`, solving to `O(n log n)`. 
There is an edge case, that is somewhat odd, where the stocks are just going down  monotonically (without variation etc.).

```
double BuyAndSellStockOnce(const vector<double>& prices)
{ 
	// basic setup
	double min_price_so_far = numeric_limits<double>::max(), max_profit = 0;
	for(double price : prices) //O(n)
	{ 
		double max_profit_sell_today = price - min_price_so_far;
		max_proift = max(max_profit, max_profi_sell_today);
		min_price_so_far = min(min_price_so_far, price);
	}
	return max_profit;
}
```
Essentially we just go through, we set the range, then we go through every price. 

For every price in `prices`, we look at what the profit is, if we minus the number today, with the lowest that we have come against so far. 
Then we look at the max profit, which is just maxing whether todays is largest than the others that we have come up against. 
Then we look at the minimum price situation, if today has the lowest price so far, then we add that to the lowest price. 

The way that we iterate through the list, means that we are making sure that we buy then sell. 
The simple equation at the beginning keeps that in check. 
This seems very easy, but not at first glance. 
For some reason my brain feels as though we were shorting the position, of course we are not. 

The variant here is that we are asked to find the longest subarray of elements that are the same. 
The one I wrote was apparently the best time complexity, `O(n): 
```
template <typename T> 
size_t lenghtOfSame(std::vector<T> const& vi)
{ 
	if(vi.empty()) return r0;
	
	size_t maxLength = 1;
	size_t soFar = 1;
	for(int i = 1; i < vi.size(); ++i)
	{ 
		soFar = (vi[i] == vi[i - 1]) ? soFar +1 : 1;
		maxLength = std::max(maxLength, soFar);
	}
	return maxLength;
}
```

Using the ternary operator here is very cool, if there is a simple if - else, give that some thought. 


Now to the real meat and potatoes: 
### Buy and Sell Stock TWICE
The instructions mean that we have to buy after the first sale, I assume that means we have to buy after the first sell. 

You may want to just do the one day trade, twice, however, we are not taking advantage of previous calculations. 

Say that we have the best solution between `A[0, j]` where `0 < j < n`. 
Then we can do a reverse iteration for the other subarray that we have. 
This means that we can just combine the results that we have. 
The idea being that we find the solution up until j, then after that, so we have two subarrays almost. 
The fact that we have to do it in this order `buy - sell - buy - sell` to me indicates that we have to go through the array, we have to iterate through it. 
Remember the second array that we are going backwards through it, but only up to j. 
So forward up to `j` then backwards to presumably `j + 1`.  
In this sense, let's look at going forward, then going backwards, let's say that we go over the whole thing, in this array: `12, 11, 13, 9, 12, 8, 14, 13, 15`. 
Going forward: for each day, the max profit is : `0, 0, 2, 2, 3, 3, 6, 6, 7`. 
Going Backwards : for each day, the max profit is: `7, 7, 7, 7, 7, 7, 2, 2, 0`. 
The numbers correlate, however, they just occur in a different order. 
Then thinking about it like this: 
When we move through the first one, we are looking at so far. 
Then when we move backwards, we are looking at so far. 
So then we have to take, where `M`  `M[i] = F[i - 1] + B[i]`. 
Therefore we get something rather beautiful. 
It's taken that `F[-1] = 0`. 

```
double BuyAndSellStockTwice(std::vector<double> const& prices)
{ 
	double max_total_profit = 0;
	vector<double> first_buy_sell_profits(size(prices), 0);
	double min_price_so_far = std::numeric_limits<double>::max();

	// Forward phase; Recording maximum profit for each day if we were to sell
	for(int i = 0; i < size(prices); ++i) 
	{ 
		min_price_so_far = min(min_price_so_far, prices[i]);
		max_total_profit = max(max_total_profit, prices[i] - min_price_so_far);
		first_buy_sell_profits[i] = max_total_profits;
	}
	
	// Backward phase: 
	double max_price_so_far = numeric_limits<double>::min();
	for(int i = size(prices); i > 0; --i)
	{ 
		max_price_so_far = max(max_price_so_far, prices[i]);
		max_total_profit = 
			max(max_total_profit, 
				max_price_so_far - prices[i] + first_buy_sell_profits[i - 1]);
	}
	return max_total_profit;
}
```

#### Computing an Alteration
Write a program that takes an array A of `n` numbers. 
Then rewrite to get a new array B, that has the property that `B[0] <= B[1] >= B[2] <= B[3]`. 
We want to try and solve this locally, with `O(1)` space. 
One thing to think about when we have a collection of numbers: Even as small as `n = 3`. 
`6 9 8` what is the median here? `8` of course. 
We will be using a `swap` here somewhere, which is the norm when we want to change the array in place. 

Here's something to note, that if we want to use `quicksort` and `quickselect` then we should know about partition. 
Reorganizing around a pivot element so that all the elements smaller than or equal to the pivot up on the left side, and all elements greater than the pivot end up on the right side. 
Remember the Dutch National Flag problem that we looked through earlier. 

We must come to the realization at some point, that not only is this quite hard to think about, lol, but every number < or > is a very local thing. 
Take `1 9 10 2 5 7 2` Let's go through. 
If A[0] <= A[1], do nothing, that satisfies our rule. 
Then 9 needs to be greater than the next. 
It's not here, therefore we can swap them, we know this swap is safe, as if 9 is greater than the 1 before it, but not greater than the one to the right of it, we know that whatever number to the right of it, will be larger than the first. 
Gotta really think about numbers here. 

Therefore we know we can swap quite happily. 

There are different rules for when we are at the odd and the even interval. 
The first interval will be 0 (even) therefore, we need to know that A[0] <= A[1]. 
If that's not the case, then we need to swap. 
Then the next interval, which will be 1, then we take the idea that A[1] >= A[2]. etc. 
If not, then we swap. 
The odd and even thing doesn't matter, in terms of interval, as long as we alternate!

```
void Rearrange(vector<int>& A)
{ 
	for(size_t i = 1; i < size(A); ++i)
	{ 
		if((!(i % 2) && A[i - 1] < A[i]) || ((i % 2)) && A[i - 1] > A[i])))
			swap(A[i - 1], A[i]);
	}
}
```
`if(i % 2)` true if `i` is odd. The same as `if(i & 1)` if `i` is odd. 
Therefore `!(i % 2)` if `i` is not odd. 

Here we are starting at `i = 1` and then just looking backwards, therefore we have no overflow issues down the road. Whenever you are comparing, try to compare with elements that are behind the one you are looking at, so that we have no overflow. 
The idea, I'm terming, as comparing backwards. 
There is also the lovely idea here that we only need A[i] and A[i - 1] in memory. 

#### Enumerate All Primes to `n`
Obviously a natural number is a prime, if it is > 1, divisible only by 1 and itself. 
They play a great role in cryptographic algorithms. 
In RSA, we take modulo `n` where `n` is the product of two primes. 
Write a program that takes in an integer and returns all the primes between 1 and that integer. 
There was something that was the greatest common divisor. 
Where we take the greatest number that can divide both numbers, and I remember that being used for some primes(?). Can't remember exactly. 
There's a thing to remember, that in order to divide a still receive a whole number, we take this idea. 


There is something to be said for taking a map of primes, or an array of primes, and just going through those. 
However, seeing as the result is, either prime or not, we can have a boolean array for this. 

Oh, i see this pattern that the book is using, essentially, in order to generate primes, we only need 0-10. 
We know that 0 and 1 are not primes. 
Then we look at two, ok two is a prime, however, we know that everything that is divisible by two, is not going to be a prime. 
Therefore we take away all numbers that are divisible by two. 
We would need to know what length of array that we want to check for. 

```
// Returna all primes up to and including n.
vector<int> GeneratePrimes(int n)
{ 
	vector<int> primes;
	// setting everything above 0 and 1 to true
	deque<bool> is_prime(n + 1, true);
	is_prime[0] = is_prime[1] = false;
	for(int p = 2; p <= n; ++p)
	{ 
		if(is_prime[p])
		{ 
			primes.emplace_back(p);
			for(int i = p * 2; i <= n; i += p)
			{ 
				is_prime[i] = false;
			}
		}
	}
	return primes;
}
```
So here, we are creating a dequeue (easier to iterate through). Through the first iteration, every number will be True. 
If that is the case, then we put into primes (which is the vector of the actual numbers that are primes), then we go through is_prime, and make sure that all the numbers divided by that number. 
Although not immediately obvious the time complexity of this is going to be every time that we iterate over n, only looking at x numbers that are multiples of every p, given that p is found to be `is_prime`. 
Therefore we will be looking at `O(n/2 + n/3 + n/5 + n/7 + n/11 ...)` , it's really not obvious. 
What we are looking at here is the prime harmonic sum, if all the prime numbers where n, then we have the harmonic sum. 
The harmonic sum, behaves like `log(n)` therefore, spacing them out even further by making sure that they are primes, means that they are further apart, making them `log(log(n))`. 
Then we have done that n times `O(n log log n)`. 
The space complexity is definitely dominated by the addition of `is_prime` and then if you consider the return, and possibly aversion of copy elision, then we have to be careful. 
You can, in an interview setting, look at two options and see if there are better time complexities in the second version. 
We can even do better if we think about culling some the `p`'s that we have already divided by. 
Think about this, the overlap of the numbers. 
We are taking the first instance of the number divisible by itself and 1, put it in the return primes vector. Then we go through all the others: 
2 is fine, as everything above 2 will be true, and needs to be false. 
Then we take 3, then we go through and look at all that are divisible by 3. 
However, notice the overlap of 6, we are going through that one twice. 
Then we get to 5, and we go over ten and probably 15 as well. 
Think about how this grows super fast. 
Then we can ignore even numbers, for obvious reasons. 
```
vector<int> GeneratePrimes(int n)
{ 
	if(n < 2)
		return {};

	const int size = floor(0.5 * (n - 3)) + 1;
	vector<int> primes;
	primes.emplace_back(2);

	deque<bool> is_prime(size, true);
	for(int i = 0; i < size; ++i)
	{ 
		if(is_prime[i])
		{
			int p = ( i * 2 ) + 3; // only getting odd numbers
			// have to have the + 3, so that when we have 
			// i = 0, we get 3, 
			primes.emplace_back(p); 
			for(long long j = 2LL * i * i + 6 * i + 3; j < size; j += p)
			{
				is_prime[j] = false;
			}
		}
	}
	return primes;
}
```
As we are only taking the odd numbers, then `is_prime`need only be that, that's why it's half the numbers as n. 
A little bit of set theory there. 
When we have `n`, we have to think about what that means in terms of set, redefining sets, that we are going to iterate over, must be a critical part of all this. 
Then we are only starting from 3, therefore, we are taking away 3 from the original set, / 2 then add the 1. 
Say if n is size 10, yet we are looking at numbers that are starting from, 3 only looking at the odd numbers, then we need that extra one, in order to make sure we still have proper size after the division. 
Then (10 - 3) / 2 = 3. Therefore we need 4. 
As there are 3 5 7 9, numbers. 

I think it helps that we are thinking about what numbers we actually want to go over there. 

This means that p has to be i * 2 + 3 in order to get the corresponding number. 

OOH here is an idea, that if we have an array, that is virtually mapping to another one. So 0 is relating to 3, 1 is to 5 etc. then we have to use a function, and its inverse in order to get that mapping as we go. 

The `long long j = 2LL * i * i + 6 * i + 3;`
I think the key here is that any odd prime, all below it will already be dealt with. 
Therefore we take the i * i. 
This will skip even numbers and map to the right index. 
It ensures that we get the right mapping from `i` to get to `p`. 

#### Permute the Elements of an Array
Essentially just rearranging elements of an array. 
`<a b c d>` `<b d c a>`. 
Here there are 24 permutations. 
This is the permutations think: so here, we are picking 4 of the 4: `4 P 4` = 24. 
Which is the same as `4!`
If we think about permutations as a form of mapping between arrays and indexes. 
That's always a thing I'm starting to think about. 
Say that we have the elements: 
```
indexes:     0  1  2  3 
elements:    1  2  3  4
```
The mapping here is quite obvious, if we only take the indexes, then we know that the element in there is going to be `[i + 1]`. 
That's just an idea, there are more ways to map of course. 
Then we can be given a permutation: 
`<2 0 1 3>` meaning that the element at location 0, maps to element 2. Elements at location 1 to 0. Element at location 1 to 2, and elements at location 3 to 3. 
Meaning we will have `<a b c d> to < b c a d>`. 
Now we have to apply this. 
```
initial: a  b  c  d
permute: 2  0  1  3
end:     b  c  a  d
```

I think the main thing to think about here is swapping: 
The thing we have to think about here, is what is actually going on, when something moves, 
What we notice early on with this one, is that after the second swap, it is complete. 
We dodge when `i == P[i]`. 
```
a b c d
3 0 2 1
b d c a
```
When we move an element we know that it is in the right place, therefore we have to move the permutation as well. 

```
d b c a
1 0 2 3
b d c a
0 1 2 3
```

I think the key to this one is that we are trying to get the permutation to normal number line as well. 

Stop thinking about the problems, with the first line of thinking, considering that to make 100% sense. 
Open all options, think about what we are trying to do, what will the end result look like. 

Therefore the solution being: 
```
template <typename T>
void permute(vector<T>& A, vector<int>& P)
{ 
	for(int i = 0; i < A.size(); ++i)
	{ 
		if(i != P[i])
		{ 
			swap(A[i], A[P[i]]);
			swap(P[i], P[P[i]]);
		}
	}
}
```
The brute force would be really obvious: 
```
template <typename T> 
void brutePermute(std::vector<T>& A, std::vector<int>& P)
{ 
	vector<T> b(A.size());
	for(int i = 0; i < A.size(); ++i)
		b[P[i]] = A[i];
	A = b;
}
```
The solution above does not take any more space complexity. 

The **variant** here is that we do not modify P, and we do not use any additional storage: 
Think about the idea that the permutations have to be positive. 
When we have a problem, maybe we should list, all the aspects of what is going on: 
For instance a permutation represents an index, therefore it cannot go to a negative value. 
A permutation can be inversed, meaning what do we have to do in order to get the values back to where they started. 

#### Compute the Next Permutation
The idea is of course we have `n!` permutations per `n` elements, and we have dictionary ordering for them, where `0 1 2` is the lowest and `2 1 0` is the highest. 
We have to give back the next permutation. 

`1 0 3 2` and the next one is `1 2 0 3`. 

If the input is already that last possible permutation in terms of dictionary ordering, then we just return an empty array. 

Let's read through it. 

Take a look at what we are actually doing, with the tools, within the array, tools that are the array etc. 

`1 0 3 2` then the next is `1 2 0 3`
`4 6 7 8` the next is `4 7 6 8`
`4 7 6 8` the next is `4 7 8 6`
There's definitely a swap in there, for two numbers. 
`2 9 9 9` then the next is `9 2 9 9`

What is the least valuable part of a number, the furthest to the right. 

The largest number of any set of numbers, is just going to be a decreasing permutation. 
The numbers cannot repeat. 
`9 8 7 6 5 4 3 2 1 0` is the largest permutation that we can get out of these numbers. 
`9 8 7 6 5 3 2 1 4 0`
The further to make a swap to the left, the larger the change is going to be. 
If the right hand number is < than the left hand number for swapping. 
Going smaller: 
`7 2 3 5` then the next is `7 2 5 3`
Changing the two will result in hundreds in difference, changing the 5, which must be the case, as we are swapping the numbers, then usually we are looking at a difference in tens. 
If we can swap the last two, then we will, the difference has to be negative, in that the second number must be higher than the last. 
`8 2 9 1` then the next is `8 9 1 2`. 
`7 3 8 6 2` then the next is (`7 6 8 3 2`)  `7 6 2 3 8`
Going from the back, as we need to go up in number, the only way to do that is to swap the 6 with the 3. Then we reduce the number behind that to be the lowest, we know that those numbers are already the largest they can be, as they are descending, therefore, we just have to swap all those numbers around. 

Let's take the idea, from the back we check to see that each number is increasing. From the last example, we will arrive at the 3. Then we need to get the number with the smallest difference before that number. 
`7 3 5 6 2` `7 3 6 2 5`

The book does it really well with some algorithms that I haven't really used before: I think I will need to go through them at some point: 
```
vector<int> NextPermutation(vector<int> perm)
{ 
	// finding the first entry from the right that is smaller than the entry immediately after it
	// This is a realy nice way of doing it
	auto inversion_point = is_sorted_until(rbeing(perm), rend(perm));

	// it's all decreasing (from left to right)
	if(inversion_point == rend(perm))
		return {};

	// then we are swapping the inversion point number with the smallest element greater than it, after the inversion point
	// Since the perm is sorted after that inversion point, this can be done rather easily
	auto least_upper_bound = upper_bound(rbeing(perm), inversion_point, *inversion_point);
	// Then we have the pointer to that number that comes immediately after the first in value
	
	// Didn't know this was a thing either
	iter_swap(inversion_point, least_upper_bound);
	
	// Then reverse afterwards
	reverse(rbegin(perm), inversion_point);
}
```

`std::upper_bound(begin(A), end(A), value)` Here we will be return the point at which the element that is immediately after value, is found. Say with `7 3 8 9` will return the point of 8. 

God damn this is a great answer to this. 

It does take some thinking through. 
Using algorithms. 

Find the thing that we have to do, see if there are algorithms to do it, that will obviously be better than our own. 
And then write th thing. 


#### ABSOLUTELY DO NOT STARTING BY OPTIMIZING FOR TIME OR SPACE
#### WORK THROUGH IT WITHOUT WORRY
### THEN OPTIMIZE
Another thing that we have to think about, from the `Writing Efficient Programs` book, is what is the context in which the function is going to be used. 
Ie. if we are going to inject lots of elements into a data structure. 


Algorithms that we use are usually highly optimized anyways. 
#### Sample Offline Data
The need to randomly sample a subset of customers in order to roll out new features to. 

Write an algorithm that takes as input an array of distinct elements and a size, and returns a subset of the given size of the array elements. 
All subsets should be equally as likely. 
I think what it's trying to get at is that if we go through the array already. Say that we want to create subset of 5 from 10. 
Then every element could have a `n / k` chance of being picked, however, this will select more of less than 5. 
There are $\binom{n}{k}$ subsets (where orientation does matter, subsets, consisting of the same elements, are the same, regardless of their orientation).
"Enumerating all subsets of `k`" means listing all possible subsets of a given size `k` from a larger set. 
`S = {a, b, c, d}`, enumerating all subsets of size `k = 2`. 
`{a, b}, {a, c}, {a, d}, {b, c}, {b, d}, {c, d}`

The book is talking about building a subset of `[k - 1]`. 

```
void RandomSampling(int k, vector<int>& A)
{ 
	default_random_engine seed((random_device())()); // ranodm number geenrator
	for(int i = 0; 1 < k; ++i)
	{ 
		// generate a random number index in [i, size(A)-1]
		swap(A[i], A[uniform_int_distribution<int>{i , static_cast<int>(A.size() - 1)}(seed)]);
	}
}
```

They use `default_random_engines` here, and then very nicely the `uniform_int_distribution`


#### Sample Online Data
Design a func that takes an input of size k, and reads packets, continously maintaining a uniform random subset of size k of the read packets. 
So we read in `n` packets and have to get a subset of `k` packets. 
Struggling to understand this one: 
Say that we have k = 2 and the packets that we are reading in are `p q r s t u v`. 
So we get the first two packets `{p, q}`. Then, to see if `r` is selected, that will be 2 / 3. 1/ 3 + 1/ 3. 
To see if we select `t`, that will be 2 / 4. 
Then if we select the next, that will be 2/ 5 chance. 
It's like if we had all the packets at once, and then were looking at the probability that we are picking them as we go along. 

One assumption that I suppose we are making is that there are `k` elements in the stream that we are receiving. 

```
// assumption that there are at least k elements in the stream 
vector<int> OnlineRandomSample(vector<int>::const_iterator stream_begin, 
	vector<int>::const_iterator stream_end, 
	int k)
{ 
	vector<int> running_sample;

	// storesult the first k elements
	for(int i = 0; i < k; ++i)
		running_sample.emplace_back(*stream_begin++);

	default_random_engine seed((random._device())());
	int num_seen_so_far = k;
	while(stream_begin != stream_end)
	{ 
		int x = *stream_begin++;
		++num_seen_so_far;
		// generate a random num in [0, num_seen_so_far - 1]
		// if this number is [0, k-1], we replace sample with x
		if(const int idx_to_replace =
		uniform_int_distributions<int>{0, num_seen_so_far - 1}(seed); 
			idx_to_reaplce < k)
			{ 
				running_sample[idx_to_replace] = x;
			}
	}
	return running_sample;
}
```
To note that each subset are not totally independent of each other, in that for each iterate, the subset will only differ, at maximum, by one element. 

### Compute a Random Permutation
We know that permutation of n = 3 is 3! = 6. 

Then we are looking at swapping, for a1, we have three choices, then a2 we have three choices, then a3 we have 3 choices, meaning we have 27 choices. Assuming that elements can swap with themselves. 

27 / 6, therefore, each permutation is not equally as likely. 

Therefore, we want to design an algorithm that creates uniformly random permutations of {0 , 1, ..., n - 1}. 
We can use random number generators that return integers within the same set. 
However, we want to use as few calls as possible. 

Here's a way of thinking about it, say that we have `n = 4`, we could go through and pick a random number that is in the set {0 , 1, 2, 3}, and then if the number has repeated (lookup using a hash table) (remember, if we are going to search, then we might as well use a lookup table), then we won't add it, this means that each number is essentially given an equal chance. 
Then the logical conclusion is that upon finding a number, we just reduce the set that the random generator can pick from. 
```
vector<int> ComputeRandomPermutation(int n)
{ 
	vector<int> permutation(n);
	// initialize to standard increasing 
	iota(beign(permutation), end(permutation), 0);
	default_random_engine((random_device())());
	for(int i = 0; i < n - 1; ++i) 
		swap(permutation[i], permutation[uniform_int_distribution<int>{ 
			i , static_cast<int>(n - 1)(seed)}]);
}
```

The general idea again is that we are A[i] for a random integer that is between A[i] (inclusive) and the end, then we move up by one. 
If all the elements have an equal chance of being picked in that subset, then I think the whole thing has an equal chance to be picked. 


#### Compute A Random Subset
Remember the binomial, $\binom{n}{k} = n! / ((n - k)!k!)$ , this is for choice. 
For something such as A B C D E, we have choices, there are 5! permutations of this. 
Write a program to take in `n`and `k` where `k <= n`, returning a subset of size k. 

This is super similar to the others that we have written before. 

My main thing  is that we pick at random into the array, and then we need to check that we have already added that element. Not sure if that is how to do it, however, that seems to be the right way. 
Ordered maps use a balanced binary tree:
Remember that they are implemented using a balanced binary tree, typically a `RED-BLACK` tree. This is so that we can binary search the tree, and then we are able to search for something in a map: this means that the search time complexity is `log n`. 
The insert is `log n` as you have to go to the depths. 
The delete is obviously `log n` as well. 
The red black tree is self balancing? 
[[Red Black Binary Tree]]

An unordered map, however, uses hashing, so finding a key on a average takes constant time, the time it takes to hash. 
The worst case time complexity `O(n)`, I'm assuming that there is a lot of collisions here. 
The average lookup case here is better than the map. 

Using an unordered map here will really benefit, if k is way smaller than n, then we are saving a whole bunch of time, than going over the whole set. 

The idea being that our map `H` is empty at the start, and then we move through `k` iterations. 
Then we choose a random number between r in `[0, n - 1 - i]`, 

Supposing that n = 100, k = 4. Supposing we get the random number = 28. 
We update H to (0, 28) and (28, 0). 
This means tat A[0] is 28, and therefore A[28] = 0. 
Then let's say that we get 42, then we have (1, 42) and (42, 1) in H. 
Imagine that we get 28 again,then we have (0, 28) (28, 2) (1, 42) (42, 1) (2, 0).
If we then get 64, then we have (64, 3) (3, 64). 

The random subset is the 4 elements corresponding to the indices 0, 1, 2, 3 ie. <28, 42, 0, 64>. 

```
// returning a random k sized subset of {0, 1, ..., n - 1};
vector<int> RandomSubset(int n, int k)
{ 
	unordered_map<int, int> changed_elements;
	default_random_engine seed((random_device())()); // Random num gen
	for(int i = 0; i < k; ++i)
	{
		// generate random index in [i, n - 1]. 
		int rand_idx = uniform_int_distribution<int>{i , n-1}(seed);
		if(auto ptr1 = changed_elements.find(rand_idx), 
			ptr2 = changed_elements.find(i);
			ptr1 == end(changed_elements) && ptr2 == end(changed_elements))
		{ 
			changed_elements[rand_idx] = i;
			changed_elements[i] = rand_idx
		} else if(ptr1 == end(changed_elements) && 
			ptr2 != end(changed_elements))
		{ 
			changed_elements[random_idx] = ptr2 -> second;
			ptr2->second = rand_idx;
		} else if(ptr1 != end(changed_elements) && 
			ptr2 == end(changed_elements))
		{ 
			changed_elements[i] = ptr1->second;
			ptr1->second = i;
		} else 
		{ 
			int temp = ptr2->second;
			changed_elements[i] = ptr1->second;
			changed_elements[rand_idx] = temp;
		}
	}
	vector<int> result;
	for(int i = 0; i < k; ++i)
	{ 
		result.emplace_back(changed_elements[i]);
	}
	return result;
}
```

The idea here is that we are building the subset within the hash map, that means that we can swap elements, without actually swapping elements. 
The reason that we have to go through all the trouble is that otherwise there are not equal opportunities for every subset. 

### Generate NonUniform Random Numbers
Given a set of numbers, and their probabilities, if we were to call out function 1000 times, we would see that ratio given in the numbers called. 
The idea being that the numbers don't matter themselves, however, that we are picking them, in a nonuniform fashion is very important. 

To note, that if all the probabilities are the same, then we just make a call to a random number generator, for i between 0 and the size of the set of numbers, then return the number that is found at that index. 

The way that I felt was really obvious, was to create  range for each probability, say a probability is 1/19, then the range here is that we create a number between [0, 1) then we see if the generated number is between that range or not. 1/19 is 0.0526, so anything between [0, 0.0526) will give us that number. 
Say the next number will be 5/19 then we say that the generated number will have to be between [1/ 19, 6/19), this is just fairly obvious, makes us look really cool. Then the last will be whatever the sum of the others is, to one, [n/19, 1]. where n/19 signifies a summation of all the other probabilities up until the last. 
Note that whenever we search something, for instance, searching an array of n disjoint intervals, takes `O(n)`, however, are they going to be ordered, well yes of course, therefore we can do it in `O(log n)` , the time complexity of searching using a binary tree. 

```
int NonUniformRandomGeneration(vector<int> const& values, 
			vector<double> const& probabilities)
{ 
	vector<double> prefix_sum_probabilities;
	// Creating the endpoints for the intervals corresponding to the probabilities
	partial_sum(cbegin(probabilities), cend(probabilities), back_inserter(prefix_sum_probabilities));

	default_random_engine seed((random_device())());

	const double uniform_0_1 = generate_canonical<double, numeric_limits<double>::digits>(seed); // generates a random number [0, 1)
	// the numeric_limits thing is to make sure that we have the right precision
}
```


Quite note on **Partial sum** algorithm: 
Within `<numeric>. 
```
vector vi{1, 2, 3, 4, 5};
vector<int> result(vi.size());

std::partial_sum(begin(vi), end(vi), begin(result));
printoutCont(result); // 1 3 6 10 15


// using a custom binary operator not just the sum
std::partial_sum(begin(vi), end(vi), begin(result), std::multiplies<>());
printoutCont(result)l; // 1 2 6 24 120
```
As you can see, that as we go through vi, we are accumulating and adding to `result`, depending on the element of that which we are iterating over. 
The reason that we use `back_inserter()` above is that if we don't initialize `prefix_sum_probabilities` with a size, then we have no way to `push_back` as the algorithm does not do that for us, it will just write into an index. 
Therefore if we get the `back_inserter` then writing into the index will give us the `push_back` insertion that we need here. 
You can alternatively create eh size of the array as we initialize it. 
This means that the algorithm can handle size dynamically, rather than having to statically create the size of the array before hand, here the size won't matter, however, if we continually add to the array, then using `back_inserter` will allow us to the whole time. 

