The OpenGL state machine is just a bunch of variables that define how OpenGL should currently operate. 

The state of OpenGL is commonly referred to as the OpenGL context. 
#openglcontext

## Context
The state of the OpenGL state machine, is the context of it. 
We change that state, by setting some options, manipulating some buffers, and then render using that current context. 

Say that we want to tell openGL to draw lines and not triangles or vice versa, we are changing the state of the "machine" as it were. 

There are several state-changing functions, that will change the context, and several state-using functions, that will perform based on the current state. 
