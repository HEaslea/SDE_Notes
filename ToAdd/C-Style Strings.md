Char arrays with a null pointer  `\0` at the end. 
Ordinarily we use pointers to manipulate these strings. 

![[Pasted image 20240216005441.png]]

They are defined in the `cstring` header. 
They functions do not verify their string parameters. 

```
char ca[] = {'C', '+', '+'}; // not null terminated
cout << strlen(ca) << endl; // disaster as not null terminated
```

However, when I used this in visual code, it was fine, so perhaps it isn't a disaster lol. 

![[Pasted image 20240216180610.png]]
This is a weird one to follow; create a char array for apple. Then create a buffer of 80 characters. 

Then print about guessing a favorite fruit. 
Then flush out the `stdout`, meaning that we get rid of all the memory in `stdout` which is a text stream that essentially holds all the character ready for output. 
Then scan for input, `%79s` means that we can only write 79 characters in, remember that we would have a null character too. 

If `strcmp` == 0, then the two strings that are compared are equal. 

![[Pasted image 20240216184604.png]]
Another weird thing with c-style strings is that this will print out the actually element rather than a series of pointers. It just "assumes" you want the actual element and not the pointer here, which is very weird. 

![[Pasted image 20240216184758.png]]

 This bit of  code would output all of the memory address of each of the elements.

The idea with these arrays is that when you say `ca1`, it returns a pointer to the first element. 
Therefore the expression `ca1 + ca2`, is illegal as it tries to add two pointers together, which is very silly. 

However, we can use `strcat` and `strcpy`. Yet, we must use a "buffer" array in order to hold the resulting string. 
This array must be large enough to hold the generated string, including the null character at the end. 
```
strcpy(largeStr, ca1); // copies ca1 into largeStr
strcat(largeStr, ""); // adds space at the end of largeStr
strcat(largeStr, ca2); // concatenates ca2 onto largeStr 
```
Just  remember to think about the size that we generate into.
This can be a security risk of course. 
This whole chapter is a little weird, because at every turn, the book says to use `string` rather than c-style strings. They are really weird to work with as  well. 

