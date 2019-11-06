# Semantic Analysis

- Used to catch the rest of the errors
- Things like `"hello" / 7` would be found here

## Type-checking VS Type-inference

- Inference
  - Gives types to variables
  - Dynamic typing

### Dynamic VS Static Typing

- The difference is when type checking happens
- In dynamically typed languages, type-checking happens at run-time, at the last possible moment, e.g just before we execute `x + 2` we check that `x` is an integer
  - Slower since type checking at run-time, errors only caught at run-time when it's too late
  - There are static typed versions of dynamically typed languages, e.g javascript has `typescript` and `flow`
- In statically typed languages, type-checking happens at compilation
- Simple imperative language type checking:

```text
a ::= Unit|Int|Bool|List<a>
```

- Examples in the language
  - Program: `3` has type `Int`
  - Program: `true` has type `Bool`
  - Program: `3 + 4` has type `Int`
  - Program: `3 + x` has type `Int`, if x is also of type `Int`, otherwise it's a type error
- We type-check using assumptions about the types of free variables, need to split the program into parts (divide and conquer)
- We write `Gamma ⊢ P:alpha`
  - Means that program P has type alpha under the assumptions as given by Gamma
    - If `Gamma(x) = alpha` then `Gamma ⊢ x : alpha`
    - If `Gamma ⊢ P : Int` and also `Gamma ⊢ Q : Int` then `Gamma ⊢ P + Q : Int`
  - Writing that out for large programs is tedious
    - `(Assumption₁ ... Assumption`<sub>`n`</sub>`) / Conclusion`
  - Do this recursively, so start off with a high level statement, then break down the assumptions until they've been broken into their atomic parts
