# Day 2: Machine Cycles, Timing, Address Decoding, and Instruction Formats

Day 2 moves from "what the 8085 is" into "how it communicates with memory and I/O." The main idea is that every instruction is broken into **machine cycles**, and every machine cycle is broken into **T-states**. Once you understand this, timing diagrams, memory interfacing, instruction length, and addressing modes become connected instead of separate topics.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [Machine cycle status/control table](images/Day%202/day-2-machine-cycle-status-control-table.png) | `IO/M`, `S1`, `S0`, `/RD`, `/WR`, and `/INTA` identify the current bus cycle. |
| 2 | [Opcode fetch cycle overview](images/Day%202/day-2-opcode-fetch-cycle-overview.png) | The CPU reads the opcode from memory and decodes the required work. |
| 3 | [Opcode fetch timing diagram](images/Day%202/day-2-opcode-fetch-timing-diagram.png) | Address, `ALE`, status lines, `/RD`, and data during an opcode fetch. |
| 4 | [MVI B,05H timing example](images/Day%202/day-2-mvi-b-05h-timing-example.png) | A two-byte instruction needs opcode fetch plus memory read. |
| 5 | [I/O read timing diagram](images/Day%202/day-2-io-read-timing-diagram.png) | `IN` reads data from an I/O port into the accumulator. |
| 6 | [Address decoding and chip select](images/Day%202/day-2-address-decoding-chip-select.png) | Decoders convert address lines into memory or I/O chip-select signals. |
| 7 | [ROM interface with MPU](images/Day%202/day-2-rom-interface-address-decoder.png) | ROM address pins, data pins, chip select, and output enable. |
| 8 | [Partial address decoding and foldback](images/Day%202/day-2-partial-address-decoding-foldback.png) | Incomplete decoding creates repeated/alias address ranges. |
| 9 | [Memory mapped vs I/O mapped I/O](images/Day%202/day-2-memory-mapped-vs-io-mapped-io.png) | Two ways to connect peripherals to an 8085 system. |
| 10 | [Memory chip capacity question](images/Day%202/day-2-memory-chip-capacity-question.png) | `4K x 8` means 4096 byte locations, 12 address lines, 8 data lines. |
| 11 | [Memory map highest address question](images/Day%202/day-2-memory-map-lowest-address-question.png) | Address range calculation using start address plus size minus one. |
| 12 | [Instruction set classification](images/Day%202/day-2-instruction-set-classification.png) | Instruction length, addressing modes, and operation groups. |
| 13 | [Instruction symbols and abbreviations 1](images/Day%202/day-2-instruction-symbols-abbreviations-1.png) | Meaning of common symbols used in instruction descriptions. |
| 14 | [Instruction symbols and abbreviations 2](images/Day%202/day-2-instruction-symbols-abbreviations-2.png) | Registers, register pairs, memory symbol `M`, port, flags, and operations. |
| 15 | [One-byte instruction forms](images/Day%202/day-2-one-byte-instruction-forms.png) | Operand information may be encoded in the opcode or implied. |
| 16 | [Two-byte MVI instruction example](images/Day%202/day-2-two-byte-instruction-mvi-example.png) | Opcode byte plus one immediate data byte. |
| 17 | [Implicit addressing mode](images/Day%202/day-2-implicit-addressing-mode.png) | Instructions where the operand is understood, often the accumulator. |

## Handwritten Notes Linked To Day 2

These handwritten pages are the revision layer for timing, memory interfacing, address decoding, memory/I/O mapping, and instruction formats. Read them after the screenshot index, then use the detailed explanations below to clean up the exact timing and byte-order rules.

| Page | Handwritten note | How to revise it with the screenshots |
| --- | --- | --- |
| [till46 p008](images/HandWrittenNotes/till46/page-008.jpg) | <img src="images/HandWrittenNotes/till46/page-008.jpg" width="420"> | Pairs with memory mapped versus I/O mapped I/O. Focus on whether the device is selected through the memory address space or the I/O port space. |
| [till46 p009](images/HandWrittenNotes/till46/page-009.jpg) | <img src="images/HandWrittenNotes/till46/page-009.jpg" width="420"> | Use with `ALE` and timing diagrams. It links `T`-state, machine cycle, and low-address latching. |
| [till46 p010](images/HandWrittenNotes/till46/page-010.jpg) | <img src="images/HandWrittenNotes/till46/page-010.jpg" width="420"> | Use with the machine-cycle status table. It separates instruction cycle, machine cycle, and T-state so timing questions do not blur them. |
| [till46 p011](images/HandWrittenNotes/till46/page-011.jpg) | <img src="images/HandWrittenNotes/till46/page-011.jpg" width="420"> | Use with opcode fetch and `ADD B`. The page shows how a complete instruction is built from one or more bus cycles. |
| [till46 p012](images/HandWrittenNotes/till46/page-012.jpg) | <img src="images/HandWrittenNotes/till46/page-012.jpg" width="420"> | Use with instruction-set symbols. It records word, instruction, opcode, address bus, and data bus meanings. |
| [till46 p013](images/HandWrittenNotes/till46/page-013.jpg) | <img src="images/HandWrittenNotes/till46/page-013.jpg" width="420"> | Use with the opcode-fetch timing diagram. Follow `ALE`, `/RD`, address lines, and data lines in order. |
| [till46 p014](images/HandWrittenNotes/till46/page-014.jpg) | <img src="images/HandWrittenNotes/till46/page-014.jpg" width="420"> | Use with `MVI B,05H`. It reinforces that the second byte is immediate data, not an address. |
| [till46 p015](images/HandWrittenNotes/till46/page-015.jpg) | <img src="images/HandWrittenNotes/till46/page-015.jpg" width="420"> | Use with instruction classification. This page is the handwritten map of addressing modes and instruction categories. |
| [till46 p016](images/HandWrittenNotes/till46/page-016.jpg) | <img src="images/HandWrittenNotes/till46/page-016.jpg" width="420"> | Use with address decoding and chip select. The key rule is that low lines select a location inside a chip while high lines select the chip. |
| [till46 p017](images/HandWrittenNotes/till46/page-017.jpg) | <img src="images/HandWrittenNotes/till46/page-017.jpg" width="420"> | Use with ROM interfacing. Track address decoder output, ROM data output, and why only the selected chip may drive the data bus. |
| [till46 p018](images/HandWrittenNotes/till46/page-018.jpg) | <img src="images/HandWrittenNotes/till46/page-018.jpg" width="420"> | Use with memory mapped/I/O mapped I/O. Pay attention to 16-bit memory addresses versus 8-bit port addresses. |
| [till46 p019](images/HandWrittenNotes/till46/page-019.jpg) | <img src="images/HandWrittenNotes/till46/page-019.jpg" width="420"> | Use with memory-capacity questions. Convert `4K x 8` to 4096 byte locations and 12 address lines. |
| [till46 p020](images/HandWrittenNotes/till46/page-020.jpg) | <img src="images/HandWrittenNotes/till46/page-020.jpg" width="420"> | Use with decoder logic. It shows how address bits combine into a chip-select signal. |
| [till46 p021](images/HandWrittenNotes/till46/page-021.jpg) | <img src="images/HandWrittenNotes/till46/page-021.jpg" width="420"> | Use with `LDA/STA` and machine-cycle counting. It connects instruction length to bus operations. |
| [till46 p022](images/HandWrittenNotes/till46/page-022.jpg) | <img src="images/HandWrittenNotes/till46/page-022.jpg" width="420"> | Use as the handwritten classification sheet: data transfer, arithmetic, logical, branch, stack/I/O/machine control. |
| [till46 p023](images/HandWrittenNotes/till46/page-023.jpg) | <img src="images/HandWrittenNotes/till46/page-023.jpg" width="420"> | Use with symbol notation. It records `A`, `B`, `C`, `D`, `E`, `H`, `L`, `M`, flags, register pairs, and memory symbols. |
| [till46 p024](images/HandWrittenNotes/till46/page-024.jpg) | <img src="images/HandWrittenNotes/till46/page-024.jpg" width="420"> | Use with one-byte instructions and control-signal generation. The important point is that implied operands need no extra data/address byte. |
| [85completed p020](images/HandWrittenNotes/85completed/page-020.jpg) | <img src="images/HandWrittenNotes/85completed/page-020.jpg" width="420"> | Use as a later recap of instruction format, machine cycles, and addressing modes after you finish Day 2 once. |

## 1. Machine Cycle Status and Control Signals

![Machine cycle status/control table](images/Day%202/day-2-machine-cycle-status-control-table.png)

The 8085 does not execute an instruction as one indivisible action. It performs a sequence of bus operations. A bus operation such as opcode fetch, memory read, memory write, I/O read, I/O write, or interrupt acknowledge is called a **machine cycle**. The status and control pins tell external hardware what type of cycle is happening.

The important lines are:

| Signal | Role |
| --- | --- |
| `IO/M` | Distinguishes memory cycles from I/O cycles. `0` means memory, `1` means I/O. |
| `S1`, `S0` | Status bits. Together with `IO/M`, they identify the machine cycle. |
| `/RD` | Active-low read command. When low, memory or I/O should place data on the bus. |
| `/WR` | Active-low write command. When low, memory or I/O should accept data from the bus. |
| `/INTA` | Active-low interrupt acknowledge signal. Used when the CPU acknowledges `INTR`. |

The slash means **active low**. A signal written `/RD = 0` is asserted; `/RD = 1` is inactive. A common beginner mistake is to read `RD = 0` as "read is off." It is the opposite: active-low means low voltage performs the action.

The status table is useful for hardware design. A memory chip, I/O chip, or decoder must not respond to every bus event. It should respond only when the status/control combination matches the operation it is designed for.

## 2. Opcode Fetch Cycle

![Opcode fetch cycle overview](images/Day%202/day-2-opcode-fetch-cycle-overview.png)

An **opcode** is the machine-code byte that tells the CPU what instruction to execute. During opcode fetch, the program counter points to the memory location containing the next opcode. The 8085 puts that address on the bus, asserts the memory-read control condition, and reads the opcode byte from memory.

Conceptually:

1. `PC` contains the address of the next instruction.
2. The address is placed on the address bus.
3. `ALE` allows the lower address byte to be latched from `AD0-AD7`.
4. Memory is enabled for a read.
5. The opcode byte comes to the CPU on `AD0-AD7`.
6. The instruction decoder decides what extra cycles are needed.
7. `PC` is advanced to the next byte unless the instruction changes program flow.

The opcode fetch cycle is special because it is the first cycle of an instruction. For many simple instructions, opcode fetch is the whole instruction. For multi-byte or memory-access instructions, opcode fetch is only the start.

## 3. Opcode Fetch Timing Diagram

![Opcode fetch timing diagram](images/Day%202/day-2-opcode-fetch-timing-diagram.png)

The timing diagram shows why `AD0-AD7` are called a **multiplexed address/data bus**. During `T1`, these pins carry the lower address bits `A0-A7`. After the lower address has been captured by an external latch, the same pins are reused as the data bus.

Important timing points:

| Time | What happens |
| --- | --- |
| `T1` | Address appears. `A8-A15` carry high address; `AD0-AD7` carry low address. |
| `ALE` high pulse | External latch stores `A0-A7`. |
| Later T-states | `AD0-AD7` stop being address pins and become data pins. |
| `/RD` low | Memory places the opcode byte on the data bus. |
| End of read | CPU samples the opcode byte and decodes it. |

You should remember the reason for multiplexing: it saves pins. The cost is extra timing complexity and the need for address latching.

## 4. `MVI B,05H` Timing Example

![MVI B,05H timing example](images/Day%202/day-2-mvi-b-05h-timing-example.png)

`MVI B,05H` is a **two-byte instruction**:

```asm
MVI B,05H
```

| Byte | Meaning |
| --- | --- |
| Byte 1 | Opcode for `MVI B,data`. |
| Byte 2 | Immediate data value `05H`. |

The CPU first fetches the opcode. After decoding it, the CPU knows one more byte is needed. It performs a memory read at the next address, reads `05H`, and loads that value into register `B`.

This is why instruction length matters. If the opcode is at address `2000H`, then the data byte is at `2001H`, and the next instruction starts at `2002H`.

Common trap: the immediate byte is not an address. In `MVI B,05H`, `05H` is the value loaded into `B`, not the memory location `0005H`.

## 5. I/O Read Timing

![I/O read timing diagram](images/Day%202/day-2-io-read-timing-diagram.png)

In I/O mapped I/O, the instruction `IN port` reads from an 8-bit port address and places the input byte into the accumulator. Intel describes `IN port` as moving the data placed on the 8-bit bidirectional data bus by the specified port into register `A`.

The timing resembles a memory read, but the status says it is an I/O operation. That distinction matters because memory and I/O devices may share the same data bus. External decoding uses `IO/M` and control signals so that only the selected port responds.

For exam thinking:

```asm
IN 05H
```

means:

```text
A <- data from input port 05H
```

It does not mean "read memory address 0005H." That would be a memory operation, not an I/O port operation.

## 6. Address Decoding and Chip Select

![Address decoding and chip select](images/Day%202/day-2-address-decoding-chip-select.png)

The 8085 can generate 16-bit addresses, so there are `2^16 = 65,536` possible byte addresses. But an individual memory chip usually covers only a smaller range. Address decoding is the hardware logic that decides which chip should respond to a given address.

For example, a `4K x 8` memory chip needs:

```text
4K locations = 4096 locations = 2^12
```

So the chip needs 12 internal address inputs. If the 8085 has 16 address lines, then 12 lines can select a location inside the chip, and the remaining high-order lines can be decoded to generate chip select.

Rule of thumb:

```text
address lines needed = log2(number of locations)
data lines needed = width of each location
```

For `4K x 8`, that means 12 address lines and 8 data lines.

## 7. ROM Interfacing

![ROM interface with MPU](images/Day%202/day-2-rom-interface-address-decoder.png)

ROM stores fixed program bytes. In an 8085 system, ROM is usually connected to the address bus and data bus like RAM, except it is read-only in normal operation.

The common pins are:

| ROM pin group | Meaning |
| --- | --- |
| Address inputs | Select which ROM location is being read. |
| Data outputs | Provide the byte stored at the selected location. |
| `CS` or `CE` | Chip select/chip enable. The ROM responds only when selected. |
| `OE` | Output enable. Allows ROM data onto the data bus during a read. |

ROM should drive the data bus only during a valid memory read of its address range. If two chips drive the data bus at the same time, bus contention occurs. That is why decoding and tri-state behavior are central hardware ideas.

## 8. Partial Decoding and Foldback

![Partial address decoding and foldback](images/Day%202/day-2-partial-address-decoding-foldback.png)

Partial decoding means the hardware does not check all high-order address bits. The device still works, but it may respond to multiple address ranges. This is called **address aliasing** or **foldback**.

Example idea:

```text
If a 4K chip uses A0-A11 internally,
but the decoder checks only A15 and ignores A14-A12,
then several 4K ranges may select the same chip.
```

This can be acceptable in very small systems because it reduces hardware. But it wastes address space and can confuse programs. In exam questions, if an address range repeats unexpectedly, suspect partial decoding.

## 9. Memory Mapped I/O vs I/O Mapped I/O

![Memory mapped vs I/O mapped I/O](images/Day%202/day-2-memory-mapped-vs-io-mapped-io.png)

The 8085 supports two common I/O styles:

| Feature | Memory mapped I/O | I/O mapped I/O |
| --- | --- | --- |
| Address space used | 16-bit memory address space | 8-bit I/O port address space |
| Control type | Memory read/write | I/O read/write |
| Instructions usable | Many memory-reference instructions | Mainly `IN` and `OUT` |
| Address capacity | Uses part of 64 KB memory map | Up to 256 input ports and 256 output ports in typical 8085 teaching model |
| Tradeoff | Flexible instructions, but consumes memory addresses | Saves memory space, but instruction choice is limited |

Important: memory mapped I/O is not "faster by definition" and I/O mapped I/O is not "always better." The choice depends on hardware simplicity, instruction flexibility, address space, and system design.

## 10. Memory Chip Capacity Question

![Memory chip capacity question](images/Day%202/day-2-memory-chip-capacity-question.png)

For a chip marked `4K x 8`:

```text
4K = 4 x 1024 = 4096 = 2^12 locations
x 8 = each location stores 8 bits
```

So:

| Property | Value |
| --- | --- |
| Number of locations | 4096 |
| Address lines needed | 12 |
| Data lines needed | 8 |
| Total capacity | 4096 bytes = 4 KB |

Common trap: `4K x 8` is not 32 KB. It is 32 kilobits, which equals 4 kilobytes. In 8085 memory maps, because each address points to one byte, a `4K x 8` chip occupies 4096 addresses.

## 11. Memory Address Range Calculation

![Memory map highest address question](images/Day%202/day-2-memory-map-lowest-address-question.png)

If an `8K` RAM has lowest address `1000H`, then:

```text
8K = 8 x 1024 = 8192 decimal = 2000H bytes
highest address = starting address + size - 1
highest address = 1000H + 2000H - 1 = 2FFFH
```

Why subtract one? Because the starting location is counted as the first byte. A block of 1 byte starting at `1000H` ends at `1000H`, not `1001H`.

Common trap:

```text
Wrong: 1000H + 2000H = 3000H
Correct: 1000H + 2000H - 1 = 2FFFH
```

## 12. Instruction Set Classification

![Instruction set classification](images/Day%202/day-2-instruction-set-classification.png)

The 8085 instruction set can be classified three useful ways:

| Basis | Classes |
| --- | --- |
| Length | One-byte, two-byte, three-byte |
| Addressing mode | Direct, register, register indirect, immediate, implicit/implied |
| Operation type | Data transfer, arithmetic, logical, branch/control, stack/I/O/machine control |

Instruction length tells how many memory locations the instruction occupies. Addressing mode tells where the operand comes from. Operation type tells what the instruction does.

Example:

```asm
MVI B,32H
```

Length: 2 bytes. Addressing: immediate. Operation group: data transfer.

```asm
JMP 2050H
```

Length: 3 bytes. Addressing: direct branch address. Operation group: branch.

## 13. Symbols and Abbreviations

![Instruction symbols and abbreviations 1](images/Day%202/day-2-instruction-symbols-abbreviations-1.png)

Instruction manuals use compact notation. You should be comfortable reading it.

| Symbol | Meaning |
| --- | --- |
| `addr` | 16-bit memory address. |
| `data` or `D8` | 8-bit immediate data. |
| `data16` or `D16` | 16-bit immediate data. |
| `r` | One register: `A`, `B`, `C`, `D`, `E`, `H`, or `L`. |
| `rp` | Register pair: `BC`, `DE`, `HL`, or `SP`, depending on instruction. |
| `M` | Memory location addressed by `HL`. |
| `port` | 8-bit I/O port address. |

The biggest trap is `M`. There is no physical register named `M` in the 8085. `M` means memory at the address stored in `HL`.

## 14. Register Pair and Operation Notation

![Instruction symbols and abbreviations 2](images/Day%202/day-2-instruction-symbols-abbreviations-2.png)

Some symbols describe actions:

| Notation | Meaning |
| --- | --- |
| `(A)` | Contents of accumulator. |
| `(HL)` | Contents of register pair HL, usually interpreted as an address. |
| `((HL))` | Contents of memory at the address held in HL. |
| `<-` | Transfer or assignment direction. |
| `+`, `-`, `AND`, `OR`, `XOR` | Arithmetic or logical operation. |
| `CY`, `Z`, `S`, `P`, `AC` | Carry, zero, sign, parity, auxiliary carry flags. |

When reading instruction definitions, separate the **container** from the **content**. `H` is the register. `(H)` is the value inside register `H`.

## 15. One-Byte Instructions

![One-byte instruction forms](images/Day%202/day-2-one-byte-instruction-forms.png)

A one-byte instruction contains all required information in the opcode itself. There is no extra immediate byte or address byte.

Examples:

| Instruction | Why one byte? |
| --- | --- |
| `MOV A,B` | Source and destination registers are encoded in the opcode. |
| `ADD B` | `A` is implied; register `B` is encoded in the opcode. |
| `CMA` | Accumulator is implied. |
| `HLT` | No operand is needed. |

This explains why some instructions seem short even though they do useful work. The operand is either encoded as bits inside the opcode or understood from the instruction definition.

## 16. Two-Byte `MVI` Instruction

![Two-byte MVI instruction example](images/Day%202/day-2-two-byte-instruction-mvi-example.png)

Two-byte instructions usually contain:

```text
byte 1 = opcode
byte 2 = 8-bit immediate data or 8-bit port address
```

For:

```asm
MVI A,32H
```

the opcode says "move immediate data to accumulator," and the next byte supplies the data `32H`. The program counter must skip both bytes before the next instruction begins.

Common trap: if a program starts at `2000H`, the second instruction may not start at `2001H`; it starts at `2002H` if the first instruction is two bytes.

## 17. Implicit Addressing Mode

![Implicit addressing mode](images/Day%202/day-2-implicit-addressing-mode.png)

In **implicit** or **implied** addressing, the operand is not written because the instruction definition already determines it.

Examples:

| Instruction | Implied operand/action |
| --- | --- |
| `CMA` | Complement accumulator. |
| `RAL` | Rotate accumulator left through carry. |
| `DAA` | Decimal-adjust accumulator after BCD addition. |
| `STC` | Set carry flag. |
| `CMC` | Complement carry flag. |

The exam trick is to ask "where is the operand?" The answer is: it is built into the instruction meaning. For accumulator instructions, the accumulator is assumed.

## Points To Remember

- Every instruction begins with opcode fetch.
- `AD0-AD7` first carry low address, then carry data.
- `ALE` is used to latch `A0-A7`; it is not a read or write signal.
- Active-low signals are active when they are `0`.
- `M` means memory addressed by `HL`, not a separate register.
- `4K x 8` means 4 KB, not 32 KB.
- Address range end = start + size - 1.
- In `MVI`, the second byte is data, not an address.
- Memory mapped I/O uses memory space; I/O mapped I/O uses port space.
- Partial decoding can make the same device appear at multiple addresses.

## Sources

[S1] Intel Corporation, [MCS-80/85 Family User's Manual, January 1983](https://www.bitsavers.org/components/intel/MCS80/MCS80_85_Users_Manual_Jan83.pdf). Used for 8085 bus timing, opcode fetch, machine cycle status/control signals, instruction/data formats, addressing modes, `IN`/`OUT`, and instruction behavior.

[S2] Intel Corporation, [8080/8085 Assembly Language Programming Manual, May 1981](https://www.bitsavers.org/pdf/intel/ISIS_II/9800301-04_8080_8085_Assembly_Language_Programming_Manual_May81.pdf). Used for assembly notation, instruction categories, immediate data, register notation, and instruction-set conventions.
