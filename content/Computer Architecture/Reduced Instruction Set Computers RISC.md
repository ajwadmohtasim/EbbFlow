---
title: Reduced Instruction Set Computers (RISC)
draft: false
tags:
---
Before proceeding, we need to understand the gap between what High Level Languages (HLLs) provide and what computer hardware actually does at the low level.

**High Level Languages offer three key advantages:**
1. **Concise algorithm expression** - You can write `result = a + b + c` instead of multiple assembly instructions
2. **Compiler handles details** - The compiler manages memory allocation, register usage, and optimization automatically
3. **Support for structured/object-oriented programming** - Languages like C++, Java naturally support these paradigms

This introduces the two main approaches to bridging the HLL-hardware gap:
- Complex Instruction Set Computer 
- Reduced Instruction Set Computer

## CISC Characteristics
**Complex Instruction Set Computer (CISC)** Tries to provide complex instructions that directly support high-level operations.

**The goals of complex instruction sets are:**
1. **Ease compiler writing** - If hardware provides complex operations, compilers have less work to do
2. **Improve execution efficiency** - Complex operations implemented in microcode should be faster than multiple simple instructions
3. **Support sophisticated HLLs** - Enable even more advanced programming language features

**The CISC Process:**
5. High-level code is written (like `array[i] = array[j] + constant`)
6. Compiler translates this into complex instructions
7. **Few instructions generated** - One complex instruction might handle the entire operation
8. **Complex instructions require:**
    - **Complex hardware** - More transistors, more complex control units
    - **Variable instruction size** - Instructions can be 1 byte to 15+ bytes
    - **Different clock cycles** - Simple instructions might take 1 cycle, complex ones might take 50+

**Example:** A single CISC instruction might be: `ADD memory[register+offset], immediate_value, memory[register2+offset2]`

## RISC Characteristics

RISC takes the opposite approach.
**The RISC Process:**
1. Same high-level code is written
2. Compiler generates many simple instructions
3. **More instructions generated** - What CISC does in 1 instruction, RISC might need 5-10
4. **Simple instructions enable:**
    - **Simpler hardware** - Fewer transistors, simpler control logic
    - **Fixed instruction size** - All instructions are the same length (typically 32 bits)
    - **Uniform clock cycles** - Most instructions take exactly 1 clock cycle

Let the software (compiler) be complex, keep the hardware simple and fast.

## Instruction Execution Comparison

**CISC approach:**
```
ADD #10, #11, #12  // One complex instruction
```
**RISC approach:**
```
LOAD R1, #10      // Load first operand
LOAD R2, #11      // Load second operand  
LOAD R3, #12      // Load third operand
ADD R2, R3 -> R2  // Add second and third
ADD R1, R2 -> R1  // Add first to result
```
Notice that RISC needs 5 instructions to do what CISC does in 1, but each RISC instruction is much simpler and faster to execute.

## RISC vs CISC

| **Aspect**                    | **CISC (Complex Instruction Set Computer)**                                  | **RISC (Reduced Instruction Set Computer)**                            |
| ----------------------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| **1. Design Philosophy**      | Hardware handles complexity → more complex processors                        | Software (compiler) handles complexity → simpler processors            |
| **2. Instruction Complexity** | Complex instructions; can take **10–50+ cycles**                             | Simple instructions; typically **1–3 cycles**                          |
| **3. Memory Operations**      | **Load and Store integrated** into instructions (e.g., memory-to-memory add) | **Separate Load and Store** instructions; operations on registers only |
| **4. Overall Speed**          | Slower → each instruction takes many cycles                                  | Faster → fewer cycles per instruction                                  |
| **5. Main Objective**         | Minimize **code size** (fewer instructions per program)                      | Maximize **execution speed** and hardware simplicity                   |
| **6. Design Orientation**     | **Hardware-oriented** (solves complexity in hardware)                        | **Software-oriented** (compiler optimizations handle complexity)       |
| **7. Instruction Size**       | **Variable length** (e.g., x86: 1–15 bytes)                                  | **Fixed length** (typically 32 bits for all instructions)              |
| **8. Addressing Modes**       | Many **complex addressing modes** (e.g., register + offset + scaled index)   | Few **simple addressing modes** (e.g., register + offset)              |
## Using Register :  Load, Operate, Store

**The Three-Step Process:**
1. **Load:** Move data from memory to registers
2. **Operate:** Perform calculations using register data
3. **Store:** Move results from registers back to memory

**RISC processors creates register pressure, but why?**
- RISC processors keep partial results in registers (not memory)
- More operations happen in registers simultaneously
- This increases demand for registers significantly

*Now the question arise - How do we use registers efficiently?*

## Optimizing Register Usage

RISC's register pressure problem has two solutions:

**Software-Based Approach:**
- Analyze the code to understand register usage patterns
- Determine when registers are actually needed
- Use graph coloring algorithms to minimize register requirements
- **Goal:** Use as few physical registers as possible for a given program

**Hardware-Based Approach:**
- Simply adding more registers isn't feasible (complexity, cost, instruction encoding)
- Use "Circular Buffer Organization of Overlapping Windows"
- **Goal:** Provide the illusion of many registers while efficiently managing physical registers

## Software-Based Approach (Graph Coloring)

Consider the following scenario 
- A code appears to use 6 registers (A, B, C, D, E, F)
- Through analysis, we can determine that only 4 physical registers are actually needed, but we're given 
- **How?** By understanding when each register is actively being used
**The key idea** : If two variables are never used at the same time, they can share the same physical register.
![[GraphColoring.png | center]]
This diagram shows the lifetime analysis of registers:

**Reading the Timeline:**
- **Symbolic registers:** A, B, C, D, E, F (what the programmer sees)
- **Time axis:** Shows when each register is actively being used
- **Actual registers:** R1, R2, R3, R4 (physical registers available)
![[GraphColoring2.png | center]]
What we need to understand is that our registers have a lifespans that are overlapping with One another. A is overlapping with B and C register's lifespan. These means, If A is currently using R1 Actual register, B/C cannot use it. However, during A's lifespan D/E/F doesn't conflict with the timing and so they are free to use R1 register. By extension D is chosen since it's the earliest one to arrive. 
B however requires a full register all the time and overlaps with all of the registers. C can have E/F. So we get one remaining that will be using R4. 
![[GraphColoring3.png | center]]
Now comes for the graph Construction. The only thing we need to do here is to Connect Registers that overlaps with each other. Each edge connects registers that are **simultaneously active**. Registers connected by an edge **cannot** share the same physical register. 
A is connected to B and C because B/C are overlapping with A. These are all simultaneously active at any certain point in their lifespan. B is connected to all of them because it overlaps with all. Similarly we build of C,D,E,F.

## Hardware-Based Approach (Circular Buffer)

A **procedure** (also called a function or subroutine) is a self-contained block of code that performs a specific task and can be called from other parts of a program. 

When you have procedure calls in programs, several register management challenges arise:

1. **Parameter passing**: The calling procedure needs to pass arguments to the called procedure
2. **Local variable storage**: Each procedure needs registers for its own local variables
3. **Preservation**: The caller's register values must be preserved so execution can resume exactly where it left off
4. **Return values**: Results must be passed back to the calling procedure

**Traditional Solution**: Expensive save-and-restore operations to memory on every procedure call, which is slow and inefficient

The hardware-based approach creates the illusion that each procedure has its own complete set of registers, while actually sharing and reusing a larger pool of physical registers cleverly. So, Instead of each procedure having completely separate registers, adjacent procedures in the call chain **share some registers** in a specific pattern:

- **Temporary registers** of the calling procedure become **parameter registers** of the called procedure
- Each procedure gets its own **local registers**
- **Temporary registers** are used for communication between levels
We call this a **Register window**.
![[OverlappingWindows.png | 600]]
**Result**: When Procedure A calls Procedure B:
- A's temporary registers automatically become B's parameter registers
- No explicit copying or moving of data needed
- Parameters are "passed" simply by advancing to the next window
![[CircularBuffer.png | center]]
### Physical Structure
- **Inner circle**: Six register windows (w0 through w5)
- **Outer circle**: The actual register storage areas for different procedures
- **Current Window Pointer (CWP)**: Shows which procedure is currently active
- **Saved Window Pointer (SWP)**: Manages saved register sets
### Register Types in Each Window
- **Local registers**: Private to that procedure
- **Parameter registers**: For receiving arguments
- **Temporary registers**: For passing arguments to the next procedure

For the above example, we have 6 register windows (w0 through w5) tailored in a circular path. The first thing we need to realize is that, when we're shifting from one register window to another, we're essentially switching to the next procedure/function. Each have their parameters, local, and temp registers. Temp registers are excusable. Meaning, once we're done with that procedure window, we can discard it. The optimization comes here. 
We use it as the parameter register for the next Window. We shift to the next window and work on those registers. If we're done with that and return whatever calculation we did, we comeback to the previous. 

Let's say we went from w3 to w4. w3's temp. register becomes w4's parameter register to be used. when done with w4, we return the value and we come out of the w4 - Then w4's parameter register is no longer used. It becomes w3's temp register again!

The entire thing works in a clockwise fashion. We start from w0 up-to w5. But here's another concept we need to understand. Since we may go beyond w5. 

This is where **Current Window Pointer (CWP)** and **Saved Window Pointer (SWP)** comes in. **CWP** holds the point where **SWP** may pass (resulting to save the entire state of the registers into Memory (RAM)) or return back (resulting in restoring the previous state of the window which was stored in Memory (RAM) previously. )
