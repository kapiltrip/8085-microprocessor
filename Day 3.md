# Day 3: Data Transfer, Register Pairs, and Logical Instructions

Day 3 focuses on how the 8085 moves data between registers and memory, how `HL` acts as a memory pointer, and how logical instructions such as `ORI`, `XRA`, and accumulator operations affect data and flags. This day is important because many later programming problems become simple once you can trace register contents carefully.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [MVI M,data and indirect addressing](images/Day%203/day-3-mvi-m-data-indirect-addressing.png) | `M` means memory location addressed by `HL`; immediate data is stored there. |
| 2 | [LHLD direct addressing example](images/Day%203/day-3-lhld-direct-addressing-example.png) | Load `L` and `H` directly from two consecutive memory locations. |
| 3 | [XCHG register-pair question](images/Day%203/day-3-xchg-register-pair-question.png) | `XCHG` exchanges `HL` and `DE` register pairs. |
| 4 | [Register-pair program tracing](images/Day%203/day-3-register-pair-program-tracing.png) | Step-by-step tracing of `XRA`, `MOV`, `INX`, `DAD`, and register-pair effects. |
| 5 | [ORI data immediate with accumulator](images/Day%203/day-3-ori-data-immediate-accumulator.png) | Immediate OR operation: `A <- A OR data`. |
| 6 | [XRA A clears accumulator](images/Day%203/day-3-xra-a-clear-accumulator-note.png) | XORing a value with itself gives zero; `XRA A` clears `A`. |

## Handwritten Notes Linked To Day 3

Each handwritten page is shown first as a large full-page image. Click the image or page title to open the high-resolution extracted page, then read the deeper explanation below it.

### [till47 p001](images/HandWrittenNotes/till47/page-001.jpg)

<a href="images/HandWrittenNotes/till47/page-001.jpg"><img src="images/HandWrittenNotes/till47/page-001.jpg" alt="till47 p001 handwritten note" width="960"></a>

Explanation: This page is mainly about Instruction byte length, addressing modes, `MVI`, `LDA`, and `JMP`. Use with the addressing-mode recap. It compares immediate, direct, register, and implicit examples. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Addressing-mode test:** Every addressing-mode page can be solved by asking one question: where is the operand? It may be inside the instruction itself, inside a register, at the memory address written in the instruction, or at a memory address stored in a register pair. Once the operand source is clear, instruction length and machine-cycle count become much easier to justify.

**Data movement:** For data-transfer instructions, separate the value being moved from the address used to reach it. `MVI` places immediate data, `MOV` transfers between registers or memory through `M`, `LDA/STA` use a direct 16-bit address, and `LHLD/SHLD` move the `HL` pair through consecutive memory locations. That distinction prevents confusing data bytes with address bytes.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till47 p002](images/HandWrittenNotes/till47/page-002.jpg)

<a href="images/HandWrittenNotes/till47/page-002.jpg"><img src="images/HandWrittenNotes/till47/page-002.jpg" alt="till47 p002 handwritten note" width="960"></a>

Explanation: This page is mainly about Implicit, memory, and register addressing plus flag layout. Use with register and memory addressing. It connects `M`, register operands, and flag layout. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Flag reasoning:** Do not revise flags as isolated definitions. First compute the 8-bit result, then ask whether the result is zero, whether bit 7 is set, whether parity is even, whether there was a carry from bit 3 to bit 4, and whether there was a carry or borrow out of the byte. This is especially important because in subtraction the carry flag represents borrow.

**Addressing-mode test:** Every addressing-mode page can be solved by asking one question: where is the operand? It may be inside the instruction itself, inside a register, at the memory address written in the instruction, or at a memory address stored in a register pair. Once the operand source is clear, instruction length and machine-cycle count become much easier to justify.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till47 p003](images/HandWrittenNotes/till47/page-003.jpg)

<a href="images/HandWrittenNotes/till47/page-003.jpg"><img src="images/HandWrittenNotes/till47/page-003.jpg" alt="till47 p003 handwritten note" width="960"></a>

Explanation: This page is mainly about `MOV A,M`, `MOV M,R`, `LDA`, and `STA`. Use with `MOV A,M`, `MOV M,R`, `LDA`, and `STA`. The key distinction is register indirect versus direct addressing. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Addressing-mode test:** Every addressing-mode page can be solved by asking one question: where is the operand? It may be inside the instruction itself, inside a register, at the memory address written in the instruction, or at a memory address stored in a register pair. Once the operand source is clear, instruction length and machine-cycle count become much easier to justify.

**Data movement:** For data-transfer instructions, separate the value being moved from the address used to reach it. `MVI` places immediate data, `MOV` transfers between registers or memory through `M`, `LDA/STA` use a direct 16-bit address, and `LHLD/SHLD` move the `HL` pair through consecutive memory locations. That distinction prevents confusing data bytes with address bytes.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till47 p004](images/HandWrittenNotes/till47/page-004.jpg)

<a href="images/HandWrittenNotes/till47/page-004.jpg"><img src="images/HandWrittenNotes/till47/page-004.jpg" alt="till47 p004 handwritten note" width="960"></a>

Explanation: This page is mainly about `MVI M,data`, `LXI H`, and register-indirect memory access. Use with `MVI M,data`. First load `HL`; only then does `M` point to the desired memory location. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Data movement:** For data-transfer instructions, separate the value being moved from the address used to reach it. `MVI` places immediate data, `MOV` transfers between registers or memory through `M`, `LDA/STA` use a direct 16-bit address, and `LHLD/SHLD` move the `HL` pair through consecutive memory locations. That distinction prevents confusing data bytes with address bytes.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till47 p005](images/HandWrittenNotes/till47/page-005.jpg)

<a href="images/HandWrittenNotes/till47/page-005.jpg"><img src="images/HandWrittenNotes/till47/page-005.jpg" alt="till47 p005 handwritten note" width="960"></a>

Explanation: This page is mainly about One-, two-, and three-byte instructions with `LDA`. Use with instruction length. It shows why `LDA` is three bytes while simple register operations can be one byte. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Data movement:** For data-transfer instructions, separate the value being moved from the address used to reach it. `MVI` places immediate data, `MOV` transfers between registers or memory through `M`, `LDA/STA` use a direct 16-bit address, and `LHLD/SHLD` move the `HL` pair through consecutive memory locations. That distinction prevents confusing data bytes with address bytes.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till47 p006](images/HandWrittenNotes/till47/page-006.jpg)

<a href="images/HandWrittenNotes/till47/page-006.jpg"><img src="images/HandWrittenNotes/till47/page-006.jpg" alt="till47 p006 handwritten note" width="960"></a>

Explanation: This page is mainly about `LDAX`, `STAX`, and register-pair indirect addressing. Use with `LDAX` and `STAX`. These are register-pair indirect instructions using `BC` or `DE`, not `HL`. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Data movement:** For data-transfer instructions, separate the value being moved from the address used to reach it. `MVI` places immediate data, `MOV` transfers between registers or memory through `M`, `LDA/STA` use a direct 16-bit address, and `LHLD/SHLD` move the `HL` pair through consecutive memory locations. That distinction prevents confusing data bytes with address bytes.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till47 p007](images/HandWrittenNotes/till47/page-007.jpg)

<a href="images/HandWrittenNotes/till47/page-007.jpg"><img src="images/HandWrittenNotes/till47/page-007.jpg" alt="till47 p007 handwritten note" width="960"></a>

Explanation: This page is mainly about `LHLD`, `SHLD`, and low-byte/high-byte order. Use with `LHLD` and `SHLD`. Remember low address stores/loads the low byte first. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Data movement:** For data-transfer instructions, separate the value being moved from the address used to reach it. `MVI` places immediate data, `MOV` transfers between registers or memory through `M`, `LDA/STA` use a direct 16-bit address, and `LHLD/SHLD` move the `HL` pair through consecutive memory locations. That distinction prevents confusing data bytes with address bytes.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till47 p008](images/HandWrittenNotes/till47/page-008.jpg)

<a href="images/HandWrittenNotes/till47/page-008.jpg"><img src="images/HandWrittenNotes/till47/page-008.jpg" alt="till47 p008 handwritten note" width="960"></a>

Explanation: This page is mainly about `LDAX`, `XCHG`, and memory/register-pair tracing. Use with `XCHG` and `LDAX`. Track whether the instruction moves data, an address, or a register-pair value. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Data movement:** For data-transfer instructions, separate the value being moved from the address used to reach it. `MVI` places immediate data, `MOV` transfers between registers or memory through `M`, `LDA/STA` use a direct 16-bit address, and `LHLD/SHLD` move the `HL` pair through consecutive memory locations. That distinction prevents confusing data bytes with address bytes.

**Trace method:** For trace pages, make a row for every instruction. Update only the register, memory byte, flag, stack location, or program counter value that the instruction actually changes. This slower row-by-row method is the shortest reliable way to avoid losing track of `HL`, `SP`, carry, or memory contents.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till47 p009](images/HandWrittenNotes/till47/page-009.jpg)

<a href="images/HandWrittenNotes/till47/page-009.jpg"><img src="images/HandWrittenNotes/till47/page-009.jpg" alt="till47 p009 handwritten note" width="960"></a>

Explanation: This page is mainly about `LXI H`, `SHLD`, register-pair memory storage. Use with `SHLD`. It reinforces memory layout for `HL`: `L` at lower address, `H` at next address. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Data movement:** For data-transfer instructions, separate the value being moved from the address used to reach it. `MVI` places immediate data, `MOV` transfers between registers or memory through `M`, `LDA/STA` use a direct 16-bit address, and `LHLD/SHLD` move the `HL` pair through consecutive memory locations. That distinction prevents confusing data bytes with address bytes.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till47 p010](images/HandWrittenNotes/till47/page-010.jpg)

<a href="images/HandWrittenNotes/till47/page-010.jpg"><img src="images/HandWrittenNotes/till47/page-010.jpg" alt="till47 p010 handwritten note" width="960"></a>

Explanation: This page is mainly about `XCHG`, `DAD`, and register-pair trace practice. Use with register-pair program tracing. Make a table for `A`, `B`, `C`, `D`, `E`, `H`, `L`, `BC`, `DE`, and `HL`. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Trace method:** For trace pages, make a row for every instruction. Update only the register, memory byte, flag, stack location, or program counter value that the instruction actually changes. This slower row-by-row method is the shortest reliable way to avoid losing track of `HL`, `SP`, carry, or memory contents.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

## 1. `MVI M,data`: Immediate Data to Memory Through `HL`

![MVI M,data and indirect addressing](images/Day%203/day-3-mvi-m-data-indirect-addressing.png)

`MVI M,data` is one of the most important instructions for understanding 8085 notation.

```asm
LXI H,2400H
MVI M,08H
HLT
```

Meaning:

```text
HL <- 2400H
memory[HL] <- 08H
therefore memory[2400H] <- 08H
```

The symbol `M` does not mean a separate register. It means **the memory location whose address is currently stored in the `HL` pair**. So when `HL = 2400H`, `M` means `memory[2400H]`.

`MVI M,08H` combines two ideas:

| Part | Meaning |
| --- | --- |
| `MVI` | Move immediate data. |
| `M` | Destination is memory addressed by `HL`. |
| `08H` | 8-bit immediate data byte. |

Because the instruction stores data into memory, the CPU needs more than just opcode fetch. It must fetch the opcode, fetch the immediate byte, and then perform a memory write to the address held in `HL`.

Common trap: students sometimes write "M = 2400H." That is wrong. `HL = 2400H`; `M` is the memory byte stored at address `2400H`.

## 2. `LHLD addr`: Load H and L Direct

![LHLD direct addressing example](images/Day%203/day-3-lhld-direct-addressing-example.png)

`LHLD addr` loads the `HL` pair from two consecutive memory locations.

```asm
LHLD 2500H
```

The 8085 performs:

```text
L <- memory[2500H]
H <- memory[2501H]
```

The lower-order register `L` is loaded from the lower address, and the higher-order register `H` is loaded from the next address. This matches the little-endian style used by the 8085 for 16-bit values: low byte first, high byte second.

If:

```text
memory[2500H] = 34H
memory[2501H] = 12H
```

then after:

```asm
LHLD 2500H
```

we get:

```text
L = 34H
H = 12H
HL = 1234H
```

Common trap: do not load `H` from the first address. The mnemonic says `LHLD`, but the memory order is low byte first.

## 3. `XCHG`: Exchange `HL` and `DE`

![XCHG register-pair question](images/Day%203/day-3-xchg-register-pair-question.png)

`XCHG` exchanges the contents of the `HL` and `DE` register pairs:

```text
H <-> D
L <-> E
```

So if:

```text
HL = 2500H
DE = 1234H
```

then after:

```asm
XCHG
```

we get:

```text
HL = 1234H
DE = 2500H
```

This instruction is useful when one instruction requires an address or value in `HL`, but the program currently has the useful value in `DE`. Instead of moving each register separately, `XCHG` swaps both pairs in one instruction.

Common trap: `XCHG` does not exchange `BC` with anything. It only exchanges `DE` and `HL`.

## 4. Register-Pair Program Tracing

![Register-pair program tracing](images/Day%203/day-3-register-pair-program-tracing.png)

The program-tracing screenshot is testing whether you can track register contents line by line. The reliable method is to make a table and update only the registers touched by each instruction.

Example style:

```asm
XRA A
MOV L,A
MOV H,L
INX H
DAD H
```

Interpretation:

| Instruction | Effect |
| --- | --- |
| `XRA A` | `A <- A XOR A`, so `A = 00H`. |
| `MOV L,A` | `L <- A`, so `L = 00H`. |
| `MOV H,L` | `H <- L`, so `H = 00H`. |
| `INX H` | `HL <- HL + 1`, so `HL = 0001H`. |
| `DAD H` | `HL <- HL + HL`, so `HL = 0002H`. |

Key idea: `INX H` increments the 16-bit register pair `HL`; it is not the same as incrementing only `H`. `DAD H` adds the selected register pair to `HL`, so `DAD H` doubles `HL`.

Common trap: `DAD` affects the carry flag, but it does not update all arithmetic flags the way an 8-bit `ADD` instruction does.

## 5. `ORI data`: Immediate OR With Accumulator

![ORI data immediate with accumulator](images/Day%203/day-3-ori-data-immediate-accumulator.png)

`ORI data` performs a bitwise OR between the accumulator and an 8-bit immediate value:

```text
A <- A OR data
```

Example:

```asm
MVI A,82H
ORI 0FH
```

Binary view:

```text
82H = 1000 0010
0FH = 0000 1111
OR  = 1000 1111 = 8FH
```

OR is useful for **setting bits**. Wherever the immediate data has `1`, the result will have `1`. Wherever the immediate data has `0`, the original accumulator bit is preserved.

Flags: logical instructions update condition flags based on the result. In normal 8085 study, `ORI` resets carry and auxiliary carry and updates sign, zero, and parity according to the result.

Common trap: OR is not addition. `82H OR 0FH` is `8FH`, not `91H`.

## 6. `XRA A`: Clearing the Accumulator

![XRA A clears accumulator](images/Day%203/day-3-xra-a-clear-accumulator-note.png)

`XRA r` performs exclusive-OR between the accumulator and the selected operand:

```text
A <- A XOR r
```

When the operand is `A` itself:

```asm
XRA A
```

the result is always zero, because every bit XORed with itself gives `0`:

```text
0 XOR 0 = 0
1 XOR 1 = 0
```

Therefore:

```text
A <- A XOR A = 00H
```

This is a compact way to clear the accumulator. It also sets the zero flag because the result is zero. In 8085, XOR-type logical instructions reset the carry flag.

Common trap: `XRA A` is not the same as `MVI A,00H` in timing and flag behavior. `MVI A,00H` loads zero but does not affect flags; `XRA A` clears `A` and affects flags.

## Points To Remember

- `M` means `memory[HL]`.
- `LHLD addr` loads `L` from `addr` and `H` from `addr + 1`.
- `XCHG` swaps `DE` and `HL`, not `BC`.
- `INX H` increments the full 16-bit `HL` pair.
- `DAD rp` adds a 16-bit register pair to `HL`.
- OR is used to set bits; AND is used to mask bits; XOR is often used to toggle or clear by self-XOR.
- `XRA A` clears accumulator and affects flags.
- Immediate data is a value stored after the opcode, not an address unless the instruction definition says so.

## Sources

[S1] Intel Corporation, [MCS-80/85 Family User's Manual, January 1983](https://www.bitsavers.org/components/intel/MCS80/MCS80_85_Users_Manual_Jan83.pdf). Used for instruction behavior, register-pair operation, flags, direct/indirect/immediate addressing, and accumulator logical instructions.

[S2] Intel Corporation, [8080/8085 Assembly Language Programming Manual, May 1981](https://www.bitsavers.org/pdf/intel/ISIS_II/9800301-04_8080_8085_Assembly_Language_Programming_Manual_May81.pdf). Used for assembly notation and examples of data transfer and logical instructions.
