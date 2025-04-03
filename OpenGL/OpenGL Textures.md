We tell each vertex where in the texture they are referencing, then the interpolation will do the rest. 

Texture coordinates will range from 0 to 1. 
In order to get the colour for that fragment, that pixel, means that we have to use sampling. 

Lower left is (0,0), top right (1,1);

![[Pasted image 20240614224028.png]]

We have to do this manually, something that a 3D modelling software will do for us. 

However, let's get it on. 
Let's say our coordinates will map to what we have here: 

```
float texCoords[] = 
{ 
	0.0f, 0.0f, // lower-left 
	1.0f, 0.0f, // lower right
	0.5f, 1.0f // top-center
};
```

There are quite a few ways to wrap, and to actually do texturing, therefore we will have to tell openGL how we want to do it. 

### Texture Wrapping
Specifying coordinates out of range.
There are ways to repeat: 
```
GL_REPEAT : this is default

GL_MIRRORED_REPEAT : Mirroring on repeat

GL_CLAMP_TO_EDGE : Clamps coordinates between 0 and 1, the result is that higher out_of_range coordinates will be clamped to the edge, resulting in a stretched edge pattern

GL_CLAMP_TO_BORDER : Coordinates outside the range will have a user-specified border colour
```


![[Pasted image 20240614224957.png]]

There are also 3D textures too, however, let's stick to 2D for now. 
[Docs.gl for this](https://docs.gl/gl4/glTexParameter)

```
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_MIRRORED_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_MIRRORED_REPEAT);
```

s-axis is "bottom" (x) : t-axis is "left" (y). 

![[Pasted image 20240614230739.png]]
If we did choose the `GL_CLAMP_TO_BORDER`, then we will  need to specify the colour: 
```
float borderColour[] = {1.0f, 1.0f, 0.0f, 1.0f};
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, borderColour);
```

### Texture Filtering
Which colour do we sample, when the texel to pixel ratio is not just 1. 
Texel - texture pixel (resolution inferred). 
So when we interpolate, and we have a point that we want to sample from, what's the best way to do it, we can specify. 
For now, let's look at `GL_NEAREST` and `GL_LINEAR`. 

`GL_NEAREST` is the default, and we take texel that the center of our point (texture coordinate) is closest to. 
![[Pasted image 20240614232213.png]]
Pretty fucking simple. 

`GL_LINEAR` ((bi)linear filtering) takes an interpolated value from the texture coordinates neighbouring texels, approximating a colour between them. 
The closer the point is to the centre, the more that texel will contribute. 

The way you can see this the most is if we have this as a low resolution texture, on a large model, making texels more noticeable. 

`GL_NEAREST` will keep that blocky feel, however `GL_LINEAR` will blur things a little more, obviously. 

![[Pasted image 20240614235358.png]]


If we you want more an 8-Bit look, then obviously `GL_NEAREST` is the one. 

You can set up for **magnifying** and **minifying** operations. 
You can set linear for up-scaled textures, and nearest for nicely ratio'd textures. 
We can se this too: 
```
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
```

### MipMaps 
Finally, the meat and potatoes.
If we have a high resolution texture, that is for a very small object, then we can have a really hard time sampling a colour, therefore we have a bunch of textures of lower resolution ready depending on how far the object is. 

![[Pasted image 20240615000048.png]]
There's less cache memory as the object moves further away. 

I don't want to create these, but, weirdly imo, openGL will do it for us, with a single call to `glGenerateMipMaps`. 

When switching, there may be some artefacts. 
Then we need to filter which mimap to use as well, similar to texture filtering. 
![[Pasted image 20240615000425.png]]
Setting this is fairly easy too: 
```
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```

Mipmaps are more for when the resolution of the texture needs to be downscaled. 
Texture magnification won't use mipmaps, actually giving it that option will result in an error. 

### Loading and  Creating Textures
There is one way to format the images that we will be using for our textures, such as picking a format `.PNG` and then writing our own image loader to convert the image format into a large array of bytes. 
While it's not that hard to write one, apparently, it's still cumbersome and what if you want to support more file formats? 
Are you going to write an image formatter for each one? I'm not, not right now at least. 

There are image loading libraries that we can use. A library like `stb_image.h` does all the heavy lifting. 

The basics is that it is a single header file, that we download, include into the packages, and then use the whole `#include "stb_image.h"`. 

In order to use the definitions that are found within the header file, we have define something before the header file, if you imagine that the header file code is just pasted in. 

Then we should see something like this: 
```
#define STB_IMAGE_IMPLEMENTATION
#include "stb_image.h"
```

Remember that we use `""` here in order to show that the header file is in the directory and doesn't require `$(SolutionDir)bollocks`. 

Then we can just load the image using `stbi_load`. 

```
int width, height, nrChannels; // needed for later
unsigned char* data = stbi_load("container.jpg", )
```
### Generating a Texture

Like with most in OpenGL, we have to generate a thing, here we just generate a texture: 
```
unsigned int texture; 
glGenTextures(1, &texture);
// then just bind it
glBindTexture(GL_TEXTURE_2D, texture);
```

Then we need to use the data that we formatted earlier: 
```
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
glGenerateMipmap(GL_TEXTURE_2D);
```

The first being the texture target; setting this to `GL_TEXTURE_2D`, means that we are generating a texture on the currently bound texture object at the same target. Meaning that `GL_TEXTURE_1D` or `GL_TEXTURE_3D` will not be affected. 

The second is the mipmap level, meaning that we can do this manually. 
Then we say which format that we want to store the texture. In this case, we might just have the RGB values, therefore that's al we need. 

Then obviously width and height, these we got from `stbi_image_load` earlier. 
The 0 after that is just some legacy stuff, should remain 0. 

Then we specify the format and datatype of the source image. 
We loaded with `RGB`, and they are stored as `char(bytes)`. 

Then passing in the actual data. 


Now, we have the image data connected and bound up. 

Generating Mipmaps, means that, thank fucking god, we don't have to do it ourselves. 

Then it's a good idea from the `stbi_image` to then free the image, freeing the space in memory. 
`stbi_image_free(data)`. 

![[Pasted image 20240618132252.png]]

Here's the whole thing, where we also change some states that tell the machine how we want those filterings to come about. 
Here we are just stating the `U,V (s, t)` will have repeating wrap arounds. Then as we get closer we just linear filtering, magnifying, as well as when we are minifying. 


## Applying Textures

Then we just do what we did before, we specify, using two floats, where on out texture, that vertex will fall, and the interpolation will do the rest for us in that regard for every pixel on our screen. 

![[Pasted image 20240618133008.png]]
Remember that adding any vertex attribute data will mean that we have to add more to the stride of the other vertex pointers. 
In this case, the stride will be `+= sizeof(float) * 2`; 

Then, of course we need to change our vertex and fragment shader: 
```
#version 330 core
layout(location = 0) in vec3 aPos;
layout(location = 1) in vec3 aColor;
layout(location = 2) in vec2 aTexCoord;

out vec3 ourColor;
out vec2 TexCoord;

int main(){ 
	gl_Position = vec4(aPos, 1.0);
	ourColour = aColor;
	TexCoord = aTexCoord;
}
```

In our fragment shader, GLSL has a built in data type for texture objects called a sampler, takes as a postfix, the texturetype we want  eg. `sampler1D, sampler3D, in our case sampler2D`. 
Then we add a texture to the fragment shader by simply declaring a `uniform sampler2D`, that we later assign our texture to: 
```
#version 330 core
out vec4 FragColor;

in vec3 ourColor; 
in vec2 TexCoord;

uniform sampler2D ourTexture;

int main()
{ 
	FragColor = texture(ourTexture, TexCoord);

}
```
The `texture` function here, ourTexture is a corresponding texture sampler, and the next is the Coordinates that we want to sample. 

This does some work for texture interpolation as well.

Remember that we need to bind our textures. 
```
glBindTexture(GL_TEXTURE_2D, texture);
glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```

To note, I think all of these so far are being positioned in clipping space(?).

The main purpose of texture units is when we have more than one texture in our scene, which is usually all the time. 

Texture unit 0 will be defaulted active. 

So if we just have one texture unit, then that will be active automatically, and we don' t have to activate it in the instance above. 

However, if we have two. 

![[Pasted image 20240618134800.png]]


```
#version 330 core
.... 

uniform sampler2D texture1; 
uniform sampler2D texture2;

int main()
{ 
	FragColor = mix(texture(texture1, TexCoord), texture(texture2, TexCoord), 0.2);
}
```

The 0.2 is just the bias. Giving us 80% of the first texture, and 20% of the second. 



