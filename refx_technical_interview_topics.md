# refx technical interview topics

## data structures

vectors / arrays / strings

- [x] resizing dynamic-size vectors

hash tables

- [x] implement with array
- [x] chaining via list or tree
- [x] open addressing
- [ ] hash map vs hash table vs hash set

stacks & queues

- [x] implement a stack
- [x] implement a queue

linked lists

- [ ] singly-linked, circular, doubly-linked
- [x] creating a linked list
- [x] node insertion, deletion
- [x] runner technique — [cycle detection](https://www.hackerrank.com/challenges/ctci-linked-list-cycle/problem)

trees, tries

- [x] types of trees : binary / binary search / balanced / complete / full / perfect
- [x] tree search
- [x] tree insertion
- [ ] tree deletion
- [x] tries and querying strings — [hackerrank](https://www.hackerrank.com/challenges/ctci-contacts/submissions/code/56022959)
- [x] [is this a bst](https://www.hackerrank.com/challenges/is-binary-search-tree/submissions/code/57355955)

heaps

- [x] insert
- [x] extract min/max element
- [x] heap sort

graphs

- [x] adjacency list
- [x] adjacency matrix
- [x] object and pointers
- [ ] when each is preferred?

## algorithms

tree traversal

- [x] depth-first — pre-order, [in-order](https://leetcode.com/problems/binary-tree-inorder-traversal/description/), [post-order](https://leetcode.com/problems/binary-tree-postorder-traversal/description/)
- [x] breadth-first (see graph)
- [x] [serialize/deserialize a tree](http://www.geeksforgeeks.org/serialize-deserialize-binary-tree/) ([#2](https://www.programcreek.com/2014/05/leetcode-serialize-and-deserialize-binary-tree-java/)) — [my solution](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/description/)

graph traversal

- [x] depth-first
- [x] breadth-first — [grid bfs](https://www.hackerrank.com/challenges/ctci-connected-cell-in-a-grid/submissions/code/56079847), [knights](https://www.hackerrank.com/challenges/knightl-on-chessboard/problem)

searching

- [x] binary search

sorting

- [ ] bubble sort — [my solution](https://www.hackerrank.com/challenges/ctci-bubble-sort/submissions/code/55856902), also see [local work](/Users/mmt/Dropbox/Code/coding-interview)
- [ ] [merge sort](/Users/mmt/Dropbox/Code/coding-interview)
- [ ] quick sort

## concepts

big o

- [x] Big $O$, Big $\Omega$, Big $\theta$
- [x] time, space complexity
- [x] amoritization

bit manipulation

- [x] two's complement
- [x] get bit / set bit
- [ ] clear bit / update bit

recursion

- [ ] bottom up / top down approaches
- [ ] dynamic programming — taking recursive algo and finding overlapping subproblems
- [x] [fibonacci](https://www.hackerrank.com/challenges/fibonacci-modified/submissions/code/57124401)

math & logic

- [ ] primes
- [ ] probability & combinatorics

object-oriented design

- [x] singleton
- [x] factory

c++

- [x] [virtual functions](https://www.hackerrank.com/challenges/virtual-functions/submissions/code/56597288), [abstract classes](https://www.hackerrank.com/challenges/abstract-classes-polymorphism/submissions/code/56602585)
- [x] [templates](https://www.hackerrank.com/challenges/c-class-templates/submissions/code/56605805) & [specialization](https://www.hackerrank.com/challenges/cpp-class-template-specialization/submissions)
- [x] STL — [maps](https://www.hackerrank.com/challenges/cpp-maps/submissions/code/56611818)
- [x] c++14 features, shared_ptr, unique_ptr

concurrency

- [ ] processes
- [ ] threads
- [ ] mutexes & locks
- [ ] semaphores and monitors
- [ ] deadlock / livelock
- [ ] context switching

memory

- [ ] the stack
- [ ] the heap

## problem solving

steps

1. listen carefully
2. draw an example
3. state a brute force
4. optimize
5. walk through
6. implement
7. test

my questions

1. what assumptions can I make about the input?
2. what requirements does the output have?
3. do large example on board?
4. how many solutions are there, and what work do I have to do for a given solution?
5. what work do I have to do for each element?
6. how do I move to the next element?
7. how do I stop?
8. are there any special cases?
9. propose solution via pseudo-code in 5-7 mins
10. what data structures & classes do I need?
11. optimize via BUDs
12. test cases if time

optimization strategies

- look for bottlenecks, unnecessary work, duplicated work
- define the best conceivable runtime, compare with brute force to figure out optimal algorithm
- DIY, solve the physical version of the problem intuitively
- simplify and generalize
- base case and build
- data structure brainstorm

implementation strategies

- pseudo-code main loop
- use data structures liberally
- implement each separate piece as a function

