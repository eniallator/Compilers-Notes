# Commands In RISC V

- Has main functional groups:
  - Load and store (memory address)
  - Immediates (handling of constants)
  - Computational (e.g integer arithmetic and boolean logic)
  - Jump and branch (conditional and unconditional)
  - Many others (e.g SIMD, vectoring)
    - However no time to go over this point
- Each instruction is 32 bits long in memory
- Arithmetic can only work on registers, so no immediate constants

## Commands

- `lw reg₁ offset(reg₂)`
  - Where offset is a 16-bit integer
  - Adds the content of `reg₂` and the 16 bit offset getting a new number, `n`
  - Then looks up the 32 bit value stored in memory at `n`
  - Loads that value into `reg₁` as a signed integer
- `sw reg₁ offset(reg₂)`
  - Takes the content of `reg₁` and stores it in main memory
  - Same sort of logic as `lw`
- `add reg₁ reg₂ reg₃`
  - Adds the contents of registers `reg₂` and `reg₃` and stores the result into `reg₁`
  - Note that they don't have to be different registers
- `addi reg₁ reg₂ imm`
  - Where `imm` is a 16 bit signed integer
  - Pretty much the same thing as `add`
  - What about `addi reg₁ reg₂ 0`?
    - Simulates a copy command
- Pseudo instruction: `li reg imm`
  - Stores the 32 bit integer `imm` in register `reg`
  - RISC-V cannot load 32 bit words directly
  - It does not have this command
  - Assembler automatically breaks this down into real RISC-V commands
  - When compiling, you can easily treat pseudo-instructions as real instructions

### First RISC-V Program

- Want to write `7 + 5` and store the result into `x5`

```riscv
li x6 7
li x5 5
add x5 x5 x6
```

- Write same program, but in accumulator machine form
  - One arg is in the accumulator
  - Remaining args on the stack
  - Result should be in accumulator
- Remember, no explicit SP in RISC-V
  - Also no explicit accumulator
- Must simulate both, which is easy: each register can be used as SP or as accumulator
- Using the convention, `x2` is the stack pointer and `x10` is the accumulator, then just using `x6` as a temporary register
- Customary in RISC-V assembly to write `sp` for the stack pointer, `t1` for the temporary and `a0` for the accumulator
- Want to do this pseudo-code:

```pseudocode
acc <- 7
push acc
acc <- 5
acc <- acc + top of stack
pop
```

- To translate, more conventions:
  - Stack grows downwards
  - The stack pointer `sp` points to the first **free** memory cell below (in terms of addresses) the top of the stack

```riscv
li a0 7       // acc <- 7
sw a0 0(sp)   // v
addi sp sp -4 // push acc
li a0 5       // acc <- 5
lw t1 4(sp)   // v
add a0 a0 t1  // acc < acc + top of stack
addi sp sp 4  // pop
```

- RISC-V machine code very simple
- Assemblers translate the assembly language into machine code which is very unreadable
- Useful declarations:

```riscv
.text
.align 2 // Aligns memory to 2^n cell boundaries
.globl main // Declares the entry point for the program
```
