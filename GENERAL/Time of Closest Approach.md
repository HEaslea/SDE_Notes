### Relative Velocity
This is getting the velocity of one object from the perspective of another. 
Vr  = Vp - Ve. 
This means, what is the velocity of e, from the perspective of p. 

If the e's velocity was (0, -1) and p's was (1, 0); The idea is that imagine p was moving, but to p it looks like it's standing still. e will therefore look like it's moving down one, so across to the right of p, and towards p (-1, -1). 
This is Ve - Vp: 
if we did Vp - Ve; so from e's perspective, we have (1, 1), meaning that if e is looking towards p, we are e, looking towards p, then p looks like, in teh coordinate system, it is moving to the left of us, towards +ve x, and up towards us, up the y access. 

Our relative velocity, tells us the difference of our velocity, in mention that the order of the equation will allow us to see what one point's movements looks like from another. 

## Dot Product
Imagine here that we now Vr, so we know how e is moving in perspective to p. 
And we know the distance from p to e, as a vector, if we get the dot product, then we know how much of that d is taken up every time that e and p moves. 
So we can get an idea of how fast that distance closes up. 
The dot product here tells us how much distance is closed per movement. 
The product just tell us how much the two vectors will align, meaning that if they don't align at all, in relation to p, then e must be moving slightly towards p. 

d . Vr -> is the alignment of vector of distance between the two points, and the relative velocity of e, both are from p, therefore, if e looks like it is closing that distance, ie. moving in the negated direction of distance, then we can assume they will collide. 
This is it. 
Ok, so now we know how much alignment there is. 

Only think of velocity as the rate at which an object's position changes over time. 
Having both a magnitude and a speed. 
Vr is then the rate of change of the relative position between two objects. 
Vr denotes the rate at which the relative distance between them is changing. 
So if P is at 0,0 and E is at 3, 3 and E is moving at -3, -3, then Vr is just -3, -3. 
Distance is obviously 3, 3, that is the relative distance between them both. 
Vr says that the relative rate of change of that distance is -3, -3 per whatever time that velocity is also measured in, therefore it's easier here. The relative distance will change at -3, -3 per unit time. 

So imagine a distance vector, then Vr is the way in which that distance vector will change per time. 

The dot product measures how much of the relative velocity is pointing in the direction of the relative position d.
Giving a scalar value for the rate at which the distance between the player and the enemy is changing in the direction of d. 

Vr is the rate at which the distance between the two is changing. 
d . Vr is the scale value for the rate at which the distance between the player and the enemy is changing **in the direction of d**. 



#### Vr is the rate of change of the relative distance between two objects. 
Imagine an object at 0, 0, p, moving at 1, 1. And e at 2, 3, moving at -1, -1. 
The distance between them is 2, 3, obviously, so the distance between them at 1 second. 
p will be at 1, 1, and e will be at 1, 2; 
The distance then is 0, 1. 
Vr, will be -1 -1, -1 -1, therefore -2 , -2. 
After they move, the distance is 0, 1, which is just 2, 3, + -2, -2. Doesn't that make a whole load of sense. 

If any point, d is 0,0, then they will collide, and we can work out that time there by looking at how many Vr's we applied to d (the distance between the two). 

The relative distance between them is the distance of e from p. 

Then we might also look at how d . Vr changes, remember that Vr can also represent how e moves if p were to stand still, then if d and Vr have a negative dot product, then e is moving directly towards p. 
### Magnitude of Vr
The magnitude gives us a scalar for the rate at which the distance between the two objects is changing. 
Then we can square that. 
Calculated using Vr . Vr = ||Vr||<sup>2</sup>. 
d . Vr gives rate of change in distance, taking the rate of change in distance to time duration. 


Remember that the magnitude of velocity, is the speed. 

So another way to think about it is as a derivative and we are trying to find the point at which the distance between the two is the minimum, this is actually what this equation is. 
However, it just looks really weird: 


### READ_ME
So here we are looking at some important concepts with derivatives.  
![[Pasted image 20240725195201.png]]
![[Pasted image 20240725195333.png]]Here we are expanding the dot product before taking the derivative. 

![[Pasted image 20240725195507.png]]


The relative velocity is that, where we model the movement, and we can essentially keep one object still, move the other by the relative velocity, and the distance between the two objects will remain the same, ie. the movement is still modelled correctly. 
