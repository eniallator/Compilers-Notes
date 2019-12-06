# Realistic Code Generation

- Target is a 'real' architecture (e.g RISC-V)
- Source language features e.g procedures and classes/objects
- Code generation is typically split into 3 parts
  - Generation of intermediate code
  - Optimisation
  - Generation of machine code
- Leads to various complications
- PSA don't mention intel to this guy

## Key Issues

### Memory And Energy

- Optimising the generated executable, not just for speed but also memory/energy efficiency is good

### Processor Evolution

- Evolve quickly
- 90% of compilation is optimisation
- Fortunately, most machine languages are similar

## Intermediate Representation

### WebAssembly

- Javascript is slow
  - Not a good language to compile to
- Webassembly is fast
  - Typed
  - Secure (no buffer overflow possible)

## Management Of Run-Time Resources

- Terminology:
  - Static: happens at compile-time
  - Dynamic: happens at run-time
- Important to be clear about what happens when
- What happens when a program is executed?
  - Ask the OS to allocate space for it
  - The OS loads the code of the program into the allocated space. Maybe maps symbolic names? (called dynamic linking)
  - OS then jumps to the entry point in the program, i.e. the translation of the program's entry point, e.g the main method in java

### Memory

- Code is separated to the other memory (e.g data)
