# Real Code Generator

- Going to compile from a language which has:
  - Integers only
  - Procedures that can be recursive (with args)
    - Pretty difficult to do!
- Going to be a very similar CFG to the coursework
- Assume that the very first declaration takes 0 arguments
- First declaration is always the entry point for the program
- Procedure names must be disctinct, and distinct from all variable names
- All variable names inside a declaration are distinct
- Always assume the number of arguments in a call matches the number of parameters a declaration takes
- AKA we assume lexical/syntax/semantic analysis
- Example program:

```python
def myFirstProg() = fib(3);

def fib(n) =
  if n = 0 then
    1
  else if n = 1 then
    1
  else
    fib(n - 1) + fib(n - 2)
```

- We only use a tiny fraction of RISC-V's power to keep the compiler easy
- The accumulator:
  - First arg assumed to be in the accumulator
  - All remaining args sit on the top of the stack
  - Result of operation is stored in the accumulator
  - After finishing the operation, all args are removed from the stack
- The code generator presented will guarantee that all of these assumptions hold
- RISC-V conventions will be used
  - `a0` is accumulator
  - `sp` is stack pointer
  - Stack pointer points to the next free space after the top of the stack
  - Stack grows downwards

## Data Type Assumptions

- Source language has integers and will translate them into 32 bit RISC-V ints
- For simplicity, don't have to worry about over/underflow of arithmetic operations
- Don't worry about alignment

## Code Generation

- We compile by recursively walking down an AST so we want to write the following:

```python
def genExp(e : Exp) =
  if e is of form
    IntLiteral (n) then ...
    Variable (x) then ...
    If(cond, thenBody, elseBody) then ...
    Add (l, r) then ...
    Sub (l, r) then ...
    Call (f, args) then ...
```

### Integer Literals

- Starting with the simplest case

```python
def genExp(e : Exp) =
  if e is of form
    IntLiteral (n) then
      'li a0 ${n}'
```

- Preserves all invariants to do with the stack/accumulator as required. `li` is a pseudo-instruction and will be expanded by the assembler into several instructions

### Addition

```python
def genExp(e : Exp) =
  if e if of form
    Add(l, r) then
      genExp(l)
      'sw a0 0(sp)'
      'addi sp sp - 4'
      genExp(r)
      'lw t1 4(sp)'
      'add a0 t1 a0'
      'addi sp sp 4'
```

- Note: this evaluates from left to right
- We are doing an accumulator machine
- Why not directly store result in `t1`?
  - E.g `1 + (2 + 3)` would store 2 in `t1` which would be overridden

### Minus

- Pretty much the same thing as addition, but instead of `add a0 t1 a0`, do `sub a0 t1 a0`

### Conditional

- Want to translate `if e₁ = e₂ then e else e'`
- Need 2 new RISC-V commands:
  - `beq reg1 reg2 label`
    - If the two registers are the same, then branch to label
  - `b label`
    - Always branch to label

```python
def genExp(e : Exp) =
  if e if of form
    If(l, r, thenBody, elseBody) then
      val elseBranch = newLabel() # Not needed but useful for readability
      val thenBranch = newLabel()
      val exitLabel = newLabel()
        genExp(l)
        'sw a0 0(sp)'
        'addi sp sp -4'
        genExp(r)
        'lw t1 4(sp)'
        'addi sp sp 4'
        'beq a0 t1 ${thenBranch}'
      '${elseBranch}:'
        genExp(elseBody)
        'b ${exitLabel}'
      '${thenBranch}:'
        genExp(thenBody)
      '${exitLabel}:'
```

### Procedure Calls/Declarations

- There is an activation record
  - Stores all the data that's needed to execute an invocation of a given procedure
  - Held on the stack, because procedure entries and exits adhere to a bracketing principle
  - Invocation result and some procedure arguments are often passed in register, and not in AR

| Function Call | Activation Record                       |
| ------------- | --------------------------------------- |
| main          | Main's AR                               |
| f(3)          | Result<br>Argument: 3<br>Return Address |
| f(2)          | Result<br>Argument: 2<br>Return Address |

#### Designing An AR

- Result always in the accumulator, So no need to store in AR
- Only variables in the language are procedure params
  - Hold in AR
  - For procedure call `f(e1, ..., en)` just push the result of evaluating `e1, ..., en` onto the stack
- There are no global variables/statics
- Only the variables local to the procedure
- AR needs to store the return address
- The stack calling discipline ensure that on invocation exit, `sp` is the same as on invocation entry
  - i.e the stack point is in the same place before and after calling the procedure
- No registers need to be preserved in accumulator machines
  - Because no register is used except for accumulator and `t1`
  - When procedure is invoked, all previous evaluations are already discharged or tucked away on the stack
- So the ARs for a procedure with `n` arguments will look like this

| Stack          |
| -------------- |
| caller's FP    |
| argument n     |
| ...            |
| argument 1     |
| return address |

- Right to left argument layout makes indexing a bit easier
- A pointer to the top of current AR (i.e where the return address sits) is useful
  - Called the frame pointer and lives in register `fp`
  - Need to restore the caller's FP on procedure exit
  - So store it in the AR on entry into the procedure
  - FP makes accessing variables easier
- AR responsibility division for calling `f(7, 100, 33):

| Who    | What                                                             |
| ------ | ---------------------------------------------------------------- |
| Caller | Caller's FP<br>Third arg: 33<br>Second arg: 100<br> First arg: 7 |
| Callee | FP: Return address<br>SP                                         |

- In RISC-V, the callee must put the return address on the stack because the callee can read the location of where the jump happened

#### Code Generation For Procedures

##### Caller's Side

- To be able to get a return address for a procedure call easily, there is a RISC-V instruction `jal label`
  - Stands for jump and link
  - Jumps unconditionally to `label`, stores the address of next instruction (syntactically following `jal label`) in register `ra`
  - In many other architectures, the return address is automatically placed on the stack by a `call` instruction
- Example of a procedure call with 3 arguments. General case is similar.

```python
case Call(f, List(e1, e2, e3)) then
  'sw fp 0(sp)' # Save FP on stack
  'addi sp sp -4'
  genExp(e3) # We choose right-to-left ev. order
  'sw a0 0(sp)' # Save 3rd arg on stack
  'addi sp sp -4'
  genExp(e2)
  'sw a0 0(sp)' # Save 2nd arg on stack
  'addi sp sp -4'
  genExp(e1)
  'sw a0 0(sp)' # Save 1st arg on stack
  'addi sp sp -4'
  'jal ${(f + "_entry")}' # Jump to f, save return
                          # Addr in $ra
```

- Important notes:
  - Caller first saves the FP
  - Caller then saves procedure params in reverse order
  - Implicitly the caller saves the return in `ra` by executing `jal`
    - The AR is incomplete since the return address isn't on the stack in the AR and the completion of it is the callee's responsibility
  - How big is the AR?
    - For a procedure with `n` args, the AR is `8 + 4 * n = 4(n + 2)` bytes
    - This is known at compile time and is important for compilation of procedure bodies
  - The compilation of procedures is entirely generic in the number of procedure args, nothing special about `3`

##### Callee's Side

- In order to compile a declaration `d` like:

```python
def f(x1, ..., xn) = body
```

- We use a procedure for compiling declarations like so:

```python
def genDecl(d) = ...
```

- We need two new RISC-V instructions:
  - `jr reg`
    - Jump relative
    - Unconditional jump
    - Jumps to the address stored in the register `reg`
  - `mv reg reg'`
    - Move
    - Copies the content of the 2nd arg into the 1st arg
    - Pseudo instruction
- The code generation:

```python
def genDecl(d : Declaration) =
  val sizeAR = (2 + d.args.size) * 4
          # Each procedure arg takes 4 bytes,
          # In addition the AR stores the
          # Return address and old FP
  '${d.id}_entry:' # Label to jump to
    'mv fp sp' # FP points to top of current AR
    'sw ra 0(sp)' # Put return address on stack
    'addi sp sp -4' # Now AR is fully created
    genExp(d.body)
    'lw ra 4(sp)' # Load return address into $ra
                  # Could also use $fp
    'addi sp sp ${sizeAR}' # Pop AR off stack in one go
    'lw fp 0(sp)' # Restore old FP
    'jr ra' # Hand back control to caller
```

#### Variables And Frame Pointer

- Variables are just the procedure params
- They're on the stack in the AR, pushed by caller
- How do we access them?
  - Obvious solution, use the SP with appropriate offset
    - Does not work easily
- Problem: stack grows and shrinks when intermediate results are computed (in accumulator machine approach)
  - Variables are not a fixed offset from `sp`
- Solution: use frame pointer `fp`
  - Always points to the top of an AR as long as invocation is active
- Can always access variables/parameters at offsets from the frame pointer
  - E.g, first arg is at `fp + 4`, second is `fp + 8`, third is `fp + 12`, etc.
- In code:

```python
def genExp(e : Exp) =
  if e is of form Variable(xi) then
    val offset = 4 * i
      'lw a0 ${offset}(fp)'
```

- E.g to translate this: `xi := e`, do this:

```python
def genExp(exp : Exp) =
  if exp is of form Assign(xi, e) then
    val offset = 4 * i
    genExp(e)
      'sq a0 ${offset}(fp)'
```
