#interpreterdesign
A grammatical representation for a language and an interpreter to interpret sentences in that language. 

Useful for parsing and interpreting simple languages or expressions. 

- **Abstract Expression Interface**: An interface for interpreting expressions. 
- **Terminal Expression**: representing leaf nodes in the expression tree (ie. numbers). 
- **Non-Terminal Expression**: this will represent the non-leaf nodes (operations like addition and subtraction). 
- **Context**: Holding input string and the current position being interpreted. 

Remember we're not looking to re-invent, rather to write to make extensible etc. 

We might start with the idea that our Tokens are expressions: 
```
class Context;

class Expression { 
public: 
	virtual ~Expression() = default;
	virtual int interpret(Context& context) = 0;
};
```

Then we can define our terminal expressions: 
```
class NumberExpression : public Expression { 
public: 
	explicit NumberExpression(int vale) : value(value) {}

	int interpret(Context& context) override { 
		return value;
	}
private: 
	int value;
};
```

Our non-terminal expressions, each needs to be defined. 
This is where our language is most greatly defined, let's say that you want bytecode that will create and define wizards. 
The obvious things are the numbers, we might change the fireball of the wizard, the damage might go to 60, therefore we may say 60. 
The number is the number, however, fireball, change all of that needs implementation, not just the 
`return value;`. 

Eg. 
```
class AddExpresion : public Expression { 
public: 
	AddExpression(Expression* left, Expression* right) : left(left), right(right)
	{ }
	~AddExpression() 
	{ 
		delete left;
		delete right;
	}

	int interpret(Context& context) override { 
		return left->interpret(context) + right->interpret(context);
	}
private: 
	Expression * left;
	Expression * right;
};
```

Then our Context, vital to what we are doing might be this: 
```
class Context { 
public: 
	explicit Context(const std::string& input) : input(input) index(0) {}

	char getNextToken() { 
		while(index < input.size() && issapce(input[index])){ 
			++index;
		}
		return index < input.size() ? input[index++] : '\0';
	}

	void skipToken(char token) { 
		if(index < input.size() && input[index] == token)
		{ 
			++index;
		}
	}

	int currentIndex() const { 
		return index;
	}

	char currenToken() const { 
		return index < input.size() ? input[index] : '\0';
	}

	std::string getRemainingInput() const { 
		return index < input.size() ? input.substr(index) : "";
	}
private: 
	std::string input;
	size_t index;
};
```

Here is a puzzle piece, how do we get it all together: Combining expression with contexts.

```
class Interpreter { 
public: 
	int interpret(const std::string& input)
	{ 
		Context context(input);
		std::stack<Expression*> stack;

		char token = context.getNextToken();

		while(token != '\0')
		{ 
			if(isdigit(token))
			stack.push(new NumberExpression(token - '0'));
		} else if (token == '+') 
		{ 
			context.skipToken(token);
			Expression* right = stack.top(); stack.pop(); 
			Expression* left = stack.top(); stack.pop()
			stack.push(new AddExpression(left, right));
		} else if(token == '-')
		{ 
			context.skipToken(token);
			Expression* right = stack.top(); stack.pop(); 
			Expressoin* left = stack.top(); stack.pop();
			stack.push(new SubtractExpression(left, right));
		}
		token = context.getNextToken();
	}
	int result = stack.top()->interpret(context);
	delete stack.top();
	stack.pop(); 
	return result;
	}
};
```

Then in main: 
```
int main() { 
	Interpreter interpreter; 
	std::string expression = "3 4 + 2 -";
	int result = interpret.interpret(expression);
}
```

