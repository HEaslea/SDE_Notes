Throwing allows us to create custom problem evaluation, meaning that we can throw an exception when a problem is detected. 
The idea is that we try (throw if we detect a problem) and catch if we detect one. 

```
void divide(int n, int d){
    if (d == 0){
        throw std::runtime_error("BIG ERROR");
    }
    cout << "Result: " << n / d << endl;
}

int main(){
    try{
        divide(10,0);
    } catch (const std::runtime_error& e) {
        cerr << "Caught exception: " << e.what() << endl;
    }
```


The idea is this: 
```
void someFunction{ 
	throw 42;
}


int main(){ 
	try{
        someFunction();
    } catch (int value) {
        cout << "int detected: " << endl;
    } catch (const std::runtime_error& e){
        cerr << "Caught runtime error" << e.what() << endl;
    } catch (...) {
        cerr << "Caught another type of error" << endl;
    }
}// this will still work and will output "int detected:"
```



This is literally it ngl, in C++ you can throw types, and it will be type specific, meaning that we can just throw any type that we want

[Great Video on Exceptions and Good Usage of Them](https://www.youtube.com/watch?v=Oy-VTqz1_58)

![[Pasted image 20240402192641.png]]

The exception swallower here is going to take any exception from anywhere, and call it. Meaning that we can say something along the lines of "I know there is an exception that will be thrown somewhere and this will catch it, rather than letting the application go dead". 

Usually need exception swallowers somewhere in the code. 

The main thing is that they are expensive: 
![[Pasted image 20240402213828.png]]
**Object slicing**: when an object of a subclass type is copied to an object of superclass type, you would lose some information this way: 
```
class A{ 
	int foo;
};

class B : public A{ 
	int bar;
};
```

So here we have object `B` which will have two data members, both `foo` and `bar`. 
When we do something like this: 
```
B b; 
A a = b; 
```
 Then `a` here is losing `b`'s `bar` data member. 
 This is kind of benign, it doesn't really matter: 

```
B b1; 
B b2;
A& a_ref = b2;
a_ref = b1; // b2 = b1 you think it would be a copy(?) NOOOO
// b2 now contains a mixture of b1 and b2;
```
You might say that b2 will be copy of b1 after this, however, you'll see that b2 is a Frankenstein creature, made from some chunks of b1 (the chunks that B inherits from A). 

What happened? Well, C++ by default doesn't treat assignment operators as `virtual`. Thus, the line `a_ref = b1` will call the assignment operator of `A`, not that of `B`. This is because, for non-virtual functions, the **declared** (formally: _static_) type (which is `A&`) determines which function is called, as opposed to the **actual** (formally: _dynamic_) type (which would be `B`, since `a_ref` references an instance of `B`). Now, `A`'s assignment operator obviously knows only about the members declared in `A`, so it will copy only those, leaving the members added in `B` unchanged.

![[Pasted image 20240402214632.png]]
![[Pasted image 20240403004915.png]]