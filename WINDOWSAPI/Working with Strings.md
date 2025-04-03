Windows natively supports Unicode strings for UI elements, file names, and so forth. 
It's an international character encoding standard that provides a unique number for every character across languages and scripts. 

Preferred due to its wide range, for multiple languages etc. 

Using UTF-16, in which each character is encoded as one or two 16-bit values. 
They are called **wide characters**, so as to distinguish them from 8-bit ANSI characters. 

The visual C++ compiler supports the built-in data type `wchar_t` for wide characters. 

The header file `WinNT.h` also defines the following **typedef**. 
`typedef wchar_t WCHAR;`

We will see both versions. To declare a wide-character literal or wide-character string literal, put `L` before the literal. 
```
wchar_t a = L'a';
wchar_t *str = L"hello";
```

Some more typedefs: 
![[Pasted image 20240407203016.png]]

### Unicode and ANSI Functions
Microsoft provides two parallel sets of APIs, one for ANSI strings and another for Unicode Strings. 
```
SetWindowTextA // for ANSI string
SetWindowTextW // takes a Unicode string
```

Internally, this is funny, the ANSI version will just translate to Unicode. 
The Windows header also defines a macro that resolves to the Unicode Version. 

![[Pasted image 20240407203507.png]]
Newer applications should always call the Unicode Versions. 

#### TCHARS
Back in the day, we didn't know if we needed one or the other, therefore it was very useful to have macros that could decide that depending on the platform. 
Therefore we have these ideas here. 
![[Pasted image 20240407205201.png]]
That's why you have used `TEXT("Some string literal")`. 

Both will resolves to whatever type of encoding char set that we need. 
```
SetWindowText(TEXT("My Application")); 

// will resolve to one of the following
SetWindowTextW(L"My Application"); // Unicode Function with Wide Character string

SetWindowTextA(L"My Application"); // ANSI function
```

They are less useful today, as most applications should be using Unicode. 
