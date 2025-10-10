---
title: 8086 Microprocessor
draft:
tags:
---

![[Nonoverlapping vs Overlapping Segments.png | 600 center]]


# 80x86 Addressing Modes

The CPU accesses operands(data) in various ways, called **addressing modes**. The number of addressing modes is determined when the microprocessor is designed. The 8086 provides a total of 7 distinct addressing modes.
1. [[#Register Addressing mode|Register]]
2. [[#Immediate Addressing mode|Immediate]]
3. [[#Direct Addressing mode|Direct]]
4. [[#Register indirect addressing mode|Register indirect]]
5. Based relative
6. Indexed relative
7. Based indexed relative


## Register Addressing mode
The register addressing mode involves the <u>use of registers to hold the data</u>. Memory is not accessed during execution - making it relatively fast.
```
MOV BX,DX       ;copy the contents of DX into BX
MOV ES,AX
ADD AL,BH       ;add the contents of BH to contents of AL
```
*Note : The Source and Destination registers must of same size. A source of 16bit register will throw an error when the destination register is 8 bit. For example* `MOV CL, AX`.

## Immediate Addressing mode
The <u>source operand is a constant</u> (8bit / 16bit data) and when the instruction is assembled, the operand comes immediately after the opcode. 
```
MOV AX,2550H    ;move 2550H into AX
MOV CX,625      ;load decimal value 625 into CX
MOV BL,40H
```
Addressing mode executes quickly but it's use in programming is limited. It can be used to load information into any registers except the segment/flag registers.
To move information to the segment registers, the data requires to be moved to a general-purpose register first and then to segment register. It will produce an error otherwise.
```
MOV AX,2550H
MOV DS,AX

MOV DS,0123H    ;illegal
```
![[Immediate Addressing Mode.png | 600 center]]
## Direct Addressing mode
 Compared to Immediate addressing mode (where the operand itself is provided in the instruction), in direct addressing, the the address of the operand is provided with the instruction.
```
MOV DL,[2400]   ;move contents of DS:2400H into DL
```
*Note : The bracket is essential*
Here, the data is in some memory location(s) and the address of the <u>data in memory comes immediately after the instruction</u>. This address is offset address and it's <u>physical address can be calculated by <b>shifting left</b> the <b>DS register</b> and adding it to the offset</u>. In the above case, the physical address is calculated by combining the contents of offset at location 2400 with `DS` (Data Segment register). 
![[DirectMemoryAddressing.png | 600 center]]


## Register indirect addressing mode
In the register indirect addressing mode, the <u>address of the memory location where the operand resides is held by a register</u>. The registers used for this purpose are `SI, DI, and BX`. These are combined with `DS` to generate the 20-bit physical address.
```
MOV AL,[BX]     ;moves the contents pointed by DS:BX into AL
```
*Note : The bracket is essential, otherwise it will be interpreted as moving contents in BX register to AL.*
The physical address is calculated by shifting `DS` left one hex position and adding `BX` to it. (Same goes for `SI` or `DI`)

