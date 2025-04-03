### Formatting String
```
name = "Alice"
age = 25
formatted_string = "My name is %s and I am %d years old." % (name, age)
print(formatted_string)
```
- `%s` -string
- `%d` - integer 
- `%f`- floating point
- `%x` - hexadecimal 
- `%r` - Repr (prints the string as a Python object representation, including quotes)
`print("Hello there, %s! %2.f" %(name, pi))`

### Padding and Width Formatting
```
num = 42
print("The number is %5d" % number) # will add 5 spaces before 42
"|%-10s|" # will pad on right, so spaces on the right
```

### Other way of formatting strings: 
```
greet = "Hello {} {}"
greet.format("There", "Tyler")

greet = "Hello {0}! {1} order for {0}"
greet.format("Marcus", "Coffee")

keyword = "Hello, my name is {name} {last_name}"
keyword.format(name="Fabrizio", last_name="Romano")
```
Using `f` before a string is better than this apparently(?).
Also adding the `=` to the `f` formatting: 
```
user = "Heinrich"
password = "Super-secret"

f"Logging in with: {user=} and {password=}"
# This outputs this below: 
"Logggin in with: user='Heinrich' and password='Super-secret'"
```
Another cool thing right here is: 
```
lanuages = ["Python", "Javascript"]
f"Two very popular languages: {", ".join(languages)}"
```

### Other Uses:

The other use might look something like: 
WrapAround use:
```
colors = ["red", "green", "blue"]
index = 0
for i in range(10): 
	print(colors[index % len(colors)])
	index += 1
```

