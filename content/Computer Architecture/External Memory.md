---
title: Nulltsuki
draft: true
tags:
---
## Magnetic Disk Fundamentals
![[ReadWriteMechanismOfMagneticDisk.png | center]]
This slide establishes the foundation of magnetic disk technology. A magnetic disk consists of a **substrate** (the base material) coated with a **magnetizable material**. Think of the substrate as the canvas and the magnetizable coating as the paint that can be magnetized to store data.

The substrate has traditionally been made from aluminum or aluminum alloy because these materials are lightweight, rigid, and provide excellent flatness - critical for high-precision data storage. Recently, glass substrates have been introduced because they offer even better surface smoothness, lower thermal expansion, and greater durability than metal substrates.

The magnetizable coating can hold tiny magnetic domains that represent binary data (0s and 1s) by pointing magnetic fields in different directions.

## Slide 3: Inductive Write/Magnetoresistive Read Head

This slide shows the heart of disk operation - the read/write head mechanism. This is a sophisticated engineering solution that uses two different technologies for writing and reading data.

**Writing Process (Inductive Write Element):** The inductive write element uses electromagnetic induction. When write current flows through the coil, it creates a magnetic field that magnetizes the recording medium beneath it. The direction of the current determines the polarity of the magnetic field, which represents either a 0 or 1.

The write mechanism exploits the fact that electricity flowing through a coil
produces a magnetic field. Electric pulses are sent to the write head, and the resulting magnetic patterns are recorded on the surface below, with different patterns for positive and negative currents.

**Reading Process (Magnetoresistive Sensor):** The MR (magnetoresistive) sensor detects changes in magnetic fields by measuring resistance changes. When the sensor passes over magnetized areas, its electrical resistance changes depending on the magnetic field direction. This resistance change is converted to electrical signals that represent the stored data.

The traditional read mechanism exploits the fact that a magnetic field moving
relative to a coil produces an electrical current in the coil. When the surface of the
disk rotates under the head, it generates a current of the same polarity as the one
already recorded.

The **shield** components prevent magnetic interference between adjacent tracks, ensuring data integrity. The **track width** determines how densely data can be packed - narrower tracks allow more data storage but require more precise positioning.

## Slide 4: Disk Data Layout
![[DiskDataLayout.png | center]]
This illustrates the geometric organization of data on a disk. 
**Key Components:**
- **Platter**: The physical disk surface
- **Track**: Concentric circles on the disk surface where data is stored
- **Sector**: Fixed-size segments within each track (typically 512 bytes or 4KB)
- **Cylinder**: All tracks at the same distance from the center across multiple platters
- **Inter-sector gap**: Space between sectors for timing and synchronization
- **Inter-track gap**: Space between tracks to prevent interference

**Read-write heads** move radially across the disk surface via the **boom** mechanism. The **spindle** rotates the entire disk assembly at constant speed (typically 5,400 to 15,000 RPM).

The **direction of arm motion** is radial (in and out), while disk rotation provides the circumferential motion needed to access any location on the disk.

## Slide 5: Disk Layout Methods Comparison

This slide compares two fundamental approaches to organizing data on disks:
![[DiskLayoutMethods.png]]
**Constant Angular Velocity (CAV):** The disk rotates at constant speed, and each track is divided into the same number of sectors. However, outer tracks are physically longer than inner tracks, so outer sectors are larger and have lower data density. This is simpler to implement but wastes storage space on outer tracks.

**Multiple Zone Recording:** The disk surface is divided into zones, with outer zones containing more sectors per track than inner zones. This better utilizes the available surface area since outer tracks can accommodate more data due to their larger circumference.

**Calculation Example:** If an inner track has circumference C₁ and an outer track has circumference C₂ = 2×C₁, then in multiple zone recording, the outer track might have twice as many sectors, achieving uniform data density across the disk surface.

## Physical Characteristics of Disk Systems
This slide categorizes different disk system designs:

**Head Motion:**
- **Fixed head**: One head per track eliminates seek time but increases cost significantly
- **Movable head**: One head per surface requires mechanical movement but reduces cost
**Disk Portability:**
- **Nonremovable**: Higher performance, better environmental protection
- **Removable**: Flexibility for data transport and archival storage
**Sides:**
- **Single-sided**: Uses only one surface of the platter
- **Double-sided**: Uses both surfaces, doubling capacity
**Platters:**
- **Single platter**: Simpler, lower capacity
- **Multiple platter**: Higher capacity, more complex
**Head Mechanism:**
- **Contact (floppy)**: Head touches surface, higher wear but simpler
- **Fixed gap**: Small air gap, moderate performance
- **Aerodynamic gap (Winchester)**: Flying heads for high performance and durability

## Timing of Disk I/O Transfer
![[TimingOfDisk.png | center]]
**Wait for Device**: Time waiting for the disk controller to become available 
**Wait for Channel**: Time waiting for the I/O channel to become free 
**Seek**: Time to position the head over the correct track 
**Rotational Delay**: Time waiting for the desired sector to rotate under the head
**Data Transfer**: Time to actually read or write the data
The **Device Busy** period encompasses seek, rotational delay, and data transfer phases.

## Slide 8: Disk Performance Parameters (Detailed)

This slide provides comprehensive definitions of disk performance metrics:

**Seek Time**: For movable-head systems, this is the time required to position the head at the target track. Typical values range from 3-15 milliseconds.

**Rotational Delay (Latency)**: Time for the beginning of the target sector to reach the head position. On average, this is half a revolution.

**Calculation Example:** For a 7,200 RPM drive:

- One revolution = 60 seconds / 7,200 = 8.33 milliseconds
- Average rotational delay = 8.33 / 2 = 4.17 milliseconds

**Access Time**: The sum of seek time and rotational delay. This represents the time to position for reading/writing.

**Transfer Time**: Once positioned, this is the time to read or write data as the sector passes under the head.

**Transfer Rate Calculation:** If a sector is 512 bytes and passes under the head in 8.33 ms / (sectors per track), the transfer rate depends on the disk's rotational speed and data density.

## Slide 9: RAID Introduction

RAID (Redundant Array of Independent Disks) combines multiple physical drives into a logical unit. The slide introduces three fundamental characteristics:

1. **Single Logical Drive**: The operating system sees the array as one drive, simplifying management
2. **Data Striping**: Data is distributed across multiple drives to improve performance
3. **Redundancy**: Parity information enables data recovery if a drive fails

RAID has 7 levels (0-6), each with different trade-offs between performance, capacity, and fault tolerance.

## Slide 10: RAID Levels Summary Table

This comprehensive table compares all RAID levels:

**RAID 0 (Striping)**:

- Requires N disks
- No redundancy - if any disk fails, all data is lost
- Highest performance for large transfers
- Best for applications prioritizing speed over reliability

**RAID 1 (Mirroring)**:

- Requires 2N disks (100% overhead)
- Complete data duplication
- Excellent read performance, moderate write performance
- Best fault tolerance for single disk failures

**RAID 2 (Hamming Code)**:

- Requires N + m disks (where m ∝ log N)
- Uses error-correcting codes
- Parallel access with synchronized spindles
- Rarely implemented due to complexity

**RAID 3 (Bit-interleaved Parity)**:

- Requires N + 1 disks
- Single parity disk
- Excellent for large sequential transfers
- Poor for transaction processing

**RAID 4 (Block-level Parity)**:

- Requires N + 1 disks
- Independent access capability
- Parity disk can become bottleneck

**RAID 5 (Distributed Parity)**:

- Requires N + 1 disks
- Parity distributed across all disks
- Good balance of performance and fault tolerance
- Most commonly implemented level

**RAID 6 (Dual Parity)**:

- Requires N + 2 disks
- Can survive two simultaneous disk failures
- Higher write penalty due to dual parity calculation

## Slides 11-13: RAID Level Diagrams and Data Mapping

These slides provide visual representations of how data is organized in each RAID level:

**RAID 0**: Data strips are distributed across disks without redundancy. Strip 0 goes to disk 0, strip 1 to disk 1, etc.

**RAID 1**: Each strip is duplicated - strip 0 appears on both disk 0 and disk 4 (its mirror).

**RAID 5**: Notice how parity is distributed. For blocks 0-3, parity P(0-3) is on disk 4. For blocks 4-7, parity P(4-7) is on disk 3, and so on.

The logical disk appears as a continuous sequence of strips to the operating system, while the array management software handles the physical distribution.

## Slide 14: RAID 0 Performance Analysis

This slide explains RAID 0's performance characteristics:

**High Data Transfer Capacity**: Requires high bandwidth throughout the entire path from host memory to individual drives. If any component creates a bottleneck, the array's performance suffers.

**High I/O Request Rate**: RAID 0 can handle multiple small I/O requests in parallel by distributing them across different drives. The key insight is that if strip size is large relative to typical I/O requests, multiple requests can be serviced simultaneously.

**Performance Calculation Example**: If a single drive can handle 100 IOPS (I/O operations per second), a 4-drive RAID 0 array can theoretically handle 400 IOPS for small, randomly distributed requests.

## Slide 15: RAID 1 Characteristics

RAID 1 differs fundamentally from other RAID levels because it achieves redundancy through complete data duplication rather than parity calculations.

**Read Performance**: Either disk can service a read request, potentially doubling read performance if requests can be distributed intelligently.

**Write Performance**: Both disks must be updated, but there's no parity calculation overhead (no "write penalty").

**Recovery**: Simple - if one drive fails, the other contains a complete copy of all data.

**Cost Analysis**: RAID 1 provides only 50% storage efficiency (N drives provide N/2 capacity), making it the most expensive per gigabyte of usable storage.

## Slide 16: RAID 2 Technical Details

RAID 2 uses parallel access with Hamming error-correcting codes. This level was designed for environments with high error rates but is rarely implemented today.

**Hamming Code Calculation**: For N data disks, you need ⌈log₂(N+1)⌉ parity disks. For example, with 4 data disks, you need 3 parity disks to create a 7-bit Hamming code.

**Synchronization**: All disk spindles are synchronized so every head is at the same position simultaneously. This enables true parallel access but requires sophisticated hardware.

## Slide 17: RAID 3 Operation

RAID 3 uses simple parity instead of complex error-correcting codes:

**Parity Calculation**: For each bit position across all data disks, the parity bit is calculated as the XOR of all data bits.

**Example Calculation**: If disks contain bits: 1, 0, 1, 1, then parity = 1⊕0⊕1⊕1 = 1

**Recovery Process**: If disk 2 fails and contains bit value X, then: X = parity ⊕ (all other data bits) = 1⊕1⊕1⊕1 = 0

**Performance**: Excellent for large sequential transfers since all disks participate in every operation. Poor for small random accesses due to parallel access requirement.

## Slide 18: RAID 4 Write Penalty

RAID 4 introduces the concept of write penalty due to parity calculation requirements:

**Write Process for Small Updates**:

1. Read old data strip
2. Read old parity strip
3. Calculate new parity: New_Parity = Old_Parity ⊕ Old_Data ⊕ New_Data
4. Write new data strip
5. Write new parity strip

**Write Penalty Calculation**: Each logical write requires 4 physical operations (2 reads + 2 writes), creating a 4:1 write penalty.

**Parity Bottleneck**: Since all writes must update the single parity disk, it becomes a performance bottleneck in write-intensive applications.

## Slide 19: RAID 5 and RAID 6 Comparison

**RAID 5** solves RAID 4's parity bottleneck by distributing parity across all disks using round-robin allocation. This eliminates the single parity disk bottleneck while maintaining the same storage efficiency (N+1 disks for N disks of capacity).

**RAID 6** provides higher fault tolerance by calculating two different parity values (P and Q) using different mathematical operations. The Reed-Solomon codes commonly used can correct up to two simultaneous disk failures.

**Reliability Calculation**: For RAID 5 with mean time to failure (MTTF) of 100,000 hours per disk and mean time to repair (MTTR) of 24 hours, the probability of losing data due to two failures is very low but non-zero. RAID 6 virtually eliminates this risk.

## Slide 20: SSD Advantages over HDD

This slide transitions to solid-state drives, highlighting their advantages:

**High IOPS**: SSDs can deliver 10,000+ IOPS compared to 100-200 for traditional HDDs because there are no mechanical moving parts.

**Lower Latency**: No seek time or rotational delay - access time is typically under 0.1 milliseconds versus 10-15 milliseconds for HDDs.

**Power Consumption**: SSDs typically consume 2-4 watts versus 6-15 watts for HDDs, crucial for mobile devices and energy efficiency.

**Durability**: No mechanical parts means better resistance to shock and vibration.

## Slide 21: SSD Architecture

This slide shows the internal structure of an SSD:

**Controller**: Manages wear leveling, error correction, and interface protocols. Contains sophisticated algorithms to optimize performance and longevity.

**Flash Memory Components**: Store actual data in NAND flash memory arrays. Multiple components allow parallel operations for higher performance.

**Data Buffer/Cache**: Volatile memory (usually DRAM) that caches frequently accessed data and buffers write operations.

**Error Correction**: Hardware-based ECC (Error Correcting Code) to handle bit errors that naturally occur in flash memory.

## Slide 22: SSD Practical Issues

This slide addresses two critical challenges unique to SSDs:

**Performance Degradation**: As the SSD fills up and undergoes many write cycles, performance can degrade due to:

- Fragmentation of free space
- Increased garbage collection overhead
- Wear leveling operations

**Limited Write Endurance**: Flash memory cells can only endure a finite number of program/erase cycles (typically 1,000-100,000 depending on the technology).

**Write Process Complexity**:

1. Read entire block (typically 128KB-256KB) into RAM buffer
2. Erase entire block (cannot overwrite existing data)
3. Modify data in buffer
4. Write entire block back to flash

**Wear Leveling Algorithm Example**: If block A has been written 1,000 times and block B only 100 times, the controller will preferentially use block B for new writes to balance wear across all blocks.

**Life Estimation**: Modern SSDs monitor and report remaining life based on write patterns and wear leveling effectiveness.

## Slide 23: Optical Disk Products Table

This comprehensive table categorizes different optical storage technologies:

**CD (Compact Disk)**: Original audio format, 12cm diameter, ~650MB capacity, read-only after manufacturing.

**CD-ROM**: Computer data version of CD, same physical format but with stronger error correction for data reliability.

**CD-R**: Write-once technology using dye layer that can be permanently altered by laser.

**CD-RW**: Rewritable using phase-change materials that can switch between crystalline and amorphous states.

**DVD**: Higher density achieved through smaller pits and shorter laser wavelength (650nm vs 780nm for CD).

**Blu-ray**: Even higher density using 405nm blue-violet laser, enabling 25GB per layer.

**Capacity Progression Calculation**:

- CD: ~650 MB
- DVD: ~4.7 GB (single layer) to 17 GB (dual layer, double sided)
- Blu-ray: 25 GB (single layer) to 100+ GB (quad layer)

## Slide 24-25: CD-ROM Production Process

These slides detail the manufacturing process:

**Master Creation**: A high-intensity laser creates microscopic pits in a photosensitive material on a glass substrate.

**Stamping Process**: The master is used to create a metal stamper (die) that can press the pit pattern into polycarbonate plastic.

**Layer Structure** (from bottom to top):

1. **Polycarbonate substrate**: Provides structural support and contains the pits
2. **Aluminum layer**: Reflective surface that reflects laser light
3. **Protective acrylic**: Protects against scratches and environmental damage
4. **Label**: Printed identification and artwork

**Reading Process**: A laser shines through the transparent polycarbonate, reflects off the aluminum layer, and returns to a photodetector. Pits and lands (flat areas) reflect light differently, creating the digital signal.

## Slide 26: CD-ROM Block Format

This slide shows the detailed structure of a CD-ROM data block:

**Total Block Size**: 2,352 bytes

- **SYNC**: 12 bytes for synchronization
- **ID**: 4 bytes identifying the sector
- **Data**: 2,048 bytes of actual user data
- **L-ECC**: 288 bytes of layered error correction code

**Error Correction Strategy**: CD-ROM uses multiple layers of error correction because optical storage is more prone to errors than magnetic storage due to dust, scratches, and manufacturing defects.

**Efficiency Calculation**: User data efficiency = 2,048 / 2,352 = 87.1%. The remaining 12.9% is overhead for synchronization, identification, and error correction.

## Slide 27: CD-ROM Advantages and Disadvantages

**Advantages**:

- **Mass Production**: Once the master is created, copies are very inexpensive to produce
- **Portability**: Removable media allows easy data distribution and archival storage
- **Standardization**: Universal compatibility across different systems

**Disadvantages**:

- **Read-Only**: Cannot be modified after manufacturing
- **Slow Access**: Access time of 100-200 milliseconds compared to 10-15 ms for hard drives, due to the need to physically move the optical head and focus the laser

**Cost Analysis**: Initial mastering cost might be $1,000-$5,000, but individual disks cost under $1 to produce, making CD-ROM ideal for software distribution to many users.

## Slide 28: CD-R vs CD-RW Technologies

**CD-R (Write-Once)**: Uses an organic dye layer that permanently changes chemical structure when heated by a high-intensity laser. The changed areas have different reflectivity, creating the equivalent of pits and lands.

**CD-RW (Rewritable)**: Uses phase-change materials that can switch between two stable states:

- **Crystalline state**: Highly reflective, represents a "land"
- **Amorphous state**: Low reflectivity, represents a "pit"

**Phase Change Process**: Different laser power levels cause different effects:

- High power: Melts material, which cools rapidly into amorphous state
- Medium power: Heats material enough to recrystallize
- Low power: Reading without changing state

**Rewrite Limitation**: The phase-change material degrades after approximately 1,000 rewrite cycles due to thermal stress and material migration.

## Slide 29: DVD Structure Comparison

This slide compares CD-ROM and DVD-ROM construction:

**DVD Advantages**:

1. **Smaller pits**: Enable higher data density
2. **Shorter wavelength laser**: 650nm vs 780nm allows tighter focus
3. **Multiple layers**: Semi-reflective layers allow reading through to deeper layers
4. **Double-sided capability**: Data on both sides of the disk

**Capacity Calculation**:

- Single layer, single sided: 4.7 GB
- Single layer, double sided: 9.4 GB
- Dual layer, single sided: 8.5 GB
- Dual layer, double sided: 17 GB

**Layer Focusing**: The laser can focus on different depths to read different layers, with the upper layer being semi-reflective to allow light to pass through to the lower layer.

## Slide 30-31: Magnetic Tape Technology

These final slides cover magnetic tape storage:

**Tape Structure**: Flexible polyester base coated with magnetizable particles (metal oxide or pure metal).

**Track Organization**: Multiple parallel tracks run lengthwise along the tape. Modern tapes may have hundreds of tracks.

**Serpentine Recording**: The head assembly writes data in one direction across several tracks, then reverses direction and writes the next set of tracks in the opposite direction. This maximizes tape utilization.

**Block Structure**: Data is organized in physical records separated by inter-record gaps needed for start/stop operations during reading and writing.

**Performance Characteristics**:

- **Sequential Access**: Excellent for large data transfers
- **Random Access**: Poor due to mechanical tape movement
- **Capacity**: Very high - modern tapes can store multiple terabytes
- **Cost per GB**: Lowest among all storage technologies

**Use Cases**: Primarily for backup, archival storage, and applications requiring very large sequential data transfers.

This completes the comprehensive coverage of all slides in the External Memory chapter. Each storage technology represents different trade-offs between performance, capacity, cost, and reliability, allowing system designers to choose the most appropriate solution for their specific requirements.