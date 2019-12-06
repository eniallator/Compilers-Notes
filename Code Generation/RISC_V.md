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

### Conventions

- x0 is always 0
- x1 return address
- x2 stack pointer
- x3 global pointer
- x4 thread pointer
- ...

### Datatypes

- Address space is circular
