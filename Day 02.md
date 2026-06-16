# Day 02: Machine Cycles, Timing, Address Decoding, and Instruction Formats

Day 02 moves from "what the 8085 is" into "how it communicates with memory and I/O." The main idea is that every instruction is broken into **machine cycles**, and every machine cycle is broken into **T-states**. Once you understand this, timing diagrams, memory interfacing, instruction length, and addressing modes become connected instead of separate topics.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [Machine cycle status/control table](images/Day%2002/day-2-machine-cycle-status-control-table.png) | `IO/M`, `S1`, `S0`, `/RD`, `/WR`, and `/INTA` identify the current bus cycle. |
| 2 | [Opcode fetch cycle overview](images/Day%2002/day-2-opcode-fetch-cycle-overview.png) | The CPU reads the opcode from memory and decodes the required work. |
| 3 | [Opcode fetch timing diagram](images/Day%2002/day-2-opcode-fetch-timing-diagram.png) | Address, `ALE`, status lines, `/RD`, and data during an opcode fetch. |
| 4 | [MVI B,05H timing example](images/Day%2002/day-2-mvi-b-05h-timing-example.png) | A two-byte instruction needs opcode fetch plus memory read. |
| 5 | [I/O read timing diagram](images/Day%2002/day-2-io-read-timing-diagram.png) | `IN` reads data from an I/O port into the accumulator. |
| 6 | [Address decoding and chip select](images/Day%2002/day-2-address-decoding-chip-select.png) | Decoders convert address lines into memory or I/O chip-select signals. |
| 7 | [ROM interface with MPU](images/Day%2002/day-2-rom-interface-address-decoder.png) | ROM address pins, data pins, chip select, and output enable. |
| 8 | [Partial address decoding and foldback](images/Day%2002/day-2-partial-address-decoding-foldback.png) | Incomplete decoding creates repeated/alias address ranges. |
| 9 | [Memory mapped vs I/O mapped I/O](images/Day%2002/day-2-memory-mapped-vs-io-mapped-io.png) | Two ways to connect peripherals to an 8085 system. |
| 10 | [Memory chip capacity question](images/Day%2002/day-2-memory-chip-capacity-question.png) | `4K x 8` means 4096 byte locations, 12 address lines, 8 data lines. |
| 11 | [Memory map highest address question](images/Day%2002/day-2-memory-map-lowest-address-question.png) | Address range calculation using start address plus size minus one. |
| 12 | [Instruction set classification](images/Day%2002/day-2-instruction-set-classification.png) | Instruction length, addressing modes, and operation groups. |
| 13 | [Instruction symbols and abbreviations 1](images/Day%2002/day-2-instruction-symbols-abbreviations-1.png) | Meaning of common symbols used in instruction descriptions. |
| 14 | [Instruction symbols and abbreviations 2](images/Day%2002/day-2-instruction-symbols-abbreviations-2.png) | Registers, register pairs, memory symbol `M`, port, flags, and operations. |
| 15 | [One-byte instruction forms](images/Day%2002/day-2-one-byte-instruction-forms.png) | Operand information may be encoded in the opcode or implied. |
| 16 | [Two-byte MVI instruction example](images/Day%2002/day-2-two-byte-instruction-mvi-example.png) | Opcode byte plus one immediate data byte. |
| 17 | [Implicit addressing mode](images/Day%2002/day-2-implicit-addressing-mode.png) | Instructions where the operand is understood, often the accumulator. |

## Handwritten Notes Linked To Day 02

Each handwritten page is shown first as a large full-page image. The explanation below the image adds the technical layer: instruction behavior, bus cycles, flags, timing, address formation, or hardware reason behind the note.

### [till46 p008](images/HandWrittenNotes/till46/page-008.jpg)

<a href="images/HandWrittenNotes/till46/page-008.jpg"><img src="images/HandWrittenNotes/till46/page-008.jpg" alt="till46 p008 handwritten note" width="960"></a>

Technical explanation: memory-mapped I/O and I/O-mapped I/O differ in address space and control signaling. Memory-mapped I/O uses ordinary 16-bit memory addresses and memory-style read/write cycles. I/O-mapped I/O uses `IN` and `OUT` with an 8-bit port address and I/O control cycles. The data bus still carries the byte; the difference is how external decoding selects memory versus a device.

The practical exam distinction is address width and instruction choice. Memory-mapped I/O can use memory instructions and the full 16-bit address bus; I/O-mapped I/O uses the `IN/OUT` instruction format and an 8-bit port number. The external decoder and `IO/M` status decide which device responds.

### [till46 p009](images/HandWrittenNotes/till46/page-009.jpg)

<a href="images/HandWrittenNotes/till46/page-009.jpg"><img src="images/HandWrittenNotes/till46/page-009.jpg" alt="till46 p009 handwritten note" width="960"></a>

Technical explanation: `ALE` exists because the lower address and data share `AD0-AD7`. During the first T-state of a bus cycle the low address is valid on those pins, so an external latch captures it when `ALE` pulses. After that, the same pins can become the bidirectional data bus. Without the latch, memory decoding would lose `A0-A7` before the read or write finished.

A T-state is one processor clock state. Instruction timing is the number of T-states multiplied by the clock period. The counts are not arbitrary: every external memory or I/O access needs a bus cycle. Opcode fetch has address output, `ALE`, memory read control, data capture, and decode work, so it is longer than a plain memory read. Extra operand bytes, memory operands, stack transfers, I/O transfers, and slow memory wait states all add timing cost.

A machine cycle is one external bus operation: opcode fetch, memory read, memory write, I/O read, I/O write, interrupt acknowledge, and so on. An instruction cycle is the whole instruction and can contain several machine cycles. This is why instruction length, addressing mode, and T-state count are connected: every extra byte or external operand has to be fetched, read, or written on the bus.

### [till46 p010](images/HandWrittenNotes/till46/page-010.jpg)

<a href="images/HandWrittenNotes/till46/page-010.jpg"><img src="images/HandWrittenNotes/till46/page-010.jpg" alt="till46 p010 handwritten note" width="960"></a>

Technical explanation: `IO/M`, `S1`, and `S0` tell external hardware what type of machine cycle is occurring. That status decoding is how the system distinguishes opcode fetch, memory read, memory write, I/O read, I/O write, interrupt acknowledge, halt, and bus idle behavior. `/RD` and `/WR` then provide the actual read/write strobes, so status lines describe the cycle and control lines perform the transfer.

A machine cycle is one external bus operation: opcode fetch, memory read, memory write, I/O read, I/O write, interrupt acknowledge, and so on. An instruction cycle is the whole instruction and can contain several machine cycles. This is why instruction length, addressing mode, and T-state count are connected: every extra byte or external operand has to be fetched, read, or written on the bus.

A T-state is one processor clock state. Instruction timing is the number of T-states multiplied by the clock period. The counts are not arbitrary: every external memory or I/O access needs a bus cycle. Opcode fetch has address output, `ALE`, memory read control, data capture, and decode work, so it is longer than a plain memory read. Extra operand bytes, memory operands, stack transfers, I/O transfers, and slow memory wait states all add timing cost.

### [till46 p011](images/HandWrittenNotes/till46/page-011.jpg)

<a href="images/HandWrittenNotes/till46/page-011.jpg"><img src="images/HandWrittenNotes/till46/page-011.jpg" alt="till46 p011 handwritten note" width="960"></a>

Technical explanation: a T-state is one processor clock state. Instruction timing is the number of T-states multiplied by the clock period. The counts are not arbitrary: every external memory or I/O access needs a bus cycle. Opcode fetch has address output, `ALE`, memory read control, data capture, and decode work, so it is longer than a plain memory read. Extra operand bytes, memory operands, stack transfers, I/O transfers, and slow memory wait states all add timing cost.

A machine cycle is one external bus operation: opcode fetch, memory read, memory write, I/O read, I/O write, interrupt acknowledge, and so on. An instruction cycle is the whole instruction and can contain several machine cycles. This is why instruction length, addressing mode, and T-state count are connected: every extra byte or external operand has to be fetched, read, or written on the bus.

Opcode fetch is not just any memory read. The `PC` is placed on the address bus, `ALE` latches the low address, `/RD` enables memory output, and the opcode enters the instruction register. The CPU then decodes that byte to know whether more bytes, a memory access, an I/O cycle, a stack operation, or only internal execution is required.

### [till46 p012](images/HandWrittenNotes/till46/page-012.jpg)

<a href="images/HandWrittenNotes/till46/page-012.jpg"><img src="images/HandWrittenNotes/till46/page-012.jpg" alt="till46 p012 handwritten note" width="960"></a>

Technical explanation: separate opcode bytes from operand bytes. The opcode selects the operation; following bytes may be immediate data, a port number, a low address byte, or a high address byte. One-byte instructions encode everything in the opcode. Two-byte instructions usually add one data or port byte. Three-byte instructions usually add a 16-bit address or 16-bit immediate value, stored low byte first in memory.

When object code is placed in memory, `PC` steps through bytes, not mnemonics. If the opcode says the instruction has operand bytes, the following memory locations are consumed as data/address bytes. This is why a trace must increment `PC` by instruction length before deciding the next instruction address.

### [till46 p013](images/HandWrittenNotes/till46/page-013.jpg)

<a href="images/HandWrittenNotes/till46/page-013.jpg"><img src="images/HandWrittenNotes/till46/page-013.jpg" alt="till46 p013 handwritten note" width="960"></a>

Technical explanation: a T-state is one processor clock state. Instruction timing is the number of T-states multiplied by the clock period. The counts are not arbitrary: every external memory or I/O access needs a bus cycle. Opcode fetch has address output, `ALE`, memory read control, data capture, and decode work, so it is longer than a plain memory read. Extra operand bytes, memory operands, stack transfers, I/O transfers, and slow memory wait states all add timing cost.

A machine cycle is one external bus operation: opcode fetch, memory read, memory write, I/O read, I/O write, interrupt acknowledge, and so on. An instruction cycle is the whole instruction and can contain several machine cycles. This is why instruction length, addressing mode, and T-state count are connected: every extra byte or external operand has to be fetched, read, or written on the bus.

Opcode fetch is not just any memory read. The `PC` is placed on the address bus, `ALE` latches the low address, `/RD` enables memory output, and the opcode enters the instruction register. The CPU then decodes that byte to know whether more bytes, a memory access, an I/O cycle, a stack operation, or only internal execution is required.

`MVI B,05H` is a clean timing example because the first byte is the opcode and the second byte is immediate data. The CPU fetches the opcode from the address in `PC`, increments `PC`, then performs a memory read for `05H` and loads it into `B`. The data byte is not executed as an instruction; it is consumed as the operand selected by the opcode.

### [till46 p014](images/HandWrittenNotes/till46/page-014.jpg)

<a href="images/HandWrittenNotes/till46/page-014.jpg"><img src="images/HandWrittenNotes/till46/page-014.jpg" alt="till46 p014 handwritten note" width="960"></a>

Technical explanation: `MVI B,05H` is a clean timing example because the first byte is the opcode and the second byte is immediate data. The CPU fetches the opcode from the address in `PC`, increments `PC`, then performs a memory read for `05H` and loads it into `B`. The data byte is not executed as an instruction; it is consumed as the operand selected by the opcode.

A T-state is one processor clock state. Instruction timing is the number of T-states multiplied by the clock period. The counts are not arbitrary: every external memory or I/O access needs a bus cycle. Opcode fetch has address output, `ALE`, memory read control, data capture, and decode work, so it is longer than a plain memory read. Extra operand bytes, memory operands, stack transfers, I/O transfers, and slow memory wait states all add timing cost.

### [till46 p015](images/HandWrittenNotes/till46/page-015.jpg)

<a href="images/HandWrittenNotes/till46/page-015.jpg"><img src="images/HandWrittenNotes/till46/page-015.jpg" alt="till46 p015 handwritten note" width="960"></a>

Technical explanation: separate opcode bytes from operand bytes. The opcode selects the operation; following bytes may be immediate data, a port number, a low address byte, or a high address byte. One-byte instructions encode everything in the opcode. Two-byte instructions usually add one data or port byte. Three-byte instructions usually add a 16-bit address or 16-bit immediate value, stored low byte first in memory.

Addressing mode means where the operand comes from. Immediate addressing puts the operand in the instruction stream. Register addressing uses an internal register. Direct addressing stores the 16-bit memory address inside the instruction. Register-indirect addressing uses a register pair as a pointer. Implied addressing builds the operand into the instruction definition, such as accumulator, carry, or stack behavior.

### [till46 p016](images/HandWrittenNotes/till46/page-016.jpg)

<a href="images/HandWrittenNotes/till46/page-016.jpg"><img src="images/HandWrittenNotes/till46/page-016.jpg" alt="till46 p016 handwritten note" width="960"></a>

Technical explanation: memory interfacing is mostly address decoding. A `4K x 8` chip has 4096 byte locations, so it needs 12 low-order address lines because `2^12 = 4096`. Higher address lines decide which chip or block is enabled. Full decoding uses enough high address bits to make one unique range; partial decoding can make the same chip respond at mirrored address ranges.

For chip-select problems, write the address range in binary and mark which high-order bits are constant. Those constant high bits feed the decoder. The low bits go into the chip as internal address inputs, so changing them selects a location inside the chip rather than selecting a different chip.

### [till46 p017](images/HandWrittenNotes/till46/page-017.jpg)

<a href="images/HandWrittenNotes/till46/page-017.jpg"><img src="images/HandWrittenNotes/till46/page-017.jpg" alt="till46 p017 handwritten note" width="960"></a>

Technical explanation: memory interfacing is mostly address decoding. A `4K x 8` chip has 4096 byte locations, so it needs 12 low-order address lines because `2^12 = 4096`. Higher address lines decide which chip or block is enabled. Full decoding uses enough high address bits to make one unique range; partial decoding can make the same chip respond at mirrored address ranges.

A ROM interface example is solved by separating capacity from placement. Capacity gives the number of low address lines. Placement gives the high address pattern. The decoder asserts chip select only when the high address bits match that pattern and the processor performs a memory read cycle.

### [till46 p018](images/HandWrittenNotes/till46/page-018.jpg)

<a href="images/HandWrittenNotes/till46/page-018.jpg"><img src="images/HandWrittenNotes/till46/page-018.jpg" alt="till46 p018 handwritten note" width="960"></a>

Technical explanation: memory-mapped I/O and I/O-mapped I/O differ in address space and control signaling. Memory-mapped I/O uses ordinary 16-bit memory addresses and memory-style read/write cycles. I/O-mapped I/O uses `IN` and `OUT` with an 8-bit port address and I/O control cycles. The data bus still carries the byte; the difference is how external decoding selects memory versus a device.

In I/O-mapped transfer, the accumulator is usually the data endpoint: `IN port` loads `A`, and `OUT port` sends `A`. In memory-mapped transfer, ordinary memory instructions can target a device address, but that also consumes part of the memory address space.

### [till46 p019](images/HandWrittenNotes/till46/page-019.jpg)

<a href="images/HandWrittenNotes/till46/page-019.jpg"><img src="images/HandWrittenNotes/till46/page-019.jpg" alt="till46 p019 handwritten note" width="960"></a>

Technical explanation: memory interfacing is mostly address decoding. A `4K x 8` chip has 4096 byte locations, so it needs 12 low-order address lines because `2^12 = 4096`. Higher address lines decide which chip or block is enabled. Full decoding uses enough high address bits to make one unique range; partial decoding can make the same chip respond at mirrored address ranges.

Highest-address questions use `last = base + size - 1`. For `4K`, the size is `1000H`, not decimal 4000. If the starting address is aligned, the low 12 bits run from `000H` to `FFFH` while the decoded high bits remain fixed.

### [till46 p020](images/HandWrittenNotes/till46/page-020.jpg)

<a href="images/HandWrittenNotes/till46/page-020.jpg"><img src="images/HandWrittenNotes/till46/page-020.jpg" alt="till46 p020 handwritten note" width="960"></a>

Technical explanation: memory interfacing is mostly address decoding. A `4K x 8` chip has 4096 byte locations, so it needs 12 low-order address lines because `2^12 = 4096`. Higher address lines decide which chip or block is enabled. Full decoding uses enough high address bits to make one unique range; partial decoding can make the same chip respond at mirrored address ranges.

Partial decoding is a common hidden issue. If not all high-order lines are decoded, multiple address ranges can select the same chip. Full decoding avoids this aliasing by using enough high bits to make exactly one intended address block active.

### [till46 p021](images/HandWrittenNotes/till46/page-021.jpg)

<a href="images/HandWrittenNotes/till46/page-021.jpg"><img src="images/HandWrittenNotes/till46/page-021.jpg" alt="till46 p021 handwritten note" width="960"></a>

Technical explanation: a T-state is one processor clock state. Instruction timing is the number of T-states multiplied by the clock period. The counts are not arbitrary: every external memory or I/O access needs a bus cycle. Opcode fetch has address output, `ALE`, memory read control, data capture, and decode work, so it is longer than a plain memory read. Extra operand bytes, memory operands, stack transfers, I/O transfers, and slow memory wait states all add timing cost.

A machine cycle is one external bus operation: opcode fetch, memory read, memory write, I/O read, I/O write, interrupt acknowledge, and so on. An instruction cycle is the whole instruction and can contain several machine cycles. This is why instruction length, addressing mode, and T-state count are connected: every extra byte or external operand has to be fetched, read, or written on the bus.

### [till46 p022](images/HandWrittenNotes/till46/page-022.jpg)

<a href="images/HandWrittenNotes/till46/page-022.jpg"><img src="images/HandWrittenNotes/till46/page-022.jpg" alt="till46 p022 handwritten note" width="960"></a>

Technical explanation: separate opcode bytes from operand bytes. The opcode selects the operation; following bytes may be immediate data, a port number, a low address byte, or a high address byte. One-byte instructions encode everything in the opcode. Two-byte instructions usually add one data or port byte. Three-byte instructions usually add a 16-bit address or 16-bit immediate value, stored low byte first in memory.

Classification by length, operation, and addressing mode is useful because it predicts bus activity. A data-transfer instruction may be one byte if register-only, two bytes if immediate/port-based, or three bytes if a 16-bit address is embedded in the instruction.

### [till46 p023](images/HandWrittenNotes/till46/page-023.jpg)

<a href="images/HandWrittenNotes/till46/page-023.jpg"><img src="images/HandWrittenNotes/till46/page-023.jpg" alt="till46 p023 handwritten note" width="960"></a>

Technical explanation: the 8085 register set mixes 8-bit storage and 16-bit addressing. `B`, `C`, `D`, `E`, `H`, and `L` are 8-bit registers, but `BC`, `DE`, and `HL` can be used as 16-bit pairs. `HL` has the special memory role: `M` means the memory byte addressed by `HL`. `PC` and `SP` are 16-bit because program bytes and stack bytes live in the 64 KB memory space.

Separate opcode bytes from operand bytes. The opcode selects the operation; following bytes may be immediate data, a port number, a low address byte, or a high address byte. One-byte instructions encode everything in the opcode. Two-byte instructions usually add one data or port byte. Three-byte instructions usually add a 16-bit address or 16-bit immediate value, stored low byte first in memory.

Addressing mode means where the operand comes from. Immediate addressing puts the operand in the instruction stream. Register addressing uses an internal register. Direct addressing stores the 16-bit memory address inside the instruction. Register-indirect addressing uses a register pair as a pointer. Implied addressing builds the operand into the instruction definition, such as accumulator, carry, or stack behavior.

### [till46 p024](images/HandWrittenNotes/till46/page-024.jpg)

<a href="images/HandWrittenNotes/till46/page-024.jpg"><img src="images/HandWrittenNotes/till46/page-024.jpg" alt="till46 p024 handwritten note" width="960"></a>

Technical explanation: separate opcode bytes from operand bytes. The opcode selects the operation; following bytes may be immediate data, a port number, a low address byte, or a high address byte. One-byte instructions encode everything in the opcode. Two-byte instructions usually add one data or port byte. Three-byte instructions usually add a 16-bit address or 16-bit immediate value, stored low byte first in memory.

Control-signal generation is the bridge between the decoded instruction and the outside bus. After opcode fetch, the control unit chooses whether to assert memory read, memory write, I/O read, I/O write, interrupt acknowledge, or no external transfer. That is why a one-byte instruction can still take multiple T-states: the CPU must fetch and decode the opcode even when no explicit operand byte follows.

### [85completed p020](images/HandWrittenNotes/85completed/page-020.jpg)

<a href="images/HandWrittenNotes/85completed/page-020.jpg"><img src="images/HandWrittenNotes/85completed/page-020.jpg" alt="85completed p020 handwritten note" width="960"></a>

Technical explanation: the 8085 register set mixes 8-bit storage and 16-bit addressing. `B`, `C`, `D`, `E`, `H`, and `L` are 8-bit registers, but `BC`, `DE`, and `HL` can be used as 16-bit pairs. `HL` has the special memory role: `M` means the memory byte addressed by `HL`. `PC` and `SP` are 16-bit because program bytes and stack bytes live in the 64 KB memory space.

Separate opcode bytes from operand bytes. The opcode selects the operation; following bytes may be immediate data, a port number, a low address byte, or a high address byte. One-byte instructions encode everything in the opcode. Two-byte instructions usually add one data or port byte. Three-byte instructions usually add a 16-bit address or 16-bit immediate value, stored low byte first in memory.

A T-state is one processor clock state. Instruction timing is the number of T-states multiplied by the clock period. The counts are not arbitrary: every external memory or I/O access needs a bus cycle. Opcode fetch has address output, `ALE`, memory read control, data capture, and decode work, so it is longer than a plain memory read. Extra operand bytes, memory operands, stack transfers, I/O transfers, and slow memory wait states all add timing cost.

A machine cycle is one external bus operation: opcode fetch, memory read, memory write, I/O read, I/O write, interrupt acknowledge, and so on. An instruction cycle is the whole instruction and can contain several machine cycles. This is why instruction length, addressing mode, and T-state count are connected: every extra byte or external operand has to be fetched, read, or written on the bus.

Addressing mode means where the operand comes from. Immediate addressing puts the operand in the instruction stream. Register addressing uses an internal register. Direct addressing stores the 16-bit memory address inside the instruction. Register-indirect addressing uses a register pair as a pointer. Implied addressing builds the operand into the instruction definition, such as accumulator, carry, or stack behavior.

8085 interrupts combine priority, masking, and vectoring. `TRAP` is highest priority and non-maskable. `RST 7.5`, `RST 6.5`, and `RST 5.5` are maskable vectored interrupts with fixed restart addresses: `RST n` maps to `n x 8`, so `RST 7.5` starts at `003CH`, `RST 6.5` at `0034H`, and `RST 5.5` at `002CH`. `INTR` is maskable and non-vectored, so external hardware must supply the instruction during acknowledge.

Support chips offload repeated interface work from the CPU. `8255` provides programmable parallel I/O ports, `8253` provides programmable timing/counting, `8257` manages DMA channels, and `8259` prioritizes and vectors interrupt requests. Devices such as `8272`, `8275`, and `8279` specialize further for floppy, display, keyboard, or display-control tasks.

## 1. Machine Cycle Status and Control Signals

![Machine cycle status/control table](images/Day%2002/day-2-machine-cycle-status-control-table.png)

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

![Opcode fetch cycle overview](images/Day%2002/day-2-opcode-fetch-cycle-overview.png)

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

![Opcode fetch timing diagram](images/Day%2002/day-2-opcode-fetch-timing-diagram.png)

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

![MVI B,05H timing example](images/Day%2002/day-2-mvi-b-05h-timing-example.png)

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

![I/O read timing diagram](images/Day%2002/day-2-io-read-timing-diagram.png)

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

![Address decoding and chip select](images/Day%2002/day-2-address-decoding-chip-select.png)

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

![ROM interface with MPU](images/Day%2002/day-2-rom-interface-address-decoder.png)

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

![Partial address decoding and foldback](images/Day%2002/day-2-partial-address-decoding-foldback.png)

Partial decoding means the hardware does not check all high-order address bits. The device still works, but it may respond to multiple address ranges. This is called **address aliasing** or **foldback**.

Example idea:

```text
If a 4K chip uses A0-A11 internally,
but the decoder checks only A15 and ignores A14-A12,
then several 4K ranges may select the same chip.
```

This can be acceptable in very small systems because it reduces hardware. But it wastes address space and can confuse programs. In exam questions, if an address range repeats unexpectedly, suspect partial decoding.

## 9. Memory Mapped I/O vs I/O Mapped I/O

![Memory mapped vs I/O mapped I/O](images/Day%2002/day-2-memory-mapped-vs-io-mapped-io.png)

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

![Memory chip capacity question](images/Day%2002/day-2-memory-chip-capacity-question.png)

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

![Memory map highest address question](images/Day%2002/day-2-memory-map-lowest-address-question.png)

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

![Instruction set classification](images/Day%2002/day-2-instruction-set-classification.png)

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

![Instruction symbols and abbreviations 1](images/Day%2002/day-2-instruction-symbols-abbreviations-1.png)

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

![Instruction symbols and abbreviations 2](images/Day%2002/day-2-instruction-symbols-abbreviations-2.png)

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

![One-byte instruction forms](images/Day%2002/day-2-one-byte-instruction-forms.png)

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

![Two-byte MVI instruction example](images/Day%2002/day-2-two-byte-instruction-mvi-example.png)

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

![Implicit addressing mode](images/Day%2002/day-2-implicit-addressing-mode.png)

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

## Research Deep Dive: Bus Cycles, Decoding, and Address Aliasing

A useful way to study Day 02 is to separate three layers that often get mixed together:

| Layer | Question it answers |
| --- | --- |
| Instruction layer | What operation is the CPU trying to perform? |
| Bus-cycle layer | Which external transfer is needed right now? |
| Hardware decoding layer | Which memory or I/O chip is selected by the address/control signals? |

### Instruction Cycle Versus Machine Cycle

`MVI B,05H` is one instruction cycle, but it is not one machine cycle. It needs:

| Machine cycle | Why it is needed |
| --- | --- |
| Opcode fetch | Fetch the opcode byte for `MVI B,data`. |
| Memory read | Fetch the immediate data byte `05H`. |

That is why the program counter advances by 2. The first byte tells the CPU what to do; the second byte is the operand.

### Status Lines Are External Clues

The 8085 internally knows which cycle it is performing, but external hardware needs signals. `IO/M`, `S1`, and `S0` tell the outside system whether the current bus operation is opcode fetch, memory read, memory write, I/O read, I/O write, or interrupt acknowledge.

The practical meaning:

```text
Memory chip select should not respond to an I/O cycle.
I/O port hardware should not respond to an ordinary memory cycle.
```

That separation is why memory-mapped I/O and I/O-mapped I/O produce different decoding designs.

### Full Decoding Example

Suppose a `4K x 8` ROM must occupy:

```text
2000H to 2FFFH
```

`4K = 4096 = 1000H`, so the range is correct:

```text
2000H + 1000H - 1 = 2FFFH
```

The lower 12 address lines `A0-A11` select a byte inside the ROM. The higher address lines must be decoded so the chip is enabled only when the address begins with `2` in hex:

```text
0010 xxxx xxxx xxxx
```

So the key high nibble is:

```text
A15 A14 A13 A12 = 0010
```

### Partial Decoding And Foldback

If the hardware does not decode all high-order address lines, the same physical chip can appear at repeated address ranges. That is called aliasing or foldback.

Example idea:

```text
Only A15 and A14 are decoded.
A13-A12 are ignored.
```

Then several different address blocks may select the same chip. This is cheaper hardware, but it wastes address space and can create confusing duplicate addresses.

### Timing Formula

Use this for numerical timing questions:

```text
instruction time = total T-states x clock period
clock period = 1 / clock frequency
```

If the clock is `3 MHz`, one T-state is:

```text
1 / 3,000,000 = 0.333 microsecond
```

An instruction taking 10 T-states therefore takes about:

```text
10 x 0.333 = 3.33 microseconds
```

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
