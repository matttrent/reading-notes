# refx technical interview strategies

1. resolve ambiguity
   - what assumptions can I make about the input?
   - what requirements does the output have?
   - in the range of 3-5 questions, make all assumptions explicit
2. do a largish example
3. base questions
   - how many solutions are there, and what work do I have to do for a given solution?
   - what work do I have to do for each element?
   - how do I move to the next element?
   - how do I stop? are there any special cases?
4. brute force solution
   - state algorithmic complexity, ask if what they expect
5. optimize
   - are you making the right tradeoffs?  are you using all the info?
   - optimize via bottlenecks, unnecessary work, duplicate work
   - think best conceivable runtime
   - pattern match?  simplify and generalize?  base case & build?  data structures?
6. write pseudo code
   - spend 5-7 minutes on pseudocode
   - walk-through solution
7. write actual code
  - write main loop, with each subproblem as a method
  - use as many data structures & classes as possible
8. test
   - invalid data
   - base cases


## 5 steps to answer a question

1. ask to resolve ambiguity
    - in the range of 3-5 questions
    - make sure to make all your assumptions explicit
2. design algorithm
    - state algorithmic complexity, ask if what they expect
    - are you making the right tradeoffs?
    - are you using all the info?
3. write pseudo code
    - assume valid input / error checking?
    - ask if they would like you to go to the whiteboard
    - spend 5-7 minutes on pseudocode
4. write actual code
    - use as many data structures as you can
5. test
    - general cases
    - extreme cases
    - user error

## 5 algorithmic approaches

### 1. examplify

- write out specific examples
- derive a rule

### 2. pattern matching

- consider related problems and their algorithms
- modify that solution to match this problem

### 3. simplify and generalize

- change a constraint (size, datatype) to simplify
- choose a solution to the simpler problem
- generalize the solution to the original problem

### 4. base case and build

- solve n=1
- solve n=2,3 given n=1
- work up to general rules
- often yields recursive solution

### 5. data structure brainstorm

- run through all the data structures you can
- do a thought experiment on how you would proceed with that

## properties of good code

- correct
- efficient
- simple
- readable
- maintainable

### other rules

- use data structures generously
- appropriate code reuse
- modular
- flexible and robust
- error checking