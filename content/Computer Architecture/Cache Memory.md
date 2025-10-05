---
title: Nulltsuki
draft: true
tags:
---
![[3levelofCache.png]]
What if we introduce multiple levels of cache? 
• L2cache is slower and typically larger than the L1 cache 
• L3cache is slower and typically larger than the L2 cache


![[Cache-MainMemory.png | center]]
**Main Memory:**
- Consists of 2n addressable words
- Each word has a unique n-bit address
- Memory consists of a number of fixed-length blocks of K words each
- There are $M = 2^n/K$ blocks
**Cache Memory:**
- Consisting of m blocks, called lines; 
- Each line contains K words; 
- $m≪M$
- Each line also includes control bits(Not shown in the figure)
![[CacheReadOp.png | center]]

![[CacheOrganization.png]]**When a cache hit occurs (word is in cache):** 
- the data and address buffers are disabled
- communication is only between processor and cache
- no system bus traffic.

![[CacheDesign.png]]
There are two types of cache addresses: 
- Physical addresses: Actual memory addresses; 
- Logical addresses: Virtual-memory addresses;
**Main idea behind virtual memory:** 
Disregard amount of main memory available; 
Transparent transfers to/from: • main memory and... • ...secondary memory: • Idea: use RAM, when space runs out use HD ;) • Requires a hardware memory management unit (MMU): • to translate virtual addresses into a physical addresses;

![[VirtualandPhysicalCache.png]]

Three techniques can be used for mapping blocks into cache lines: 
- Direct
- Associative; 
- Set associative
