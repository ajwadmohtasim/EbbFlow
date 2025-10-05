---
title: Processor Pipelining
draft: false
tags:
---
## Processor Organization 

**The Five Fundamental Processor Operations**
Before understanding pipelining, we must grasp what a processor actually does. Every instruction execution involves these five core operations:

**1. Fetch Instruction**: The processor reads an instruction from memory (register, cache, main memory)
**2. Interpret Instruction**: The instruction is decoded to determine what action is required
**3. Fetch Data**: The execution may require reading data from memory or an I/O module
**4. Process Data**: The execution may require performing arithmetic or logical operations
**5. Write Data**: The results may require writing data to memory or an I/O module

It is to be noted that, the processor needs small internal memory (**registers**) to store data temporarily during these operations.

These five operations is crucial because pipelining will break them into separate stages that can operate in parallel. Each stage will specialize in one aspect of instruction processing.

## Pipelining Introduction

### Core Concepts and Definitions
**Main Idea**: Break each job into smaller stages to improve system performance.

**Parallelism**: Instead of doing one complete task at a time, we do different parts of multiple tasks simultaneously. Imagine washing dishes - while you're washing one dish, you could be rinsing another and drying a third.

**Assembly Line Analogy**: Just like Henry Ford revolutionized car manufacturing by having each worker specialize in one task, processors can specialize different hardware units for different instruction stages. This analogy is more profound than it might initially appear - it represents a shift from craft production (one person does everything) to mass production (specialization and standardization).

**Instruction Execution**: We break the five operations(Fetch Instruction, Interpret Instruction, Fetch Data, Process data, Write Data) into <u>pipeline stages</u>.

**Steady State**: The **steady state** of a pipeline is the condition when <u>all stages of the pipeline are fully occupied</u>, and <u>instructions (or data) are being processed simultaneously in all stages</u>. 
This is the "sweet spot" where the pipeline operates at full efficiency. Think of it as when all workers on an assembly line are busy and products come off the line at regular intervals. Before steady state, the pipeline is "filling up." After steady state, we achieve maximum throughput.

**Throughput**: <u>The rate at which we complete instructions (i.e., the number of instructions that exit the pipeline per unit of time).</u> In steady state, we complete one instruction per clock cycle instead of waiting for each instruction to finish completely before starting the next. In steady state, ideally:

$$
\begin{aligned}
\text{Throughput} &= 1 \text{ instruction per cycle} \\
 &= \frac{1}{\text{Clock cycle time}} \quad \text{(in ideal conditions)}
\end{aligned}
$$

In **pipelining**, **steady state** and **throughput** are key concepts that relate to how efficiently the pipeline operates over time.

**5-Stage Pipeline (Classic RISC):**
IF = Instruction Fetch  
ID = Instruction Decode  
EX = Execute  
MEM = Memory access  
WB = Write Back


## Assembly Line - Unpipelined Approach

![[AssemblyLine- Unpipelined.png | 600 center]]
**Sequential Processing**
In the unpipelined approach, we have "Single Car production by one composite team." This represents the traditional way of thinking about task completion:
- One team does ALL the work for one car
- They must "Start and finish a job before moving to the next"
- If building one car takes 3 time units, then 3 cars take 9 time units
- Only ONE car is being worked on at any given time

**Time Analysis**:
![[Unpipelined.png | center]]

**Efficiency Problem**: Most of the factory sits idle most of the time. When the team is working on stage A of car 1, the equipment for stages B and C is unused. This represents a fundamental waste of resources.

This approach mirrors how early computers worked - they had to complete one instruction entirely before beginning the next. While this seems logical and straightforward, it leaves expensive hardware sitting idle most of the time. Understanding this inefficiency is crucial for appreciating why pipelining represents such a significant breakthrough.

## Pipelining (Assembly Line – 3 stage)
![[Pipeline-3stage.png | center]]
Now we "Break the job into smaller stages" with three specialized teams:
- Team A: Handles first stage of every car
- Team B: Handles second stage of every car
- Team C: Handles third stage of every car

**Key Observations**:
- **Steady State**: Reached after cycle 3 (when first car completes)
- **Parallelism**: After cycle 3, all three teams work simultaneously
- **Throughput**: One car completed every cycle after steady state
- **Speedup**: 3x improvement (3 cars in 5 cycles vs 9 cycles)

Each team becomes expert at their specific task, and no equipment sits idle after steady state is reached. This specialization leads to both efficiency gains and expertise development.

## Pipelining Applied to Instructions
![[Pipeline-3stage_2.png | center]]
**From Cars to CPU Instructions**
Now applying this concept to processor instructions with our three stages:
- **Stage A**: Fetch
- **Stage B**: Decode
- **Stage C**: Execute

If we do the **performance calculations**:
**Assumptions**:
- Clock period: $1 ns$ (very fast processor)
- Each substage takes equal time for execution ($1ns$)
- Perfect conditions (no delays or conflicts)

**Unpipelined Performance**:
```
Time   :   1    2    3    4    5    6    7    8    9(ns)
Instr 1:   A    B    C
Instr 2:                  A    B    C
Instr 3:                                 A    B    C
```
- Time per instruction: $3 × 1ns = 3ns$
- Frequency: $1/3ns = 333 MHz$
- Instructions per second: $333$ million
- Each instruction must complete before the next begins

**Pipelined Performance**:
```
Time   :   1    2    3    4    5    6    7    8    9(ns)
Instr 1:   A    B    C    Finish ......
Instr 2:        A    B    C    Finish.....   
Instr 3:             A    B    C    Finish.....   
```
- Clock cycle time: $1ns$ (determined by slowest stage)
- Frequency: $1/1ns = 1 GHz$
- Instructions completed per cycle: 1 (after steady state)
- **Theoretical Speedup**: 3x improvement

## Pipelining Overheads
### Why Real Pipelines Aren't Perfect
Pipelines has issues and we need to deal with them to avoid stalling/errors.

Consider the following Problem Scenario:
![[PipelineOverhead.png | center]]
**Example Instructions**:
- Instruction 1: $\text{ADD  r1, r2 → r3 (where  r1=3, r2=5)}$
- Instruction 2: $\text{MULT r4, r5 → r6 (where r4=1, r5=9)}$

**The Critical Issue**: *Data corruption between stages.*
The pipeline has two independent stages. The first stage fetches an instruction and **buffers** it. When the second stage is free, the first stage passes it the buffered instruction. While the second stage is executing the instruction, the first stage takes advantage of any unused memory cycles to fetch and buffer the next instruction. This is called instruction prefetch or fetch overlap

So what could happen is, when **Stage 1** finishes processing Instruction 1, it passes values 3(**r1**) and 5(**r2**) to **Stage 2**. However, during the SAME clock cycle, Stage 1 starts processing Instruction 2 and begins reading values 1 and 9. If there's any timing issue, Stage 2 might receive mixed data (like 3 and 9 instead of 3 and 5).

So in a nutshell, <u>when you decompose a system into parts that must work together, the interfaces between parts become critical points of failure. </u> The faster you try to operate, the more precise these interfaces must be.

**The Solution**? We need proper "handoff" mechanisms between stages.

![[Pipeline Overhead Latch.png | center]]
**Making Pipeline Stages Work Together**
The solution is to "Decouple the stages" using latches and clock circuits. So, when direct coordination is too difficult, introduce intermediate storage. 

**How Latches Work**:
- A latch is like a temporary storage box between stages
- It only changes its output on the rising edge of the clock
- This ensures clean, synchronized data transfer between stages

This can be expanded however.
![[SimplifiedPipelineArchitecture.png | center]]
The latches serve to decouple the stages from each other. Information flows between adjacent stages under the control of a common clock applied to all the latches simultaneously. Through this, we can eliminate the data mismatch.

### Problem Scenario:
- Each stage: 3ns (actual work time)
- Latch operation: 0.2ns (overhead time)
**What is the actual speed up? Calculate the performance.**
Theoretically we should get the same improved performance weather we add Latches or not. 

**Unpipelined performance**:
- Total time per instruction: 3ns + 3ns + 0.2ns = 6.2ns
- Frequency: 1/6.2ns = 161 MHz

**Pipelined performance** (2-stage with latches):

- Clock cycle time: 3ns + 0.2ns = 3.2ns
- Frequency: 1/3.2ns = 312.5 MHz (not the ideal 320 MHz!)
- **Actual Speedup**: 312.5/161 = 1.94x (not the theoretical 2x)

**"So an n-stage pipeline is not going to give me a speedup of factor of n."** This is one of the most important realizations in computer architecture - real systems have overheads that reduce theoretical performance.

This overhead teaches us that every engineering solution has costs. The latch solves our synchronization problem but adds delay. We constantly balance the benefits of a solution against its costs. 

## Pipelining Calculations

**Unpipelined Time:**  
$$T_{\text{unpipelined}} = T + T_{\text{ovh}}$$  
- $T$ = actual work time  
- $T_{\text{ovh}}$ = overhead time  
- Work time plus any overhead  
- This represents our baseline for comparison  

**Pipelined Clock Cycle Time:**  
$$T_{\text{cycle}} = \frac{T}{N} + T_{\text{ovh}}$$  
- $\frac{T}{N}$ = work time divided among $N$ stages  
- $T_{\text{ovh}}$ = overhead still exists per stage  
- This becomes our new "heartbeat" of the system  
- Overhead doesn't decrease with more stages  

**Total Time Per Instruction (Pipelined):**  
$$T_{\text{instr}} = N \times \left( \frac{T}{N} + T_{\text{ovh}} \right) = T + N \times T_{\text{ovh}}$$  
- Applies to the **first** instruction  
- After pipeline fills, we get one instruction per cycle  
- Reveals the startup cost of pipelining  

**Clock Speed:**  
$$f = \frac{1}{\frac{T}{N} + T_{\text{ovh}}}$$  
- Higher clock speed means faster operation  
- Limited by the slowest stage plus overhead  
- Shows the importance of balanced stage delays  

**Ideal Speedup:**  
$$S_{\text{ideal}} = \frac{T + T_{\text{ovh}}}{\frac{T}{N} + T_{\text{ovh}}}$$  
- Compares unpipelined and pipelined cycle times  
- Shows maximum possible improvement  
- Real-world speedup is less due to hazards  


**Cycles Per Instruction (CPI )**: Average of 1 in steady state 
- In an **ideal pipelined processor**, **once the pipeline is full**, **you get one instruction out per clock cycle**
- Hazards will however increase this number

---
These formulas reveal a critical insight: as we add more pipeline stages (increase N), the overhead term `N × Tovh` grows linearly while the benefit `T/N` shrinks. This relationship explains why we can't just keep adding pipeline stages indefinitely - eventually, the overhead dominates.

**However, there's a critical context that needs clarification.** The understanding of what "total time per instruction" has different contexts.

**Formula 1** : $$\text{Unpipelined time} = T + T_{ovh}$$
This represents the complete time to execute one instruction from start to finish.

**Formula 2**: $$\text{Pipeline clock cycle} = \frac{T}{N} + T_{ovh}$$
This is the time for each pipeline stage plus overhead.

**Formula 3**: $$\text{Total time per instruction} = N\cdot\frac{T}{N} + T_{ovh} = T + N\cdot T_{ovh}$$This formula is describing the **latency** (time for the first instruction to complete), not the **throughput** (time between instruction completions in steady state).

### Consider the problem where, our values: $T = 5ns$, $N = 5$, $T_{ovh}= 0.2ns$ 

**Unpipelined:**
- Time per instruction = $T + T_{ovh} = 5 + 0.2 = 5.2ns$
**Pipelined Clock Cycle(Throughput):**
- Clock cycle time = $\frac{T}{N} + T_{ovh} = 5/5 + 0.2 = 1.2ns$
- This is how often you get results after steady state
- For performance analysis, we care about throughput because that determines how fast we can process many instructions.
**Total Time Per Instruction (Latency):**
- Using the formula: $N(\frac{T}{N} + T_{ovh}) = 5(1 + 0.2) = 5 × 1.2 = 6ns$
- This is how long you wait for the first result

This $6ns$ represents the time for the **first instruction** to travel through all 5 stages. After that, we get one instruction completed every $1.2ns$.

So after 10ns total time:
- **Unpipelined:** Completes 10/5.2 ≈ 1.9 instructions
- **Pipelined:** Completes 1 + (10-6)/1.2 ≈ 4.3 instructions
The pipelined system is about 4.33x faster in throughput

## The 5-Stage Pipeline Example
![[Pipeline-5stage.png | center]]
### Processor Architecture
Processors typically use this five-stage structure or variations of it.

| **Stage**   | **Cycle** | **Component**                   | **Operation**                                                                                                                 |
| ----------- | --------- | ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Stage 1** | CC1       | **IM (Instruction Memory)**     | - Fetch instruction from memory<br>- Update Program Counter (PC)                                                              |
| **Stage 2** | CC2       | **Reg (Register File)**         | - Decode instruction **opcode**<br>- Read source operands from registers<br>- Register reads occur in **first half** of cycle |
| **Stage 3** | CC3       | **ALU (Arithmetic Logic Unit)** | - Perform arithmetic or logic operations<br>- Compute effective address for load/store                                        |
| **Stage 4** | CC4       | **DM (Data Memory)**            | - Read/write data to memory (only for **load/store**)<br>- Other instructions execute **NOP** here                            |
| **Stage 5** | CC5       | **Reg (Register Write)**        | - Write result back to destination register<br>- Register writes occur in **second half** of cycle                            |

**Questions to Consider**:
1. **When is steady state reached?** After 5 cycles (when first instruction completes all stages)
2. **What speedup is achieved?** Let's calculate with latch overhead of 0.2ns...
	**Unpipelined Performance:**
	- Work time: 5 stages × 1ns = 5ns
	- Timing overhead: 0.2ns (basic synchronization)
	- Total time per instruction: 5.2ns
	- Frequency: 1/5.2ns = 192.3 MHz
	
	**Pipelined Performance:**
	- Clock cycle time: 1ns (stage work) + 0.2ns (latch) = 1.2ns
	- Frequency: 1/1.2ns = 833.3 MHz
	
	Total time per instruction : N(T/N + Tovh) = 5(1 + 0.2) = 5 × 1.2 = 6ns
	
	**Results:**
	- **Actual speedup: 4.33x** (833.3 MHz ÷ 192.3 MHz)
	- **Theoretical maximum: 5x** (perfect 5-stage pipeline)
	- **Pipeline efficiency: 86.7%** (4.33/5.0 × 100%)
	- **Performance lost to overhead: 13.3%**

This calculation demonstrates why real pipelines never achieve the theoretical N-fold speedup - overhead always reduces the actual performance gain. 
## Instruction Examples

**Example 1: ADD r1, r2 → r3**
- **CC1**: Fetch ADD instruction from memory
- **CC2**: Decode ADD operation, read values from r1 and r2
- **CC3**: Compute r1 + r2 in the ALU
- **CC4**: NOP (*no memory access needed for ADD, however next Example cycle needs memory access at CC4*) 
- **CC5**: Write the sum result to register r3
Notice how this instruction uses most pipeline stages effectively, with only CC4 being unused. 

**Example 2: LOAD r6, [r3]+8**
This is more complex because it involves memory access:

- **CC1**: Fetch LOAD instruction from memory
- **CC2**: Decode LOAD operation, read value from r3
- **CC3**: Compute effective address (r3 + 8)
- **CC4**: Access memory at the computed address and read the data
- **CC5**: Write the loaded value to register r6

**Concrete Example**: If r3 contains 0x1000, then:
- Effective address = 0x1000 + 8 = 0x1008
- If memory location 0x1008 contains value 85
- Then 85 gets stored in register r6

**Understanding Memory Addressing**: The [r3]+8 notation means "use the value in r3 as a base address and add 8 to it." This is common for accessing array elements or data structures.

|  **Stage / Instr**  | **CC1** |         **CC2**         |           **CC3**            |            **CC4**             |            **CC5**             |        **CC6**         |
| :-----------------: | :-----: | :---------------------: | :--------------------------: | :----------------------------: | :----------------------------: | :--------------------: |
|  `ADD r1, r2 → r3`  |  Fetch  | Decode, read `r1`, `r2` |    Execute `r1 + r2 = 30`    |     NOP (no memory access)     |     Write-back → `r3 = 30`     |           –            |
| `LOAD r6, [r3] + 8` |    –    |          Fetch          | Decode, read `r3` (= 0x1000) | Execute `EA = r3 + 8 = 0x1008` | Memory read `MEM[0x1008] = 85` | Write-back → `r6 = 85` |

**Key Observation**: Different instruction types use the pipeline stages differently. ADD instructions don't need the memory stage, while LOAD instructions use all five stages.
## Pipeline Hazards Introduction

**When Things Go Wrong**
Now we encounter the major challenges that prevent pipelines from achieving ideal performance. Think of hazards as traffic jams in our assembly line - they disrupt the smooth flow and force parts of the system to wait.

**Three Categories of Pipeline Hazards**:

**1. Structural Hazards**:

- Different instructions conflict for the same hardware resource
- Like two cars trying to use the same lane at the same time
- **Example**: Two instructions both need to access memory simultaneously
- **Root cause**: Insufficient hardware resources

**2. Data Hazards**:

- An instruction cannot continue because it needs a value not yet produced by an earlier instruction
- Like needing flour for a recipe, but the previous step hasn't finished grinding the wheat
- **Example**: Second instruction needs result from first instruction
- **Root cause**: Dependencies between instructions

**3. Control Hazards**:

- Fetch cannot continue because we don't know the outcome of a branch instruction
- Like not knowing which road to take until we reach the intersection
- **Special case of data hazard** but treated separately because solutions are different
- **Root cause**: Uncertainty about program flow

**Why Hazards Matter**: Each type of hazard can force the pipeline to stall, reducing our hard-earned performance gains. So, Understanding hazards is essential because they represent the gap between theoretical and actual performance.

## Structural Hazards
![[DataHazard.png | center]]

**The Specific Conflict Scenario**: In our 5-stage pipeline, consider what happens in CC4:

- **Instruction 1**: Currently in CC4, accessing Data Memory (DM)
- **Instruction 4**: Trying to enter CC1, needs Instruction Memory (IM)
- **Problem**: Both IM and DM are the same physical memory!

This conflict isn't just theoretical - it happens in real processors with unified caches or when both instruction and data access the same memory bank.

**Cascaded Effect**:
- CC4: Instruction 4 blocked (can't fetch)
- CC5: Instruction 4 still blocked (Instruction 2 now using DM)
- CC6: Instruction 4 still blocked (Instruction 3 now using DM)

**Result**: "Three cycles would be wasted out of six cycles"

**Additional Conflict**: Register file access

- If register file has only one I/O port
- CC5: Instruction 1 trying to write while Instruction 4 trying to read
- **Another resource conflict!**

**Performance Impact Analysis**:
- **6 cycles elapsed**
- **Only 3 instructions completed**
- **Expected in ideal pipeline**: 6 instructions after steady state
- **Actual performance**: 50% of ideal performance

Structural hazards don't just affect the conflicting instructions; they create a cascade of delays affecting the entire pipeline.

This cascading effect is why structural hazards are particularly damaging to performance. A single resource conflict can ripple through the entire pipeline, affecting instructions that have no inherent dependency on the conflicted resource.

## Structural Hazard Solutions
**Two Main Approaches**:
**1. Eliminate Contention/Provide More Resources**:
- **Separate Memory Spaces**: Harvard Architecture
    - Instruction Memory (IM) completely separate from Data Memory (DM)
    - Different physical paths, can operate simultaneously
    - **Cost**: More hardware, more complex design
    - **Benefit**: Eliminates memory conflicts entirely

**2. Multiple I/O Ports**:
- Register file with multiple read and write ports
- Allow simultaneous read and write operations
- **Cost**: More complex register file design
- **Benefit**: Eliminates register access conflicts

**Trade-off Analysis**:
- **Benefit**: Eliminates structural hazards completely
- **Cost**: More hardware, higher complexity, more power consumption
- **Decision**: Modern processors usually implement these solutions because the performance gain justifies the cost

Modern processors typically use Harvard architecture (separate instruction and data caches) and multi-ported register files to avoid structural hazards.

## Data Hazards Introduction

![[DataHazardHardwardIntroductionExample.png | center]]
## Problem Setup

- **Instruction 1**: ADD r1, r2 → r3 (produces result in r3)
- **Instruction 2**: ADD r3, r4 → r5 (needs r3 from Instruction 1)
- **Pipeline**: 5-stage (IM → Reg → ALU → DM → Reg)
- **Hazard**: Instruction 2 needs r3 before Instruction 1 produces it

This is why data hazards represent one of the most significant challenges in pipeline design - they force a choice between correctness and performance.

If we want to fix the issue, one possible solution could be:
![[DataHazardHardwardIntroductionExample2.png | center]]
But that's not ideal! There is no pipelining here.
Now this begs us the question - 
>we want to start the next instruction as soon as possible, but we can't start it until we have the data it needs. But How?

**Two Solution Approaches**:

**1. Compiler Solution**:
- Compiler detects dependencies during code generation
- **Problem**: Different hardware implementations have different pipelines
- **Consequence**: Programs must be compiled specifically for target hardware
- **Limitation**: Not flexible for modern processors with varying configurations

**2. Hardware Solution**:

- Hardware detects dependencies during execution
- **Advantage**: Same program runs on different processors
- **Implementation**: More complex hardware logic required

The choice between compiler and hardware solutions reflects a broader question in computer architecture: should complexity reside in software (compilers) or hardware? Modern processors generally favor hardware solutions because they provide more flexibility and better performance across diverse workloads.

**Hardware Requirements**: The processor needs <u>dependency detection logic</u> that compares source registers of incoming instructions with destination registers of instructions still in the pipeline.

This detection logic must operate quickly enough to make decisions within a single clock cycle, adding complexity to the processor design. The hardware must track which registers are "in flight" - being computed by instructions currently in the pipeline.

**The Stalling Mechanism**: When a dependency is detected, the pipeline control logic inserts NOPs into the pipeline. This effectively stretches the time between dependent instructions, allowing the first instruction to complete before the second instruction needs its result.

![[DataHazardHardwardSolution.png | center]]


**Step-by-Step Process**:
**CC1**: Instruction 1 fetches normally 
**CC2**:
- Instruction 1 moves to register read
- Instruction 2 fetches normally
**CC3**:
- Instruction 1 moves to ALU
- Instruction 2 tries to read registers
- **Hardware Detection**: Dependency detected! r3 not ready
- **Action**: Insert NOP (stall) for Instruction 2
**CC4-CC5**:
- Instruction 2 continues stalling
- Instruction 1 completes and writes r3
**CC6**:
- Instruction 2 finally can read r3 value
- Pipeline resumes normal operation

## Bypassing/Forwarding Introduction

Instead of always waiting for results to be written to registers and then read back, **we can "shortcut" the process by sending data directly where it's needed**.
![[DataHazardForwarding_1.png | center]]
One bypassing method is use the result when it is computed. In other words, don't wait for it to be stored in a register. But this requires extra connections in the data path. But sometimes, we can't always avoid stalls by forwarding. Especially if value were not computer when needed and can't forward-backward in time. We call it Load-use Data Hazard. 

This occurs in a pipelined processor when an instruction that **loads data from memory** is immediately followed by an instruction that needs to use that **loaded data before the load operation has completed and written the data to the register**

```
Instruction 1: LOAD r0, [r1]  # Load Register r0 with the value from memory adress in r1
Instruction 2: SUBTRACT r0, r3 → r2
```

![[DataHazardForwarding_1.5.png | center]]
Understand the issue here, Instruction 1 can never load `r0` until CC4 (DM - Direct Memory). But Instruction 2 ALU already requires `r0` value. So we get a stall here which can't be helped. 

![[DataHazardForwarding_2.png | center]]


**Key Terms**:
- **Point of Production (PoP)**: When data becomes available
- **Point of Consumption (PoC)**: When data is needed
If PoP is after PoC then stall cycles will be needed. Else Bypassing / Forwarding is possible. 

**Example Analysis - 1**:
```
Instruction 1: ADD r1, r2 → r3
Instruction 2: ADD r3, r4 → r5
```

![[DataHazardForwarding_3.png | center]]


The MUX here helps multiplexing the inputs from different latches. 


**Example Analysis - 2**:
```
Instruction 1: LOAD r1, [r2] + 8
Instruction 2: LOAD r4, [r1] + 4
```

**Observation**:
- Instruction 1 must access memory to get r1's value
- Memory access happens in CC4 (Data Memory stage)
- Instruction 2 needs r1 for address calculation in CC3
- **Problem**: PoC (CC3) < PoP (CC4) - bypassing impossible!

Since the Memory access takes time, and we cannot bypass data that doesn't exist yet. The processor must wait for the memory system to respond.
![[DataHazardForwarding_4.png | center]]

## Control Hazards Introduction
Control hazards represent the third major class of pipeline problems and often the most challenging to solve effectively. They arise from the fundamental nature of program execution - programs don't just execute instructions sequentially; they make decisions and jump to different locations.

**Example Branch Instruction**:
```
BEZ r1, 0x100    ; Branch to address 0x100 if r1 equals zero
```
This instruction says "Branch to address 0x100 if register r1 equals zero." Notice how this single instruction actually contains two completely different programs hidden inside it:

**Program Path A (Branch Taken)**: If r1 equals zero, jump to address 0x100 and continue executing from there 
**Program Path B (Branch Not Taken)**: If r1 does not equal zero, continue with the next sequential instruction
The processor must choose between these two fundamentally different futures, and it must choose quickly enough to keep the pipeline full.

**Understanding Branch Instructions**: All branches are essentially two-cycle instructions:
- **CC1**: Instruction Fetch
- **CC2**: Decode, operand fetch, and branch decision

**The Timeline Problem**:
![[ControlHazardsTable.png | center]]

**The Critical Question**: In CC3, which instruction should we fetch?

Control hazards affect 15-20% of all instructions in typical programs, making this a critical performance issue.
Unlike data hazards where we know what data we need (we just have to wait for it), control hazards leave us completely uncertain about what to do next. This uncertainty is what makes control hazards particularly challenging. Think about this from the processor's perspective: every clock cycle, it must fetch the next instruction to keep the pipeline full. But when it encounters a branch, it faces a dilemma - it must choose which instruction to fetch before it knows which instruction is correct.
## Control Hazard Solutions

|**Method**|**Description**|**Pros**|**Cons**|
|---|---|---|---|
|**1. Brain-Dead Solution (Stall)**|Introduce a **stall cycle** after every branch. Do not fetch the next instruction until the branch decision is known.|Simple implementation|Wastes 1 cycle per branch → **inefficient**, especially since ~60% of instructions can be branches|
|**2. Branch Prediction (Static)**|Predict the most likely branch path (e.g., assume branch is taken). In 80% cases, the branch is taken, so fetch the **TRUE path** instruction first. If wrong, fetch the other path.|Improves performance over stalling|Wrong prediction may cause **incorrect register updates**, requiring rollback|
|**3. Hardware Prediction (Dynamic)**|Hardware predicts branch outcome and **speculatively fetches instructions**. If prediction is wrong, discard the fetched instructions.|Higher accuracy with advanced predictors|Same problem as above (false path effects) + **hardware complexity**|
|**4. Branch Delay Slot**|The **instruction immediately after the branch** (delay slot) is always executed, regardless of branch outcome. Compiler tries to schedule an **independent instruction** in that slot.|Uses otherwise wasted slot efficiently|Requires **compiler support** and **reordering instructions** safely|
