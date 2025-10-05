---
title: Nulltsuki
draft: true
tags:
---




![[ComputerOSHardwareSoftware.png]]
An **operating system (OS)** is software that controls application execution and serves as an interface between applications and hardware. Its main objectives are:
- **Convenience:** Make the computer easier to use.
- **Efficiency:** Use system resources effectively.

Computer systems are organized in layers. End users interact with applications, not the hardware. Writing applications to directly manage hardware would be highly complex, so **system programs** (including utilities) provide common functions like file management and I/O control. Application developers rely on these utilities during development, and applications invoke them during execution.

**The OS typically provides services in the following areas:**
- Program creation
- Program execution
- Access to I/O devices
- Controlled access to files
- System access
- Error detection and response
- Accounting

Normally, we think of a control mechanism as something external to that which is controlled, or at least as something that is a distinct and separate part of that which is controlled
This is not the case with the OS, which as a control mechanism is unusual in two respects: 
- The OS functions in the same way as ordinary computer software; that is, it is a program executed by the processor. 
- The OS frequently relinquishes control and must depend on the processor to allow it to regain control. 
![[OSresourceManager.png]]
Like other computer programs, the OS provides instructions for the processor. The key difference is in the intent of the program. The OS directs the processor in the use of the other system resources and in the timing of its execution of other programs. But in order for the processor to do any of these things, it must cease executing the OS program and execute other programs. Thus, the OS relinquishes control for the processor to do some “useful” work and then resumes control long enough to prepare the processor to do the next piece of work. The mechanisms involved in all this should become clear as the chapter proceeds. Figure 8.2 suggests the main resources that are managed by the OS. A portion of the OS is in main memory. This includes the kernel, or nucleus, which contains the most frequently used functions in the OS and, at a given time, other portions of the OS currently in use. The remainder of main memory contains user programs and data.



---
![[LogicalPhysicalAddress.png]]

## Problem with Variable Partitioning

- **External fragmentation**: Memory becomes fragmented with unusable gaps
- **Internal fragmentation**: Wasted space within allocated partitions
- **Contiguous allocation requirement**: Processes need continuous memory blocks
## Paging Solution

### Core Concept

- Divide **physical memory** into fixed-size chunks called **page frames**
- Divide **process memory** into same-sized chunks called **pages**
- Typical size: 4KB per page/frame

### Key Benefits

- **Eliminates external fragmentation**: All chunks are same size
- **Minimizes internal fragmentation**: At most, waste < 1 page (in last page only)
- **Non-contiguous allocation**: Pages can be placed in any available frames

## How Paging Works

### Process Loading

1. OS maintains **free frame list** of available frames
2. When loading a process:
    - Find required number of free frames
    - Load process pages into any available frames
    - Frames don't need to be contiguous

### Address Translation Components

#### Page Table

- **One per process**
- Maps logical page numbers to physical frame numbers
- Maintained by OS, accessed by processor
#### Logical Address Structure

```
Logical Address = (Page Number, Offset)
```

- **Page Number**: Which page in the process
- **Offset**: Byte position within that page (0 to page_size-1)

#### Physical Address Structure

```
Physical Address = (Frame Number, Offset)
```

- **Frame Number**: Actual memory frame location
- **Offset**: Same as logical offset (unchanged)

### Translation Process

1. **Input**: Logical address (page_num, offset)
2. **Lookup**: Page table converts page_num → frame_num
3. **Output**: Physical address (frame_num, offset)


## Step-by-Step Paging Process

### 1. System Initialization

- OS divides physical memory into fixed-size frames (e.g., 4KB each)
- Maintains free frame list of available frames

### 2. Process Creation

- Program loaded from disk
- OS divides process memory into pages (same size as frames)
- Creates empty page table for the process

### 3. Memory Allocation

- OS finds required number of free frames from free list
- Assigns each process page to an available frame
- Updates page table with page→frame mappings
- Removes used frames from free list

### 4. Address Translation (Runtime)

- Process generates logical address (page_num, offset)
- Hardware looks up page_num in process page table
- Gets corresponding frame_num
- Forms physical address (frame_num, offset)
- Accesses memory at physical location

### 5. Memory Access

- CPU uses physical address to read/write data
- Same offset ensures correct byte within frame

### 6. Process Termination

- OS frees all frames used by process
- Returns frames to free frame list
- Destroys page table

This happens automatically for every memory access, with hardware handling the translation transparently.

## Example Walkthrough

### Setup

- Process A has 4 pages: 0, 1, 2, 3
- Available frames: 13, 14, 15, 18
- Page table mapping:
    - Page 0 → Frame 18
    - Page 1 → Frame 13
    - Page 2 → Frame 14
    - Page 3 → Frame 15

### Address Translation Example

```
Logical Address: (Page 1, Offset 30)
Page Table Lookup: Page 1 → Frame 13
Physical Address: (Frame 13, Offset 30)
```

**Result**: Both addresses point to the same physical memory location - the 30th byte in what's physically Frame 13.

## Key Advantages

- **Process independence**: Each process has its own logical address space starting from 0
- **Memory efficiency**: No external fragmentation, minimal internal fragmentation
- **Flexibility**: Pages can be loaded anywhere in memory
- **Transparency**: Programs use simple logical addresses while OS handles physical placement
- **Multiple processes**: Can run simultaneously without memory conflicts

## Implementation Notes

- **Hardware support**: Processor must support page table lookups
- **OS responsibility**: Maintain page tables and free frame lists
- **Automatic translation**: Happens transparently for every memory access


![[Translation Lookaside Buffer.png | center ]]
This figure beautifully illustrates one of the most sophisticated aspects of modern computer systems - how your processor translates virtual addresses into physical addresses while simultaneously managing high-speed cache memory. Let me walk you through this step by step, as understanding this process is crucial for grasping how modern memory management works.

## The Big Picture: Why We Need This Complexity

Before diving into the mechanics, let's understand why this system exists. When you write a program and declare a variable, your program thinks it's storing that variable at a specific memory address. However, that's actually a "virtual address" - a fiction that the operating system and hardware maintain to provide several crucial benefits. The real physical memory might be located somewhere completely different, and this translation system bridges that gap while providing incredible performance optimizations.

## Understanding Virtual Addresses: The Starting Point

The process begins on the left side of the diagram with a "Virtual address" that gets divided into two components: a "Page #" and an "Offset." Think of this like a mailing system where the **page number is like a neighborhood designation**, and the offset is like the specific house number within that neighborhood. This division is fundamental because virtual memory systems organize memory into fixed-size chunks called pages, typically 4KB each.

For example, if your virtual address is something like 0x12345678, the processor might interpret the first portion (like 0x12345) as the page number and the remaining portion (like 0x678) as the offset within that page. This means "go to virtual page 0x12345 and look at byte position 0x678 within that page."

## The TLB: Your Speed-Critical Address Translator

The **Translation Lookaside Buffer, or TLB**, serves as the first and fastest step in address translation. Think of the TLB as an express lane for frequently used address translations. It's essentially a small, extremely fast cache that stores recent virtual-to-physical address mappings.

When the processor needs to translate a virtual address, it first checks the TLB using the page number as a lookup key. If the TLB contains a translation for that page number (a "TLB hit"), it immediately provides the corresponding physical page address. This is incredibly fast - typically just one processor cycle.

However, if the page number isn't found in the TLB (a "TLB miss"), the system must take a slower path to the page table in main memory. This is where the process becomes more complex but demonstrates the elegant fallback mechanisms in modern processors.

## The Page Table: Your Complete Address Directory

When the TLB misses, the system consults the page table, which you can see represented in the lower portion of the diagram. The page table is like a comprehensive phone book that contains the complete mapping of every virtual page to its corresponding physical page location. Unlike the TLB, which only holds recently used translations, the page table contains entries for all virtual pages that are currently valid in the system.

The page table lookup uses the page number from the virtual address as an index to find the correct entry. Once found, this entry provides the physical page address that corresponds to the virtual page. The system then typically updates the TLB with this new translation so that future accesses to the same page will result in TLB hits.

## Constructing the Real Address: The Magic Combination

Here's where the elegance of the system becomes apparent. Once you have the physical page address from either the TLB or the page table, the system combines it with the original offset from the virtual address to create the complete "**Real address.**" This combination happens through simple concatenation - **the physical page address becomes the high-order bits, and the offset becomes the low-order bits**.

This process is brilliant because the offset within a page remains the same whether you're talking about virtual or physical addresses. Only the page portion changes during translation. This consistency simplifies the hardware design and ensures that the relative positions of data within a page are preserved.

## Cache Operation: The Final Performance Layer

The real address then enters the cache subsystem, shown on the right side of the diagram. The cache operation divides the real address into two components: a "Tag" and a "Remainder." This division serves a different purpose from the virtual address division - **it's designed to optimize cache lookup performance**.

The cache uses the **remainder portion to index into specific cache locations**, while the **tag portion serves as an identifier to confirm that the data found** at that location is actually the data you're looking for. When the cache finds a match between the stored tag and the computed tag (a "cache hit"), it can immediately return the requested value. This provides access to data in just a few processor cycles.

## When Things Don't Go as Planned: Miss Scenarios

The diagram also shows the paths taken when lookups fail. On a cache miss, **the system must access main memory, which is significantly slower than cache access**. Main memory access might take dozens or even hundreds of processor cycles, which is why cache hits are so crucial for system performance.

Similarly, TLB misses require page table access, which adds additional latency to the address translation process. Modern processors implement various optimizations to minimize the impact of these misses, including multi-level TLBs and sophisticated replacement algorithms.

## The Elegant Parallel Processing

What makes this system particularly sophisticated is that many of these operations can happen in parallel or be pipelined. For instance, while the TLB is performing its lookup, other parts of the processor can be preparing for subsequent operations. This parallelism helps mask some of the inherent latency in the memory system.

## Why This Matters: The Performance Implications

Understanding this process helps explain why certain programming practices are more efficient than others. Programs that exhibit good "locality of reference" - meaning they tend to access nearby memory locations repeatedly - will experience more TLB hits and cache hits, leading to dramatically better performance. Conversely, programs that randomly access memory locations scattered throughout the address space will suffer from frequent misses and poor performance.

This system also explains why virtual memory enables powerful operating system features like process isolation, memory sharing between processes, and efficient memory allocation. Each process can have its own virtual address space, with the page tables providing the mapping to potentially scattered physical memory locations.

The combination of TLB, page table, and cache creates a memory hierarchy that provides the illusion of having vast amounts of fast memory while actually using a small amount of expensive fast memory (cache) backed by larger amounts of slower memory (main memory). This hierarchy is fundamental to making modern computing systems both powerful and affordable.

Would you like me to trace through a specific example with actual addresses to make this process even more concrete, or would you prefer to explore how this relates to other aspects of computer architecture like the RISC processors we discussed earlier?




# Access Control

## AP Access Control Bits
Hardware-level memory protection in each page table entry, checked during address translation.

**Four access levels:**
- **No Access**: Triggers hardware exception (used for deallocated memory, catches bugs)
- **Read Only**: Code, libraries, constants (prevents code injection attacks)
- **Read-Write**: Program data, variables (normal application memory)
- **Privileged Only**: OS kernel, drivers only (system-critical regions)

## Privilege Levels

- **User Mode**: Applications run with limited access to their own allocated memory
- **Kernel Mode**: OS operates with full system access, can modify page tables
- Creates security barrier - applications cannot access system memory or other processes

## Domain-Based Organization

Memory organized into domains (logical security zones) containing related pages.

**Two access relationships:**

- **Clients**: Normal programs that must strictly obey individual page permissions
- **Managers**: System software (OS kernel, drivers) that can bypass page permissions within their managed domains for maintenance operations

## How It Works

1. Every memory access triggers permission check in hardware
2. TLB caches both address translations AND permissions for speed
3. Violations generate immediate hardware exceptions
4. Operates below software level - difficult to circumvent

## Security Benefits

- Isolates processes from each other
- Prevents malicious code from accessing system areas
- Catches memory bugs (use-after-free, buffer overflows)
- Enables safe multitasking

**Example**: Compromised web browser cannot read other applications' memory or modify system files due to user-mode restrictions and domain isolation.






