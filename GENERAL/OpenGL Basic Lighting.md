Light being really complicated, there are way too many factors. 

Lighting models, based on the phsyics of light as we understand it. 
There is the Phong lighting models, as well as the Blinn Phong model. 

For just the Phong, there are three main components. 
1. Ambient - just general light
2. Diffuse - simulating directional impact a light objects has on an object. Most significant, the brighter the object is, the more it faces to the light source.
3. Specular - Simulating the bright spot, that appears mostly on shiny objects. They are more inclined to the colour of the light.
Then we combine them. 

### Ambient Lighting
When we take into account all the light that is bouncing around, from thing to thing, when we take that into account, we are looking at global illumination algorithms. 
However, we don't want to take all that into account. 
A simplistic model of global illumination, namely ambient lighting. 
We take the light's colour, multiply it with a small constant ambient factor, multiply this with the object's colour. 
Then use that as the fragment's colour in the cube object's shader. 

It might look like this : 
```
void main() 
{ 
	float ambientStrength = 0.1;
	vec3 ambient = ambientStrength * lightColor;

	vec3 result = ambient * objectColor;
	FragColour = vec4(result, 1.0);
}
```

### Diffuse Lighting
Perhaps the most important part of all this. 
![[Pasted image 20240805001210.png]]
There is a light source, with a light ray target at a single fragment of our object. 
We need to measure the angle between. Therefore we need a normal vector. 
Then, dot product to get the angle between them. 

Here we will need to make sure that all the vectors are normalized. 

We need **normal vectors**, and the **directed light ray**, this is just a basic idea of the position of the light, and the position of the fragment. 

Here the book adds them manually. 
Therefore you would need to add that to the vertex shader. 
```
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;
```

All the lighting calculations are done in the fragment shader so we need to forward the normal vectors from the vertex shader to the fragment shader: 
let's do that. 

```
layout (location = 1) in vec3 aNormal;

out vec3 Normal;

void main() 
{ 
	gl_Position = projection * view * model * vec4(aPos, 1.0);
	Normal = aNormal;
}
```

Then in the fragment shader: 
```
in vec3 Normal;
```

Then to get the diffuse color; we would also need the light colour; 
For now, this light's source is a single static variable that we can declare: 
```
uniform vec3 lightPos;
```

This can then be updated as we go along. 
Then we need the fragment's position. 
This would be done in world space. 
Therefore we need the vertex position that is found after the model matrix: 
This can be done between shaders again really easily: 
```
out vec3 FragPos;
out vec3 Normal;

void main() 
{ 
	gl_Position = projection * view * model * vec4(aPos, 1.0);
	FragPos = vec3(model * vec4(aPos, 1.0));
	Normal = aNormal;
}
```

Then the fragment shader will need to take in this `FragPos`. 

First normalize the normal: 
```
vec3 norm = normalize(Normal);
vec3 lightDir = normalize(lightPos - FragPos);
```

In this way, we are just taking the normalized dot product, remember that as we scale vectors, as they get bigger, then the dot product will change as well. Remember `||a|| ||b|| cos Theta` . 

Then easily just take the max between 0.0f, and the dot product, the max of to normalized vector dot products is obviously going to be 1, and 0 after perpendicular. 

`float diff = max(dot(norm, lightDir), 0.0);`
`vec3 diffuse = diff * lightColour;`

Then in the fragment shader: 
```
vec3 result = (ambient + diffuse) * objectColour;
FragColor = vec4(result, 1.0);
```

Remember that if we scale non-uniformly, then our normal vectors will be wrong as well. 
That's why we also have the normal matrix, which will remove this weird effect. 

The normal matrix can be defined as : 
`the transpose of the inverse of the upper-left 3x3 part of the model matrix`. 

Therefore in the vertex shader we can generate the normal matrix by using the `inverse` and `transpose` functions in the vertex shader that work on any matrix type. 
Remember that normals are literally direction vectors, they have no point in relation to the model at all. 
`Normal = mat3(transpose(inverse(model))) * aNormal;` 
That's the idea. 

Inverse is definitely a costly operation for shaders, try and avoid it if we can. 
Normally lol, we might want to do it on the CPU and then via a uniform, send it to the program. 

If we don't do some weird non-uniform scaling, then it might be fine. 


### Specular Lighting
Similarly, here we need the direction to the eye as well, and how well the reflection is aligned to that vector too. 
Specular lighting is based on the reflective properties of surfaces. 
![[Pasted image 20240805003827.png]]

We take the reflection of the light direction around the normal of the surface. 
The closer the angle again, the greater the intensity of the light. 

The view vector is another variable that we need for specular lighting which we can calculate using the viewer's world space position, and the fragments position. 

However, if we do this in view space, then viewer will always be at 0,0,0. 
`uniform vec3 viewPos;`
We just pass this the camera position. 

Then we can look at another constant known as the `specularStrength`. 
The closer to 1 it is, the brighter that we get. 
```
vec3 viewDir = normalize(viewPos - fragPos);
// reflect is it's own thing
vec3 reflectDir = reflect(-lightDir, norm);
// don't really need the -lightDir here
```
This is because reflect expects that we take the first vector to point from the light source towards the fragment. 
Then it's pretty obvious: 
```
float spec = pow(max(dot(viewDir, reflectDir), 0.0), 32);
vec3 spcular = specularStrength * spec * lightColour;
```

The 32 here is the shininess. The higher it is, the more we are reflecting the light instead of scattering it all around the thus the smaller the highlight becomes. 
Say for instance that our dot is 0.5, then we put that to the power of 32, we get a small value, if we put it to the power of 64, we get an even smaller value, very basic. 
Therefore, the higher the shininess, we have to be closer to the central point in order to be a specular value. 

![[Pasted image 20240805004853.png]]
The product of changing the shininess. 

Then we get the result: 
```
vec3 result = (ambient + diffuse + specular) * objectColour;
FragColour = vec4(result, 1.0);
```

Now this is just great, as we can understand that the lighting that we use, is just a model. 
Therefore there are so many models that can exist, that can change the way that lighting works, how fantastic. 
Doing the lighting in the fragment, means that we get more realistic lighting, per fragment, that makes something look  a lot more realistic. 

When we use the Phong shading in the vertex shader we will get Gourard shading, rather than Phong Shading, meaning that the values will be interpolated between the vertices. 

