# Day 9: 8086 Pin Signals, BIU/EU, Queue, and Register Organization

Day 9 is the transition from 8085 to 8086. The biggest change is not only that the 8086 is a 16-bit processor. The deeper change is that 8086 separates bus work and execution work into two cooperating units: the **Bus Interface Unit** and the **Execution Unit**. This creates instruction prefetching and introduces segment-based memory addressing.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [8086 pin configuration](images/Day%209/day-9-8086-pin-configuration.png) | 8086 pinout with multiplexed address/data/status/control signals. |
| 2 | [8086 min/max mode signals](images/Day%209/day-9-8086-min-max-mode-signals.png) | Compare minimum-mode and maximum-mode signal meanings. |
| 3 | [8086 internal architecture](images/Day%209/day-9-8086-internal-architecture.png) | 8086 is divided into BIU and EU. |
| 4 | [8086 block diagram](images/Day%209/day-9-8086-block-diagram.png) | Block diagram showing registers, bus interface, ALU, and control logic. |
| 5 | [8086 bus interface unit](images/Day%209/day-9-8086-bus-interface-unit.png) | BIU handles address/data transfer, queue fetch, memory, and I/O access. |
| 6 | [8086 execution unit](images/Day%209/day-9-8086-execution-unit.png) | EU receives opcodes from the queue, decodes, and executes instructions. |
| 7 | [8086 general-purpose register uses](images/Day%209/day-9-8086-general-purpose-register-uses.png) | AX, BX, CX, and DX have general and special-purpose roles. |

## Handwritten Notes Linked To Day 9

Each handwritten page is shown first as a large full-page image. Click the image or page title to open the high-resolution extracted page, then read the explanation below it.

### [85completed p021](images/HandWrittenNotes/85completed/page-021.jpg)

<a href="images/HandWrittenNotes/85completed/page-021.jpg"><img src="images/HandWrittenNotes/85completed/page-021.jpg" alt="85completed p021 handwritten note" width="960"></a>

Explanation: This page is mainly about 8086 bus interface unit, execution unit, prefetch queue, and registers. Use with the BIU/EU screenshots. It introduces 8086 architecture, queue, ALU, registers, and segment registers. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

The CPU-side idea is to separate the data path from the status path: the accumulator holds the working or result byte, while the flags describe that result for the next instruction or branch. For register pages, keep 8-bit registers and 16-bit register pairs separate. `B-C`, `D-E`, and `H-L` can be used together as address or data pairs, while `PC` and `SP` have special control roles. For 8086 pages, separate the external bus interface from instruction execution. The BIU handles fetching, address generation, segmentation, and the queue, while the EU decodes and executes instructions.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [85completed p022](images/HandWrittenNotes/85completed/page-022.jpg)

<a href="images/HandWrittenNotes/85completed/page-022.jpg"><img src="images/HandWrittenNotes/85completed/page-022.jpg" alt="85completed p022 handwritten note" width="960"></a>

Explanation: This page is mainly about 8086 architecture sketch, BIU, EU, queue, and internal buses. Use with the block diagram. It sketches internal buses, queue, BIU, EU, and physical address generation. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For 8086 pages, separate the external bus interface from instruction execution. The BIU handles fetching, address generation, segmentation, and the queue, while the EU decodes and executes instructions.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [86tilllnow p001](images/HandWrittenNotes/86tilllnow/page-001.jpg)

<a href="images/HandWrittenNotes/86tilllnow/page-001.jpg"><img src="images/HandWrittenNotes/86tilllnow/page-001.jpg" alt="86tilllnow p001 handwritten note" width="960"></a>

Explanation: This page is mainly about 8086 pin diagram, AD bus, 16-bit data bus, and min/max mode idea. Use with the pin configuration. It compares 8086 with 8085 and identifies multiplexed address/data pins. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For signal pages, group each pin by job: address/data transfer, control, status, clock/reset, interrupt, DMA, or serial I/O. This makes the pin diagram readable instead of a list to memorize blindly. The multiplexed-bus point is that the same physical pins carry low address first and data later, so `ALE` marks the moment when the low address must be captured externally. For 8086 pages, separate the external bus interface from instruction execution. The BIU handles fetching, address generation, segmentation, and the queue, while the EU decodes and executes instructions.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [86tilllnow p002](images/HandWrittenNotes/86tilllnow/page-002.jpg)

<a href="images/HandWrittenNotes/86tilllnow/page-002.jpg"><img src="images/HandWrittenNotes/86tilllnow/page-002.jpg" alt="86tilllnow p002 handwritten note" width="960"></a>

Explanation: This page is mainly about Minimum-mode and maximum-mode signal meanings. Use with min/max mode signals. It lists the control signals that change meaning with `MN/MX`. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For signal pages, group each pin by job: address/data transfer, control, status, clock/reset, interrupt, DMA, or serial I/O. This makes the pin diagram readable instead of a list to memorize blindly. For 8086 pages, separate the external bus interface from instruction execution. The BIU handles fetching, address generation, segmentation, and the queue, while the EU decodes and executes instructions.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [86tilllnow p003](images/HandWrittenNotes/86tilllnow/page-003.jpg)

<a href="images/HandWrittenNotes/86tilllnow/page-003.jpg"><img src="images/HandWrittenNotes/86tilllnow/page-003.jpg" alt="86tilllnow p003 handwritten note" width="960"></a>

Explanation: This page is mainly about `AD0-AD15`, `A16-A19/S3-S6`, `NMI`, `INTR`, and multiplexed 8086 signals. Use with multiplexed 8086 pins. It covers `AD0-AD15`, `A16-A19/S3-S6`, `NMI`, `INTR`, and bus status signals. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For signal pages, group each pin by job: address/data transfer, control, status, clock/reset, interrupt, DMA, or serial I/O. This makes the pin diagram readable instead of a list to memorize blindly. The multiplexed-bus point is that the same physical pins carry low address first and data later, so `ALE` marks the moment when the low address must be captured externally. For interrupts, keep four separate ideas: who requested service, whether it can be masked, where the CPU jumps, and how the interrupted program returns after the ISR.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [86tilllnow p004](images/HandWrittenNotes/86tilllnow/page-004.jpg)

<a href="images/HandWrittenNotes/86tilllnow/page-004.jpg"><img src="images/HandWrittenNotes/86tilllnow/page-004.jpg" alt="86tilllnow p004 handwritten note" width="960"></a>

Explanation: This page is mainly about 8086 minimum/maximum mode, control signals, and EU/BIU split. Use with min/max mode and the EU/BIU split. It connects signal mode to system design. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For signal pages, group each pin by job: address/data transfer, control, status, clock/reset, interrupt, DMA, or serial I/O. This makes the pin diagram readable instead of a list to memorize blindly. For 8086 pages, separate the external bus interface from instruction execution. The BIU handles fetching, address generation, segmentation, and the queue, while the EU decodes and executes instructions.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [86tilllnow p005](images/HandWrittenNotes/86tilllnow/page-005.jpg)

<a href="images/HandWrittenNotes/86tilllnow/page-005.jpg"><img src="images/HandWrittenNotes/86tilllnow/page-005.jpg" alt="86tilllnow p005 handwritten note" width="960"></a>

Explanation: This page is mainly about 8086 ALU, flags, bus interface, queue, and address path sketch. Use with internal architecture. It shows ALU, flags, bus interface, and queue support. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

The CPU-side idea is to separate the data path from the status path: the accumulator holds the working or result byte, while the flags describe that result for the next instruction or branch. For 8086 flags, separate status flags from control flags. Status flags describe the arithmetic or logical result, while `TF`, `IF`, and `DF` control single-step tracing, interrupt response, and string direction. For 8086 pages, separate the external bus interface from instruction execution. The BIU handles fetching, address generation, segmentation, and the queue, while the EU decodes and executes instructions.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [86tilllnow p006](images/HandWrittenNotes/86tilllnow/page-006.jpg)

<a href="images/HandWrittenNotes/86tilllnow/page-006.jpg"><img src="images/HandWrittenNotes/86tilllnow/page-006.jpg" alt="86tilllnow p006 handwritten note" width="960"></a>

Explanation: This page is mainly about BIU/EU, segment registers, instruction queue, and memory segmentation. Use with BIU/EU and segmentation. It summarizes the queue, segment registers, and physical-address calculation. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For register pages, keep 8-bit registers and 16-bit register pairs separate. `B-C`, `D-E`, and `H-L` can be used together as address or data pairs, while `PC` and `SP` have special control roles. For 8086 pages, separate the external bus interface from instruction execution. The BIU handles fetching, address generation, segmentation, and the queue, while the EU decodes and executes instructions.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

## 1. Why 8086 Feels Different From 8085

![8086 internal architecture](images/Day%209/day-9-8086-internal-architecture.png)

<img src="images/HandWrittenNotes/85completed/page-021.jpg" width="960">

The 8085 is an 8-bit processor with a 16-bit address bus, so it directly addresses `2^16 = 64 KB`. The 8086 has a 16-bit data bus and a 20-bit address bus, so it can address:

```text
2^20 = 1,048,576 bytes = 1 MB
```

The 8086 also changes the internal organization. Instead of treating instruction fetch and execution as one simple sequential flow, it splits work:

| Unit | Main job |
| --- | --- |
| BIU | Generates physical addresses, fetches instruction bytes, reads/writes memory or I/O, and maintains the instruction queue. |
| EU | Decodes and executes instructions using the ALU, flags, and general-purpose registers. |

The important consequence is overlap. While the EU executes the current instruction, the BIU can fetch upcoming instruction bytes into the queue. This does not make every instruction faster by itself, but it reduces wasted bus time when instruction bytes can be fetched ahead.

## 2. 8086 Pin Configuration

![8086 pin configuration](images/Day%209/day-9-8086-pin-configuration.png)

<img src="images/HandWrittenNotes/86tilllnow/page-001.jpg" width="960">

The 8086 is a 40-pin processor, but it exposes more address information than the 8085. It does this by multiplexing pins.

Important pin groups:

| Pin group | Meaning |
| --- | --- |
| `AD0-AD15` | Multiplexed address/data lines. They carry address bits during the early part of a bus cycle and data during the data part. |
| `A16/S3-A19/S6` | Multiplexed high-address/status lines. They carry high address bits and later status information. |
| `BHE/S7` | Bus High Enable/status. Helps select the upper byte of the 16-bit data bus. |
| `NMI`, `INTR` | Non-maskable and maskable interrupt inputs. |
| `RD`, `WR`, `M/IO`, `DT/R`, `DEN`, `ALE` | Minimum-mode bus control signals. |
| `MN/MX` | Selects minimum mode or maximum mode. |

Just as `AD0-AD7` are multiplexed in 8085, the 8086 multiplexes `AD0-AD15`. The reason is pin economy. A full 20-bit address bus plus 16-bit data bus plus control pins would need many more pins. Multiplexing saves pins but requires external latching and timing logic.

The 8086 can transfer a word through its 16-bit data bus, but memory is still byte-addressable. `BHE` and address bit `A0` together help decide whether the bus transfer involves the lower byte, upper byte, or full word.

## 3. Minimum Mode and Maximum Mode

![8086 min/max mode signals](images/Day%209/day-9-8086-min-max-mode-signals.png)

<img src="images/HandWrittenNotes/86tilllnow/page-002.jpg" width="960">

<img src="images/HandWrittenNotes/86tilllnow/page-004.jpg" width="960">

The `MN/MX` pin changes how the 8086 system is controlled.

| Mode | When used | Control idea |
| --- | --- | --- |
| Minimum mode | Single-processor systems | 8086 itself directly provides bus control signals such as `M/IO`, `RD`, `WR`, `ALE`, `DEN`, `DT/R`, `INTA`. |
| Maximum mode | Multiprocessor or coprocessor-style systems | 8086 outputs status signals such as `S2`, `S1`, `S0`; an external bus controller generates detailed bus commands. |

Minimum mode is easier to understand because it resembles 8085-style system design: the CPU outputs the control signals needed by memory and I/O. Maximum mode is used when the system needs more complex bus coordination. In maximum mode, the processor provides status information, and external hardware interprets that status to generate command signals.

This is why some pins have two names. The same physical pin can have one meaning in minimum mode and a different meaning in maximum mode. When reading any 8086 pin table, always check the selected mode first.

## 4. Bus Interface Unit

![8086 bus interface unit](images/Day%209/day-9-8086-bus-interface-unit.png)

<img src="images/HandWrittenNotes/86tilllnow/page-006.jpg" width="960">

The BIU is responsible for the outside-world interface and address generation.

Main BIU parts:

| BIU part | Role |
| --- | --- |
| Segment registers `CS`, `DS`, `SS`, `ES` | Hold segment base values used for address calculation. |
| Instruction pointer `IP` | Holds offset of the next instruction inside the code segment. |
| Address-generation adder | Combines segment base and offset to form a 20-bit physical address. |
| Bus-control logic | Performs memory and I/O reads/writes. |
| Instruction queue | Stores prefetched instruction bytes for the EU. |

The physical address calculation is:

```text
physical address = segment x 10H + offset
```

or equivalently:

```text
physical address = segment shifted left 4 bits + offset
```

Example:

```text
CS = 3000H
IP = 1234H
physical address = 30000H + 1234H = 31234H
```

The segment register gives the starting region; the offset selects a byte inside that region. This is how 16-bit registers can still produce a 20-bit address.

## 5. Execution Unit

![8086 execution unit](images/Day%209/day-9-8086-execution-unit.png)

<img src="images/HandWrittenNotes/86tilllnow/page-005.jpg" width="960">

The EU executes instructions. It does not normally fetch instruction bytes directly from memory; it takes them from the BIU queue.

Main EU parts:

| EU part | Role |
| --- | --- |
| ALU | Performs arithmetic and logical operations. |
| General-purpose registers | Hold operands and results. |
| Flag register | Stores condition and control flags. |
| Instruction decoder/control | Decodes opcodes and coordinates execution. |

When an instruction needs memory data, the EU asks the BIU for a memory access. The BIU calculates the physical address and performs the bus operation. This division is the heart of the 8086 architecture:

```text
EU decides what operation is needed.
BIU handles where and how the memory/I/O access happens.
```

If a branch, call, return, or interrupt changes the flow, the prefetched queue may no longer contain the correct upcoming bytes. Then the queue must be flushed and refilled from the new address.

## 6. Block Diagram and Queue

![8086 block diagram](images/Day%209/day-9-8086-block-diagram.png)

<img src="images/HandWrittenNotes/85completed/page-022.jpg" width="960">

The block diagram shows why the 8086 is often taught as a two-stage architecture:

```text
BIU: fetches and interfaces
EU: decodes and executes
```

The queue is a small buffer between those units. The 8086 queue holds prefetched instruction bytes. The BIU fills it when bus time is available; the EU consumes bytes from it when executing.

This creates a simple pipeline-like overlap:

```text
BIU fetches future instruction bytes
EU executes current instruction
```

Do not overstate this as modern pipelining. The 8086 does not have a deep modern pipeline. But the queue is still important because it separates instruction fetch from instruction execution.

## 7. General-Purpose Registers

![8086 general-purpose register uses](images/Day%209/day-9-8086-general-purpose-register-uses.png)

The 8086 has four main 16-bit general-purpose registers:

| Register | High/low halves | Common special use |
| --- | --- | --- |
| `AX` | `AH`, `AL` | Accumulator; default in many arithmetic, I/O, multiply/divide operations. |
| `BX` | `BH`, `BL` | Base register; can participate in effective-address formation. |
| `CX` | `CH`, `CL` | Count register; used by loops, shifts/rotates, and string repetition. |
| `DX` | `DH`, `DL` | Data register; used with `AX` in multiply/divide and for some I/O port addressing. |

This is a major difference from 8085. In 8085, the accumulator `A` is the central 8-bit working register. In 8086, `AX`, `BX`, `CX`, and `DX` are all more capable, and many can be split into 8-bit halves.

Example:

```asm
MOV AX,1234H
```

then:

```text
AH = 12H
AL = 34H
AX = 1234H
```

This makes the 8086 flexible: it can work with 16-bit words or 8-bit bytes using parts of the same register.

## Points To Remember

- 8086 has a 16-bit data bus and 20-bit address bus.
- 20 address lines allow `1 MB` physical memory addressing.
- `AD0-AD15` are multiplexed address/data lines.
- `A16/S3-A19/S6` are multiplexed high-address/status lines.
- `MN/MX` selects minimum mode or maximum mode.
- BIU handles bus operations, physical address generation, and instruction prefetch.
- EU decodes and executes instructions using the ALU, flags, and registers.
- Physical address = `segment x 10H + offset`.
- The instruction queue lets fetch and execute overlap.
- `AX`, `BX`, `CX`, and `DX` are general-purpose but have common special roles.

## Sources

[S1] Intel Corporation, [The 8086 Family User's Manual, October 1979](https://www.ardent-tool.com/CPU/docs/Intel/808x/manuals/9800722-03.pdf). Used for 8086 architecture, BIU/EU organization, segmentation, address formation, pins, modes, registers, and instruction-set context.

[S2] Intel Corporation, [iAPX 86,88 User's Manual, August 1981](https://www.dosdays.co.uk/media/intel/1981_iAPX_86_88_Users_Manual.pdf). Used for 8086/8088 programming model, flags, registers, addressing, and bus-interface concepts.
