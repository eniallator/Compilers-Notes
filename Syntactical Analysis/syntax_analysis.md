# Syntax Analysis

- We want to
  - Specify that a program is syntactically correct programs are correct
  - Check that a input program is syntactically correct according to the specification, and output an AST
- FSAs and REs can't count
  - So can't for example balance brackets

## Context Free Grammars

- Context free grammars will specify what the syntax of a language is
- They have variables on the left, and then the variables will be assigned to strings on the right where the right can consist of other variables/strings which can lead to recursive definitions

### CFG Example

- `A = {a, b}`
- `V = {S}`
- The initial variable is `S`
- `R` contains only 3 reductions
  - `S -> a S b`
  - `S -> S S`
  - `S -> epsilon`
- Note that epsilon is the empty string
- Now the CFG is `(A, V, S, R)`

### Language Accepted By A CFG

- The language of a CFG is the set of all strings over the alphabet of the CFG that can be arrived at by rewriting from the initial variable
