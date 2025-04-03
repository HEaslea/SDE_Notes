We have compiled our shaders, then attached them, then linked them and that's all fine, no errors. 
The thing to note is that these programs hold the shaders for the GPU to use. 

Therefore another way of drawing a triangle in a different colour, is to create another program, with a different colour shader, which has a different fragment shader in it, producing a different colour. 

Then we just change the program that we use in order to draw the second triangle. 

The state machine, the global state: 
![[Pasted image 20240610151726.png]]

Clearly the global state, which is the state machine that is openGL has a **current program**, the key being current, meaning that we can create as many programs as we want, and just change it to whatever we want, so that the fragment shaders will be different. 
Our shaders at this point are really basic, if they get the colours from a VBO, then that's fine, but the very first one was super simple and that was outputting just a colour for every vertex going into it. 

Obviously, you would want each vertex to have its own colour attrib, rather than changing the program every time, duh, but that the idea I'm more trying to get across here is that we can indeed change the program. 


