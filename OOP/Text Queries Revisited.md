Remember the text query that we had ages ago, we'll add some inheritance to it. 

We'll be running queries based on this text here: 

```
Alice Emma has long flowing red hair.
Her Daddy says when the wind blows
through her hair, it looks almost alive,
like a fiery bird in flight.
A beautiful fiery bird, he tells her,
magical but untamed.
"Daddy, shush, there is no such thing,"
she tells him, at the same time wanting
him to tell her more.
Shyly, she asks, "I mean, Daddy, is there?"
```

We want to support the following queries: 
- Word queries find all the lines that match a given `string`: 
> **Executing Query for: 
> Daddy    Daddy occurs 3 times
> (line 2) Her Daddy says when the wind blows
> (line 7) "Daddy", shush, there is no such thing,"
> (line 10) Shyly, she asks, "I mean, Daddy, is there?"**

- Not queries, using the ~operator, yield lines that don't match the query: ie. printing lines that don't contain the `Word` so all lines with `~Word`. 
- Or queries, using the `|` operator, return lines matching either of two 
- And queries, using the `&` operator, return lines matching both queries
- and then we want to be able to combine the two operations `fiery & bird | wind`


We want to use normal C++ precedence rules to evaluate. 

#### An Object-Orientated Solution
It says something about something about something not working with the way that we did it before. 

The best way of doing it apparently, is to model our different kind of queries as independent classes that shared a common base class: 
```
WordQuery // Daddy
NotQuery // ~Alice
OrQuery // hair | Alice (case sensitive?)
AndQuery // hair & Alice 
```
These classes will only have two operations: 
- `eval` taking a `TextQuery` object and returns a `QueryResult`
- `rep`, returning the `string` representation of the underlying query. 


### Abstract Base Class
#abstractbase
These four query types are not related vis inheritance ; they are conceptually siblings. 
Each class shares the same interface, they'll all be inheriting from an abstract class. 
Our abstract class will be `Query_base`.

`Query_base` will define `eval` and `rep` as **pure virtual functions**. 
Each class **has to override** these functions. 
We'll lump together `AndQuery` and `OrQuery`, and we'll say that they are linked, and will therefore inherit from `BinaryQuery`. 


![[Pasted image 20240510131629.png]]
> When we define a class as publicly inherited from another, the derived class should reflect an "Is a" relationship to the base class. We can use the derived class object whenever we need an object of the base class, due to the subobjects in the derived objects (that represent the base class). 
> There is also a "Has a" relationship. 

#### Hiding Hierarchies
Our program will handle/evaluate queries, yet we need to create these queries. The best way to do so is write C++ expressions to create queries. We want to generate the compound query previously described by writing this type of code: 
`Query q = Query("fiery") & Query("bird") | Query("wind");`

Here, we assume the definition of a interface class named `Query`. 

The `Query` class will store a pointer to `Query_base`. 
That pointer will be bound to an object of a type derived from `Query_base`.  Remember that `Query_base` is abstract. No implement at all, so we will have to use the derived classes etc. 

The `Query` class will provide the same operations as the `Query_bas` class; `eval` to evaluate the associated query, and `rep` to generate a `string` version of the query. With an overloaded output operator to display the associated query. 

Users will create an manipulate `Query_base` objects only indirectly through operations on `Query` objects. 
• The & operator will generate a Query bound to a new AndQuery.
• The I operator will generate a Query bound to a new OrQuery.
• The operator will generate a Query bound to a new NotQuery.
• The Query constructor that takes a string will generate a new WordQuery.

![[Pasted image 20240510133127.png]]
Essentially parsing, that's what it feels like from a compiler's perspective, evaluating expressions is just shorthand for compiler evaluation. 

OOP is usually a struggle to understand the design of a program, and how the implementation works. 
![[Pasted image 20240510155926.png]]
Let's actually write some code, and just see how it works: 
```
class Query_base{ 
	friend class Query;
protected: 
	using line_no = TextQuery::line_no; // used in the eval functions

	virtual ~Query_base() = default;

private: 
	// eval returns the QueryResult that matches this Query
	virtual QueryResult eval(const TextQuery&) const = 0; 

};
```



TBH a lot of this stuff is just going over what we did in the part before, worth a read, but probably not writing it all out as such. 

Abstract class, we don't want to actually use that class directly. 