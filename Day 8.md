# Day 8: I/O Handshaking, DMA, and 8085 Support Chips

Day 8 covers the May 31 afternoon screenshots. The session moves from CPU-controlled I/O into DMA and the Intel support chips commonly used around 8085-style systems. The main idea is that a microprocessor system is not only the CPU: practical systems need peripheral controllers for parallel I/O, timers, DMA, interrupts, storage, displays, and keyboards.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [I/O data transfer handshaking sequence](images/Day%208/day-8-io-data-transfer-handshaking-sequence.png) | CPU and I/O module exchange ready/accepted status signals. |
| 2 | [DMA data transfer scheme](images/Day%208/day-8-dma-data-transfer-scheme.png) | DMA transfers data directly between I/O and memory. |
| 3 | [DMA HOLD/HLDA transfer steps](images/Day%208/day-8-dma-hold-hlda-transfer-steps.png) | DMA controller takes bus control through `HOLD` and `HLDA`. |
| 4 | [Burst mode DMA data transfer](images/Day%208/day-8-burst-mode-dma-data-transfer.png) | Device transfers a block after taking control of the bus. |
| 5 | [Cycle stealing DMA data transfer](images/Day%208/day-8-cycle-stealing-dma-data-transfer.png) | DMA transfers small units while reducing CPU interference. |
| 6 | [Cycle stealing DMA efficiency note](images/Day%208/day-8-cycle-stealing-dma-efficiency-note.png) | Cycle stealing can use bus cycles when CPU is not using the bus. |
| 7 | [I/O mapped port number question](images/Day%208/day-8-io-mapped-port-number-question.png) | I/O mapped devices are identified by 8-bit port numbers. |
| 8 | [Intel 8155 programmable peripheral interface](images/Day%208/day-8-intel-8155-programmable-peripheral-interface.png) | 8155 provides RAM, I/O ports, and a timer. |
| 9 | [Intel 8255 programmable peripheral interface](images/Day%208/day-8-intel-8255-programmable-peripheral-interface.png) | 8255 gives programmable parallel I/O ports. |
| 10 | [Intel 8253 programmable interval timer](images/Day%208/day-8-intel-8253-programmable-interval-timer.png) | 8253 has counters, control word register, and read/write logic. |
| 11 | [Intel 8253 operating modes](images/Day%208/day-8-intel-8253-operating-modes.png) | Timer modes 0 through 5. |
| 12 | [Intel 8257 DMA controller](images/Day%208/day-8-intel-8257-dma-controller.png) | 8257 is a four-channel programmable DMA controller. |
| 13 | [Intel 8259 programmable interrupt controller](images/Day%208/day-8-intel-8259-programmable-interrupt-controller.png) | 8259 manages multiple interrupt request inputs. |
| 14 | [Intel 8259 PIC features](images/Day%208/day-8-intel-8259-pic-features.png) | 8259 is compatible with 8085/8086/8088 systems. |
| 15 | [Intel 8272 floppy disk controller](images/Day%208/day-8-intel-8272-floppy-disk-controller.png) | 8272 interfaces floppy disk systems to the microprocessor. |
| 16 | [Intel 8275 and 8279 display interfaces](images/Day%208/day-8-intel-8275-8279-display-interfaces.png) | 8275 handles CRT display; 8279 handles keyboard/display interface. |

## Handwritten Notes Linked To Day 8

Each handwritten page is shown first as a large full-page image. Click the image or page title to open the high-resolution extracted page, then read the deeper explanation below it.

### [85completed p011](images/HandWrittenNotes/85completed/page-011.jpg)

<a href="images/HandWrittenNotes/85completed/page-011.jpg"><img src="images/HandWrittenNotes/85completed/page-011.jpg" alt="85completed p011 handwritten note" width="960"></a>

Explanation: This page is mainly about Programmed I/O, I/O ports, and data-transfer modes. Use with programmed I/O. It separates I/O ports, port address, and data-transfer control. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**I/O view:** The page is separating device selection from actual data transfer. A port number or memory address selects the external interface, while control signals and the data bus perform the read or write. In I/O-mapped I/O, `IN` and `OUT` use port addresses; in memory-mapped I/O, normal memory-reference instructions access the device as if it were a memory location.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p012](images/HandWrittenNotes/85completed/page-012.jpg)

<a href="images/HandWrittenNotes/85completed/page-012.jpg"><img src="images/HandWrittenNotes/85completed/page-012.jpg" alt="85completed p012 handwritten note" width="960"></a>

Explanation: This page is mainly about Synchronous/asynchronous transfer, strobe, handshaking, and timing. Use with handshaking. It compares synchronous, asynchronous, strobe, and handshaking transfer. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Transfer method:** The transfer-mode comparison is about timing agreement between sender and receiver. Synchronous transfer assumes timing is known in advance, strobe transfer adds a control pulse to mark valid data, and handshaking adds feedback so both sides know when data is ready and accepted. Handshaking is slower than blind transfer, but safer for devices that do not run at the CPU speed.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p013](images/HandWrittenNotes/85completed/page-013.jpg)

<a href="images/HandWrittenNotes/85completed/page-013.jpg"><img src="images/HandWrittenNotes/85completed/page-013.jpg" alt="85completed p013 handwritten note" width="960"></a>

Explanation: This page is mainly about DMA basics, `HOLD/HLDA`, bus control, and DMA controller role. Use with DMA overview. It shows `HOLD`, `HLDA`, DMA controller, and bus-control handover. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Signal grouping:** Read the pin and signal pages by function, not by pin number. Some pins carry address/data, some control the current bus operation, some report status, some handle interrupts, some support DMA through `HOLD/HLDA`, and some handle serial I/O. Grouping them this way makes the pin diagram easier to reconstruct from memory.

**DMA meaning:** DMA is about temporary bus ownership. The CPU initializes the transfer, but the DMA controller requests the bus, waits for acknowledgement, generates addresses/control signals, moves data between I/O and memory, and then returns the bus. Burst mode favors fast block transfer, while cycle stealing reduces long CPU blocking by taking smaller bus opportunities.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p014](images/HandWrittenNotes/85completed/page-014.jpg)

<a href="images/HandWrittenNotes/85completed/page-014.jpg"><img src="images/HandWrittenNotes/85completed/page-014.jpg" alt="85completed p014 handwritten note" width="960"></a>

Explanation: This page is mainly about DMA modes: burst, cycle stealing, and transfer-rate reasoning. Use with burst and cycle stealing. It records when the CPU is blocked and when bus cycles are shared. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**DMA meaning:** DMA is about temporary bus ownership. The CPU initializes the transfer, but the DMA controller requests the bus, waits for acknowledgement, generates addresses/control signals, moves data between I/O and memory, and then returns the bus. Burst mode favors fast block transfer, while cycle stealing reduces long CPU blocking by taking smaller bus opportunities.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p015](images/HandWrittenNotes/85completed/page-015.jpg)

<a href="images/HandWrittenNotes/85completed/page-015.jpg"><img src="images/HandWrittenNotes/85completed/page-015.jpg" alt="85completed p015 handwritten note" width="960"></a>

Explanation: This page is mainly about 8255 programmable peripheral interface and port grouping. Use with 8255. It shows ports, groups, mode control, and why programmable I/O chips are useful. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**I/O view:** The page is separating device selection from actual data transfer. A port number or memory address selects the external interface, while control signals and the data bus perform the read or write. In I/O-mapped I/O, `IN` and `OUT` use port addresses; in memory-mapped I/O, normal memory-reference instructions access the device as if it were a memory location.

**Support-chip role:** These pages show why a microprocessor system needs helper chips. The CPU should not directly perform every low-level I/O, timing, DMA, interrupt, disk, display, or keyboard task in software. A support chip exposes registers, status bits, and control words so the CPU can configure behavior at a higher level.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p016](images/HandWrittenNotes/85completed/page-016.jpg)

<a href="images/HandWrittenNotes/85completed/page-016.jpg"><img src="images/HandWrittenNotes/85completed/page-016.jpg" alt="85completed p016 handwritten note" width="960"></a>

Explanation: This page is mainly about 8253 timer, 8257 DMA controller, and support-chip overview. Use with 8253 and 8257. It links timer/counter functions and DMA-controller channels. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**DMA meaning:** DMA is about temporary bus ownership. The CPU initializes the transfer, but the DMA controller requests the bus, waits for acknowledgement, generates addresses/control signals, moves data between I/O and memory, and then returns the bus. Burst mode favors fast block transfer, while cycle stealing reduces long CPU blocking by taking smaller bus opportunities.

**Support-chip role:** These pages show why a microprocessor system needs helper chips. The CPU should not directly perform every low-level I/O, timing, DMA, interrupt, disk, display, or keyboard task in software. A support chip exposes registers, status bits, and control words so the CPU can configure behavior at a higher level.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p017](images/HandWrittenNotes/85completed/page-017.jpg)

<a href="images/HandWrittenNotes/85completed/page-017.jpg"><img src="images/HandWrittenNotes/85completed/page-017.jpg" alt="85completed p017 handwritten note" width="960"></a>

Explanation: This page is mainly about 8259, 8257, 8272, 8275, 8279, and peripheral-controller purposes. Use with the support-chip list. It maps 8259, 8272, 8275, 8279, and other controllers to their system jobs. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**DMA meaning:** DMA is about temporary bus ownership. The CPU initializes the transfer, but the DMA controller requests the bus, waits for acknowledgement, generates addresses/control signals, moves data between I/O and memory, and then returns the bus. Burst mode favors fast block transfer, while cycle stealing reduces long CPU blocking by taking smaller bus opportunities.

**Support-chip role:** These pages show why a microprocessor system needs helper chips. The CPU should not directly perform every low-level I/O, timing, DMA, interrupt, disk, display, or keyboard task in software. A support chip exposes registers, status bits, and control words so the CPU can configure behavior at a higher level.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p018](images/HandWrittenNotes/85completed/page-018.jpg)

<a href="images/HandWrittenNotes/85completed/page-018.jpg"><img src="images/HandWrittenNotes/85completed/page-018.jpg" alt="85completed p018 handwritten note" width="960"></a>

Explanation: This page is mainly about Instruction-set/addressing/interrupt recap used to connect support chips back to 8085 programming. Use as a recap page. It connects addressing, interrupts, and instruction types back to peripheral interfacing. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Interrupt logic:** For interrupt pages, keep four questions separate: which source requested service, whether the request can be masked, where the CPU jumps, and how the interrupted program returns. Vectored interrupts already imply the service address, while non-vectored handling needs extra information or an externally supplied instruction. Priority matters only when more than one request is active.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

## 1. Programmed I/O and Handshaking

![I/O data transfer handshaking sequence](images/Day%208/day-8-io-data-transfer-handshaking-sequence.png)

<img src="images/HandWrittenNotes/85completed/page-011.jpg" width="960">

<img src="images/HandWrittenNotes/85completed/page-012.jpg" width="960">

In simple programmed I/O, the CPU controls the transfer directly. The program checks whether a device is ready, then reads or writes data using instructions such as `IN` and `OUT`. This is easy to understand, but it wastes CPU time when a device is slow.

Handshaking solves the timing mismatch between CPU and peripheral. The CPU may be fast, but an input device, printer, display interface, disk controller, or communication device may not be ready at the exact moment the CPU wants to transfer data.

The basic handshaking idea:

```text
source says data is ready
destination accepts or acknowledges
data transfer occurs
status is cleared or updated
next transfer begins
```

Compared with a fixed synchronous transfer, handshaking is safer because the transfer happens only when both sides are ready. Compared with a simple strobe, handshaking gives feedback: the sender knows the receiver accepted the data.

## 2. I/O Mapped Port Addressing

![I/O mapped port number question](images/Day%208/day-8-io-mapped-port-number-question.png)

The 8085 has special I/O instructions:

```asm
IN port
OUT port
```

In standard 8085 teaching, the port address is 8 bits, so there can be up to 256 input port addresses and 256 output port addresses. The same numeric port value can be used for input and output if hardware decoding treats read and write separately.

This is different from memory mapped I/O:

| Feature | I/O mapped I/O | Memory mapped I/O |
| --- | --- | --- |
| Address size in basic 8085 teaching | 8-bit port address | 16-bit memory address |
| Main instructions | `IN`, `OUT` | Memory-reference instructions |
| Address space used | I/O port space | Part of 64 KB memory space |
| Control signal type | I/O read/write | Memory read/write |

The screenshot's port-number question depends on this rule: I/O mapped devices are identified by port numbers, not full 16-bit memory addresses.

## 3. DMA: Direct Memory Access

![DMA data transfer scheme](images/Day%208/day-8-dma-data-transfer-scheme.png)

![DMA HOLD/HLDA transfer steps](images/Day%208/day-8-dma-hold-hlda-transfer-steps.png)

<img src="images/HandWrittenNotes/85completed/page-013.jpg" width="960">

DMA means **Direct Memory Access**. It lets a controller move data between an I/O device and memory without the CPU executing one instruction per byte.

The CPU still sets up the transfer. It may program the DMA controller with:

- starting memory address;
- transfer count;
- transfer direction;
- device/channel selection;
- mode of operation.

After setup, the DMA controller requests the system bus:

```text
DMA controller asserts HOLD
8085 completes current bus operation
8085 responds with HLDA
DMA controller controls address, data, and control buses
data moves between I/O and memory
DMA controller releases HOLD
8085 resumes bus control
```

The key hardware idea is bus ownership. During DMA, the CPU is not the bus master. The DMA controller temporarily becomes the bus master so it can generate addresses and read/write signals.

## 4. Burst Mode and Cycle Stealing

![Burst mode DMA data transfer](images/Day%208/day-8-burst-mode-dma-data-transfer.png)

![Cycle stealing DMA data transfer](images/Day%208/day-8-cycle-stealing-dma-data-transfer.png)

![Cycle stealing DMA efficiency note](images/Day%208/day-8-cycle-stealing-dma-efficiency-note.png)

<img src="images/HandWrittenNotes/85completed/page-014.jpg" width="960">

DMA mode decides how aggressively the controller uses the bus.

| DMA mode | What happens | CPU effect |
| --- | --- | --- |
| Burst mode | DMA transfers a whole block while holding the bus. | CPU is blocked from bus access during the burst. |
| Cycle stealing | DMA transfers one byte or a small unit at a time. | CPU is slowed but not blocked for a long continuous burst. |

Burst mode is efficient for moving large blocks quickly, but the CPU loses the bus until the burst finishes. Cycle stealing is friendlier to CPU execution because DMA uses individual bus cycles. The total transfer may take longer, but the CPU can continue between stolen cycles.

The note about efficiency means: if the CPU is not using the bus during some internal operation, a DMA transfer can sometimes use that available bus time. In real hardware, exact behavior depends on timing and controller design, but the study-level idea is that cycle stealing reduces long CPU suspension.

## 5. 8155 and 8255 Programmable Peripheral Interfaces

![Intel 8155 programmable peripheral interface](images/Day%208/day-8-intel-8155-programmable-peripheral-interface.png)

![Intel 8255 programmable peripheral interface](images/Day%208/day-8-intel-8255-programmable-peripheral-interface.png)

<img src="images/HandWrittenNotes/85completed/page-015.jpg" width="960">

The 8155 and 8255 are peripheral interface chips. They let the processor connect to external devices through programmable ports instead of building all control logic from discrete gates.

The 8255 is especially important in 8085 courses. It provides three 8-bit ports:

```text
Port A
Port B
Port C
```

Port C can also be split into upper and lower parts for control/handshaking. The processor writes a control word to configure port direction and mode. This is why the chip is called programmable: the same hardware can act as input, output, or handshake-capable interface depending on the control word.

The 8155 combines RAM, I/O, and timer functions. It is useful in small systems because it reduces the number of separate chips needed.

## 6. 8253 Programmable Interval Timer

![Intel 8253 programmable interval timer](images/Day%208/day-8-intel-8253-programmable-interval-timer.png)

![Intel 8253 operating modes](images/Day%208/day-8-intel-8253-operating-modes.png)

<img src="images/HandWrittenNotes/85completed/page-016.jpg" width="960">

The 8253 is a programmable timer/counter. It has multiple counters that can be loaded with count values and programmed into different modes.

Typical timer uses:

- generating delays;
- producing square waves;
- counting external events;
- creating periodic interrupts;
- timing I/O operations.

The CPU writes a control word to tell the 8253 which counter to use, how to load the count, and what mode to operate in. The common modes listed in the screenshot are mode 0 through mode 5. You do not need to memorize every waveform at first; the main point is that a timer chip offloads timing work from software loops.

## 7. 8257 DMA Controller

![Intel 8257 DMA controller](images/Day%208/day-8-intel-8257-dma-controller.png)

The 8257 is a programmable DMA controller. In typical 8085 study, it has four DMA channels. Each channel can hold address/count information for a device transfer.

Its job is to:

1. accept DMA requests from devices;
2. request the system bus from the CPU;
3. generate memory addresses;
4. generate control signals for read/write transfer;
5. update address/count information;
6. release the bus when the transfer is complete.

So the 8257 is the hardware that makes the Day 8 DMA diagrams practical.

## 8. 8259 Programmable Interrupt Controller

![Intel 8259 programmable interrupt controller](images/Day%208/day-8-intel-8259-programmable-interrupt-controller.png)

![Intel 8259 PIC features](images/Day%208/day-8-intel-8259-pic-features.png)

<img src="images/HandWrittenNotes/85completed/page-017.jpg" width="960">

The 8085 has only a limited number of interrupt input pins. The 8259 expands interrupt handling by accepting multiple interrupt request inputs and presenting a controlled interrupt request to the CPU.

The 8259 can:

- prioritize multiple interrupt requests;
- mask selected interrupt inputs;
- provide vector information;
- be cascaded for more interrupt sources;
- work with 8085, 8086, and 8088 style systems depending on configuration.

In system terms, the 8259 is an interrupt traffic controller. Devices request service from the 8259; the 8259 decides priority and communicates with the CPU.

## 9. 8272, 8275, and 8279

![Intel 8272 floppy disk controller](images/Day%208/day-8-intel-8272-floppy-disk-controller.png)

![Intel 8275 and 8279 display interfaces](images/Day%208/day-8-intel-8275-8279-display-interfaces.png)

The later support chips are specialized controllers:

| Chip | Main role |
| --- | --- |
| 8272 | Floppy disk controller. It handles low-level disk interface tasks. |
| 8275 | CRT controller. It helps generate display timing and character display control. |
| 8279 | Keyboard/display interface. It scans keyboard inputs and drives display outputs. |

The reason these chips exist is the same reason DMA and interrupt controllers exist: a general-purpose CPU should not have to perform every low-level timing and control task directly. Support chips turn complex peripheral timing into programmable registers and status/control signals.

## Points To Remember

- Programmed I/O keeps the CPU involved in the transfer.
- Handshaking prevents data transfer until both sides are ready.
- DMA transfers data directly between I/O and memory after setup.
- `HOLD` and `HLDA` are the 8085 bus request/acknowledge signals used for DMA.
- Burst DMA blocks the CPU for a continuous block transfer.
- Cycle stealing transfers smaller units and reduces long CPU blocking.
- 8255 is for programmable parallel I/O.
- 8253 is for timers/counters.
- 8257 is for DMA control.
- 8259 is for interrupt control.
- 8272, 8275, and 8279 are specialized storage/display/keyboard support chips.

## Sources

[S1] Intel Corporation, [MCS-80/85 Family User's Manual, January 1983](https://www.bitsavers.org/components/intel/MCS80/MCS80_85_Users_Manual_Jan83.pdf). Used for 8085 bus control, `HOLD/HLDA`, memory/I/O cycles, and peripheral-interface context.

[S2] Intel Corporation, [8080/8085 Assembly Language Programming Manual, May 1981](https://www.bitsavers.org/pdf/intel/ISIS_II/9800301-04_8080_8085_Assembly_Language_Programming_Manual_May81.pdf). Used for `IN`, `OUT`, I/O mapped instruction behavior, and programming notation.
