## Colours
We have referred to them before, when we colour vertex attribute. 

Digitally represented with RGB. 
These values [0,1]. 

`glm::vec3 coral(1.0f, 0.5, 0.31f);`

The colour that we see of objects is actually the colour that they reflect, not the colour that they "have". 

![[Pasted image 20240723182901.png]]
White light will be a collection of all the colours. 

Essentially, this is the very basic of the very basic. 

When we have a light-source, in OpenGL, we want to give it a colour. 
If we have a white light for instance, and we then multiple an object's colour value, with the light source, then the resulting colour would be the reflected colour of the object. 
eg. with just one channel. white light (R1.0) and a Red object(R1.0) we of course get (R1.0), the same with all colours for that object, they are just x by 1.0. 
Let's look at when we have a coral value: 

```
glm::vec3 lightColour(1.0f, 1.0f, 1.0f);
glm::vec3 toyColour(1.0f, 0.5f, 0.31f);
glm::vec3 result = lightColour * toyColour; 
// duh this is just 1.0f 0.5f 0.31f
```

What would happen if we had a green source: 
```
glm::vec3 lightColour(0.0f, 1.0f, 0.0f); // remember RGB
glm::vec3 toyColour(1.0f, 0.5f, 0.31f);
glm::vec3 result = lightColour * toyColour; 
// not even more complex, only the green aspect is shown
// result = 0.0f, 0.5f, 0.0f
```
This is very cool indeed. 

So we might think that the colour channels of an object are really just manipulators for the light.
The toy here is reflecting all the red light coming to, half of the green light coming to it, and close to a 1/3 of the blue light coming towards it.
![[Pasted image 20240723185205.png]]

#### Lighting a Scene
Soon, we'll be creating interesting visuals by simulating real-world lighting making extensive use of colours. 

First, we need something to actually cast our light on, perchance a cube. 
Perchance a small man. 
Let's take the cube. 
For now, we can represent the light source with a cube as well. 

First we'll need a vertex shader to draw the container. 
The code should be nothing new, we don't need the textures any more. 

```
#version 330 core
layout(location = 0) in vec3 aPos; 

uniform mat4 model;
uniform mat4 view; 
uniform mat4 projection;

void main() 
{ 
	gl_Position = projection * view * model * vec(aPos, 1.0f);
}
```
If you had texture data, you can still keep that, but just change the vertex attribute pointers. 

It's probably wise to create a `lightVAO`: 

```
unsigned int lightVAO;
glGenVertexArrays(1, &lightVAO);

glBindBuffer(GL_ARRAY_BUFFER, VBO);
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), 
	(void*)0);
glEnableVertexAttribArray(0);
```

The one thing that is really different is this: 
The fragment shader. 
This is for both the light source and the container: 
```
#version 330 core
out vec3 FragColour;

uniform vec3 objectColor;
uniform vec3 lightColor;

void main() 
{ 
	FragColor = vec4(lightColor * objectColor, 1.0);
}
```
One thing to remember is that we don't want the light source's object colour to affect the light source, which is what it will do here. 
To accomplish this, we need to create a second set of shaders, that we'll use to draw the light source cube, making it safe from any changes to the lighting shaders. 
Of course the vertex shader will be the same, creating positions of vertices on our screen is the same. 

Another simple fragment shader, making sure that the source cube's colour remains bright, by defining a constant white colour on the lamp: 
```
#version 330 core
out vec4 FragColor;

void main 
{ 
	FragColor = vec4(1.0);
}
```

So when we want to draw an object, that isn't a light source, then we use the other shader that we wrote, when we want to draw a light source, then we can just use this one :). 

Later we will slowly move towards a more realistic light source. 

The main purpose of the light source cube is to show where the light comes from. 

This means that no matter the other light sources in the scene, we have a bright cube. 
That's the basis of this shader. 

`glm::vec3 lightPos(1.2f, 1.0f, 2.0f);`

Then we translate the light source cube to the light source's position and scale it down before rendering it: 
```
model = glm::mat4(1.0f);
model = glm::translate(model, lightPos);
model = glm::scale(model, glm::vec3(0.2f));
```

Then our code might look something like this: 
```
lightCubeShader.use();
// set the model, view and projection matrix uniforms
[...]
glBindVertexArray(lightCubeVAO;)

glDrawArrays(GL_TRIANGLES, 0, 36);
```



