# refx technical interview data structures

## lists

advantages over arrays

- can't overflow
- insertions and deletions are simpler
- moving pointers is more efficient than moving large items

disadvantages to arrays

- require extra space for pointer fields
- do not allow efficient random access
- arrays have better memory locality and cache performance

other notes

- often advantageous to employ a runner technique
- where 2 pointers advance down the list, one ahead of the other

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

## trees

### kinds of trees

binary tree
:   tree where each node has have no more than 2 children

binary search tree
:   tree where each node has have no more than 2 children
:   where right child > node > left child, for all nodes

k-ary trees
:   tree where each node has have no more than k children

tries
:   an ordered tree 
:   the nodes contain no data themselves, but the position among
    the list of children determines their place
:   most often used to associate data with string keys
:   each level of node

### array representation

- can represent a linked list in an array / set of arrays
- 3 possibilities
    - 3 arrays (1 of vals, 2 of ints for next/prev)
    - 1 array of structs (of val, and ints for prev next)
    - 1 array with ints for val/prev/next stored sequentially

- int L stores index of head of list
- at each index, stores a value, and indices of next/prev
- keep a free list of unused node to allocate more

## heaps

- nearly complete binary tree
- completely filled on all levels except the lowest
- thus height of \\( log(n) \\)

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

- \\( N \times N \\) matrix
- stores 1 (or the weight) at index \\( (i,j) \\)
- if an edge exists between the \\(i\\)th and \\(j\\)th nodes
- \\( O(1) \\) insertion and deletion
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
- harder to verify a given edge \\((i,j)\\) is in the graph
- but less of an issue, because algorithms can be designed to avoid that

pointers and objects

- vertices stored in some structure or allocated individually
- each vertex contains both data and pointers to its adjacent nodes
- provided you have \\( i \\), easy to verify a edge \\((i,j)\\) in graph
- insertion and deletion dependent on the underlying vertex data storage

comparison

question                         | winner |
-------------------------------- | ------ |
faster to test if (x,y) in graph | matrix |
faster to find degree of node    | list   |
less memory on small graphs      | list (m+n) vs (n^2) |
less memory on big graphs        | matrix (small win) |
edge in sertion or deletion      | matrix O(1) vs O(d) |
farst to traverse graph          | list \theta(m+n) vs \theta(n^2) |
better for most problems         | list   |


