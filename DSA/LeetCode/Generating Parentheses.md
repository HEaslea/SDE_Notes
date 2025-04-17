This is a play on the idea of checking valid parentheses and that one is fairly simple. 

#### Check Valid Parentheses
There are a couple of ways of doing this: 
The first is very simple, we count the number of `open` that is `(` and then `)`, `close`. 
Then we do a simple check per char, if `close > open` there were too many closing parens, therefore, it's not valid: 
```
bool checkValidParen(std::string& s)
{ 
	int open = 0, close = 0; 

	for(char c : s)
	{ 
		if(c == '(')
			open++;
		else
			close++;
			
		if(close > open)
			return false;
	}
	return (open==close);
}
```

`open==close` is obvious, they always have to finish as the same. 

(disclaimer this was written very quickly so it might not work at all, passed all the checks that I had). 

This is super simple : and works, very little memory used and super efficient

There is a slightly different version of this, where there is a universal symbol `*` that can act as either or nothing, very cool: 
[Valid Parenthesis String](https://leetcode.com/problems/valid-parenthesis-string/)

However, back to the task at hand. 

There is a more important tactic, slightly more complex for this, but much more useful for other tasks that are very similar: 

Think about having to build one of these test cases that would be valid : 
- We could brute force a bunch of strings - then see what is valid and take a pool of the valid ones etc. 

This is almost like bisectioning, we just keep going and then we eventually get what we need. 
There is no finesse to this, we just build and see if valid, if so then take. 

But what if we want to build something that is valid from the ground up. 

Recursion makes the most sense (at least to me) - in that there are only two options when building: 
1. Add a `(`
2. Add a `)`
Just recurse on those two options. 

The iterative option is always a tough one for me, because we need to start thinking that we need a place to store the "current", which is easy in recursion, as the compiler takes care of that in the frame on the stack for us. 
But where do we keep it without recursion. 

Here's the answer: `stack` and `queue`, that's something we should be thinking about a possible solution always. 

Here we can use a `stack` to see if the `string` is valid. 

```
bool checkValidParen(std::string& s)
{ 
	std::stack<char> st; // here's our stack, representing all the opens
	
	for(char c : s)
	{ 
		if (c == '(' ) st.push(c); // push opening onto the stack
		else if (c == ')' ) // if closing, no stack add, take from 
		{ 
			if(st.empty()) return false;
			st.pop() // take away one opening from the stack
		}
	}
	return st.empty(); // if not empty, then not same number of (  and  )
}
```

Short and sweet and is a more finessed form of what we made earlier. 
The thing with a `stack` and `queue`, when we are putting in the same object over and over, it really becomes a more abstracted counter. 

### Generating Parentheses
This is the meat and potatoes. 
Think about the recursion of this: when we pass in`current + "("` or `current + ")"` -> we feel that there is a place where that is "saved" -> however, something I struggle with is that thinking about iteratively doing it -> where do we "save" these -> `stack` and `queue` is the answer. 

Remembering this: 
- `stack` - DFS - taking the just processed "NODE" or "MODEL". LIFO.
- `queue` - BFS - taking the "NODE" or "MODE" that was processed first. FIFO.

NODE - Graph-like
MODEL - any construction that we are working with eg. "()()(" is a current construction

We're going to use a queue for BFS. 
Here's the code and then just talk through it: 

```
std::vector<std::string> generateParen(int n){  // n being the number of openings
	std::vector<std::string> toRet; 
	std::queue<std::tuple<std::string, open, close>> q; // or std::queue<Node>
	// this is almost identical to what we pass into the recursion
	
	q.push({"", 0, 0});

	while(!q.empty()) // this comes up a lot, especially in Djikstra's (duh)
	{ 
		// using Structured Binding with the tuple
		auto [current, open, close] = q.front();
		q.pop() // this comes up a lot as well, front then pop
		
		if(current.size == n * 2) // the perfect size
		{ 
			result.push_back(current);
			continue; // the rest is unnecessary
		}

		if(open < n) // if we can add an open, and push that new one
			q.push({current + "(", open + 1, close});

		if(close < open)
			q.push({current + ")", open, close + 1});
	}
	return toRet;
}
```

Very very cool indeed. 

When we have a new MODEL/NODE, then we push that onto `queue`, with it's attributes as well. 
This is why a NODE: 
```
struct Node
{ 
	std::string current;
	int open;
	int close;
};
```

Or using a tuple and some structured bindings. 

The key takeaway here is that when we iterate through a solution that requires some building, we save those `current`'s somewhere : in an `array`, `queue` or `stack` depending on if we need to save them permanently, or if we want to go through BFS or DFS. 

