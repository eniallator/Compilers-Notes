# Register Machines

## Compilation

- Each CPU has a finite number of registers (e.g 16, 32, 128) which can be a problem
- Small expressions can fit in registers, however larger expressions may have trouble fitting
  - Generate code that has all parameters in registers
  - Generate code that has some parameters in main memory
  - Detect which of the 2 above is the case and be able to seamlessly switch between the 2
- All of the above make making compilers more difficult

## Simple Register Machine

- Similar to the stack machine
- However, we have additional registers
  - Some are special purpose

## Dealing With Registers

- 3 simpler sub-problems
  - Generate code assuming an unlimited supply of registers
  - Modify the translator to evaluate expressions in which minimise the number of registers needed
  - Invent a scheme to handle cases when registers run out

## Commands For Register Machines

- We assume an unlimited supply of registers `R0`, `R1`, `R2`, `R3`, ... ranged over by `r`, `r'`. We call these general purpose registers (as distinct from PC, SP)
- `Nop`
  - Does fuck all
- `Pop r`
  - Removes the top of the stack and stores it in register `r`
- `Push r`
  - Pushes the contents of the registers `r` on stack
- `Load r x`
  - Loads the content of memory location `x` into register `r`
- `LoadImm r n`
  - Loads integer `n` into register `r`
- `Store r x`
  - Stores the content of register `r` in memory location `x`
- `CompGreaterThan r r'`
  - Compares the contents of register `r` with the contents of register `r'`. Stores `1` ub `r` if the former is bigger than the latter, otherwise stores `0`
- `CompEq` r r'`
  - Same as `CompGreaterThan` except does equality
- `Jump I`
  - Jumps to `I`
- `JumpTrue r I`
  - Same as before
- `JumpFalse r I`
  - Same as before
- `Plus I r'`
  - Same as before
- ...

## Source Language

- The source language is not changed, still has standard coding constructs like selection/iteration
- Everything that's difficult to compile e.g procedures/objects is left out - will come back to this

## Code Gen For Register Machines

- Important conventions:
  - 1: The result of evaluating the expression is returned in register `target`
  - 2: The code generated can use registers `Ri`, `Ri + 1` upwards, but must leave registers below unchanged

## Bounded Number Of Registers

- It's easy to compiler to register machine code when registers are unlimited
- Another extreme: Just one register called the accumulator
  - Operations only affect the accumulator
- Combining 2 strategies
  - While > 1 free registers remain, be greedy where we use the register machine strategy above for compilation
  - When limit is reached, use accumulator strategy where the last register is the accumulator

## Accumulator Machine

- This machine has a stack and just one register
  - Unary operations work like a register machine
  - Binary operations retrieves the second value from memory and uses the accumulator, where the result is stored in the accumulator
