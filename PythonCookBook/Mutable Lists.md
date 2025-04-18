Remember Remember Remember that Python doesn't copy by instinct, implicitly, and you have to start thinking that way as well. 

Stare and think about this code a lot:
```
testList = [1]
toAdd = testList # this is an implicit reference
noAdd = testList # this is also a reference

print(f"Testlist : {testList}, toAdd : {toAdd}, noAdd : {noAdd}")

"""
testList : [1, 2], toAdd : [1, 2], noAdd : [1, 2]
"""
```
Notice how they are all the same. 
Remember this always. 

### Getting a Copy, Not a Reference
```
testList = []
toAdd = testList.copy()
noAdd = testList.copy()

testList.append(1)
toAdd.append(2)
noAdd.append(3)

print(f"testList : {testList}, toAdd : {toAdd}, noAdd : {noAdd}")

"""
testList : [1], toAdd : [2], noAdd : [3]
"""
```
See how they are all different, `copy()` actually allocates some more memory here. 

### 2D Lists
Be careful with these because although the object might be copied to another location, having a 2d list, means that we have a reference in both those locations, rather than a reference to a reference. 
In order to properly copy this, we have to `deepcopy()` means that we copy all the internals over as well, and not just the references. We need `import copy`, as `list.copy()` exists, but `list.deepcopy()` does not exist!

```
import copy

testList = [[]]
toAdd = copy.deepcopy(testList)

testList[0].append(1)
toAdd[0].append(2)
print(f"TestList : {testList}, toAdd : {toAdd}")

"""
TestList : [[1]], toAdd : [[2]]
"""
```

This was really important for a problem that I was working on. 

### The Problem Where This Arose
Generate Combinations : Iterative
The recursive way of doing it is pretty simple: however the iterative : using a `queue`

See [[Iterative Practice and Notes]] where we use `stack` and `queue` in order to build an iterative notion of of a recursion

```
def generateCombinations(n : int, k : int) -> list: 
    queue = []
    queue.append([[], 1]) # append an empty list
    toRet = []
    print(queue)
    while queue: 
        current = queue.pop(0) #[[], n]
        toAdd = copy.deepcopy(current)
        noAdd = copy.deepcopy(current)


        if(len(current[0]) == k):
            toRet.append(current[0])    
            continue

        if current[1] > n: 
            continue
    
        #add N
        toAdd[0].append(toAdd[1])
        toAdd[1] += 1
        queue.append(toAdd)

        #no add N
        noAdd[1] += 1
        queue.append(noAdd)
        
    return toRet

print(generateCombinations(8, 2))
```

The deepcopy is very necessary due to the list in a list, the 2D list that is being used here.
[[Generate Combinations]]