Say that we want to do a rectangle, not a triangle, meaning that our rectangle will just be tessellated to two triangles: 
the data may look something like this: 
![[Pasted image 20240607141150.png]]
There is now some overlap of vertices, we aren't indexing them, they don't wind etc. Yikes. 

All we really need is 4 vertices, not 6. 
If we have 100000 triangles, imagine the overlap. 

Therefore we want to store only the unique vertices, then specify the order in which we want to draw these in. 

The EBO, it's a buffer, just like a vertex buffer object, storing the indices that OpenGL uses to decide what vertices to draw. 
This is called index drawing, and this is the solution to the problem. 
So take those vertices above. 
Here is where our winding order comes in: 
![[Pasted image 20240607141626.png]]
There is only 4 indices [0, 3]; 

Then we create the element buffer object: 
```
unsigned int EBO; 
glGenBuffers(1, &EBO);
```

Then we add the data, just like a VBO; 
In this case, we bind it to the `GL_ELEMENT_ARRAY_BUFFER`. 
```
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(int) * 6, indices, GL_STATIC_DRAW);
```

Then instead of drawing arrays, we call `glDrawElements`. 
We just need to bind this EBO before that: 
```
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```
Primitive we want to draw in, number of elements that we are drawing, there are 6 indices, therefore we need to write 6. Then the type of those indices, obviously unsigned int. Then the offset in the EBO. 

Now, same with the VBO, we don't want to specify the EBO every single time, therefore we just add it to the VAO, that can contain that EBO. 

![[Pasted image 20240607142436.png]]

EBO - is really just the index, so that we don't have to rewrite the vertex data, and we can just use indexes for them. 

