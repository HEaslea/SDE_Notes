Super Complex System -> Comprised of Simple/Readable Code

Maths Maths Maths Maths Maths
Maths Maths Maths Maths Maths
Maths Maths Maths Maths Maths
Maths Maths Maths Maths Maths
Maths Maths Maths Maths Maths
Maths Maths Maths Maths Maths
Maths Maths Maths Maths Maths
Maths Maths Maths Maths Maths

WoW -> Feedback is possible prone to survivor bias -> in that a game so old is only played by those to tolerate potentially awful mechanics or outdated systems, which receives little/no negative feedback, but could be driving away new players. Closed Beta of only veteran players -> Necessary but little to no rework of heavily implemented outdated systems




FELLOWSHIP TOOK MY FUCKING IDEA


Remember that what they see is an aesthetic

Don't reinvent the wheel without making a car!

Success is something you attract, not something that you chase endlessly

"A GAME ABOUT US" -> ambient game about development of human sensibilities


COMMON BUG -> A  common error in my code is the cyclical nature of some of my objects
: ie.
```
class Renderer 
{ 
	//...
	App* rApp;
};

class App
{
	//...
	Renderer aRenderer;
};
```

This was causing errors of "undefined Renderer class" in the App.cpp, meaning that we just had some weird cyclical error that one wouldn't be defined by the time the other required, leading me to believe that if I had rearranged the headers, then we might have a "undefined App class" in Renderer.cpp. Excluding the fact that pointers can point to a undefined type when in a class, giving rise to the PIMPL ideology. 


There is a list of things that need to be done, it's just about figuring out in which order, and who should do them

