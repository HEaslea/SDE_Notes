So before we used the view matrix in order to move around the scene. 
OpenGL has no built in camera, so we have abstracted and we have to figure one out. 

If we moved everything the "opposite way" that we want to move, then it seems like we are moving the way that we want to. 

We might want to make a fly style camera, that let's us fly around the 3D scene. 

When we talk about the camera space, we are looking at every vertex coordinates from the camera's perspective. 
So the camera is the origin now. 

The view matrix transforms all the world coordinates into view coordinates. 
In order to actually have a camera, we need its position in world space, the direction it's looking at, a vector pointing to the right and a vector pointing upwards from the camera. 

The camera's position is simply a vector in world space that points to the camera's position. 

Say if we were to set a camera in the same position that we did in the last one: 
```
glm::vec3 cameraPos = glm::vec3(0.0f, 0.0f, 3.0f);
```

### Camera Direction
Super simple, getting a target, diff of two vectors, normalizing the result, ez peasy: 
However, here we want to negate the direction we get, remember that the camera points towards the negative z-axis, therefore instead of `target - ourPos` we will have `ourPos - target`. 

So now we have a vector pointing towards the camera's positive z-axis: 
```
glm::vec3 cameraTarget = glm::vec3(0.0f, 0.0f, 0.0f); 
glm::vec3 cameraDirection = glm::normalize(cameraPos - cameraTarget);
// this is done so that -z is still in front of the camera !!!!!!!!
```

`direction` here is not the best name, as it's actually pointing in the opposite direction to what it is targeting. 

Remember that the -ve z axis is pointing into the screen.
Next we need the +ve x axis, known as the right vector. 
In order to do this we use a little trick, we get the up vector and the direction vector from step2. 
The up vector here will be pointing up in world space. 
Then we just use the cross product in order to get that right axis. 
If we switch the cross product order, remember that we'd get a vector that points in the -ve x axis direction: 
`glm::vec3 up = glm::vec3(0.0f, 1.0f, 0.0f);`
`glm::vec3 cameraRight = glm::normalize(glm::cross(up, cameraDirection));`

Then we need the up axis, in order to do that we just grab the cross of the `cameraDirection` and `cameraRight`. 

`glm::vec3 cameraUp = glm::cross(cameraDirection, cameraRight);`

### Look At
A cool thing about matrices is that if we define a coordinate space using 3 perpendicular (or non-linear) axes you can create a matrix with those 3 axes plus a translation vector and you can transform any vector to that coordinate space by multiplying it with this matrix. 
Still transforming world coordinates to camera coordinates. 

We need the eye position, the coordinates of the camera in world coordinates. 
The point in the scene that that camera is pointing at. 
The up vector of the camera, which helps define the camera's orientation. 

So we've done the whole, calculating the forward vector, remember that we actually took the negation of this for the +ve z axis. 
Here they are already negated.
![[Pasted image 20240707010529.png]]
Don't think you need to normalize the y-axis one, if the others are normalized as well. 
So the vectors in this matrix at the end, are in world space coordinates, would be a bit pointless if we had them in camera position, now that I think about it. 

Here, out rotation matrix is that of the upper 3x3. 
That on the right is the translation part. 

Remember that these are direction vectors, not point vectors, that is a distinction that needs to be made. 

The translation part is responsible for making the `eye` position being the origin of the camera's coordinate system. 
 So remember that video where it was talking about rotating with matrices, we are defining new basis vectors. 
 Remember that eye is the position of the camera in world space. 

Remember that in order to get the new vectors, we have to define the new basis vectors in terms of the old coordinate space. 


#changingbasisvectors
Here's an explanation: 
Imagine that we have standard basis vectors in 3D cartesian coordinate system are: 
Simply, we have these vectors: 
```
i = (1,0,0) for the x axis
j = (0,1,0) for the y axis
k = (0,0,1) for the z axis
```
This is the basis vectors in our world coordinate space:
We have to define direction vectors for our new basis vectors. 
Let's say our camera position in world space is `(3, 2, 5)`. 
The centre is obviously `(0,0,0)`
And our up vector that we will be using is `(0,1,0)` which is just the y axis of our world coordinate space. 

In this case, as we are looking to the centre, our forward is going to be the negation of camera to centre, which is just `(3, 2, 5)`. 
Then we can just normalize. 
That's our new basis vector for the -ve z axis
We have to normalize these vectors so that none of the objects that we are rotating in that space will scale. 



All in all, what we are doing is defining the new basis vectors, and then translating so that the origin is moved to the origin of the new space. 
Simply with a 2d coordinate system: 
Normal Coordinates: 
- Right e<sub>x</sub> = (1,0);
- Up e<sub>y</sub> = (0,1);

So within these coordinates let's say that our new right is: 
- New Right : r = (2, 1);
- New Up: u = (-1, 1);

Let's say as well that we want to move the origin by a translation vector: 
- Translation Vector: t = (3, -2);

Remember that everything at this point has already been converted to world space. 

So We use the new basis vectors as columns: 


When we change coordinate spaces, we are describing the same point, just with a different method of getting there. 


[Great Video About It](https://www.youtube.com/watch?v=P2LTAUO1TdA)

We are talking about the same point in space, but we have to talk about getting there slightly differently. 

The thing to remember is that if we have a point, and then we say that that point is in B's language, then we have a matrix that defines what B's basis vectors are to A, then that matrix is A. 
The matrix us describing what B's basis vectors are to A, therefore we can scale our vector to be in A's space. Therefore we need A<sup>-1</sup> to get from A to B. 


Look at physical notes pinned to the wall as well. 
The great thing is that GLM will do this all for us: 

using the LookAt matrix function, all we need to do is supply, camera position in world space, a target position (where we are looking at), and a vector that represents the up vector in world space. 
Then all we need to do is use this: 
```
glm::mat4 view; 
view = glm::LookAt(glm::vec3(0.0f, 0.0f, 3.0f), 
					glm::vec3(0.0f, 0.0f, 0.0f), 
					glm::vec3(0.0f, 1.0f, 0.0f));
```

So we have our position of our "camera", our target we are lookingat, and the up vector of the world space respectively. 

Say that we want to roate our camera around the scene, our target will still be at `(0,0,0)`. 
So we need an x,z coordinate each frame, representing the circle around the origin. 

The easiest way to do this is: 
```
const float radius = 10.0f;
float camx = sin(glfwGetTime()) * radius;
float camy = cos(glfwGetTime()) * radius;
```








This is going to be me writing all this out again: 

Getting the camera's position is sort of a red herring, we don't "move" the camera, we sort of move everything around in the world, so that the camera looks like it moves, we move those objects inversely. 

```
// everything will be moved (0.0f, 0.0f, -3.0f;)
glm::vec3 cameraPosition = glm::vec3(0.0f, 0.0f, 3.0f;)

// where are we looking
glm::vec3 cameraTarget = glm::vec3(0.0f, 0.0f, 0.0f);

// up is always the same, we just need something in order to get a cross product
glm::vec3 up = glm::vec3(0.0f, 1.0f, 0.0f);


// forward vector of the camera
glm::vec3 forward = glm::normalize(cameraTarget - cameraPosition);

// getting the right vector
glm::vec3 right = glm::normalize(glm::cross(up, foward));

// then we we need the actual up now that we have used the regular one
glm::vec3 recalculatedUp = glm::cross(foward, right);

// then we create our view matrix using the lookAt method
// we needed all that for the lookAt, kind of crazy but yeah
glm::mat4 view = glm::lookAt(cameraPosition, casmeraPosition + forward, recalculatedUp);
```

So then, we get the model, we have our view from above, and then we use our projection: 
```
glm::mat4 model = glm::mat4(1.0f);
glm::mat4 projection = glm::perspective(glm::radians(45.0f), aspectRatio, 0.1f, 100.0f);

glm::mat4 mvp = projection * view * model;

// you can combine this if you want
glUniformMatrix4fv(mvlLocation, 1, GL_FALSE, glm::value_ptr(mvp));
```

The lookAt matrix handles these inverse, rotations etc. I dont want to do it right now, to be clear, I could if I wanted, but I don't :). 
Remember that it's GLM's lookAt, so we have to play by these rules. 

### LookAt
Remember that we define vectors as directions, they can be said to be points, in that when we move to a different basis, we are talking about getting to the same point, just in a different basis. 
So we need the new basis vectors. 

Remember that 3B1Br video where we say that each basis system is just a language, and we want to say the same thing in a different language. 

Imagine if we have our standard basis coordinates, and we say that that's Tom's coordinate system. It also seems more obvious to us. Then we have Mary's, and Mary's world says that her coordinates are also (1, 0) , (0, 1). However, Mary's are not that to Tom's, meaning that her basis coordinates are actually at (2, 1), (-1, 1) to Tom. 
Think for a moment about Mary's favourite coffee spot, at (5, 5). 
To her, that spot is at (5, 5). 
Now, she somehow converses with Tom to meet her there, how would she do that? 
She knows that they aren't "the same point" to both of them. 
We take Mary's matrix, denoting her basis vectors as columns: 
5[2, 1] + 5[-1, 1] = [10 , 5] + [-5, 5] = [5,10]
This is the coffee shop Mary was talking about, which is [5, 5] to her, but really it's [5, 10] to Tom. 


So Mary goes to her [5,5] which is Tom's [5, 10]. 
Notice that we have used Mary's matrix, her basic coordinates in order to translate Tom's coordinates, into hers. 
![[Pasted image 20240716153719.png]]
The 5,5 example: 
![[Pasted image 20240716153739.png]]
So now we've seen that Mary's matrix translates from her language to Tom's. 
Now what if Mary needs to now Tom's language to hers. 
She message Tom, actually mate, there's a nicer place at [3, 3] using your (Tom's) coordinates. 
Now Mary has to think, where does that mean for her? 
Well using the inverse, to translate from Tom's to hers (not hers to Tom's), we get the following. 

![[Pasted image 20240716154624.png]]
Using the matrix to translate from Tom's to Marys', we get this: 
![[Pasted image 20240716154709.png]]

We can see more clearly how that works. 

You can see how a basic matrix can hold a scale and a rotation. 
If our new matrix is just (2, 0) (0, 10) we are scaling the x by 2, then the y by 10; If we had our basic coordinates, and we wanted to say our 1, 1, in their world, then it would be half of the x, and 1/10 the y. That makes a lot of sense.

On tot he LookAt. 
We can define a space using 3 perpendicular (or even non-linear) axes. 
Curvilinear coordinates are when we have curved axis. and basis coordinates. 
![[Pasted image 20240716160059.png]]
Defining our up, right and front axes, we are essentially transforming everything into that basis. Remember that if we have the Camera's basis, and we want to take World coordinates and put them into Camera's, then we take the world coords and * by the position. Remember that if we have Mary's matrix, and we are putting things in relation to Tom, then we just times by Mary's matrix. 
The Camera is looking at the coordinates that the World has given it, said, nice, these aren't according to my space though, and then the camera said, I know my coordinate system, I'll just times it by that.
ChatGPT agrees with me and this too :). 

![[Pasted image 20240716160943.png]]
So in order to look at a given target, we need everything to kind of rotate, we give it that **reversed** direction, remember that instead of saying, `target - camPos`, we are  doing `target + camPos`. Meaning that we are taking the vector from the target to the camPos. 
Remember that, that is crucial for our front vector. That vector has to be going into the camera, not out of the camera. 

Also remembering that we need to have orthogonal axes, meaning that we need to recalculate our Up, otherwise we will find that there is a skew. 

### Walk Around
Doing the movement ourselves using some input factors. 

We need some callbacks in order to do this: 
Remember that callback functions are passed as an argument to another function, they will be called to at some point, they are mostly used in event-driven system, they are used widely in asynchronous operations. 
In event driven programming, we might see that the flow of a program is controlled by the events (duh). 

Therefore we can register Callbacks, meaning that we have Events, as types, and we can register to that event a callback, therefore when we push something to a queue, we 

We actually need a function that we pass into the callback. 
Something like this : 
```
void processInput(GLFWwindow* window)
{ 
	// ...
	const float cameraSpeed = 0.05f; // adjust 
	if(glfwGetKey(window, GLFW_KEY_W) == GLFW_PRESS)
		cameraPos += cameraSpeed * cameraFront;
	if(glfwGetKey(window, GLFW_KEY_S) == GLFW_PRESS)
		cameraPos -= cameraSpeed * cameraFront;
	if(glfwGetKey(window, GLFW_KEY_A) == GLFW_PRESS)
		cameraPos -= glm::normalize(glm::corss(cameraFront, cameraUp)) * cameraSpeed;
	if(glfwGetKey(window, GLFW_KEY_A) == GLFW_PRESS)
		cameraPos += glm::normalize(glm::corss(cameraFront, cameraUp)) * cameraSpeed;
}
```

This is very similar to the strafe idea in games (if not it exactly). 

### Movement Speed
This is all really obvious so far, we are only moving our camera pos when we move left and right etc., we can get different effects where we move our camera, but not our player etc. etc.
All very cool indeed. 
Since every computer will run at varying processing speeds. 
We don't want someone moving faster because their pc is faster, so far, if this function calls 100 * a second, then the camera will move at the scale of moveSpeed 100 times a second. 
If it calls once, then it moves 0.5f every second, (not 50). 
You see the issue. 
Therefore we usually have to keep track of delta time, that stores the time it took to render the last frame. 

When we multiple a velocity by delta time, we get the position, if the time is longer, then we go further and we make up for the fps difference. 

Then the idea of speed becomes "average speed". 
Then, the camera will move at the same "speed" on every single computer. 

```
float deltaTime = 0.0f; 
float lastFrame = 0.0f; // time at the end of last frame

// within every frame in our process input area
float cameraSpeed = 2.5f * deltaTime;
```


In order to move the camera Front to the mouse, we need to move the front vector to how the mouse moves. 

This does become a little bit complicated. 

### Euler Angles
Let's think about these a little bit more. 
Rather than quaternions for now. 

![[Pasted image 20240716171346.png]]

For our camera system, we wouldn't need roll at all right, so we only think about yaw and pitch. 
Basic trig for this: 
![[Pasted image 20240716171823.png]]
For everything our h will be 1. 
Therefore sin y = y / 1, therefore sin y = y
![[Pasted image 20240716172858.png]]
Yaw being from the positive x axis here. 

```
glm::vec3 direction; 
direction.x  = cos(glm::radians(yaw)); // remember to convert to radians first
direction.z = sin(glm::radians(yaw));
```

This is just for this plane however, this is just the yaw. 

So How do we get the pitch. 
The same way, just now we have only to get the y coordinate. 
![[Pasted image 20240716173324.png]]
`direction.y = sin(glm::radians(pitch));`

We have to notice here, that are x and z are going to influenced by this pitch direction. 

Therefore we need to take that into consideration. 
```
direction.x = cos(glm::radians(yaw)) * cos(glm::radians(pitch));
direction.y = sin(glm::radisn(pitch));
direction.z = sin(glm::radians(yaw)) * cos(glm::radians(pitch));
```


Think about it as a unit sphere, if our Y increases, that means that our x and z must decrease too. 
They didn't have this tech in doom (joking). 

### Mouse Inputs
Horizontal mouse movements will affect the yaw, and vertical will the pitch of course. 

The trick is to see how much the mouse movements have changed. 
And then calculate the pitch and yaw off that. 

First thing's first, we have to tell GLFW, that we should hide the cursor and capture it. 

Capturing means that the cursor should stay in the centre of the window, (unless we lose focus or quit that window). 
This is really easy: 
```
glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_DISABLED); 
```

The mouse won't leave the window. 

Then we need to get the mouse movements from GLFW `mouse_callback`

`void mouse_callbakc(GLFWwindow* window, double xpos, double ypos);`o
`xpos` and `ypos` here are the current mouse positions. 
Every time the mouse moves, we want to call the mouse_callback function.

`glfwSetCursorPosCallback(window, mouse_callback);` 
Setting the callback respective to the Event type corresponding to the mouse. 

1. Calc the mouse offset since the last frame. 
2. Add the offset to the camera's yaw and pitch values. 
3. Add some constraints to the minimum/maximum pitch values.
4. Calculate the direction vector

Storing the last is easy. 
Let's say that we initialize them to be the centre of our screen. 
`float lastX = w/2, lastY = h/2;`

In the mouse's callback function, we calculate the offset movement between the last and the current frame. 

```
float xoffset = xpos - lastX; 
float yoffset = ypos - lastY;

lastX = xoffset; 
lastY = yoffset;

const float sensitivity = 0.1f; // how fast we turn 
xoffset *= sensitivity; 
yoffset *= sensitivity; 
// however fast we moved, get 1/10th of that. 

yaw += xoffset; 
pitch += yoffset;
```

It's important that we add some restraints so that the player's don't flip the LookAt matrix: 
In order to do this all we have to do is make sure that the yaw and pitch don't go above 90 and below -90. 

simple: 
```
if(pitch > 89.0f)
	pitch = 89.0f
if(pitch < -89.0f)
	pitch = -89.0f
```

No horizontal rotation constraining. 

Now, we have to actually find the direction vector that we are going to be using: 
```
glm::vec3 direction; 
direction.x = cos(glm::radians(yaw)) * cos(glm::radians(pitch));
direction.y = sin(glm::radians(pitch));
direction.z = sin(glm::radians(yaw)) * cos(glm::radians(pitch));
```

Just something to note is that the initial position will not be that of the centre, as the cursor must come into the window in some way. 

Just a quick check: 
```
if(firstmouse) // set to true initially
{ 
	lastX = xpos;
	lastY = ypos;
	firstmouse = false;
}
```

```
void mouse_callback(GLFWwindow* window, double xpos, double ypos)
{ 
	if(firstmouse)
	{ 
		lastX = xpos; 
		lastY = ypos;
		firstmouse = false;
	}

	// purely 2D on the screen now
	float xoffset = xpos - lastX; 
	float yoffset = ypos - lastY;

	lastX = xpos;
	lastY = ypos;

	float sensitivity = 0.1f;

	xoffset *= sensitivity;
	yoffset *= sensitivity;
	
	yaw += xoffset;
	pitch += yoffset;

	// lock pitch
	if(pitch > 89.0f)
		pitch = 89.0f;
	if(pitch < -89.0f)
		pitch = -89.0f;

// transferring to 3D

	glm::vec3 direction; 
	direction.x = cos(glm::radians(yaw)) * cos(glm::radians(pitch));
	direction.y = sin(glm::radians(pitch));
	direction.z = sin(glm::radians(yaw)) * cos(glm::radians(pitch));
}
```

The next step is implementing this as best as you can really. 

 