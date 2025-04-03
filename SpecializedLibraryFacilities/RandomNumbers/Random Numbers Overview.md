We often need random numbers. 

`<random>` offers the answer, through a set of cooperating classes : 

**Random-Number Engines** and **Random-Number Distribution Classes**

An engine generates a sequence of `unsigned` random nums.
Distribution - uses engine to generate random nums of a specified type, in a given range, distributed according to particular probability distribution. 

#### Random Number Engines and Distribution
The engines are function object classes, defining a call operator, that taking no args, and returning a random `unsigned` nujm. 

```
default_random_engine e;// generates random unsigned ints

for(size_t i = 0; i < 10; ++i)
	// e() producing next random num
	cout << e() << " ";
```
The issue with this is that if we compile 10 times, the numbers will be the same for the ten times. 


![[Pasted image 20240524225606.png]]

Usually getting these into a particularly range early is annoying: 
```
e()%10; // giving us a range of 0-9
e()%10 + 1; // giving us a range 1-10 inclusive
```

What is better than this, is: 
#### Distribution Types and Engines
For a spec'd range, use an object of a distribution type: 

```
// uniformly distribute from 0 to 9 inclusive 
uniform_int_distribution<unsigned> u(0,9);
default_random_engine e;
for(size_t i = 0; i < 10; ++i){ 
	// u uses e as a source for numbers
	// each call returns a uniformly distributed value in the spec'd range
	cout << u(e) << "";
}
```
I can imagine that `u` calls `e()`. 


This code will produce output such as : 
```
0 1 7 4 6 2 9 4 0 6 8 9
```

Remember that the range is [] not (); 
The distribution will use the engine to generate and then to scale that number passed back to the range that we want. 
We don't want to do `u(e())`, otherwise we are just passing a number into it. 

> **Usually when we refer to a random number generator, we are referencing to the engine and the uniform distribution. **

`rand` is similar to this. 

We can look at `min, max` 
`cout << "Min; " << e.min() << "max: " << e.max() << endl;`. 

#### Engines Generate a Sequence of Numbers
Here we are dealing with the idea that the numbers will be same every time that we generate them without providing a new seed. 
Imagine that we need a vector that will have a uniform distribution in the range of 0 to 9. 
```
// almost surely the wrong way to generate this
// output from this function will be the same 100 numbers on every call!
vector<unsigned> bad_randVec(){ 
	default_random_engine e;
	uniform_int_distribution<unsigned> u(0,9); 
	vector<unsigned ret;
	for(size_t i =0; i < 100; ++i)
		ret.push_back(u(e));
	return ret;
}
```

Something interesting that you can do; 
```
vector<unsigned> v1(bad_randVec());
vector<unsigned> v2(bad_randVec());
cout << ((v1 == v2)? "Equal" : "Not Equal") << endl;
```


However, however, however, if we were to do this with a function, then we can realise that our engines actually retain state: 
```
vector<unsigned> good_randVec()
{ 
	static default_random_engine e;
	static uniform_int_distribution<unsigned> u(0,9);
	vector<unsigned> ret;
	for(size_t i = 0; i < 100; ++i){ 
		ret.push_back(u(e))
	}
	return ret;
}
```


There is also the idea of seeding a generator: 
#### Seeding a Generator
Producing the same sequence of numbers is really great until we we want to come out of testing. 
Providing a seed. 
A value that an engine can use to cause it to start generating numbers at a new point in its sequence. 

```
default_random_engine e1; // using default seed
default_random_engine e2(214747584);// use the given seed value
// if they have the same seed, thne they wll generate the same number 
```

The best way to do it, is using the time function. 

`<ctime>`  time will return a given time from the given epoch. 
`default_random_engine e1(time(0));`
Somewhat a random seed. 
> **The time passed between using `time(0)` is really important, as here, time uses the seconds passing, therefore, if you're seeding very quickly, then it will be the same seed**

Seeding this quickly next to each other will create the same seed
```
using namespace::chrono_literals; // so can use ms
default_random_engine e(time(0));
std::this_thread::sleep_for(1000ms);
default_random_engine e2(time(0));
cout << e() << "     " << e2() << endl;
```
This will create two different numbers: If the sleep is < 1000 by too much, then they will produce the same number. 


#### Other Kinds of Distribution
The number produced are `unsigned` numbers, and they are all just as likely to be generated. 
![[Pasted image 20240525005642.png]]
##### Generating Random real Numbers
We can define an object of type `uniform_real_distribution`. 
```
defualt_random_engine e; // generates unsigned random integers

// uniformly distributed from 0 to 1 inclusive
uniform_real_distribution<double> u(0,1);
for(size_t i = 0; i < 10; ++i){ 
	cout << u(e) << " ";
}
```
#### Generating Numbers that are not Uniformly Distributed
The lib defines 20 distribution types. 
Some of them will use `lround` function from `cmath` in order to round to the nearest. 
If we want to generate 200 numbers centered around a mean of 4, with a standard deviation of 1.5. 
```
default_random_engine e; // generates random integers
normal_distribution<> n(4, 1.5);
vector<unsigned> vals(9); // 9 elements of 0
for(size_t i = 0; i != 200; ++i)
{ 
	unsigned v = lround(n(e)); // round to the nearest integer
	if(v < vals.size()) // only because there is a small chance that we can create a val greater than
		++vals[v];
}
for(size_t j = 0; j != vals.size(); ++j)
{ 
	cout << j << ":" << string(vals[j], '*') << endl;
}
```

If there were always symmetry around the mean here then we wouldn't really have a distribution. 

#### The bernouli_distribution Class 
This one is not a template, it's just a normal class. 
It will return a `bool` value. 
It returns `true` with a given probability, by default it is `0.5`. 
Say we want to decide who is to go first, we have a range of 0 to 1 to select the first player. 

```
string resp; 
default_random_engine e; // e has state, so it must be outside the loop
bernoulli_distribution b; // 50 / 50 odds by default
do { 
	bool first = b(e); // if true, the program will go first
	cout << (first? "We go first" : "You get to go first") << endl;

// play the game passing the indicator as to who goes first
cout << (play(first))? "Sorry, you lost" : "Congrats you won!") << endl;
cout << "Play again? Enter 'yes' or 'no'" << endl;
} while (cin >> resp && resp[0] == 'y');
```
We need to declare engines outside the loop, so they aren't defined in the loop, and you get the same number over and over again. 

`bernoulli_distribution b(.55); // give the house a slight edge`. 
If we use this definition for `b`, then the program has `55/45` odds of going first. 


I just wanna get this somewhere into my code: 
```
int arr[] = {0,1,2,3};
cout << 0[arr] << endl;
```