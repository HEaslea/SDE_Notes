#classes #classmembersrevisited 
[[Classes Start]]
Here is an example that I just came across: 
The idea is that we have a deck of cards: 
And in the card class I HAD (changed later!): 
```
class Card{ 
	SUIT c_suit;
	CARD c_card;

public: 
	Card(SUIT s, CARD c) : c_suit(c), c_card(c) {}

	SUIT getSuit() { return c_suit; }

	CARD getCard() { return c_card; }
};

int main(){ 
	vector<Card> mockDeck;
	for(int i = CLUB; i <= SPADE; ++i){ 
		for(int j = ACE; i <= KING; ++j){ 
			mockDeck.push_back(Card(static_cast<SUIT>(i), static_cast<CARD>(j)));
		}
	}

	for(const auto& card : mockDeck){  // here is our error
		cout << card.getCard() << ":" << card.getSuit() << " ";
	}
	cout << endl;
}
```

So you can see the line with the error in it. 
We are creating a `const` reference to the card in the `mockDeck`. Therefore our object here is `const`. 
Now if you look at the `Card` class, you'll see that if wee `getSuit()` and `getCard()` we don't need a `const` object, as the functions there are `non_const`. 
In order to fix this, if we put `const` in the functions: 
```
SUIT getSuit() const { return c_suit; }
CARD getCard() const { return c_card; }
```
This means that we can pass a `non_const` object to these functions. 
We cannot pass a `const` object to a `non_const` function. 