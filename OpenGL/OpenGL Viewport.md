#openglviewport
This is the rendering window size: 
`glViewPort(0, 0, 800, 600)`
If this is smaller than the actual window, then we can display things around this view port, we are telling openGL how much of the screen that the viewport is going to take. 

OpenGL will use the data specified by `glViewport` to transform the 2D coordinates it processed to coordinates on your screen. 
For example, the processed point of location (-0.5, 0.5) would, in this case, with its final transformation, be mapped to (200, 450) in screen coordinates. So there is that mapping, from projective space, render imagine (whatever the fuck you want to call it), and then that has to be mapped to the screen coordinates. 
Projective space coordinates in OpenGL are [-1,1], 0 in the middle obviously. 

The moment that the user resizes the window, the viewport should be adjusted as well. 
Using a `framebuffer_size_callback()`. 

There are many callback functions: 
Callback functions are those that are passed another function, and call that given function within itself. 

There can be a huge amount of these, allowing us to do loads of things with it. 

