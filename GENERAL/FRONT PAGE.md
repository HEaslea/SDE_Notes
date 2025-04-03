```
void PrintInt(int&& a){
    printf("%d\n", a);
}

void PrintInt2(const int& ca){
    printf("%d\n", ca);
}

void PrintTest(const int&& cra){
    printf("%d\n", cra);
}

int main(){
    PrintInt(42); // these are all fine
    PrintInt2(10);
    PrintTest(500);


	int f = 69;
	PrintInt(f); // ERROR as rvalue reference cannot be bound to lvalue
	PrintInt2(f); // is fine as f is copied into a const int
	// you wouldn't be able to change ca in PrintInt2
```



[[Handles]]
[[Screen Buffer]]
