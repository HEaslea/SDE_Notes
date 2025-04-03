VO - Velocity Obstacle, ie. the set of velocities that a char will have that will result in a collision with another char at the same moment in time. 
If a Velocity within that set is chosen, then a collision will occur at some point. 
![[Pasted image 20240730183316.png]]
A relative to object B: 
Then we have the set of all velocities that will lead to a collision: 

RVO being Reciprocal Velocity Obstacle - There are of course more, 
FVO, GVO, HRVO, NVLO, etc. etc. 

VOτ A|B  - would read, the Velocity Obstacle of A induced by B for time window τ. 

D(p,r) = {q| ||q−p || < r} is our open disc of radius r, centred at p: 
D(p, r) is a set of q such that the distance between q and p is < r. 

VOτ A|B = {v|∃t ∈ [0,τ] :: tv ∈ D(pB −pA,rA +rB)}

v such that there is an time within the interval such that (::) the position of A at time t is an element of the set created by Disc of a to b, and the radius of them both added together. 

[Solid Documentation](https://gamma.cs.unc.edu/ORCA/publications/ORCA.**pdf**)

Just read the documentation here. 
The idea is that we can represent vectors in their own space. 

Now, there are infinite velocities that we can pick in order to not collide. 

However, let's go for the optimal one. 

## Optimal Reciprocal Collision Avoidance
The reciprocal nature is that the robots do not need to communicate with each other in order to make sure that they avoid, they just have their independent sets of velocities allowing for avoidance. 
The two sets for A and B are : ORCAτ A|B for A and ORCAτ B|A for B. 
Optimization will also mean that we are looking for velocities that are not too far out from the original, we don't want to have to go all the way back if we don't have to. 

The amount of velocities close to the optimization velocity is equal for A and B. 
Optimization velocities v opt A for A and v opt B for B.

Game AIPro3 and all the articles in it are free, so many resources, shock: 

[The Article in Question](https://www.gameaipro.com/GameAIPro3/GameAIPro3_Chapter19_RVO_and_ORCA_How_They_Really_Work.pdf)

1. Understanding the assumptions that RVO makes, and its guarantees. 
2. Realizing that those assumptions and guarantees are constantly violated. 
3. Realizing that it still works, and why. 
4. Tweaking to make it work even better. 
VO agents, with no communication give the illusion that they are communicating. 
Very error prone towards the beginning lol. 

The issue was of course, that one made the assumption of constant velocity of the other, meaning that when they independently changed course, that assumption was broken. 

Therefore RVO was devised. 
The assumption here is that the other agent will also using RVO. 
Meaning that each agent will move only halfway out of the way of collision, anticipating what the other will do its part as well by moving halfway in the other direction. 
![[Pasted image 20240730232234.png]]

Gross differences between them both. 
One thing to remember is that this is just avoidance between two agents, and there is **NOTHING ELSE THAT THEY HAVE TO AVOID**. 
Each agent also assumes that the current velocity of the other, is their preferred velocity. 

Including 3 agents with RVO, is a bit of a struggle, meaning that an agent will assume that which it might collide with, will move opposingly to the its own velocity, however, if there is another agent, then we will struggle, as that agent will see that preferred velocity and avoid accordingly. 
![[Pasted image 20240730232823.png]]
For more than 2 agents, perhaps RVO causes some oscillation. 
**HOWEVER** This actually does work, but why? 
There is a level of flickering at the beginning with the velocities, however, due to the fact that they have moved, this means that they can begin to cooperate with each other eventually. 
RVO over VO here will mean that they oscillate for less time. 

The goal of any gradient method is to have two agents avoid each other in a manner where they look like they just squeeze past each other. 

**Sidedness** : Two agents heading toward each other can each dodge left or right; either one is a potential solution, both work, but both are not optimal. 

Get A and B and get the time of closest approach, then gather A to B as a vector. If that vector is eastward, then A will be westward of B. 


### ORCA 
After RVO, ORCA had this sidedness in mind. 
With three agents, if one is to pass on the right and the other two to pass on the left, then we run into an issue. 
ORCA forces agents to commit to their current passing sides, in relation to the centre of the other agent that they are passing. 
It reduces, if not completely, velocity flicker that comes from inconsistent passing sides. 

Remember that if we look at the cone that RVO produces, it is a fair percentage of all viable velocities. However, ORCA will reduce based on a half plane. 
however, ORCA's constraint relaxation has the effect of resolving complex avoidance scenarios over several frames. 
