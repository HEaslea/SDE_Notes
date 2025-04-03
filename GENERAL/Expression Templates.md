Based on the idea of proxying objects, where we don't evaluate something immediately, only when we really need it. 
Expression templates do this a little better: 
They build an Abstract Syntax Tree that represents the operations that will be performed lazily, rather than being done immediately. 

Expressions are represented as objects that encapsulate the operations, therefore the compiler will optimize these computations. 

Operators `+ - *` are overloaded to create expression objects instead of performing the operation immediately. 

```
template <typename L, typename R> 
struct Add 
{ 
	const L& lhs;
	const R& rhs;

	Add(const L& l, const R& r) : lhs(l), rhs(r) {}

	double operator[](std::size_t i) const 
	{ 
		return lhs[i] + rhs[i]; // deffered evaluation
	}
};

template<typename L, typename R> 
Add<L, R> operator+(const L& lhs, const R& rhs)
{ 
	return Add<L, R>(lhs, rhs);
}
```

Then `v1 + v2 + v3` constructing a chain of expression objects, which are evaluated only when needed. 
When we do need a result (accessing an element or assigning to another structure), the entire expression is evaluated in one go. 

```
class Vector 
{ 
	std::vector<double> m_data;
public:
	Vector(std::initialize_list<double> init) : m_data(init) {}

	std::size_t size() const { return data.size(); }
	double operator[](std::size_t i) const { return data[i];}
	double& operator[](std::size_t i) const { return data[i];}
	
	template <typename Expression>
	Vector& operator=(const Expression& expr)
	{ 
		for(std::size_t i = 0; i < size(); ++i)
		{ 
			data[i] = expr[i];
		}
		return *this; // when we use with an expression like auto vec = Expression
	}
	
	friend std::ostream& operator<<(std::ostream& os, const Vector& v)
	{ 
		for(auto val : v.data) os << val << " ";
		return os;
	}
};


// Expression Template for Addition
template <typename L, typename R> 
struct Add 
{ 
	const L& lhs;
	const R& rhs;

	Add(const L& l, const R& r) : lhs(l), rhs(r) {}

	double operator[](std::size_t i) const 
	{ 
		return lhs[i] + rhs[i]; // deferred evaluation
	}
};

// Overload the + operator to return an expression template 
template <typename L, typename R>
Add<L, R> operator+(const L& lhs, const R& rhs)
{ 
	return Add<L, R>(lhs, rhs);
}

int main() 
{ 
	Vector v1 = { 1, 2, 3 };
	Vector v2 = { 4, 5, 6 };
	Vector v2 = { 7, 8, 9 };
	
	v1 = v2 + v3; // no temporary vectors are created here
}
```

Here `v2 -> L` and `v3 -> R` (both are vectors). Then that will make an `Add<Vector, Vector>` and the computation has not been made yet. 
`Add` will then look something like this : and will be instantiated as such: 
```
struct Add 
{ 
	 const Vector& lhs;
	 const Vector& rhs;

	Add(const Vector& l, const Vector& r) : lhs(l), rhs(r) {}


	double operator[](std::size_t i) const 
	{ 
		return lhs[i] + rhs[i]; // Deferred Evaluation
	}
};
```

Then we are using the overloaded `operator=` in the `Vector` class: 
```
template <typename Expression> 
Vector& operator=(const Expression& expr)
{ 
	for(std::size_t i = 0; i < size(); ++i)
	{ 
		data[i] = expr[i];
	}
	return *this;
}
```

Then for each `i`: 
```
double operator[](std::size_t i) const 
{ 
	return lhs[i] + rhs[i]; // here for v2 and v3
}
```
Then computation will happen element by element: 

This means that the actual computation of `v2 + v3` will only happen when accessing individual elements during assignment to `v1`. 

`v2 + v3` would otherwise make a temporary `Vector` object. 
