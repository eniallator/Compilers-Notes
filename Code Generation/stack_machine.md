# Stack Machine

- Consists of the following:
  - **Main memory**, addressed from zero up to some limit. Content of each memory cell is an integer. Stores code and data
  - A **program counter** (PC), pointing into the memory, to the command to execute next
  - A **current instruction register** (IR/CI), holding the instruction currently being executed
  - A **stack pointer** (SP), pointing into the memory to the topmost item on the stack. The stack grows downwards. Note: in some architectures the SP points to the first free memory cell above or below the top of the stack
  - A **temporary register**, holding an integer

## Assembly Commands For Stack Machine

- `Nop`
  - Does nothing
- `Pop x`
  - Removes top of stack and puts it in `x`
  - `x` is usually a memory location
- `PushAbs x`
  - Push the content of the variable `x` on stack
- `PushImm n`
  - Pushes number `n` on to the stack
- `CompGreaterThan`
  - Pops the two top elements off the stack
  - If first is greater than second, `1` is pushed to the stack, otherwise `0`
- `CompEq`
  - Same thing as `CompGreaterThan` except checks equality
- `Jump I`
  - Jumps to `I` (`I` is an integer)
- `JumpTrue I`
  - Jumps to address/label `I` if the top of the stack is not 0
  - Top of stack is also removed
- `Plus`
  - Adds top 2 elements of stack and puts result back on stack
- `Minus`
  - Subtracts top from element just below top
  - Same as `Plus`
- `Times`
  - Same as `Plus` but multiplies
- `Divide`
  - Same as `Plus` but divides
- `Negate`
  - Negates the top of stack (0 is replaced by 1, any non-0 number is replaced by 0)
- Any commands with arguments take up 2 memory locations with the argument straight after the command
- Symbolic addresses are much easier to use so there are labels for that
- Also need to be relocatable as well so symbolic addresses are much better

## Advantages/Disadvantages Of Stack Machines

- Simplicity
  - Easy to describe/understand
- Simple compilers
  - No register allocation needed so code generation is much simpler
- Compact object code
  - Saves memory
- Simple CPUs
  - Cheap, easy to manufacture

---

- Slow
  - The stack is in main memory so it's very slow
