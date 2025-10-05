---
title: Nulltsuki
draft: true
tags:
---
## Generic Model of an I/O Module (Figure 7.1)
![[GenericModelofIO.png | center]]
This slide presents the fundamental architecture of how I/O systems connect to computers. The I/O Module acts as an intermediary between the computer's internal system bus and external devices.

The three key components of the system bus are:

- **Address Lines**: These carry memory addresses or I/O device addresses that the processor wants to access
- **Data Lines**: These carry the actual information being transferred between components
- **Control Lines**: These carry control signals that coordinate operations (like read/write commands)
//verify
The I/O module connects to multiple peripheral devices through dedicated links. This design provides several advantages: it standardizes the interface to the system bus, allows multiple devices to share the bus efficiently, and provides electrical isolation between the high-speed system bus and potentially slower peripheral devices.

## External Devices Overview

This slide defines external devices and their relationship to the computer system. External devices serve as the bridge between the computer's digital world and the physical environment where humans and other systems operate.

The key concept here is that **external devices don't connect directly to the system bus**. Instead, they connect through I/O modules, which handle the complex task of translating between the computer's internal protocols and the diverse requirements of different peripheral devices. This link carries three types of information: control signals (commands from the computer to the device), status signals (information about the device's current state), and data (the actual information being transferred).

## Categories of External Devices

External devices fall into three distinct categories, each serving different purposes:

**Human Readable Devices**: These are designed for direct interaction with computer users. Video display terminals (VDTs) convert **digital information into visual displays** that humans can read, while printers create physical copies of digital documents. These devices must handle character codes, graphics, and user interface elements.

**Machine Readable Devices**: These communicate with other equipment rather than humans. Magnetic disk and tape systems store and retrieve large amounts of digital data, while sensors convert physical phenomena (temperature, pressure, light) into digital signals, and actuators convert digital commands into physical actions (motors, valves, switches).

**Communication Devices**: These enable computers to communicate with remote systems. This category includes network interfaces, modems, and wireless communication devices that allow data exchange between geographically separated computers or between a computer and remote peripherals.

## Block Diagram of External Device 
![[BlockDiagramExternalDevice.png | center]]
An external device connects to an I/O module through control, data, and status signals.
- **Control signals** manage device actions (e.g. send data(input or read)/accept data(output or write)).
- **Data signals** handle bit transfer between the device and I/O module(data are in form of set of bits to be sent or received from the I/O module) .
- **Status signals** indicate the device's Ready or not ready condition.

Each device has **control logic** to execute instructions from the I/O module, and a **transducer** to convert between electrical signals and other energy forms. A **buffer** temporarily holds data during transfers—smaller (8–16 bits) for serial devices and larger for block-oriented ones like disk drives.

## Keyboard/Monitor Character Encoding

 how text information is represented digitally. The fundamental concept is that computers can only work with binary numbers, so every character must be assigned a unique binary code.

**Character Codes**: Each character (letter, number, symbol) gets a unique binary pattern. The International Reference Alphabet (IRA) uses 7 bits, allowing $2^7 = 128$ different characters. ASCII (American Standard Code for Information Interchange) extends this to 8 bits, allowing $2^8 = 256$ different characters.

**Character Types**:

- **Printable Characters**: These include all letters (A-Z, a-z), numbers (0-9), and symbols (!@#$%^&\*) that can be visually displayed
- **Control Characters**: These don't produce visible output but control how text is displayed or transmitted. For example, carriage return moves the cursor to the beginning of the line, and line feed moves it down one line

**Input Process**: When we press a key on a keyboard, a switch closes, creating an electrical signal. The keyboard's transducer detects this signal and converts it into the corresponding IRA or ASCII code. This digital code is then transmitted to the computer's I/O module through the connection interface.

**Output Process**: When the computer wants to display a character, it sends the character's IRA/ASCII code to the monitor's I/O interface. The monitor's transducer receives this code and converts it back into the appropriate visual representation on the screen, or executes the control function if it's a control character.

## Major Functions of I/O Module

There are five critical functions that every I/O module must perform:

- **Control and Timing**: Coordinates the timing and order of data transfers between fast processors and slower peripherals.
- **Processor Communication**: Handles command interpretation, data exchange, status reporting, and recognition of processor signals.
- **Device Communication**: Converts system bus protocols to match the specific needs of the connected device.
- **Data Buffering**: Temporarily stores data to manage speed differences between components.
- **Error Detection**: Monitors and reports errors to ensure reliable data transfer.

## Control and Timing Details

How control and timing work during a data transfer from an external device to the processor?

1. **Status Check**: The processor first queries the I/O module to determine if the attached device is ready for operation
2. **Status Response**: The I/O module responds with the current device status
3. **Command Issuance**: If the device is ready, the processor sends a data transfer command
4. **Data Retrieval**: The I/O module obtains data from the external device
5. **Data Transfer**: Finally, the data moves from the I/O module to the processor

This sequence ensures orderly data transfer and prevents data loss or corruption that could occur if transfers were attempted when devices weren't ready.

## Processor Communication Details

This slide elaborates on the four aspects of processor communication:

**Command Decoding**: The I/O module must interpret various commands from the processor. For example, a disk drive I/O module might receive a "SEEK track number" command, where the command travels on the control bus and the track number parameter travels on the data bus.

**Data Exchange**: Information flows bidirectionally between the processor and I/O module using the data bus. This includes both the actual data being processed and control parameters.

**Status Reporting**: Since peripheral devices are typically much slower than processors, status reporting is crucial. Common status signals include BUSY (device is currently working), READY (device is available for new operations), and various error condition indicators.

**Address Recognition**: In a system with multiple I/O devices, each must have a unique address. The I/O module monitors the address bus and only responds when its specific address is present, similar to how memory locations have unique addresses.

## Slide 11: Data Buffering Explanation

Data buffering addresses one of the fundamental challenges in computer systems: speed mismatches between components.

**Memory to Device Transfer**: Main memory can transfer data very rapidly in large bursts. The I/O module receives this high-speed data and stores it temporarily in its buffer, then sends it to the peripheral device at whatever slower rate the device can handle.

**Device to Memory Transfer**: Conversely, when data comes from a slow device, the I/O module collects it in its buffer and then transfers it to memory in an efficient burst, rather than tying up the memory system with a slow, continuous transfer.

**Bidirectional Speed Matching**: Some devices might actually be faster than memory access rates. In such cases, the I/O module buffers incoming data from the fast device and transfers it to memory at the memory's maximum rate.

This buffering function is essential for system efficiency because it prevents fast components from being slowed down by slower components.

## Slide 12: Error Detection Types

This slide categorizes the types of errors that I/O modules must detect:

**Mechanical and Electrical Errors**: These are hardware malfunctions reported by the device itself. Examples include paper jams in printers, which are detected by mechanical sensors, or bad disk tracks, which are identified when the disk drive's error correction systems detect uncorrectable data corruption.

**Transmission Errors**: These occur when data gets corrupted during transmission between the device and the I/O module. This can happen due to electrical interference, cable problems, or other communication issues.

**Parity Error Detection Example**: The slide provides a specific example using parity bits. In this system, the IRA character code uses 7 bits of an 8-bit byte. The eighth bit is set to make the total number of 1s in the byte either even (even parity) or odd (odd parity). When the I/O module receives a byte, it counts the 1s and checks if the parity is correct. If not, it knows a transmission error occurred.

For example, if using even parity and the character 'A' (binary 1000001) is transmitted, the parity bit would be set to 0 to make the total count of 1s even (2). If the received byte has an odd number of 1s, an error is detected.

## Slide 13: Block Diagram of I/O Module (Figure 7.3)

This detailed diagram shows the internal architecture of an I/O module and how it connects both to the system bus and to external devices.

**System Bus Interface**: The I/O module connects to all three system bus components:

- Data lines for information transfer
- Address lines for device selection
- Control lines for operation coordination

**Internal Components**:

- **Data Registers**: Store data temporarily during transfers
- **Status/Control Registers**: Hold device status information and control settings
- **I/O Logic**: The central control unit that manages all operations

**External Device Interface**: Each connected device has its own interface logic that handles device-specific communication protocols. The diagram shows multiple devices can connect to a single I/O module, each with dedicated data, status, and control connections.

This architecture allows the I/O module to act as an intelligent intermediary, handling the complexities of device management while presenting a standardized interface to the system bus.

## Slide 14: Three I/O Operation Techniques

This slide introduces the three fundamental approaches to handling I/O operations:

**Programmed I/O**: The processor directly controls every aspect of the I/O operation and waits for each operation to complete before continuing.

**Interrupt-driven I/O**: The processor initiates an I/O operation and then continues with other work. The I/O module interrupts the processor when the operation is complete.

**Direct Memory Access (DMA)**: The I/O module transfers data directly to/from memory without processor involvement for each data item.

These represent an evolution in I/O handling efficiency, with each successive technique reducing processor overhead and improving overall system performance.

## Slide 15: Programmed I/O Details

Programmed I/O is the simplest but least efficient I/O technique.

In this approach, the processor executes a program that gives it complete control over the I/O operation. When the processor issues an I/O command, it must wait until the entire operation completes before it can do anything else. This creates a significant inefficiency: if the processor is much faster than the I/O device (which is usually the case), the processor spends most of its time waiting idly for the slow I/O operation to finish.

For example, if a processor can execute millions of instructions per second but a disk drive takes several milliseconds to read data, the processor wastes enormous amounts of time just waiting. This is like having a very fast chef who must stop cooking entirely while waiting for water to boil, instead of preparing other ingredients.

## Slide 16: I/O Techniques Comparison Table (Table 7.1)

This table systematically compares the three I/O techniques across two dimensions:

**Interrupt Usage**:

- Programmed I/O and DMA don't use interrupts during data transfer
- Interrupt-driven I/O and DMA use interrupts to signal completion

**Data Path**:

- Programmed I/O and Interrupt-driven I/O transfer data through the processor
- DMA transfers data directly between I/O and memory, bypassing the processor

This creates four distinct combinations, with DMA being unique in providing direct memory access while also using interrupts for completion notification.

## Slide 17: Four Types of I/O Commands

When a processor communicates with an I/O module, it can issue four different types of commands:

**Control Commands**: These activate a peripheral device and specify what operation it should perform. For example, telling a printer to start printing or instructing a disk drive to move its read/write head to a specific track.

**Test Commands**: These check the status of an I/O module and its connected peripherals. The processor might test whether a device is ready, busy, or experiencing an error condition before attempting data transfer.

**Read Commands**: These instruct the I/O module to obtain data from a peripheral device and place it in an internal buffer where the processor can retrieve it.

**Write Commands**: These cause the I/O module to take data from the data bus and transmit it to the connected peripheral device.

## Slide 18: Three Techniques Flowchart Comparison (Figure 7.4)

This comprehensive flowchart shows how the same I/O operation (reading a block of data) is handled differently by each technique:

**Programmed I/O (left)**: Shows a continuous loop where the processor repeatedly checks device status, reads data word by word, and writes each word to memory. The processor cannot do anything else during this entire process.

**Interrupt-driven I/O (middle)**: The processor issues the read command, then continues with other work. When interrupted, it processes one word at a time until the entire block is transferred. This allows better processor utilization but still requires processor intervention for each data word.

**DMA (right)**: The processor simply issues a block read command and continues with other work. **The DMA module handles the entire transfer independently** and only interrupts when the complete block transfer is finished.

The efficiency improvement from left to right is dramatic - DMA requires minimal processor involvement compared to the other methods.

## Slide 19: I/O Mapping Summary

This slide explains two different approaches for how processors address I/O devices:

**Memory Mapped I/O**: I/O devices share the same address space as memory. From the processor's perspective, accessing an I/O device looks identical to accessing a memory location. This approach provides a large selection of addressing modes and doesn't require special I/O instructions, but it uses up some of the memory address space.

**Isolated I/O (Port-mapped I/O)**: I/O devices have a separate address space from memory. The system needs special control lines to distinguish between memory and I/O operations, and the processor requires special I/O instructions. However, this approach provides a complete separation between memory and I/O addressing.

The choice between these approaches affects both hardware design and software programming models.

## Slide 20: Interrupt-Driven I/O Concept

This slide explains the fundamental improvement that interrupt-driven I/O provides over programmed I/O.

The core problem with programmed I/O is processor idle time. When the processor issues an I/O command, it has nothing productive to do while waiting for the slow I/O operation to complete. Interrupt-driven I/O solves this by allowing the processor to issue the I/O command and then immediately switch to other useful work.

The process works like this: the processor starts an I/O operation, then continues executing other programs or handling other tasks. When the I/O module completes its work, it sends an interrupt signal to the processor. The processor then temporarily suspends its current work, handles the I/O completion (such as reading the transferred data), and then resumes its previous task.

This is analogous to a chef who starts water boiling, then continues preparing other ingredients while periodically checking if the water is ready, rather than standing idle watching the pot.

## Slide 21: Simple Interrupt Processing (Figure 7.6)

This flowchart details the step-by-step process of handling an interrupt:

**Hardware Steps**:

1. A device controller issues an interrupt signal
2. The processor finishes executing its current instruction
3. The processor acknowledges the interrupt
4. The processor saves its current state (PSW - Program Status Word and PC - Program Counter) on the control stack
5. The processor loads a new PC value that points to the interrupt handler

**Software Steps**: 6. The interrupt service routine saves any additional processor state 7. The interrupt service routine processes the interrupt 8. The interrupt service routine restores the saved state 9. The processor restores the original PSW and PC, returning to the interrupted program

This process ensures that interrupts can be handled without losing the processor's previous work context.

## Slide 22: Design Issues in Interrupt Implementation

Implementing interrupt-driven I/O creates two significant challenges:

**Device Identification Problem**: In a system with multiple I/O modules, how does the processor determine which specific device caused an interrupt? If multiple devices could potentially interrupt simultaneously, the processor needs a mechanism to identify the source.

**Priority Handling Problem**: If multiple interrupts occur simultaneously or while another interrupt is being processed, how does the processor decide which one to handle first? Some interrupts might be more urgent than others (for example, a power failure warning should probably be handled before a keyboard input).

These challenges require sophisticated interrupt management systems in modern computers.

## Device Identification Techniques 

This slide begins explaining four common approaches to solving the device identification problem:

**Multiple Interrupt Lines**: The most straightforward solution is to provide separate interrupt lines for different devices or groups of devices. However, this approach has limitations because even with multiple lines, each line typically connects to several I/O modules to keep the number of required lines manageable.

**Software Poll**: This approach uses just two lines: INTR (interrupt request) and INTA (interrupt acknowledge). When an interrupt occurs, the processor executes an Interrupt Service Routine (ISR) that systematically asks each I/O module whether it caused the interrupt. While this method works, it's slow and time-consuming, especially in systems with many I/O devices, because the processor might have to check many devices before finding the one that caused the interrupt.

**Daisy Chain (Hardware Poll, Vectored)**: In this approach, the interrupt acknowledge line is connected through all I/O modules in a chain. When the processor acknowledges an interrupt, the signal passes through the chain until it reaches the interrupting device. That device then places a vector (a unique identifier or the address of its service routine) on the data bus. This is called "vectored interrupt" because the processor can use this vector as a direct pointer to the appropriate device service routine, eliminating the need for polling.

**Bus Arbitration (Vectored)**: This is the most sophisticated approach. An I/O module must first gain control of the system bus before it can raise an interrupt request. When the processor detects the interrupt and responds with an acknowledgment, the requesting module places its vector directly on the data lines. This approach provides fast identification and fair access to interrupt services.

## Slide 25: Drawbacks of Programmed and Interrupt-Driven I/O

Both programmed I/O and interrupt-driven I/O share fundamental limitations:

**Transfer Rate Limitation**: The speed of I/O operations is constrained by how quickly the processor can test device status and service the device. Even with interrupt-driven I/O, the processor must execute several instructions for each piece of data transferred.

**Processor Overhead**: Both techniques require significant processor involvement in managing I/O transfers. For every word or byte transferred, the processor must execute multiple instructions to manage the transfer process.

These limitations become particularly problematic when transferring large volumes of data. For example, reading a large file from disk or transferring a high-resolution image requires moving millions of bytes, and having the processor involved in every transfer creates a significant bottleneck.

**DMA Solution**: When large data volumes need to be transferred, Direct Memory Access (DMA) provides a much more efficient solution by allowing I/O modules to transfer data directly to/from memory without processor involvement for each data item.

## Slide 26: DMA Block Diagram (Figure 7.12)

This diagram shows the internal structure of a DMA module, which contains several specialized registers:

**Data Count Register**: Keeps track of how many data items still need to be transferred. It decrements with each transfer and signals completion when it reaches zero.

**Data Register**: Temporarily holds data during the transfer process, acting as a buffer between the I/O device and memory.

**Address Register**: Contains the current memory address where data should be written or read from. This register automatically increments after each transfer to point to the next memory location.

**Control Logic**: Manages the entire DMA operation, including bus arbitration, address generation, and transfer coordination.

**External Connections**: The DMA module connects to data lines, address lines, and various control signals including request/acknowledge lines for communicating with I/O devices, and interrupt capability for signaling completion to the processor.

## Slide 27: DMA and Interrupt Breakpoints (Figure 7.13)

This timing diagram illustrates when DMA and interrupt processing can occur during a normal instruction cycle:

**Instruction Cycle Phases**: A typical instruction cycle consists of fetching the instruction, decoding it, fetching operands, executing the instruction, and storing results.

**DMA Breakpoints**: DMA transfers can occur at multiple points during the instruction cycle, typically between any two phases. This is possible because DMA operations only need access to the memory bus, and they can "steal" bus cycles when the processor isn't actively using the bus.

**Interrupt Breakpoint**: Interrupts are typically processed only at specific points, usually after the instruction execution is complete. This ensures that instructions are not interrupted in the middle of their execution, which could leave the processor in an inconsistent state.

**Bus Stealing**: DMA uses a technique called "cycle stealing" where it temporarily takes control of the bus to perform transfers while the processor is doing internal operations that don't require bus access.

## Slide 28: Summary of Three I/O Techniques

This slide provides a comprehensive comparison of all three I/O techniques:

**Programmed I/O**: Data transfers between processor and I/O module with complete processor control. The processor must wait for each operation to complete, making this the least efficient method but the simplest to implement.

**Interrupt-driven I/O**: The processor initiates operations and continues with other work, being interrupted only when I/O operations complete. This provides better processor utilization than programmed I/O but still requires processor involvement for each data item.

**Direct Memory Access (DMA)**: I/O modules and memory exchange data directly without processor involvement in the actual data transfer. The processor only needs to set up the transfer initially and handle completion notification. This provides the highest efficiency for large data transfers.

The evolution from programmed I/O through interrupt-driven I/O to DMA represents increasing sophistication in managing the trade-off between hardware complexity and system performance.

## Slide 29: Detailed Three Techniques Flowchart (Repeated)

This slide repeats the detailed flowchart comparison to reinforce the differences between the three techniques. The key insight is seeing how processor involvement decreases dramatically from left to right:

- **Programmed I/O**: Processor involved in every step
- **Interrupt-driven I/O**: Processor involved only when interrupted
- **DMA**: Processor involved only at the beginning and end

## Slide 30: Alternative DMA Configurations (Figure 7.14)

This slide shows three different ways to implement DMA in computer systems:

**Single-bus, Detached DMA**: All components (processor, DMA controller, I/O modules, and memory) connect to a single system bus. The DMA controller is a separate unit that competes with the processor for bus access. This is simple but can create bus contention.

**Single-bus, Integrated DMA-I/O**: DMA controllers are integrated directly into I/O modules. This reduces the number of separate components and can improve performance by reducing the distance data must travel, but it increases the complexity of I/O modules.

**I/O Bus Configuration**: This uses a hierarchical bus structure with a high-speed system bus connecting the processor, DMA controller, and memory, plus a separate I/O bus for connecting peripheral devices. This approach reduces contention on the main system bus and allows I/O operations to proceed more independently.

Each configuration represents different trade-offs between cost, complexity, and performance.

## Slide 31: Universal Serial Bus (USB)

USB has become the dominant interface for connecting peripheral devices to computers. Understanding its evolution helps appreciate how I/O technology develops:

**USB 1.0**: Initial version with 1.5 Mbps (Low Speed) - suitable for simple devices like keyboards and mice.

**USB 1.1**: Added Full Speed mode at 12 Mbps while maintaining Low Speed compatibility - enabled more complex devices like audio equipment.

**USB 2.0**: Introduced Hi Speed mode at 480 Mbps while maintaining backward compatibility - enabled video devices and external storage.

**USB 3.0**: Added Super Speed mode at 5 Gbps (5,000 Mbps) - enabled high-definition video and very fast external storage.

**USB 3.1**: Introduced SuperSpeed+ at 10 Gbps - supports the most demanding modern peripherals.

**Hierarchical Tree Topology**: USB creates a network of devices controlled by a root host controller, allowing up to 127 devices to be connected through hubs in a tree structure.

## Slide 32: FireWire Serial Bus

FireWire (IEEE 1394) was developed as an alternative high-speed serial interface:

**Purpose**: Originally designed to replace SCSI (Small Computer System Interface) for smaller systems while providing high performance without the complexity and cost of mainframe I/O systems.

**Configuration**: Uses daisy-chain topology allowing up to 63 devices per bus, with up to 1,022 buses interconnectable through bridges - potentially supporting over 64,000 devices.

**Hot Plugging**: Devices can be connected and disconnected while the system is running, without requiring system shutdown or manual reconfiguration.

**Automatic Configuration**: The system automatically detects new devices and assigns addresses without requiring manual termination or configuration, making it much more user-friendly than earlier interfaces like SCSI.

**Current Status**: While FireWire provided excellent performance and was popular for video equipment, USB's broader industry support and continuous evolution have made USB more dominant in most applications.

This comprehensive overview of Chapter 7 covers all the fundamental concepts of computer I/O systems, from basic device interfaces through advanced DMA techniques to modern serial bus standards. Each concept builds upon previous ones to create a complete picture of how computers communicate with the external world.