The prefetch, is like the branch prediction, it will try and predict what other data we will need to put into the cache line, when we ask for something from memory. 

Remember this: that reading memory is constricted on latency (to get memory before we can move on), and writing is based on bandwidth (how much memory that we have to write, compared to how big the bus is). 

- **Cache Lines**: cache divided into cache lines, they are typically 64 bytes. Whenever we load anything from memory, we carry a whole cache line into cache (about 32KB for L1). Not just the element that we have decided to access. 


### Access Patterns
How data is usually accessed in memory affects caching efficiency. 

- Sequential Access : self explanatory, if this aligns well with cache line sizes making it highly efficient. 
- Strides Access : if the stride is going to be bigger than the cache line, then that might be an error, and cause a cache miss. 
- Random Access : will often lead to cache misses. The CPU cannot anticipate what we are going to need next. 
#### Prefetching
CPU attempts to guess and load data from memory to cache before it's explicitly requested. 
Reducing latency by having the data already in cache. 
- Hardware Prefetching: Managed by CPU's hardware prefetcher, detecting access patterns, automatically loads anticipated data into cache. 
- Software Prefetching: at the software and compiler level, user specific instruction to tell the CPU to load certain data ahead of time. 
###### Prefetching Strategies
 - Next line prefetching: Prefetches the next cache line following a current access. 
 - Stride Prefetching: Detecting regular striding, and prefetches accordingly.
 - Adaptive Prefetching: Just adapts to what it says. 

 ########### **Cache Miss Types and Costs**

- **Cache Misses**: Occur when the CPU requests data that is not currently in cache. There are three types:
    - **Compulsory Misses**: Occur the first time a data element is accessed, as it was not previously loaded.
    - **Capacity Misses**: Happen when the cache doesn’t have enough space to hold all required data. Some data may be evicted to make room for new data, leading to cache misses if it’s accessed again.
    - **Conflict Misses**: Arise when data mapping results in multiple items competing for the same cache line, forcing repeated evictions and reloads even when space is available in other cache areas.
#### **Practical Examples of Caching and Prefetching**

- **Sequential Array Access**:
    - When iterating over an array from start to end, the CPU prefetcher recognizes this as a sequential pattern. It loads the current cache line plus additional lines in advance, meaning that most subsequent accesses will hit the cache, minimizing trips to main memory.
- **Reverse Array Access**:
    - When traversing an array in reverse, the CPU detects this backward pattern and prefetches previous cache lines instead. This adaptation minimizes latency but is often less efficient because prefetchers are typically optimized for forward access patterns.
- **Loop Optimizations**:
    - Compilers can insert software prefetch instructions in loops with predictable access patterns to reduce cache misses, especially in cases where data might not fit within the cache due to large array sizes or complex strided access patterns.


#### **Performance Implications**

- **Latency vs. Bandwidth**:
    - _Latency_ is the delay before data begins to transfer (important in reads). Low latency is essential for random access and for applications requiring quick data retrieval.
    - _Bandwidth_ refers to the rate of data transfer (important in writes). High bandwidth is crucial for applications that involve bulk data movement, like video processing.
- **Prefetching and Cache Utilization**:
    - Effective prefetching reduces latency by keeping data ready in cache, but excessive or incorrect prefetching can waste bandwidth (by loading unneeded data) and lead to _cache pollution_, where useful data is evicted in favor of unneeded prefetched data.

#### **Optimizing Code for Cache Performance**

- **Use of Contiguous Data Structures**: Data structures like arrays are cache-friendly because elements are stored contiguously in memory, supporting sequential access.
- **Loop Optimizations**: Writing code that accesses data sequentially within loops enhances prefetching efficiency, ensuring data is in cache as much as possible.
- **Avoiding Large Strides**: Accessing data with small strides that fit within cache lines is more efficient, as large strides often waste cache space.
- **Minimizing Cache Conflicts**: By reorganizing data or using data structures that minimize collisions in the cache, cache efficiency can be improved.