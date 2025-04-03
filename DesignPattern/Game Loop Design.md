### Decoupling the progression of game time from user input and processor speed. 

Processing user input without waiting for it. 

```
while(true)
{ 
	processInput(); 
	update(); // using the update method that we will talk about later 
	render();
}
```

How Fast? Each loop, is the "speed" in the game. 
Game loop cycles in terms of seconds, we get the FPS. 
Multiple cores, GPUs, dedicated audio hardware, and the OS's scheduler will all affect how much gets done in one tick. 

Nowadays our  games have to adapt to all different forms of hardware. 

Each turn of the loop, we are **processing user input without blocking**, **updates the game state**, and **renders the game to an output device**. 
We might **track the passage of time to control the rate of gameplay**.

The problem with the loop that we have above is that we have no control of how fast it runs. 

Say that we want our game to run at 60 fps: we might run the code a little like this: 

```
while (true){ 
	double start = getCurrentTime();
	processInput(); // no blocking or waiting
	update();
	render();

	sleep(start + MS_PER_FRAME - getCurrentTime());
	// the time difference between when the start of the next frame
	// should be and what time it is now
}
```

```
#include <chrono>
#include <thread>

using namespace std;
using namespace std::chrono;

int main(){ 
	const int fps = 60; // can be user described
	const milliseconds frameDuration(1000 / fps);

	while(true){ 
		auto frameStart = steady_clock::now();
		update(); // do something
		render(); render it

		auto frameEnd = steady_clock::now();
		auto frameTime = duration_cast<milliseconds>(frameEnd - frameStart);
		if(frameTime < frameDuration){ 
			std::this_thread::sleep_for(frameDuration - frameTime);
		}
	}
}
// using cin >> fps; for changing
```



##### One Small Step, one giant step
1. Each update advances game time by a certain amount. 
2. It takes a certain amount of *real* time to process that

If the processing takes longer than the certain time passing in the game, the game will slow down, fps lag etc. 
Such, if it takes longer than 16 ms to advance the in game world by 16ms. 
But if we advance the game by more than 16 ms, then we can update less frequently, and still keep up. 

The idea is that we choose a time step to advance based on how much real time passed since the last frame. 
The longer the frame takes, the bigger steps the game takes. 
It will always keep up with real time because it will take a bigger and bigger steps to get there. 
This is a variable or fluid time steps: 

```
double lastTime = getCurrenTime();
while (true){ 
	double current = getCurrenTime();
	double elapsed = current - lastTime;
	processInput(); 
	update(elapsed);
	render();
	lastTime = current;
}
```
Each frame, we determine how much real time passed since the last game update (elapsed). 
Then we can advance by that amount of time. 

Say we have a bullet going across the screen. With a fixed time step, in each frame, it'll be moved according to its velocity. 
With a variable time step, you scale that velocity by the elapsed time. As the time step gets bigger, the bullet will have moved further and further each frame. 

The bullet will move across the screen in the same time, whether it's 20 small steps, or 4 large ones. The maths will already be done for us. 

- The game will play at a consistent rate on different hardware (slower/faster). 
- Players will faster machines will be given smoother gameplay. 


There is an issue, if two people are playing together, one has a beast of a machine, the other has a slow machine. For one, the game is running super fast, each time step is super tiny, say an arbitrary 60 fps, and other player only has about 5. 
The issue will be that the one with 60 fps will update the physics engine far more often than that of the 5fps machine. 
Due to the accumulation of errors on rounding numbers on machines, the bullets will actually end up in different positions, this is because the faster machine will be doing more equations, making the round error greater and greater. 

Essentially rounding errors at different rates will cause some errors. 
One the same machine, it's not a big deal, however, when we have to have fixed positions on two different machines, running at different rates, that can be a little tricky.

There is a way to used fixed steps, and then just render when we want to in order to make sure that it's not taking up to much processing time. 

A certain amount of real time has elapsed since that last turn of the game loop. 
This is how much time we need to simulate for the game's "now" to catch up with the players'.
We do this using a fixed time step. 

![[Pasted image 20240524134629.png]]

```
double previous = getCurrenTime(); 
double lag = 0.0;
while(true){ 
	double current = getCurrentTime();
	double elapsed = current - previous;
	previous = current;
	lag += elapsed; 
	processInput(); 
	while(lag >= MS_PER_UPDATE)
	{ 
		update(); 
		lag -= MS_PER_UPDATE;
	}
	render();
}
```
All we're really doing here is recording how far we are behind real time, in machine time, using lag, and when that is large, we update a number of times, in order to catch up. 
Once we have caught up, then we render. 

Ideally we want our fps to be around 60. 
you do need to make sure that the time step is greater than the `update()`, otherwise we will never catch up. 

The best part about this is that we have taken `render()` out of the update loop. 

If we update at regular times, however, render at arbitrary times. 
