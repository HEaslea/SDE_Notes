Modelling the Real World. 
For this lighting model, we need some core material focussed elements: 
```
#version 330 core
struct Material { 
	vec3 ambient;
	vec3 diffuse;
	vec3 specular;
	float shininess;
};

uniform Material material;
```

`ambient` is for the surface colour. 
`diffuse` vec defines the colour of the surface under diffuse lighting, usually set to the same as the ambient colour. 

Remember that at the end of the last is 
```
vec3 result = (ambient + diffuse + specular) * objectColour;
```

When we come to more convoluted shapes, we will look at Model Loading, and then we can really get at the materials that we want to use here. 



Putting the materials into the fragment shader, where they are supposed to be. 
```
void main() 
{ 
	// ambient
	vec3 ambient = lightColour * material.ambient;

	// diffuse
	vec3 norm = normalize(Normal); // that came in from the vertex
	vec3 lightDir = normalize(lightPos - FragPos);

	float diff = max(dot(norm, lightDir), 0.0);
	
	vec3 diffuse = lightColour * (diff * material.diffuse);
	// material.diffuse is the colour of the object

	vec3 viewDir = normalize(viewPos - FragPos);
	vec3 reflectDir = reflect(-lightDir, norm);
	flaot spec = pow(max(dot(viewDir, reflectDir), 0.0f), 
		material.shininess);

	vec3 specular = lightColour * (spec * material.specular);

	vec3 result = ambient + diffuse + specular;
	FragColour = vec4(result, 1.0f);
}
```

Then with the material, we need the uniforms, which is pretty easy, and we can change this as we go along, dynamically adding materials as we go. 

After this, our objects might look a little too bright. 
Well our light is the brightest white, and we have no falloff. 

ALSO, light sources have respective ambient, diffuse and specular intensities as well. 
Everything for now, all the object's ambient, diffuse and specular are being returned at * 1.0. 


This is definitely something that you will have to play with, in reality, the ambient should really have that much to do with the final colour of the cube. 

I would definitely uniform this, then we have the light shader for each object. 
Would you have to work with the idea that we don't have any lights. 
Anyway: 
```
vec3 ambient = vec3(0.1) * material.ambient;
```

We'll do the same for our lights that we did for our materials: 
```
struct Light { 
	vec3 position;

	vec3 ambient;
	vec3 diffuse;
	vec3 specular;
};
uniform Light light;
```

The ambient is usually set quite low, otherwise the object will just be lit up by the random bouncing light, if you are in a room of mirrors, that might be ok. 

```
vec3 ambient = light.ambient * material.ambient; 
vec3 diffuse = light.diffuse * (diff * material.diffuse);
vec3 specular = light.specular * (spec * material.specular);
```

We can then easily change the light colour as well. 

This is what a fragment shader for an object with multiple light sources, **might** look like: 

```
#version 330 core

struct PointLight{ 
	vec3 position; 
	vec3 colour;
	float intensity;
};

#define NUM_POINT_LIGHTS 4 // for 4 lights

in vec3 FragPos; // position of the fragment in world space
in vec3 Normal; // taking in the normal of the fragment
in vec2 TexCoords; // if you want them 


uniform sampler2D texture_diffuse1; // not seen this before 
uniform vec3 viewPos; // camera pos

uniform PointLight pointLights[NUM_POINT_LIGHTS]; // array of point lights

out vec43 FragColour;

vec3 CalculatePointLight(PointLight light, vec3 Normal, vec3 FragPos, vec3 viewDir);

// remember that this happens for every vertex
void main() 
{ 
	// normalize inputs
	// Especially if they come from a model
	vec3 norm = normalize(Normal);
	vec3 viewDir = normalize(viewPos - fragPos);

	// Texture Colour
	vec3 textureColour = texture(texture_diffuse1, TexCoords).rgb;

	// Accumulate the effects of all point lights
	vec3 result = vec3(0.0);

	for(int i = 0; i < NUM_POINT_LIGHTS; i++)
	{ 
		result += CalculatePointLight(pointsLights[i], norm, FragPos, viewDir);
	}

	result *= textureColour;
	
	FragColour = vec4(result, 1.0);
}

vec3 CalculatePointLight(PointLight light, vec3 normal, vec3 fragPos, vec3 viewDir)
{ 
	// lightDir must be from fragPos to the light 
	vec3 lightDir = normalize(light.position - fragPos);

	// diffuse
	float diff = max(dot(normal, lightDir), 0.0);

	// specular shading
	vec3 reflectDir = reflect(-lightDir, normal);
	float spec = pow(max(dot(viewDir, reflectDir), 0.0), shininess 32.0); 
	// change change shininess as we go

	float distance = length(light.position - fragPos);
	float attenuation = 1.0/ (distance * distance); // inverse square fall decay

	vec3 ambient = 0.1 * light.Colour;
	vec3 diffuse = diff * light.Colour;
	vec3 specular = spec * light.Colour;

	return(ambient + diffuse + specular) * light.intensity * attenuation;
}
```
Then we get to lighting maps, which is a whole thing. 
Then there is types of lights, and their effect and decay etc.

We'll look at this after modelling. 

a