# Notes

## RISC VS CISC

- Processors can be classified as:
  - **CISC**: complex instruction set computer
  - **RISC**: reduced instruction set computer

## Instruction set architecture

- Commands in high level languages can be translated into many low level commands since there is a semantic gap
- In the past it was thought to be better to have CPUs able to carry out higher and higher level commands (so CISC)
- However they did experiments on programs and most of the complex commands that were implemented weren't getting used
- Simpler operations are slower with the addition of more complex instructions

### RISC

- Remove all complex commands
- Optimise the remaining commands
- RISC is AKA load/store architecture
  - Can only read/write memory
  - CISC can do many more things
- In it's basic form, RISC-V has 32 integer registers
  - Load and store architecture
- USE RARS FOR LEARNING RISC-V
  - Learn it yourself, (AKA watch indian youtube tutorials)

## Risc-V

- Has 32 general purpose registers that are all 32 bits long
- Also has a program counter (PC) - not a general purpose register
- No explicit stack pointer (SP), so no explicit push/pop commands
- RISC-V has memory that must be aligned on 32 bit boundaries - nothing rly to worry about bc it can be done automatically

### Conventions

| Register Name | Symbolic Name | Description                        |
| ------------- | ------------- | ---------------------------------- |
| x0            | Zero          | is always 0                        |
| x1            | ra            | return address                     |
| x2            | sp            | stack pointer                      |
| x3            | gp            | global pointer                     |
| x4            | tp            | thread pointer                     |
| x5            | t0            | temporary/alternate return address |
| x6-7          | t1-2          | Temporary                          |
| x8            | s0/fp         | Saved register/frame pointer       |
| x9            | s1            | Saved register                     |
| x10-11        | a0-1          | Function argument/return value     |
| x12-17        | a2-7          | Function argument                  |
| x18-27        | s2-11         | Saved register                     |
| x28-31        | t3-6          | Temporary                          |

- Accumulator is usually `x10`

### Datatypes

- Address space is circular
