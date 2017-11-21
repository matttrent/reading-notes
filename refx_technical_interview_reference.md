# refx technical interview topics

## algorithmic complexity

### complexity

- Worst-case
- Best-case
- Average-case

### big-O notation

$ f(n) = O(g(n)) $

- upper bound on runtime
- there exists $ c $ such that $ c \cdot g(n) \geq f(n) $

$ f(n) = \Omega(g(n)) $

- lowerbound bound on runtime
- there exists $ c $ such that $ c \cdot g(n) \leq f(n) $

$ f(n) = \Theta(g(n)) $

- bound on runtime
- there exists $ c_1, c_2 $ such that $ c_2 \cdot g(n) \geq f(n) \geq c_1 \cdot g(n) $

### runtime classes

-   $ f(n) = 1 $ --- constant
-   $ f(n) = \log(n) $ --- logarithmic
-   $ f(n) = n $ --- linear
-   $ f(n) = n\log(n) $ --- superlinear
-   $ f(n) = n^2 $ --- quadratic
-   $ f(n) = n^3 $ --- cubic
-   $ f(n) = c^n $ --- exponential
-   $ f(n) = n! $ --- factorial

## lists

### comparison with arrays

advantages

- can't overflow
- insertions and deletions are simpler
- moving pointers is more efficient than moving large items

disadvantages

- require extra space for pointer fields
- do not allow efficient random access
- arrays have better memory locality and cache performance

other notes

- often advantageous to employ a runner technique
- where 2 pointers advance down the list, one ahead of the other

### sample code

definition

    typedef struct list {
        item_type item;
        struct list *next;
    } list;

searching --- $ O(n) $

    list *search_list(list *l, item_type x)
    {
        if (l == NULL) 
            return(NULL);
        if (l->item == x)
            return(l);
        else
            return( search_list(l->next, x) );
    }

insertion --- unsorted: $ O(1) $, sorted: $ O(n) $

    void insert_list(list **l, item_type x)
    {
        list *p;
        p = new list;
        p->item = x;
        p->next = *l;
        *l = p;
    }

deletion ---- $ O(n) $

    delete_list(list **l, item_type x)
    {
        list* temp;
        list* pred = NULL;
    
        if( *l->item == x ) {
            temp = (*l)->next;
            *l = temp;
            free temp;
            return;
        }
    
        temp = *l;
        while( temp->next != NULL ) {
            if( temp->next->item == x ) { 
                pred = temp;
                break;
            }
            temp = temp->next;
        }
    
        if( pred != NULL ) {
            pred->next = pred->next->next;
            free pred->next;
        }
    }


## hashtables

most useful topic on this list

- 2 major kinds kinds
    - chaining (linked list)
    - open addressing (resolve collisions using probing)

chaining

- each index of the hash table contains a linked list of items
- insert/delete items from that list
- search along those items to find

open addressing

- hashed items occupy elements of array directly
- (implementing a hash table using only an array)
- when inserting, if desired index is occupied, search to find an empty one

insertion:

    HASH-INSERT(T,k)
        i=0
        repeat
            j = h(k,i)
            if T[j] == NIL
                T[j] = k
                return j
            else i = i + 1
        until i == m
        error “hash table overflow”

deletion:

- is quite difficult
- have 2 options:
    - either need to rehash all values
    - can use a `deleted` flag instead of `nil` for array entry
        - then insert can be modified to also add data at that point
- generally, chaining is more often used when keys must be deleted

probing functions

- linear probing $ h(k,i) = (h'(k) + i) \mod m $
- quadratic probing $ h(k,i) = (h'(k) + c_1i + c_2i^2) \mod m $
- double hashtag $ h(k,i) = (h_1(k) + ih_2(k)) \mod m $

## trees

### kinds of trees

binary tree

- tree where each node has have no more than 2 children

binary search tree

- tree where each node has have no more than 2 children
- where right child > node > left child, for all nodes

k-ary trees

- tree where each node has have no more than k children

tries

- an ordered tree 
- the nodes contain no data themselves, but the position among the list of children determines their place
- most often used to associate data with string keys
- each level of node

### binary trees

#### sample code

definition

    typedef struct tree {
            item_type item;
            struct tree *parent;
            struct tree *left;
            struct tree *right;
    } tree;

search --- $ O(\log(n)) $

    tree *search_tree(tree *l, item_type x)
    {
        if (l == NULL) return(NULL);
        if (l->item == x) return(l);
        if (x < l->item)
            return( search_tree(l->left, x) );
        else
            return( search_tree(l->right, x) );
    }

insertion --- $ O(\log(n)) $

    insert_tree(tree **l, item_type x, tree *parent)
    {
        tree *p;                         /* temporary pointer */
        if (*l == NULL) {
           p = malloc(sizeof(tree)); /* allocate new node */
           p->item = x;
           p->left = p->right = NULL;
           p->parent = parent;
           *l = p;
           return;
        }
        if (x < (*l)->item)
           insert_tree(&((*l)->left), x, *l);
        else
            insert_tree(&((*l)->right), x, *l);
    }

deletion --- $ O(\log(n)) $

3 cases:

no children

- simply remove node

1 child

- link node's parent to its child
- remove node

2 children

- take R, in-order successor of current node N
- in-order successor is left-most child of its right subtree
- replace N.val with R.val
- delete R

### array representation

- can represent a linked list in an array / set of arrays
- 3 possibilities
    - 3 arrays (1 of vals, 2 of ints for next/prev)
    - 1 array of structs (of val, and ints for prev next)
    - 1 array with ints for val/prev/next stored sequentially
- int L stores index of head of list
- at each index, stores a value, and indices of next/prev
- keep a free list of unused node to allocate more

sample code

    ALLOCATE-OBJECT()
        if free == NIL
            error “out of space”
        else x = free
            free = x.next
        return x
    
    FREE-OBJECT(x)
        x.next = free
        free = x

### traversal

#### depth-first traversal

explore as deep as possible before exploring next node on that level

pre-order:

    visit( root )
    traverse_subtree( left )
    traverse_subtree( right )

in-order:

    traverse_subtree( left )
    visit( root )
    traverse_subtree( right )

post-order:

    traverse_subtree( left )
    traverse_subtree( right )
    visit( root )

#### breadth-first traversal

explore explore all nodes on a given level before going deeper

    breadth-first(root) 
        q = queue()
        q.enqueue(root)
        while not q.empty() do
            node := q.dequeue()
            visit(node)
            if node.left != null
                q.enqueue(node.left)
            if node.right != null
                q.enqueue(node.right)

#### tree balancing (with avl)

searching --- $ O(\log(n)) $

- performed exactly like a normal binary search tree

insertion --- $ O(\log(n)) $

- insert like normal binary search tree
- retrace the path back up the tree to the root adjusting balance factors
- if balance factor == -1/0/+1: unchanged, can stop
- if balance factor == -2/+2: rotation necessary

rotations

      B                   D
     / \                 / \
    A   D    < --- >    B   E
       / \             / \
      C   E           A   C

rebalance

- 4 rotation cases, 2 of which are symmetric to the other 2
- let P be the root of the unbalanced subtree
- with R and L denoting the right and left children of P respectively

Right-Right case and Right-Left case:

- If the balance factor of P is -2 then the right subtree outweighs the left subtree of the given node, and the balance factor of the right child (R) must be checked. The left rotation with P as the root is necessary.
- If the balance factor of R is -1, a single left rotation (with P as the root) is needed (Right-Right case).
- If the balance factor of R is +1, two different rotations are needed. The first rotation is a right rotation with R as the root. The second is a left rotation with P as the root (Right-Left case).

Left-Left case and Left-Right case:

-   If the balance factor of P is 2, then the left subtree outweighs the right subtree of the given node, and the balance factor of the left child (L) must be checked. The right rotation with P as the root is necessary.
-   If the balance factor of L is +1, a single right rotation (with P as the root) is needed (Left-Left case).
-   If the balance factor of L is -1, two different rotations are needed. The first rotation is a left rotation with L as the root. The second is a right rotation with P as the root (Left-Right case).

deletion --- $ O(\log(n)) $

- delete like a normal binary search tree
- retrace the path back up the tree to the root adjusting balance factors
- if balance factor == -1/+1: unchanged, can stop
- if balance factor == 0: tree changed depth, continue to root
- if balance factor == -2/+2: rotation necessary

## heaps

- nearly complete binary tree
- completely filled on all levels except the lowest
- thus height of $ log(n) $

following relation for accessing other nodes:

    parent(i)
        return floor(i/2)
    
    left(i)
        return 2i
    
    right(i)
        return 2i+1

2 kinds

- min-heap --- `A[parent[i] <= A[i]`
- max-heap --- `A[parent[i] >= A[i]`

operations on heaps

`max-heapify`

- $ O(\log n) $
- maintains max-heap property
- assumes `left(i)` and `right(i)` are valid max-heaps
- swaps `i` into place to preserve heap property

`build-max-heap`

- $ O(n) $
- produces max-heap from unordered input array

`heapsort`

- $ O(n\log n) $
- sorts an array in place

## searching

binary search

    int binarySearch( int[] a, int x, int low, int high ) {
        if( low > high )
            return -1;
    
        int mid = ( low + high ) / 2;
        if( a[mid] < x )
            return binarySearch( a, x, mid+1, high );
        else if( a[mid] > x )
            return binarySearch( a, x, low, mid-1 );
        else
            return mid;
    }

## sorting

comparison sorting
- compare pairs of elements to determine their ordering
- must always take $ O( n \log n ) $ to sort $ n $ elements

non-comparison sorting

- use operations other than comparison
- capable of $ O(n) $
- searching a list is slow, lack of non-random access

### bubble sort

- works by repeatedly stepping through the list to be sorted
- comparing each pair of adjacent items
- and swapping them if they are in the wrong order
- worst-case and average complexity
    - runtime $ О(n^2) $
    - memory $ O(1) $

pseudocode

    void bubbleSort( A : list of sortable items )
       repeat     
         swapped = false
         for i = 1 to length(A) - 1 inclusive do:
           /* if this pair is out of order */
           if A[i-1] > A[i] then
             /* swap them and remember something changed */
             swap( A[i-1], A[i] )
             swapped = true
           end if
         end for
       until not swapped
    end procedure

### mergesort

- __divide and conquer__ -- partition elements into 2 groups
    - subproblems can be divided across multiple processor
    - performs well in cases where data can't fit in memory
- sorting those 2 sub-problems, then merging the results
- $ O(n\log n) $ complexity
    - halving the list each time = $ O(\log n) $ recursions
    - interleaving the 2 lists is $ O(n) $
- requires extra $ n $ memory to hold results of merge

interleaving operation

    Mergesort(A[1, n])
        Merge( MergeSort(A[1, floor(n/2)]), 
               MergeSort(A[floor(n/2) + 1, n]) )

pseudocode

```C
mergesort(item_type s[], int low, int high)
{
    int i;                  /* counter */
    int middle;             /* index of middle element */
    if (low < high) {
        middle = (low+high)/2;
        mergesort(s,low,middle);
        mergesort(s,middle+1,high);
        merge(s, low, middle, high);
    } 
}

merge(item_type s[], int low, int middle, int high)
{
    int i;                  /* counter */
    queue buffer1, buffer2; /* buffers to hold elements for merging */
  
    init_queue(&buffer1);
    init_queue(&buffer2);
  
    for (i=low; i<=middle; i++) enqueue(&buffer1,s[i]);
    for (i=middle+1; i<=high; i++) enqueue(&buffer2,s[i]);
  
    i = low;
    while (!(empty_queue(&buffer1) || empty_queue(&buffer2))) {
        if (headq(&buffer1) <= headq(&buffer2))
            s[i++] = dequeue(&buffer1);
        else 
            s[i++] = dequeue(&buffer2);
    }
    while (!empty_queue(&buffer1)) s[i++] = dequeue(&buffer1);
    while (!empty_queue(&buffer2)) s[i++] = dequeue(&buffer2);
}
```

### quicksort

- divide and conquer by partitioning
- select pivot $ p $, and create high pile and low pile
- resulting in:
    - p ends up in its exact sorted position
    - all elements $ < p $ in low pile
    - all elements $ > p $ in high pile
    - recurse on 2 piles to sort them

complexity of $ O( n \log n ) $ on average case

- partition takes $ O( n ) $
- repeated for each of the $ O( \log n ) $ partition recursions
- however $ O( \log n ) $ recursion depends on size of piles
- if partions are not equal, performance drops 
- complexity rises to $ O(n^2) $ for sorted arrays
- can address somewhat by randomizing input before sorting
- requires $ \log n $ extra memory (for the stack)

comparison with merge sort

- can compare in place, does not need additional memory
- however, performs very poorly on nearly-sorted data

pseudocode

    quicksort(item_type s[], int l, int h)
    {
        int p;                  /* index of partition */
        if( (h-l) > 0 ) {
            p = partition( s, l, h );
            quicksort( s, l, p-1);
            quicksort( s, p+1, h);
        } 
    }
    
    int partition(item_type s[], int l, int h)
    {
        int i;              /* counter */
        int p;              /* pivot element index */  
        int firsthigh;      /* divider position for pivot element */
          
        p = h;
        firsthigh = l;
        for( i=l; i<h; i++ )
            if( s[i] < s[p] ) {
                swap( &s[i], &s[firsthigh] );
                firsthigh ++;
            }
        swap( &s[p], &s[firsthigh] );
        return(firsthigh);
    }

### bucket sort

- assumes input is drawn from a uniform distribution
- average runtime of $ O(n) $
- divides range of data in $ k $ equal-sized sub-intervals
- partitions array into the $ k $ buckets
- sorts each bucket independently, using bucket or other sort
- concatonates buckets
- requires $ k+n $ additional space

pseudocode

    function bucketSort(array, n) is
        buckets ← new array of n empty lists
        for i = 0 to (length(array)-1) do
            insert array[i] into buckets[msbits(array[i], k)]
        for i = 0 to n - 1 do
            nextSort(buckets[i])
        return the concatenation of buckets[0], ...., buckets[n-1]

the function `msbits(x,k)` returns the `k` most significant bits of `x`: 

    (floor(x/2^(size(x)-k))); 

## graphs

- 2nd most useful after hashtables
- 3 ways to implement a graph
    - objects and pointers
    - matrix
    - adjacency list
    - know pros / cons of each
- traversal
    - depth-first search
    - breadth-first search
    - compexity of each and tradeoffs
    - how to implement each one
- fancier stuff _(if time)_
    - djikstra's
    - A*

### ways to implement a graph

matrix

- $ N \times N $ matrix
- stores 1 (or the weight) at index $ (i,j) $
- if an edge exists between the $i$th and $j$th nodes
- $ O(1) $ insertion and deletion
- if nodes contain data, store in separate array
- reduces to triangular matrix for undirected graph
- wasteful if small amount of edges to nodes

adjacency list

- use linked lists to store the neighbors adjacent to each node
    - use array equal to length of nodes
    - each array entry is a linked list of indices of adjacent nodes
- if nodes contain data, store in separate array
- much more efficient representation of sparse graphs
- graph insertion / deletion runtime depend on array insertion / deletion
- harder to verify a given edge $(i,j)$ is in the graph
- but less of an issue, because algorithms can be designed to avoid that

pointers and objects

- vertices stored in some structure or allocated individually
- each vertex contains both data and pointers to its adjacent nodes
- provided you have $ i $, easy to verify a edge $(i,j)$ in graph
- insertion and deletion dependent on the underlying vertex data storage

comparison

| question                         | winner                              |      |
| -------------------------------- | ----------------------------------- | ---- |
| faster to test if (x,y) in graph | matrix                              |      |
| faster to find degree of node    | list                                |      |
| less memory on small graphs      | list (m+n) vs (n^2)                 |      |
| less memory on big graphs        | matrix (small win)                  |      |
| edge insertion or deletion       | matrix $O(1)$ vs $O(d)$             |      |
| farst to traverse graph          | list $\theta(m+n)$ vs $\theta(n^2)$ |      |
| better for most problems         | list                                |      |

additonal type: objects and pointers

- each node contains both its data and pointers to adjacent nodes
- not particularly beneficial that I can see

### traversal

Each vertex will exist in one of three states:

undiscovered

- the vertex is in its initial, virgin state.

discovered

- the vertex has been found, but have not yet checked all its incident edges

processed

- the vertex after we have visited all its incident edges.

- the difference between breadth-first (BFS) and depth-first (DFS) is the 
    order in which they explore vertices
- BFS is defined by a queue, exploring oldest unexplored nodes first
- DFS is defined by a stack, exploring newest unexplored nodes first

#### breadth-first search

notes

- runs in $ O(V + E) $ for an $ V $ vertex, $ E $ edge graph

advantages

- will never get trapped exploring a useless path forever
- if there is a solution, will definitely find it
- if there are multiple solutions, BFS can find the minimal one requiring  the 
  least number of steps

disadvantages

- memory requirements
- the amount of memory is proportional to the number of nodes stored
- space complexity of BFS is $ O(b^d) $
- if solution is far away from the root, will consume lot of time

pseudo code

    procedure BFS(G,v):
        create a queue Q
        enqueue v onto Q
        mark v
        while Q is not empty:
            t ← Q.dequeue()
            if t is what we are looking for:
                return t
            for all edges e in G.adjacentEdges(t) do
                u ← G.adjacentVertex(t,e)
                if u is not marked:
                     mark u
                     enqueue u onto Q

#### depth-first search

- runs in $ \Theta(V + E) $ for an $ V $ vertex, $ E $ edge graph
- can be implemented recursively
- eliminates need for explicit stack
- organizes vertices by entry/exit times
- and edges into tree and back edges 
- what gives DFS its real power

advantages

- memory requirement is linear with respect to the search graph
- time complexity to depth d is $O(b^d)$
    - generates the same set of nodes as BFS, but in a different order
- if DFS finds solution without exploring much in a path 
- then the time and space it takes will be very less

disadvantages

- possibility that it may go down the left-most path forever. 
- even a finite graph can generate an infinite tree
- DFS is not guaranteed to find the solution.
- no guarantee to find a minimal solution, if more than one solution exists

pseudo code

    procedure DFS(G,v):
        label v as explored
        for all edges e in G.adjacentEdges(v) do
            if edge e is unexplored then
                w ← G.adjacentVertex(v,e)
                if vertex w is unexplored then
                    label e as a discovery edge
                    recursively call DFS(G,w)
                else 
                    label e as a back edge

## concurrency

### processes

- process is scheduled and maintained by the kernel
- have separate virtual address spaces
- to communicate with each other must use explicit interprocess communication 
  (IPC) mechanism

properties owned by a process

- image of the program's machine code
- memory for executable code, process-specific data, call stack and a heap
- OS descriptors of allocate resources, such as 
    - file descriptors
    - handles
    - other data sources and sinks
- security attributes, such as owner and permissions
- state (context), such as registers, physical memory addressing, etc.
    - typically stored in registers when the process, and memory otherwise

pros and cons

- have a clean model for sharing state between parents and children
- file tables are shared and user address spaces are not
- it is impossible for one process to overwrite the virtual memory of another
- separate address spaces make it difficult for processes to share state
- process tend to be slower because the overhead for process control and IPC

inter-process communication (IPC)

- file
- signal
- socket
- message queue
- pipe
- named pipe
- semaphore
- shared memory
- message passing
- memory-mapped file

### threads

- thread is the smallest sequence of instructions that can be managed independently by the operating system scheduler

threads differ from processes in that:

- processes are typically independent, threads exist as subsets of a process
- processes carry considerably more state information than threads, whereas 
  threads within a process share process state, memory and other resources
- processes have separate address spaces, threads share their address space
- processes interact only through system-provided inter-process communication
- same-process thread context switching is faster than between processes

### mutexes and locks

- enforces access limits to a resource when there are multiple threads
- designed to enforce a mutual exclusion policy
- only one thread may have control of the lock at once
- exclusion is generally voluntary
    - can be manditory
    - enforced by throwing an exception if not owning the lock
- most block the thread requesting the lock until it can access the lock
    - very efficient if threads are only blocked for a short period of time
    - avoids the overhead of operating system process re-scheduling
    - wasteful if the lock is held for a long period of time

### semaphores and monitors

semaphores

- similar to a mutex, except more than 1 thread can use at once
- doesn't keep track of which resources are free, only how many

library analogy

- library has 10 study rooms, to be used by one student at a time
- students make request at front desk
- when student is done, return to desk indicate room is free
- if no rooms free, students must wait
- desk doesn't keep track of who occupies what, only how many unoccupied
- increases / decreases number based on requests
- students cannot book room ahead of time

implementation

- semaphore S -- value is number of currently available units
- 2 functions:
- V (signal) increments
    - if pre-increment value was negative
    - transfers a blocked process from waiting queue to ready queue 
    - at which point it is immediately claimed
- P (wait) decrements
    - if S now negative, sleeps until a resource becomes available
    - added to semaphore's queue

monitors

- object intended to be used by more than one thread
- all methods of the monitor are executed with mutual exclusion
- at any time, at most one thread may be executing any of its methods

pseudocode

    function V(semaphore S, integer I):
        [S ← S + I]
    
    function P(semaphore S, integer I):
        repeat:
            [if S >= I:
                S ← S - I
                break]

### deadlock / livelock

deadlock

- where two threads wait for a lock that another thread holds
- and will not give up until it has acquired the other lock

livelock

- same principle as deadlock, but threads constantly swap locks
- and neither of them progress

requirements of deadlock

- mutual exclusion
- hold and wait -- process has resources and request more without releasing
- no processes can forcibly take another's resources
- circular wait -- 2+ processes are each waiting on the others resources

### context switching

mutual exclusion

- general concept that only one concurrent process (or thread) has access to shared memory at a given time
- all others must wait until they can take possesion of it, and signal for others to wait
- both hardware and software solutions

context switch

- state of the first process must be saved somehow
- state includes all the registers that may be used
    - especially the program counter
    - any other operating system specific data that may be necessary
    - usually stored in a process control block (PCB)
- to switch processes, the PCB for the first process must be created and saved
- the OS has effectively suspended the execution of the first process
- can now load the PCB and context of the second process
    - program counter from the PCB is loaded
    - execution can continue in the new process

hardware

-   requires atomic test and set instruction
-   in one instruction
    - check value of a given address
    - and set it if it passes some conditional
-   _only_ way to ensure a process cannot be interrupted while grabbing shared resource

## memory

## the stack

- the memory set aside as scratch space for a thread of execution
- when a function is called, a block is reserved on the top of the stack for 
  local variables and some bookkeeping data. 
- when the function returns, the block becomes unused
- always reserved in a LIFO order
- freeing a block from the stack is nothing more than adjusting one pointer

## the heap

- memory set aside for dynamic allocation
- no enforced pattern to allocation and deallocation from the heap
- more complex to track which parts of the heap are allocated or free
- custom heap allocators to tune performance for different usage patterns

- each thread gets a stack
- there's typically only one heap for the application
- isn't uncommon to have multiple heaps for different types of allocation

## bit manipulations

    x ^ 0s = x      x & 0s = 0s         x | 0s = x
    x ^ 1s = ~x     x & 1s = x          x | 1s = 1s
    x ^ x  = 0s     x & x  = x          x | x  = x
    
    bool getBit( int num, int i ) {
        return ( (num & (1 << i)) != 0 );
    }
    
    int setBit( int num, int i ) {
        return num | (1 << i );
    }
    
    int clearBit( int num, int i ) {
        int mask = ~(1 << i );
        return num & mask;
    }
    
    int clearBitsMSThroughI( int num, int i ) {
        int mask = (1 << i) - 1;
        return num & mask;
    }
    
    int clearBitsIThrough0( int num, int i ) {
        int mask = ~(( 1 << (i+1)) - 1);
        return num & mask;
    }
    
    int updateBit( int num, int i, int v ) {
        mask = ~( 1 << i );
        return ( num & mask ) | ( v << i );
    }

## math

### counting

binomial coefficient

$$ \binom{n}{k} = \binom{n}{n-k} = \frac{n!}{k!(n-k)!} $$

### probability

probability of A and B

$$ P( A \cup B ) = P( B | A )P(A) $$

probability of A or B

$$ P( A \cap B ) = P(A) + P(B) - P(A \cup B)$$

### discrete math 101 reference?

## object-oriented design

singleton class

- ensures only one instance
- class has private static copy of itself
- class has static `get()` method returns copy, creating if necessary

factory method

- method that returns a new instance of the class
- returns pointer of common parent class interface
- hide class creation details from extrenal functions
- 2 usage patterns
- overloaded method where subclasses choose which class to instantiate
- concrete method where user provides type of class

## other

### NP-completeness

- NP refers to "nondeterministic polynomial time
- set of all decision problems for which
    - the instances where the answer is "yes" 
    - have efficiently verifiable proofs of the fact that the answer is "yes" 
    - these proofs have to be verifiable in polynomial time
- no polynomial-time algorithms are known for solving them
- although they can be verified in polynomial time

examples

- graph coloring
- travelling salesman
- knapsack problem
- clique problem
- hamiltonian path
- circuit satisfiability

### recursion, memorization & dynamic programming

- lots of applications require some sort of traversal of a graph of possible sub-problems
- first off, realize that most problems can be broken into sub-problems, and those sub-problems often overlap
- recursion is a depth-first traversal of the space of sub-problems
- solving
  - bottoms-up, given the base case, how do you get to the n+1.  recommended because it's concrete
  - top-down, more abstract.  how do you break a problem down into N sub-problems
- cache results in one depth-first traversal, so the next traversal can use them
- **memoization / dynamic programming:** solve complex problems by breaking them down into simpler subproblems, cache the results

pseudocode for `factorial()`

```python
def factorial(n):
 	if n == 0:
    	return 1	# base case
  	return factorial(n-1) * n
```

pseudocode for memoized `factorial()`

```python
def factorial(n, _cache={}):
	if n == 0:
    	return 1	# base case
  	elif n in _cache:
  		return _cache[n]

  	result = factorial(n-1) * n
	_cache[n] = result
    return result
```

