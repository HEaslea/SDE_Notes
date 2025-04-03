In C++, we use something called jagged arrays when we want to have multidimensional arrays: 
```
int rows = 3; 
// and array of pointers to ints
int** jaggedArray = new int*[rows];

jaggedArray[0] = new int[2]; // array 1 has 2 columns etc.
jaggedArray[1] = new int[3];
jaggedArray[2] = new int[4];

// filling out those columns
jaggedArray[0][0] = 1; jaggedArray[0][1] = 2; jaggedArray[1][0] = 3; jaggedArray[1][1] = 4; jaggedArray[1][2] = 5; jaggedArray[2][0] = 6; jaggedArray[2][1] = 7; jaggedArray[2][2] = 8; jaggedArray[2][3] = 9;
```
These are obviously jagged, as there are a different number of columns per row. 
In C# we have both **rectangular** arrays and **jagged** arrays: 

```
int[,] rectangularArray = new int[3, 3]; // giving us a 3x3 array
// 
rectangularArray[0, 0] = 1;
rectangularArray[0, 1] = 2;
rectangularArray[0, 2] = 3;
// here we are changing the first row. 


```
Remember that all of these are row then column. 
Rectangular arrays are more matrices, then arrays of arrays, they should probably be treated as such. 

This means that jagged arrays are arrays of arrays, therefore they can have variable lengths. 

Meaning, like in C#, we can write something like this: where we add arrays to the array already: 
```
int[][] jaggedArray = new int[3][];
jaggedArray[0] = new int[2];  // Row 0 has 2 columns
jaggedArray[1] = new int[3];  // Row 1 has 3 columns
jaggedArray[2] = new int[1];  // Row 2 has 1 column

// Initialize values
jaggedArray[0][0] = 1; jaggedArray[0][1] = 2;
jaggedArray[1][0] = 3; jaggedArray[1][1] = 4; jaggedArray[1][2] = 5;
jaggedArray[2][0] = 6;

// Print values
for (int i = 0; i < jaggedArray.Length; i++)
{
    for (int j = 0; j < jaggedArray[i].Length; j++)
    {
        Console.Write(jaggedArray[i][j] + " ");
    }
    Console.WriteLine();
}
```

I like that in C# we can have more matrix like containers here. 

# The Range Operator
## Index from End Operator
```
int[] numbers = {10, 20, 30, 40, 50};

Console.WriteLine(numbers[^1]); // will output 50
Console.WriteLine(numbers[^2]); // will output 40, duh
```

