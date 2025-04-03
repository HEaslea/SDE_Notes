#### Float Decimal Fraction
When working with currency, use the `decimal` module. If we try to do this with `floats` then we run into the problem of rounding and truncation. 

`from decimal import Decimal`

Then looking at something like this
```
tax_rate = Decimal('7.25')/Decimal(100)
purchase_amount = Decimal('2.95')
tax_rate * purchase_amount
```

To round to the nearest penny, we could do something like this: 
`penny = Decimal('0.01')`

Then we just need to quantize to that amount. 


```
from decimal import Decimal

round = Decimal('0.01')

newAmount = Decimal('0.023423')

print(newAmount.quantize(round)) # will output 0.02
```

We can also do something so simple as this: 
```
total_amount.quanitze(penny, decimal.ROUND_UP)
```

#### Using Fractions
`from fractions import Fraction`
```
sugar_cups = Fraction('2.5')
scale_factor = Fraction(5/8)
```

All float calculations are approximations. 

For instance: 
```
(19/155) * (155/19)
# this should be 1, but will come out at 0.9999999999... in python
```
The way to get 1 here would be: 
`round(answer, 3) # 1.0`

Never compare floating point values for exact quality. 
This is recommended to think about for all programming languages. 

`+ - * / // % and **` Addition subtraction, mult, true division, truncated division, modulo, and raising to a power. 

True division and floor division. 

We can mix `int, Fraction` and `int, Decimal`. 

We can write like this in python: 
`8.055e+67`
Then it will get the approximation: 
`(6737037547376141/(2**53))*(2**226)`

We can even use: 
```
import math
math.frexp(8.066E+67)
(0.734352....., 226)
```
Giving us the mantissa and the exponent. 

`Fraction` is the exact ratio of two integer values. 


`math` has a lot of functionality: 
`math.fsum()` will do floating point sums more carefully than `.sum()` built in function. 

Also: 
```
(19/155) * (155/19) == 1.0 # False 

math.isclose((19/155) * (155/19), 1.0) # True
```

##### Complex Numbers
We do `3.14 + 2.78j` to represent `3.14 + 2.78sqrt(-1)`
`cmath` in order to do this. 

`cmath.sqrt()` will return a complex value rather than an error. 

```
math.sqrt(-2) # will throw an error

import cmath
cmath.sqrt(-2) # will return a complex
```

#### True Division and Floor Division
*div-mod* pair - we want both parts - quotient and the remainder. 
So we have `q, r = (floor(a / b), a mod b)`
Therefore we have the base number, then the remainder. 

*The True Value*: Floating point value, good approximation to the quotient. 

*Floor Division*: `a // b`
`print(20 // 3) # 6`

```
total_seconds = 7385
hours = total_seconds // 3600 
remaining_seconds = total_seconds % 3600
```

Then we can go further like 
```
minutes = remaining_seconds // 60
seconds = remaining_seconds % 60
```

Or we could just use the `divmod()` 

```
total_seconds = 7385
hours, remaining_seconds = divmod(total_seconds, 3600)

minutes, seconds = divmod(remaining_seconds, 60)
```

**Doing True Division** 
Will give a floating-point approximation. 
Given: 
```
total_seconds = 7385
hours = total_seconds/3600
round(hours, 4)
```

Giving us a better approximation. 

Then we can also use `Fraction`

```
from fraction import Fraction
total_seconds = Fraction(7385)
```

Any integer will then be promoted to a `Fraction`
```
hours = total_seconds / 3600 # promoted to a fraction
>>> hours
>>> Fraction(1488, 720) # the number as a fraction
```

Then we can convert if we need to, as that fraction is not that useful. 
`round(float(hours), 4)`
Integers will be promoted to `Fraction` whenever we do something like this. 

`/` - true division will produce a floating point value, even with two integers. 

`//` - Truncated - floored I believe



##### String Parsing with Regular Expressions
How to separate out strings: 
Separate into different parts eg: 
`ingredient = "Kumquat : 2 cups"`
`(ingredient words) : (amount digits) (unit words)`
Using the `re` module. 

`pattern_text = r'([\w\s]+):\s+(\d+)\s+(\w+)`

`[\w\s]+` a mixture of letters and spaces. 
In order to make sure that we use `\` we have to use the `r''`
Then we have to compile the pattern: 
`pattern = re.compile(pattern_text)`
Then we need to match: 
```
match = pattern.match(ingredient)
match.groups() # ('Kumquat', '2', 'cups')
```

```
match.group(1) # 'Kumquat'
match.group(2) # '2'
match.group(3) # "cups"
```

We might have something that looks like this: 
`(?P<name>\w+)\s*[=:]\s*(?P<value>.*)`
`?P<name>` defines a name capturing group, to extract a part of the string that matches a specific pattern. By using `name` we can refer to this part later on. 

`\w+` one or more word chars (any letter lower or upper case). 

`\s*` will match any white space character. 
`*` 0 or more occurrences

`[=:]` Character class, match either : or =. `[]` define a set of chars to match. 

`?P<value>` another group named `value`. 

`.*` catches zero or more of **any char** `.` is a wildcard (capturing anything but a new line). 

Realistically this would capture something that looks like this: 
`key_name = value_here`

where the group `name` = `key_name`
and `value` = `value_here`

#### Building Complicated String with F-strings
Using the `f` prefix in order to create this: 
`f'{id} : {location} : {max_temp} / {min_temp} / {precipitation}'`

We can append a data type for each one: 
- s for string 
- d for decimal number 
- f for floating-point number

`f'{id:s} : {location:s} : {max_temp:d} / {min_temp:d} / {precipitation:f}`

Then we you can add length information before the data type: 
`f{id:3s} ...` so on an so forth.

It will interpolate data into a template. 

Just remember `f` for formatted string

- b for binary, base 2. 
- c for Unicode character. Usually we use hexadecimal numbers for these, 0x2661. 
- d is for decimal numbers
- E and e are scientific notation. 6.626E-34 or 6.626e-34 depending on whichever you want touse. 
- F and f are for floating point, can also use nan and NAN for not a number
- G and g for general use. 
- n is for locale-specific decimal numbers. Inserting , or . chars, depending on the current locale settings. 
- o is for octa, base 8. 
- s for string
- X and x are for hexadecimal, base 16. Including digits A-F, lowercase a-f as well
- % for percentage, multiplied by 100 and the output will include a % char. 

`{name:5d}` 5 digit number. 

**Fill and Alignment**: specify filler char (space is the default), change using `<>^`, left, right, centering. 
`f"{text:<10}"`
`f"{text:^10}"`
We could do it with a sign as well: 
`f"{number:=^10}"` Which will look like `=====42=====`

**Sign**: put a sign on all numbers with `+` and only on negative numbers with `-`. 

`{value:5.3f}` this means at least a width of 5, and with 3 decimal figures. 

**Alternate Form:** using `#` in order to get the alternate form. 
```
num = 255
print(f"{num:#x}") # will output 0xff
```
`#x hex, #b binary, #o octal`

**Leading Zero**: `0` in order to get leading 0 
`{code:08x}` produce a hexadecimal value with leading zeroes to pad it out to 8 chars. 

There are other functions: 
```
{name!r} if we repr(name)

{name!s} if we were to str(name)

{name!a} shows ASCII value produced by ascii(name)
```

```
value = 2**12 - 1
f'{value=} {2**7+1=}'
# will output
'value = 4095 2**7+1=129'
```
Which is great for debugging. 

We can also use `format_map()` and other `format()` methods in order to format more complex data structures. 
```
data = dict(
	id=id, location=locaiton, max_temp=max_temp, 
	min_temp=min_temp, precipitation=precipitation
)

'{id:3s} : {location:19s} : {max_temp:3d} / {min_temp:3d} / 
{precipitation:5.2f}'.format_map(data)
```

##### Building Complicated Strings from Lists of Strings
`fromt string import whitespace, punctuation`
`string.whitespace` lists all the whitespace chars that are also part of the ASCII
and `string.punctuation` is fairly obvious after that. 

Explode a string into a list: 
```
title = "Recipe 5: Rewriting an Immutable String"
title_list = list(title)

# use index() to locate position with the given value
colon_position = title_list.index(':')

# strings are immutable data structures, but lists aren't
del title_list[:colon_position+1] # replace everything up to and including colon

# We can replace punctuation by stepping through each position. 
for position in range(len(title_list)): 
	if title_list[position] in whitespace+puncutation: 
		title_list[position] = '_';
```
Remember that `range()` is `[)`. 
Then we join that list together: 
`title = ''.join(title_list)` this will 

Garbage Collection - will get rid of what we don't need. 

Strings are immutable, making them very fast- but we can put them in mutable form - list. 

#### Using Unicode Chars 
**Those that are not on our keyboards**:
We need the chart. 

In our strings, we need to use **escape chars**. 
`\` this is the escape char. 
You can do `'You rolled \u2680'`
Or even: 
`'You drew \U0001F000'`
Or even `\N{name}`
`Discard \N{MAHJONG TILE RED DRAGON}`

Python uses Unicode internally. The 128 or so chars we can type directly using the keyboard, they have internal Unicode numbers. 

`HELLO`
Python treats this as `\u0048\u0045\u004c\u004c\u004f`

RegEx uses a lot of `\` that we don't want Python's compiler to touch, therefore we need the `r` at the beginning. 

`\\` will escape. 

`\\w+[\u2680\u2681\u2682\u2683\u2684\u2685]\\d+`

Here, we couldn't use the `r`, otherwise we wouldn't be using the Unicode chars that are part of the pattern. 

Therefore the `\\w` will become `\w`. 

#### Encoding Strings - ASCII and UTF-8 Bytes
Python encodes in UTF-8. The `str()`. 
When we do `x = "hello world"`, it will be stored as Unicode code points ,not as raw bytes. 
In this case: 
- `h` -> `U+0068` Unicode code point 104
- `e` -> `U+0065` Unicode code point 101
etc etc.

Then when we want to put that into raw bytes -> 8 bit value used in storage or transmission. 
```
x_bytes = x.encode("utf-8")
print(x_bytes) # will still output b'hello'
```

- `h` -> `U+0068` binary `01101000`

Sometimes an internet protocol requires ASCII characters. ASCII can only handle a small subset of Unicode. 
o
`PYTHONIOENCODING` is the environment variable. This can be changed to ensure that a particular encoding is used everywhere. 

Opening a file with given encoding: 
```
with open('some_file.txt', 'w', encoding='utf-8') as output: 
	print('you draw \U0001f000', file=output)
with open('some_file.txt', 'r', encoding='utf-8') as input: 
	text = input.read()
```
This will store the Unicode bytes representing that Unicode. 

Then the read will basically do the same thing. 

Without the `encoding='utf-8'` -> will use default system encoding -> using the system variable -> which might not store and read Unicode. 

Unicode defines a number of encoding schemes. While UTF-8 is the most popular, there is also UTF-16 and UTF-32. A file with 1000 chars in 32, would be 4,000 8 bit bytes. 
With 8, that could be 1000 bytes at most. 

When we try and move from Unicode to ASCII, the char might not be in that set. 

`'You drew \U0001F000'.encode('ascii')` I mean that's just not possible, and will result in an error. 

We might see `b'hello'` as shorthand for a five-byte value. 
Byte values in ASCII range from `0x20 to 0x7F`. 

The `b` is a note that we are looking at bytes, not proper Unicode chars. 


#### Decoding Bytes 
A download from the internet is almost always in bytes. Then we need a decoding system to make sure we know what char set that we are using in order to get actual words from that. 
So we might have something that looks like this: 
`print(b'\xf0\x9f\x80\x80'.decode('utf-8'))`

The general idea being, how do we recover proper characters from bytes that are given to us. 

Say that we are getting data from this link: 
`https://forecast.weather.gov/product.php?site=AKQ&product=SMW&issuedby=AKQ.`

```
import urllib.request

warnings_uri = (https://forecast.weather.gov/',
				'product.php?site=AKQ&product=SMW&issuedby=AKQ')
#python will concantenate these two
with urllib.request.urlopen(warnings_uri) as source: 
	forecast_text = source.read()
```

There is also `curl` and `wget` in order to download files or interact with web services over HTTP, HTTPS, FTP. 

ie. 
`curl -O https://exmpale.com/file.txt` saving in the current directory

Will most likely print to terminal. 

In this case, we would get our data as a stream of bytes. It's not a proper string, 

It would look like this:
```
>>> fore_cast_text[:80]
b'<!DOCTYPE html PUBLIC .... etc. 
```

It was probably encoded in Unicode. `b` shows that its a collection of bytes, not proper values. 
Then let's decode this to get some real proper characters. 

When browsing web pages, there's often a hint provided how we should decode. 
If not given, then just give a few things a go. 

```
document = forecast_text.decode("UTF-8")
document[:80] # now there will be no more b'
```
Meaning that we have proper chars now. 

If this fails, then we have the encoding. 


### Using Tuples of Items
There are lots of places in python where it will make tuples for us. 

Very simple: 
```
from fractions import Fraction

my_data = ('Rice', Fraction(1/4), 'cups')
```
There' just one important thing to remember, is that we would need the `,` in order to make a singleton tuple or a one-tuple. 
```
one_tuple = ('item',)
len(one_tuple) # 1
```

**Access:**
```
my_data[1]
ingredient, amoutn, unit = my_data # like structured bindings
```

Tuples are one example of the more general `Sequence` class. 
There are functions on the tuples: 
```
len(t)
t.count('2') # how many times does this occur in the tuple
t.index('cups')
t[2]
t.index('Rice') # x no int tuple
'Rice' in t # False
```



##### Using NamedTuples
```
fromt typing import NamedTuple

class Ingredient(NamedTuple): # inheritance
	ingredient : str
	amount : str
	unit : str

item_2 = Ingredient('Kumquat', '2', 'cups')

Fraction(item_2.amount)
Fraction(2, 1)
```

Remember this, Tuples are immutable objects. 

```
from typing import NamedTuple
from fractions import Fraction
class Ingredient(NamedTuple): 
	ingredient : str
	amount : Fraction
	unit : str
	
item_3 = IngredientF('Kumquat', Fraction('2'), 'cups')


```
