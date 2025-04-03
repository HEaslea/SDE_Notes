## Data Compression

Frequency of symbols, common in ZIP and JPEG. 

Variable length binary codes to input characters, shorter codes assigned to more frequent characters. 

Minimizing the total number of bits that will need to be present. 

#### Steps for the Algorithm
1. Frequency calc - count the frequency of each char in the input data. 
2. Build a min heap : create prio queue (min heap) of nodes where each contains a char and its frequency (apparently this is a bottom up). 
3. Build the HuffMan Tree
- Remove two nodes with lowest frequency from the heap. 
- Create a new node with those two as children, with its frequency being the sum of the two. 
- Repeat until a single node remains (the root of the Huffman tree). 
4. Generate the Huffman Codes 
- Traverse the tree, assigning `0` for left and `1` for right. 
- Store the codes in the dictionary
5. Encode Data: replace each char in the input with its corresponding Huffman Code. 
6. Decode Data; then just reconstruct and decode the tree to get to the original text. 

### C++ Implementation
```
struct Node
{ 
	char ch; 
	int freq;

	// Tree in Nature
	Node* left, *right;

	Node(char ch, int freq, Node* left = nullptr, Node* right = nullptr)
		: ch(ch), freq(freq), left(left), right(right) {}
};


// using a functor for comparison
struct Compare
{ 
	bool operator()(Node* a, Node* b)
	{ 
		return a->freq > b->freq; // For implementation of Min Heap
	}
};

// Function to generate Huffman Codes
void generateCodes(Node* root, string code, unordered_map<char, string>& huffmanCode)
{ 
	if(!root) return;

	if(!root->left && !root->right)  // if we are a leaf node
	{ 
		huffmanCode[root->ch] = code;
	}
	
	generateCode(root->left, code + "0", huffmanCode);
	generateCode(root->right, code + "1", huffmanCode);
}

// Huffman Encoding Function 
void huffmanEncoding(const string& text)
{ 
	unordered_map<char, int> freq;
	for(char ch : text) freq[ch]++; // basic frequency map

	// pq using the compare functor
	// functor must have a () operator
	priority_queue<Node*, vector<Node*>, Compare> pq;
	
	while(pq.size() > 1)
	{ 
		Node* left = pq.top(); pq.pop();
		Node* right = pq.top(); pq.pop();
		pq.push(new Node('/0', left->freq + right->freq, left, right));
	}
	
	Node* root = pq.top();
	unordered_map<char, string> huffmanCode; 
	generateCodes(root, "", huffmanCode);

	cout << "Huffman Codes: \n";

	for(auto pair : huffmanCode)
	{ 
		cout << pair.first << " : " << pair.second << endl;
	}

	string encoded = "";
	for(char ch : text)
	{ 
		encoded += huffmanCode[ch];
	}

	cout << "Encoded Text: " << encoded << endl;
}

int main() 
{ 
	string text = "Huffman Coding Example";
	huffmanEncoding(text);
}
```

#### The Node
For the tree, we just need some nodes, binary tree, so that we can assign more easily to `0` and `1`. 

```
struct Node 
{ 
	char ch;
	int freq;
	Node* left, *right;

	Node(char ch, int freq, Node* left = nullptr, Node* right = nullptr)
	: ch(ch), freq(freq), left(left), right(right) {}
};
```


#### Char Frequencies
We have to calculate the char frequencies. 
```
unordered_map<char, int> freq;
for(char ch : text) freq[ch]++;
```

#### Building the Huffman Tree
```
priority_queue<Node*, vector<Node*>, Compare> pq;
for(auto pair : freq) // for the pair in our frequency map
	pq.push(new Node(pair.first, pair.second));
```

Now we have a min-heap of Nodes, that are carrying chars and their frequencies, we will have the least frequent node at the top. 

Now, we remove the two smallest nodes from the heap, combine them, and put them back in. 

```
while(pq.size() > 1)
{ 
	Node* left =  pq.top(); pq.pop();
	Node* right =  pq.top(); pq.pop();
	
	pq.push(new Node('\0', left->freq + right->freq, left, right));
}
```

Removing the two smallest nodes,  combining them, inserting them back into the heap. 
The final node left in the root of the Huffman Tree is going to be the root node. 

### Generate Huffman Codes
Recursively assigns codes: 
- left gets `0`
- right gets `1`
Then we get a dictionary of characters to Huffman Codes

```
// recursively doing this
void generateCodes(Node* root, string code, unodered_map<char, string>& huffmanCode)
{ 
	if(!root) return;

	if(!root->left && !root->right) // if 
		huffmanCode[root->ch] = code;

	generateCodes(root->left, code + "0", huffmanCode);
	generateCodes(root->right, code + "1", huffmanCode);
}
```

Then we just write out the text using this `dictionary` `unordered_map<char, string>`

### The Gist and Good Ideas to Take Forward
Build a frequency table
Construct a Huffman Tree
Assign Binary Code to the Chars
Encode the Text

The main idea being, the less frequent chars will have the longest code. 

The priority queue which is a `min-heap`, meaning that the least frequent chars will be the first ones in the queue. 
We add in to the priority queue. 
Say that we have `"aaaabbc"`, then the char frequencies: 
```
a -> 4
b -> 2
c -> 1
```
The min heap, will therefore look like this: 
`(c, 1) (b, 2) (a, 4)`

(most sorts have `O(n log n))`

### Priority_Queue
Uses a `max_heap` by default, then we just modify to make it use a `min_heap`. 
That's with our custom comparator. 
They are typically implemented with a `binary tree`, stored as an array. 


Remember the whole idea of: 
- Left Child `2 * i + 1` // if we start at index 0
- Right Child `2 * i + 2` // if we start at index 0
- Parent: inverse function: `(i - 1) / 2`

#### Building the Huffman Tree
Extracting the two smallest nodes. They will be replaced, by the implementation of the `priority_queue`
Creating a new node by merging them
Then just insert that new node back into the heap

Eventually we get down to a size of 1: 
```
while (pq.size() > 1)
{ 
	Node* left = pq.top(); pq.pop();
	Node* right = pq.top(); pq.pop();

	pq.push(new Node('/0', left->freq + right->freq, left, right));
}
```

In this case:
 we extract the two `(c, 1) and (b, 2)`. 
Creating a new internal node
`(\0, 3)` the Parent node of `c` and `b`. 

Now the new min heap looks like `(a, 4) (\0, 3)`
Then we just extract those two, then we make a parent for those two: 
We get  `(/0, 7)` Which is now the root node. 

We get something like this: 
```
       (\0, 7)
      /   \
    (\0, 3)   (a, 4)
   /   \
 (c,1) (b,2)
```

The most frequent nodes will always be at the top. 

In order to get the head, that will be the last node in the priority queue. 

`while(pq.size() > 1)`

### We have the Tree, let's Generate
Generating the tree. 

No we have to assign binary codes, for every char, with the more frequent chars, having smaller codes. 

Then we have to do a recursive traversal of the tree: 
```
void generateCode(Node* root, string code, unordered_map<char, string>& huffmanCode)
{ 
	if(!root) return; // base case

	if(!root->left && !root->right) // if leaf, then store the code
	{ 
		huffmanCode[root->ch] = code;
	}

	// go left, with adding 0
	generateCodes(root->left, code + "0", huffmanCode);

	// go right, with adding 1
	generateCodes(root->rigth, code + "1", huffmanCode);
}
```

Then decoding would be a binary search in order to find the corresponding right code point. 



### Huffman Rememberall
- frequency map
- min-heap of frequency
- extract 2, create parent of that, push that new parent into the heap
- last node will be the root
- recursively traverse the tree just made, go left with added code of 0, go right with 1
- if leaf, then add that code to a dictionary. 


