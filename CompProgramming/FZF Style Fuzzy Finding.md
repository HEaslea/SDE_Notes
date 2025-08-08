In order to fuzzy match a query against file names. 
Then we will need the idea of a smart score. 

[Some Lib that Uses FzF](https://github.com/junegunn/fzf)

Fzf  - fuzzy finder. 

## The Essentials 
The main idea is that of subsequence matching. 
We are not trying to find the closest spelling, like Levenshtein does. 

We are looking to see if the order of characters appearing matches. 

Consider the idea that `rdme` should match `README.md`. 

All the letters appear in the right order, therefore we have a match. 

Subsequence check, which is really simple works, however, some matches are not equally as good. 

`rdme` is very close to `README.md` however, not as close to `roadmap_endgame.txt`. 

Therefore we need some sort of scoring model. 

Walk through the candidate string from left to right, matching each pattern in turn. 
For every match, we add a base score and then adjust it: 

Then it becomes a scoring calculator, which is more boring, but still a little challenge.

Bonuses: 
- Consecutive Letters (no gaps)
- Start of the word, or after space, slash, dash, underscore
- Start of string (at the beginning)
- CamelCase bump (not sure about this one)
- Exact-case Match (minor bonus if the cases match exactly)

Penalties: 
- Gaps (letters are far apart)
- Late first match (patterns starts matching too far in the string)(depreciating score)

You can even have path-aware ranking, this could be a tie breaker. 

You could prefer shorter names if they are equal. 

### The Steps 
There is a precompute stage, where we look at "word boundaries" in the candidate (start of the word/camel). 

Walk through the candidate chars, trying to match the next pattern char. 

When the match occurs : add base score, subtract gap penalty, add any applicable bonuses. 

If the whole pattern is matching : add a finishing bonus (earlier finish, shorter string). 

If not fully matched then the score = very bad. 

Sort all the candidates by score, then by secondary tie-breakers if needed. 

Works really well for a thousand files, if there were millions, then we would look at adding a prefilter. 

### Bare-bones Subsequence Matcher
Just answers the idea of "does pattern appear in order inside text" and gives a score (= number of matched chars). 

Did the [Is Subsequence Leetcode](https://leetcode.com/problems/is-subsequence/) in one go, so this is pretty straightfoward.

```
def isSub(pattern : str, text : str): 
	"""Return (matched : bool, positions : list[int]). Case-insensitive."""
	if not pattern: # if there is no pattern to match here
		return True, [] # will match with everything, yet there are no indexes that do match
	pi = 0 # starting index
	pos = [] # the indexes that match
	
	# The case-insensitive partc
	p = pattern.lower()
	t = text.lower()
	for i, ch in enumerate(t): # the index and the char in the actual text
		if pi < len(p) and ch == p[pi]: 
			pos.append(i)
			pi += 1 # move the necessary pointer
			if pi == len(p):  # at the ened of the candidate
				return True, pos
	return False, [] # if we don't have all the matching letters then return false
```
This is a very either or way of doing it, if it doesn't contain all the candidate chars. 

Then we just move ahead with this, given that we have list of the indexes where they are matching. 

This is what we are going to do going forward, use that `is_sub` method and then score based on what we have in there. 

It's just wrapping around that with a scorer. 

Let's create a scoring function here. 

Here is an idea given: However, we have to remember that the implementation here just changes how close a match a `reg` (search term) is.

```
SEPS = set("/-_.,:")

def fuzzy_score(pattern : str, text : str):
    print("Going in")
    matched, pos = is_sub(pattern, text)
    # if there's nothing matching, then that's a terrible score right   
    if not matched: # so false from is_sub
        return False, -10**9, [] # huge negative score and no oeverlaps
    
    score = 0 # start from 0
    # start-of-word map (beginning, after separator, camelCase bump)
    bounds = [False]*len(text) # list of False, size of text
    for i, ch in enumerate(text):  # go through the test letter by letter, index 0
        prev = text[i-1] if i else "" # "" if there is no previous char 
        # True if at the beginning
        bounds[i] = (i == 0) or(prev in SEPS) or (prev.islower() and ch.isupper())
        #"hello_thEre" gives [True, False, False, False, False, False, True, False, True, False, False]

    last = None
    # then we go through the indexes where we have a match, from is_sub
    for k, i in enumerate(pos): # go through the indexes where there were matching chars
        s = 10 # base score for every matched char
        if last is not None: 
            # consecutive bonus, if last is a score then consecutive bonus here
            if i == last + i: 
                s += 6
            else: 
                # light gap penalty
                s -= min(3, (i - last - 1)//2)

        if bounds[i]: # if this is one of the prementioned things earlier
            s += 8 # start of word or camel bump gets a slightly lower score than being a char
        score += s
        last = i # where i is in the index of the char found in the word
    
    # then some finishing touches adding to the score based on criteria
    first = pos[0] # the first occurence 
    score += max(0, 6 - first) # earlier first hit, of the first hit occurrence
    score += max(0, 6 - (len(text) - pos[-1])) # again preferring shorter strings
    score += max(0, 5 - len(text)//20) # preferring short strings
    return True, score, pos

```

The score really is the main thing here, and we use `True, False` at the beginning here to filter very quickly. 

Then you can add path aware ranking first. This might not be necessary given the circumstances. 

We will see if we need this and then go into further detail, however, due to the paths in obsidian all being very very short, I do not think this is necessary. 

Then if we want to get the top `n`, that being the max score then we need to have a min heap, where we only have `n` things on the heap. 

If the new score that we get is greater than the smallest in the heap (the nth smallest) then we add it to the min heap, by popping and adding the new one. 

The C++ is pretty tame:  with a couple of functions at the start that are interesting
```
// static is not necessary 
static inline char lowerc(char c)
{ 
	return (char)std::tolower((unsigned char)c);
}

static inline bool is_sep(char c)
{ 
	switch (c)
	{ 
		case ' ': 
		case '/':
		case '\\':
		case '-':
		case '_':
		case '.':
		case ',':
		case ':': return true;
	}
	return false;
}

// Bare bones subsequence matcher

std::pair<bool, std::vector<int>>
is_sub(const std::string& pattern, const std::string& text)
{ 
	std::vector<int> pos;

	if(pattern.empty()) return {true, pos};
	// get the start of the pattern
	int pI = 0; 
	// then go through the text
	for(int i = 0; i < text.size(); ++i)
	{ 
		 if(lowerc(text[i]) == lowerc(pattern[pI]))
		 { 
			 pos.push_back(i);
			 if(++pi == patter.size()) return {true, pos}; // got to edge of the pattern
		 }
	}
	return {false, {}}; // we get to the end of the text without getting to end of pattern
}

// scoring based on indexes of found chars
std::tuple<bool, int, std::vector<int>>
fuzzy_score(const std::string& pattern, const std::string& text)
{ 
	// structured bindings out
	auto [matched, pos] = is_subsequence; 
	if(!matched) return{false, INT_MIN, {}};

	// precomputer word/camel boundaries
	const int n = text.size();
	std::vector<bool> bound(n, false);
	for(int i = 0; i < n; ++i)
	{ 
		char prev = (i ? text[i-1] : '\0');
		char cur = text[i];
		bound[i] = (i == 0) || is_sep(prev) || (std::islower((unsigned char)prev) && std::isupper((unsigned char)cur));
	}

	int score = 0; 
	int last = -1; // as there is no last yet
	for(int k = 0; k < pos.size(); ++k)
	{ 
		int i = pos[k]; 
		int s = 10; 
		if(last >= 0)
		{ 
			if(i == last + 1)	 s += 6;
			else                 s -= std::min(3, (i - alst - 1) / 2); // small gap penalty
		}
		if(bound[i]) S+= 8; // if this is some of the predicates that we had earlier. 
		if(text[i] == pattern[k]) s += 1;
		
		score += s;  // add to score
		last = i; // has last update as well
	}
	
	// finishing bonuses to score
	score += std::max(0, 6 - pos.front()); // earlier first hit gets points
	score += std::max(0, 6 - (n - pos.back())); // earlier finish (smaller number as last index)
	score += 5 - (int)(n / 20); // shorter string is favoured
	return {true, score, pos};
}
```

