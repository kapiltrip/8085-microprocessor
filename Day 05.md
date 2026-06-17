# Day 05: Subtraction, Compare, Instruction Storage, and Rotate Operations

Day 05 is mostly about careful instruction execution. The screenshots cover subtraction with borrow, compare flags, how instruction bytes occupy memory addresses, and bit-level accumulator rotation. These topics are common in 8085 exam questions because one wrong assumption about carry, sign, or instruction length changes the final answer.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [SBB/SUB borrow question 1](images/Day%2005/day-5-sbb-subtract-with-borrow-question-1.png) | Subtracting a larger byte from a smaller byte sets carry and sign. |
| 2 | [SBB/SUB borrow question 2](images/Day%2005/day-5-sbb-subtract-with-borrow-question-2.png) | Same subtraction idea, with two's-complement result. |
| 3 | [CMP B carry and zero flag question](images/Day%2005/day-5-cmp-b-carry-zero-flags-question.png) | `CMP` compares by internal subtraction without changing accumulator. |
| 4 | [Instruction address placement example](images/Day%2005/day-5-instruction-address-placement-example.png) | Multi-byte instructions occupy consecutive memory locations. |
| 5 | [Rotate and ORA accumulator question](images/Day%2005/day-5-rotate-ora-accumulator-question.png) | Trace `ORA`, `RRC`, `RAL`, and carry effects on accumulator bits. |

## Handwritten Notes Linked To Day 05

Each handwritten page is shown first as a large full-page image. The explanation below the image adds the technical layer: instruction behavior, bus cycles, flags, timing, address formation, or hardware reason behind the note.

### [till47 p011](images/HandWrittenNotes/till47/page-011.jpg)

<a href="images/HandWrittenNotes/till47/page-011.jpg"><img src="images/HandWrittenNotes/till47/page-011.jpg" alt="till47 p011 handwritten note" width="960"></a>

Technical explanation: 8085 subtraction is performed through two's-complement addition internally. `SUB r` computes `A - r`; `SBB r` computes `A - r - CY`; `SUI data` subtracts an immediate byte. After subtraction, `CY=1` means a borrow was required. `CMP r` performs the same internal subtraction only for flags and leaves the accumulator unchanged.

Flags are a compressed record of the last flag-affecting result. `S` copies bit 7, `Z` reports zero, `P` reports even parity, `AC` reports carry from bit 3 to bit 4, and `CY` reports carry out of bit 7. In subtraction, `CY` is interpreted as borrow. A common trace error is testing a flag after an instruction that did not update it.

### [till47 p012](images/HandWrittenNotes/till47/page-012.jpg)

<a href="images/HandWrittenNotes/till47/page-012.jpg"><img src="images/HandWrittenNotes/till47/page-012.jpg" alt="till47 p012 handwritten note" width="960"></a>

Technical explanation: the ALU is where arithmetic and logical results are physically produced, but the useful output is not only the 8-bit result. The accumulator usually supplies one operand and receives the result, while the flag flip-flops record properties of that result. Carry is generated from bit 7, auxiliary carry from bit 3 to bit 4, zero from an all-zero result, sign from bit 7, and parity from the number of 1 bits. That is why exam traces must update flags from the actual binary result, not from the instruction name alone.

Flags are a compressed record of the last flag-affecting result. `S` copies bit 7, `Z` reports zero, `P` reports even parity, `AC` reports carry from bit 3 to bit 4, and `CY` reports carry out of bit 7. In subtraction, `CY` is interpreted as borrow. A common trace error is testing a flag after an instruction that did not update it.

8085 subtraction is performed through two's-complement addition internally. `SUB r` computes `A - r`; `SBB r` computes `A - r - CY`; `SUI data` subtracts an immediate byte. After subtraction, `CY=1` means a borrow was required. `CMP r` performs the same internal subtraction only for flags and leaves the accumulator unchanged.

`CMA` complements every accumulator bit and does not mean two's-complement negation by itself. `STC` sets carry, and `CMC` complements carry. These instructions are small but important because later rotate, subtract-with-borrow, or conditional branch instructions can depend on the carry value they leave behind.

### [till47 p013](images/HandWrittenNotes/till47/page-013.jpg)

<a href="images/HandWrittenNotes/till47/page-013.jpg"><img src="images/HandWrittenNotes/till47/page-013.jpg" alt="till47 p013 handwritten note" width="960"></a>

Technical explanation: `INR` and `DCR` change an 8-bit register or memory byte, while `INX` and `DCX` change a 16-bit register pair. `INR/DCR` update most arithmetic flags but do not update carry, which is a frequent exam trap. `INX/DCX` are 16-bit pointer updates and do not set the normal arithmetic flags.

Flags are a compressed record of the last flag-affecting result. `S` copies bit 7, `Z` reports zero, `P` reports even parity, `AC` reports carry from bit 3 to bit 4, and `CY` reports carry out of bit 7. In subtraction, `CY` is interpreted as borrow. A common trace error is testing a flag after an instruction that did not update it.

### [till47 p014](images/HandWrittenNotes/till47/page-014.jpg)

<a href="images/HandWrittenNotes/till47/page-014.jpg"><img src="images/HandWrittenNotes/till47/page-014.jpg" alt="till47 p014 handwritten note" width="960"></a>

Technical explanation: the ALU is where arithmetic and logical results are physically produced, but the useful output is not only the 8-bit result. The accumulator usually supplies one operand and receives the result, while the flag flip-flops record properties of that result. Carry is generated from bit 7, auxiliary carry from bit 3 to bit 4, zero from an all-zero result, sign from bit 7, and parity from the number of 1 bits. That is why exam traces must update flags from the actual binary result, not from the instruction name alone.

Flags are a compressed record of the last flag-affecting result. `S` copies bit 7, `Z` reports zero, `P` reports even parity, `AC` reports carry from bit 3 to bit 4, and `CY` reports carry out of bit 7. In subtraction, `CY` is interpreted as borrow. A common trace error is testing a flag after an instruction that did not update it.

`DAA` is not a normal binary addition. It corrects the accumulator after BCD addition by adding `06H` to the lower nibble when needed and `60H` to the upper nibble when needed. The decision depends on nibble values and `AC/CY`. Use it only after an addition intended to represent packed BCD digits.

### [till47 p015](images/HandWrittenNotes/till47/page-015.jpg)

<a href="images/HandWrittenNotes/till47/page-015.jpg"><img src="images/HandWrittenNotes/till47/page-015.jpg" alt="till47 p015 handwritten note" width="960"></a>

Technical explanation: 8085 subtraction is performed through two's-complement addition internally. `SUB r` computes `A - r`; `SBB r` computes `A - r - CY`; `SUI data` subtracts an immediate byte. After subtraction, `CY=1` means a borrow was required. `CMP r` performs the same internal subtraction only for flags and leaves the accumulator unchanged.

Flags are a compressed record of the last flag-affecting result. `S` copies bit 7, `Z` reports zero, `P` reports even parity, `AC` reports carry from bit 3 to bit 4, and `CY` reports carry out of bit 7. In subtraction, `CY` is interpreted as borrow. A common trace error is testing a flag after an instruction that did not update it.

### [till47 p016](images/HandWrittenNotes/till47/page-016.jpg)

<a href="images/HandWrittenNotes/till47/page-016.jpg"><img src="images/HandWrittenNotes/till47/page-016.jpg" alt="till47 p016 handwritten note" width="960"></a>

Technical explanation: `INR` and `DCR` change an 8-bit register or memory byte, while `INX` and `DCX` change a 16-bit register pair. `INR/DCR` update most arithmetic flags but do not update carry, which is a frequent exam trap. `INX/DCX` are 16-bit pointer updates and do not set the normal arithmetic flags.

Flags are a compressed record of the last flag-affecting result. `S` copies bit 7, `Z` reports zero, `P` reports even parity, `AC` reports carry from bit 3 to bit 4, and `CY` reports carry out of bit 7. In subtraction, `CY` is interpreted as borrow. A common trace error is testing a flag after an instruction that did not update it.

### [till47 p017](images/HandWrittenNotes/till47/page-017.jpg)

<a href="images/HandWrittenNotes/till47/page-017.jpg"><img src="images/HandWrittenNotes/till47/page-017.jpg" alt="till47 p017 handwritten note" width="960"></a>

Technical explanation: `XCHG` swaps `HL` with `DE`; it does not touch memory. `DAD rp` adds a 16-bit register pair to `HL` and stores the 16-bit result in `HL`, with carry out recorded in `CY`. When tracing these, combine each pair as a 16-bit number first, then split the result back into high and low registers.

`DAA` is not a normal binary addition. It corrects the accumulator after BCD addition by adding `06H` to the lower nibble when needed and `60H` to the upper nibble when needed. The decision depends on nibble values and `AC/CY`. Use it only after an addition intended to represent packed BCD digits.

Logical instructions operate bit by bit. `ANA` clears a bit unless both inputs have 1, `ORA` sets a bit if either input has 1, and `XRA` sets a bit only when the inputs differ. `XRA A` is a standard way to clear the accumulator to `00H`; it also updates flags, so later conditional jumps may depend on that clear operation.

For program traces, keep a state table. Each row should list only what the current instruction changes: registers, flags, memory, `PC`, `SP`, or stack bytes. This prevents the common mistake of updating a value but forgetting the pointer that determines where the next memory access will happen.

### [till47 p018](images/HandWrittenNotes/till47/page-018.jpg)

<a href="images/HandWrittenNotes/till47/page-018.jpg"><img src="images/HandWrittenNotes/till47/page-018.jpg" alt="till47 p018 handwritten note" width="960"></a>

Technical explanation: logical instructions operate bit by bit. `ANA` clears a bit unless both inputs have 1, `ORA` sets a bit if either input has 1, and `XRA` sets a bit only when the inputs differ. `XRA A` is a standard way to clear the accumulator to `00H`; it also updates flags, so later conditional jumps may depend on that clear operation.

`CMA` complements every accumulator bit and does not mean two's-complement negation by itself. `STC` sets carry, and `CMC` complements carry. These instructions are small but important because later rotate, subtract-with-borrow, or conditional branch instructions can depend on the carry value they leave behind.

### [till47 p019](images/HandWrittenNotes/till47/page-019.jpg)

<a href="images/HandWrittenNotes/till47/page-019.jpg"><img src="images/HandWrittenNotes/till47/page-019.jpg" alt="till47 p019 handwritten note" width="960"></a>

Technical explanation: logical instructions operate bit by bit. `ANA` clears a bit unless both inputs have 1, `ORA` sets a bit if either input has 1, and `XRA` sets a bit only when the inputs differ. `XRA A` is a standard way to clear the accumulator to `00H`; it also updates flags, so later conditional jumps may depend on that clear operation.

Memory operands add a bus read before the logical operation. For example, `ORA M` first reads the byte at `[HL]`, then ORs it with `A`. The result goes back to `A`, and the flags describe the logical result, not the address or the memory byte by itself.

### [till47 p020](images/HandWrittenNotes/till47/page-020.jpg)

<a href="images/HandWrittenNotes/till47/page-020.jpg"><img src="images/HandWrittenNotes/till47/page-020.jpg" alt="till47 p020 handwritten note" width="960"></a>

Technical explanation: logical instructions operate bit by bit. `ANA` clears a bit unless both inputs have 1, `ORA` sets a bit if either input has 1, and `XRA` sets a bit only when the inputs differ. `XRA A` is a standard way to clear the accumulator to `00H`; it also updates flags, so later conditional jumps may depend on that clear operation.

`CMA` complements every accumulator bit and does not mean two's-complement negation by itself. `STC` sets carry, and `CMC` complements carry. These instructions are small but important because later rotate, subtract-with-borrow, or conditional branch instructions can depend on the carry value they leave behind.

### [till47 p021](images/HandWrittenNotes/till47/page-021.jpg)

<a href="images/HandWrittenNotes/till47/page-021.jpg"><img src="images/HandWrittenNotes/till47/page-021.jpg" alt="till47 p021 handwritten note" width="960"></a>

Technical explanation: 8085 subtraction is performed through two's-complement addition internally. `SUB r` computes `A - r`; `SBB r` computes `A - r - CY`; `SUI data` subtracts an immediate byte. After subtraction, `CY=1` means a borrow was required. `CMP r` performs the same internal subtraction only for flags and leaves the accumulator unchanged.

Flags are a compressed record of the last flag-affecting result. `S` copies bit 7, `Z` reports zero, `P` reports even parity, `AC` reports carry from bit 3 to bit 4, and `CY` reports carry out of bit 7. In subtraction, `CY` is interpreted as borrow. A common trace error is testing a flag after an instruction that did not update it.

`CMA` complements every accumulator bit and does not mean two's-complement negation by itself. `STC` sets carry, and `CMC` complements carry. These instructions are small but important because later rotate, subtract-with-borrow, or conditional branch instructions can depend on the carry value they leave behind.

Rotate instructions are best traced as eight accumulator bits plus a separate carry. `RLC` moves bit 7 into bit 0 and into `CY`; `RRC` moves bit 0 into bit 7 and into `CY`. `RAL` and `RAR` rotate through carry, so the old carry becomes part of the new accumulator and the outgoing accumulator bit becomes the new carry.

### [till47 p022](images/HandWrittenNotes/till47/page-022.jpg)

<a href="images/HandWrittenNotes/till47/page-022.jpg"><img src="images/HandWrittenNotes/till47/page-022.jpg" alt="till47 p022 handwritten note" width="960"></a>

Technical explanation: the ALU is where arithmetic and logical results are physically produced, but the useful output is not only the 8-bit result. The accumulator usually supplies one operand and receives the result, while the flag flip-flops record properties of that result. Carry is generated from bit 7, auxiliary carry from bit 3 to bit 4, zero from an all-zero result, sign from bit 7, and parity from the number of 1 bits. That is why exam traces must update flags from the actual binary result, not from the instruction name alone.

Flags are a compressed record of the last flag-affecting result. `S` copies bit 7, `Z` reports zero, `P` reports even parity, `AC` reports carry from bit 3 to bit 4, and `CY` reports carry out of bit 7. In subtraction, `CY` is interpreted as borrow. A common trace error is testing a flag after an instruction that did not update it.

Rotate instructions are best traced as eight accumulator bits plus a separate carry. `RLC` moves bit 7 into bit 0 and into `CY`; `RRC` moves bit 0 into bit 7 and into `CY`. `RAL` and `RAR` rotate through carry, so the old carry becomes part of the new accumulator and the outgoing accumulator bit becomes the new carry.

### [till47 p023](images/HandWrittenNotes/till47/page-023.jpg)

<a href="images/HandWrittenNotes/till47/page-023.jpg"><img src="images/HandWrittenNotes/till47/page-023.jpg" alt="till47 p023 handwritten note" width="960"></a>

Technical explanation: branch instructions test existing state; they do not create the condition themselves. Conditional jumps, calls, and returns read flag bits left by earlier instructions. Timing can differ between taken and not-taken branches because the processor may or may not load the target address into `PC`. This is why loop timing must count the last failed branch separately.

For condition-code questions, translate the mnemonic into a flag test: zero/non-zero checks `Z`, carry/no-carry checks `CY`, plus/minus checks `S`, and parity checks `P`. The branch is only meaningful if a previous instruction has updated the relevant flag.

### [till47 p024](images/HandWrittenNotes/till47/page-024.jpg)

<a href="images/HandWrittenNotes/till47/page-024.jpg"><img src="images/HandWrittenNotes/till47/page-024.jpg" alt="till47 p024 handwritten note" width="960"></a>

Technical explanation: rotate instructions are best traced as eight accumulator bits plus a separate carry. `RLC` moves bit 7 into bit 0 and into `CY`; `RRC` moves bit 0 into bit 7 and into `CY`. `RAL` and `RAR` rotate through carry, so the old carry becomes part of the new accumulator and the outgoing accumulator bit becomes the new carry.

Branch instructions test existing state; they do not create the condition themselves. Conditional jumps, calls, and returns read flag bits left by earlier instructions. Timing can differ between taken and not-taken branches because the processor may or may not load the target address into `PC`. This is why loop timing must count the last failed branch separately.

## 1. Subtraction and Borrow

![SBB/SUB borrow question 1](images/Day%2005/day-5-sbb-subtract-with-borrow-question-1.png)

The question uses:

```text
B = 49H
A = 3AH
```

For `SUB B`:

```text
A <- A - B
A <- 3AH - 49H
```

In decimal:

```text
3AH = 58
49H = 73
58 - 73 = -15
```

An 8-bit register cannot store `-15` directly as a signed decimal result. It stores the 8-bit two's-complement pattern:

```text
100H - 0FH = F1H
```

So:

```text
A = F1H
CY = 1
S = 1
```

Why `CY = 1`? In 8085 subtraction, carry indicates borrow. Since `A` was smaller than `B`, the subtraction required a borrow.

Why `S = 1`? The result `F1H` has MSB `1`, so the sign flag is set.

## 2. `SBB`: Subtract With Borrow

![SBB/SUB borrow question 2](images/Day%2005/day-5-sbb-subtract-with-borrow-question-2.png)

`SBB r` means:

```text
A <- A - r - CY
```

This is different from `SUB r`, which means:

```text
A <- A - r
```

Using the same values:

```text
A = 3AH
B = 49H
```

If old carry is `0`:

```text
SBB B = 3AH - 49H - 0 = F1H
```

If old carry is `1`:

```text
SBB B = 3AH - 49H - 1 = F0H
```

This is the reason many exam questions specify the previous carry flag before `SBB`. If it is not specified, you must be careful: either the problem assumes `CY = 0`, or the question is incomplete.

Common trap: after subtraction, `CY = 1` means borrow occurred. It does not mean the numerical result is positive.

## 3. `CMP B`: Compare Accumulator With Register

![CMP B carry and zero flag question](images/Day%2005/day-5-cmp-b-carry-zero-flags-question.png)

`CMP B` compares the accumulator with register `B` by internally calculating:

```text
A - B
```

but it does **not** store the result back in `A`. Only flags are affected.

For the screenshot's idea:

```text
A < B
```

then:

```text
CY = 1
Z = 0
```

Because:

- carry is set when the comparison needs a borrow, meaning `A < B`;
- zero is set only when `A = B`;
- the accumulator value remains unchanged after `CMP`.

Comparison rules:

| Relation | Carry flag | Zero flag |
| --- | --- | --- |
| `A < operand` | `CY = 1` | `Z = 0` |
| `A = operand` | `CY = 0` | `Z = 1` |
| `A > operand` | `CY = 0` | `Z = 0` |

Common trap: `CMP` is like subtraction for flags, but unlike `SUB`, it does not change accumulator contents.

## 4. Instruction Bytes in Memory

![Instruction address placement example](images/Day%2005/day-5-instruction-address-placement-example.png)

This screenshot shows how instructions are placed in memory. Each instruction occupies one, two, or three consecutive memory locations.

Examples:

| Instruction | Length | Memory layout |
| --- | --- | --- |
| `SUB M` | 1 byte | opcode only |
| `MVI A,20H` | 2 bytes | opcode, then `20H` |
| `LXI H,0701H` | 3 bytes | opcode, low byte `01H`, high byte `07H` |
| `JMP 2050H` | 3 bytes | opcode, low byte `50H`, high byte `20H` |

For 16-bit immediate data or addresses, the 8085 stores the low byte first and high byte second.

Example:

```asm
LXI H,0701H
```

is stored as:

```text
opcode for LXI H
01H
07H
```

Common trap: do not write `07H` before `01H` in memory. The register-pair display is `HL = 0701H`, but the instruction bytes are low byte first.

## 5. `ORA`, `RRC`, and `RAL` Trace

![Rotate and ORA accumulator question](images/Day%2005/day-5-rotate-ora-accumulator-question.png)

The program shown is:

```asm
MVI A,C4H
ORA A
RRC
RAL
RRC
```

Start:

```text
A = C4H = 1100 0100
```

`ORA A`:

```text
A <- A OR A = C4H
CY <- 0
```

Logical OR with itself does not change `A`, but it refreshes flags. For ordinary 8085 teaching, OR-type instructions reset carry.

`RRC` rotates accumulator right circular:

```text
before: A = 1100 0100
after:  A = 0110 0010 = 62H
CY = old D0 = 0
```

`RAL` rotates left through carry:

```text
before: A = 0110 0010, CY = 0
after:  A = 1100 0100 = C4H
CY = old D7 = 0
```

Final `RRC`:

```text
before: A = 1100 0100
after:  A = 0110 0010 = 62H
CY = 0
```

So the final accumulator is:

```text
A = 62H
```

Common trap: `RRC` and `RAL` are not the same kind of rotation. `RRC` is circular within the accumulator and copies old bit 0 to carry. `RAL` rotates through carry, so old carry enters bit 0 and old bit 7 becomes carry.

## Research Deep Dive: Flag-Correct Arithmetic

Most Day 05 mistakes come from treating hexadecimal subtraction as ordinary decimal subtraction and then guessing flags. The safer method is to solve at the binary width of the CPU.

### Carry Means Borrow In Subtraction

For 8085 subtraction:

```text
A <- A - operand
```

`CY = 1` means a borrow was required. It does not mean the result is positive.

Example:

```text
A = 20H
operand = 35H
20H - 35H needs borrow
```

As an 8-bit result:

```text
20H - 35H = EBH
CY = 1
S = 1
Z = 0
```

The stored result is `EBH`, but the carry flag tells you that unsigned `20H` was less than unsigned `35H`.

### `CMP` Is Subtraction Without Storage

`CMP B` internally evaluates:

```text
A - B
```

Only flags are updated. `A` and `B` remain unchanged.

Use this table:

| Relation after `CMP B` | `CY` | `Z` |
| --- | --- | --- |
| `A < B` | `1` | `0` |
| `A = B` | `0` | `1` |
| `A > B` | `0` | `0` |

That table is for unsigned comparison. If a question treats the byte as signed two's-complement data, the sign flag alone is not enough; you must reason from the actual signed values.

### Instruction Storage And Program Counter Discipline

For three-byte instructions such as:

```asm
LDA 2050H
```

memory stores:

| Address | Byte |
| --- | --- |
| `PC` | opcode |
| `PC + 1` | low address byte `50H` |
| `PC + 2` | high address byte `20H` |

The low byte appears first because Intel 8080/8085-style instruction streams use low-order byte first for 16-bit data/address fields.

### Rotate Instructions And Carry

Rotates are easiest if you draw nine positions:

```text
CY + A7 A6 A5 A4 A3 A2 A1 A0
```

For `RAL` and `RAR`, carry is part of the rotation path. For `RLC` and `RRC`, the accumulator rotates circularly and carry receives a copy of the outgoing bit.

| Instruction | New carry | Bit entering accumulator |
| --- | --- | --- |
| `RLC` | old `A7` | old `A7` enters `A0` |
| `RRC` | old `A0` | old `A0` enters `A7` |
| `RAL` | old `A7` | old `CY` enters `A0` |
| `RAR` | old `A0` | old `CY` enters `A7` |

## Handwritten And Screenshot Deepening

Day 05 should be revised through flag meaning under subtraction and bit operations. The handwritten pages on `SUB`, `SBB`, `SUI`, `CMP`, `DAA`, logical instructions, and rotates are connected by one rule: the result byte alone is not enough. You must also know which flags are meaningful after the instruction and how the next instruction might use them.

For subtraction, remember that the 8085 performs two's-complement addition internally, but the carry flag is interpreted as borrow. If `A` is smaller than the subtracted value in unsigned arithmetic, `CY` becomes 1. `SBB` includes the existing carry as a borrow input, so it is used for multi-byte subtraction. That is why a trace must carry `CY` forward from the lower byte to the higher byte.

`CMP` is a subtraction that throws away the result and keeps only the flags. If `A = operand`, zero is set. If `A < operand` in unsigned comparison, carry is set. If `A > operand`, both zero and carry are clear. The handwritten branch pages become easier when every `CMP` is rewritten mentally as "subtract for flags only."

The BCD material around `DAA` is deeper than a correction trick. Binary addition of packed decimal digits can produce invalid decimal nibbles such as `1010` to `1111`, or a carry between decimal digits. `DAA` checks the lower nibble, `AC`, the upper nibble, and `CY` to add `06H` and/or `60H` when required. So the handwritten `DAA` examples should be solved from the binary addition first, then corrected as BCD.

Logical instructions should be revised bit-by-bit. `ANA` clears bits where the mask is 0, `ORA` sets bits where the mask is 1, and `XRA` toggles bits where the mask is 1. `CMA` complements all accumulator bits without doing arithmetic. This is why logical instructions are useful for masking, clearing, setting, and testing individual bits in I/O or status bytes.

Rotates connect the accumulator to the carry flag. `RLC` and `RRC` rotate inside `A` and copy the wrapped bit into `CY`; `RAL` and `RAR` rotate through carry, so old `CY` becomes part of the shifted value. When checking handwritten rotate traces, draw nine boxes: eight accumulator bits plus carry. That drawing prevents the common error of treating all rotate instructions the same.

## Points To Remember

- In subtraction, carry means borrow.
- `SUB r` subtracts only the operand.
- `SBB r` subtracts operand plus old carry.
- `CMP r` changes flags but not accumulator.
- `A < operand` after compare gives `CY = 1`, `Z = 0`.
- Instruction length determines where the next instruction begins.
- For 16-bit immediate data, memory stores low byte first.
- `ORA A` can be used to update flags without changing `A`, but it resets carry in 8085 logic instruction behavior.
- `RRC` rotates right circular; `RAL` rotates left through carry.
- Always write binary when solving rotate questions.

## Sources

[S1] Intel Corporation, [MCS-80/85 Family User's Manual, January 1983](https://www.bitsavers.org/components/intel/MCS80/MCS80_85_Users_Manual_Jan83.pdf). Used for arithmetic/logical instruction behavior, carry/borrow meaning, flags, instruction length, immediate data order, and rotate instructions.

[S2] Intel Corporation, [8080/8085 Assembly Language Programming Manual, May 1981](https://www.bitsavers.org/pdf/intel/ISIS_II/9800301-04_8080_8085_Assembly_Language_Programming_Manual_May81.pdf). Used for instruction storage, mnemonic notation, and programming examples.
