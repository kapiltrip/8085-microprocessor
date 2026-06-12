# Day 4: Branching, Stack, Interrupt Control, and Bit-Level I/O

Day 4 is about control flow and stack-based execution. Earlier days explained how the 8085 fetches and executes ordinary instructions. This day explains how execution changes direction through conditional jumps, `CALL`, `RST`, stack operations, interrupt control, and bit-level serial/interrupt masking through `SIM` and `RIM`.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [Conditional jump instructions table](images/Day%204/day-4-conditional-jump-instructions-table.png) | Branching based on carry, zero, sign, parity, and other flags. |
| 2 | [CALL instruction stack note](images/Day%204/day-4-call-instruction-stack-note.png) | `CALL` saves return address on stack and jumps to subroutine. |
| 3 | [RST n restart instruction](images/Day%204/day-4-rst-n-restart-instruction.png) | `RST n` is a one-byte call to a fixed vector address. |
| 4 | [Stack pointer operation note](images/Day%204/day-4-stack-pointer-operation-note.png) | Stack operations use extra machine cycles for memory writes/reads. |
| 5 | [PUSH and POP PSW note](images/Day%204/day-4-push-pop-psw-note.png) | `PUSH PSW` and `POP PSW` save/restore accumulator and flags. |
| 6 | [Stack pointer and HL question](images/Day%204/day-4-stack-pointer-hl-question.png) | Program tracing before final stack solution. |
| 7 | [Stack pointer and HL solution](images/Day%204/day-4-stack-pointer-hl-question-solution.png) | Solved effect of `CALL` and `POP H` on `SP` and `HL`. |
| 8 | [Signed input-port loop question](images/Day%204/day-4-signed-input-port-loop-question.png) | Using `RAL` and `JNC` to test sign bit from an input port. |
| 9 | [SIM SOD and RST 7.5 mask bits](images/Day%204/day-4-sim-sod-rst75-mask-bits.png) | Accumulator bit fields used by `SIM`. |
| 10 | [NOP, DI, EI, SIM, RIM common features](images/Day%204/day-4-nop-di-ei-sim-rim-common-features.png) | One-byte implied instructions with no flags affected. |

## Handwritten Notes Linked To Day 4

Each handwritten page is shown first as a large full-page image. Click the image or page title to open the high-resolution extracted page, then read the explanation below it.

### [till73 p001](images/HandWrittenNotes/till73/page-001.jpg)

<a href="images/HandWrittenNotes/till73/page-001.jpg"><img src="images/HandWrittenNotes/till73/page-001.jpg" alt="till73 p001 handwritten note" width="960"></a>

Explanation: This page is mainly about Subroutine, conditional/unconditional `CALL`, and repeated code. Use with subroutine motivation. Repeated code becomes a callable routine; `CALL` transfers control and stores the return path. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For branch, call, and return pages, the condition is decided from flags already set by earlier work. The control-transfer instruction tests those flags; it does not create the arithmetic result itself.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p002](images/HandWrittenNotes/till73/page-002.jpg)

<a href="images/HandWrittenNotes/till73/page-002.jpg"><img src="images/HandWrittenNotes/till73/page-002.jpg" alt="till73 p002 handwritten note" width="960"></a>

Explanation: This page is mainly about Conditional calls, conditional returns, flags, and stack use. Use with conditional call/return. The condition is always decided from existing flags, not from the `CALL` instruction itself. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For flags, do not memorize only the names. Recompute the result, then decide `S`, `Z`, `AC`, `P`, and `CY` from that result; in subtraction, `CY` must be read as borrow. For branch, call, and return pages, the condition is decided from flags already set by earlier work. The control-transfer instruction tests those flags; it does not create the arithmetic result itself. For stack questions, write `SP` before and after every operation. `PUSH` and `CALL` move the stack downward before storing bytes; `POP` and `RET` read bytes and then move `SP` upward.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p003](images/HandWrittenNotes/till73/page-003.jpg)

<a href="images/HandWrittenNotes/till73/page-003.jpg"><img src="images/HandWrittenNotes/till73/page-003.jpg" alt="till73 p003 handwritten note" width="960"></a>

Explanation: This page is mainly about Conditional return/call, subroutine return, and flag conditions. Use with branch tables. It groups carry, zero, sign, and parity conditions for calls and returns. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For flags, do not memorize only the names. Recompute the result, then decide `S`, `Z`, `AC`, `P`, and `CY` from that result; in subtraction, `CY` must be read as borrow. For branch, call, and return pages, the condition is decided from flags already set by earlier work. The control-transfer instruction tests those flags; it does not create the arithmetic result itself.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p004](images/HandWrittenNotes/till73/page-004.jpg)

<a href="images/HandWrittenNotes/till73/page-004.jpg"><img src="images/HandWrittenNotes/till73/page-004.jpg" alt="till73 p004 handwritten note" width="960"></a>

Explanation: This page is mainly about Program trace with `CALL`, register updates, and flags. Use with program tracing. Update registers and flags one instruction at a time before deciding whether a branch is taken. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For register pages, keep 8-bit registers and 16-bit register pairs separate. `B-C`, `D-E`, and `H-L` can be used together as address or data pairs, while `PC` and `SP` have special control roles. For flags, do not memorize only the names. Recompute the result, then decide `S`, `Z`, `AC`, `P`, and `CY` from that result; in subtraction, `CY` must be read as borrow. For branch, call, and return pages, the condition is decided from flags already set by earlier work. The control-transfer instruction tests those flags; it does not create the arithmetic result itself.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p005](images/HandWrittenNotes/till73/page-005.jpg)

<a href="images/HandWrittenNotes/till73/page-005.jpg"><img src="images/HandWrittenNotes/till73/page-005.jpg" alt="till73 p005 handwritten note" width="960"></a>

Explanation: This page is mainly about `CALL`, `RET`, stack pointer, PSW, and push/pop purpose. Use with `CALL` and stack screenshots. The return address is pushed before `PC` is loaded with the subroutine address. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For register pages, keep 8-bit registers and 16-bit register pairs separate. `B-C`, `D-E`, and `H-L` can be used together as address or data pairs, while `PC` and `SP` have special control roles. For branch, call, and return pages, the condition is decided from flags already set by earlier work. The control-transfer instruction tests those flags; it does not create the arithmetic result itself. For stack questions, write `SP` before and after every operation. `PUSH` and `CALL` move the stack downward before storing bytes; `POP` and `RET` read bytes and then move `SP` upward.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p006](images/HandWrittenNotes/till73/page-006.jpg)

<a href="images/HandWrittenNotes/till73/page-006.jpg"><img src="images/HandWrittenNotes/till73/page-006.jpg" alt="till73 p006 handwritten note" width="960"></a>

Explanation: This page is mainly about `PUSH`, `POP`, `XTHL`, `SPHL`, and stack/register exchange. Use with `PUSH`, `POP`, `XTHL`, and `SPHL`. These are all stack/register-transfer ideas, not ALU operations. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

The CPU-side idea is to separate the data path from the status path: the accumulator holds the working or result byte, while the flags describe that result for the next instruction or branch. For stack questions, write `SP` before and after every operation. `PUSH` and `CALL` move the stack downward before storing bytes; `POP` and `RET` read bytes and then move `SP` upward.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p007](images/HandWrittenNotes/till73/page-007.jpg)

<a href="images/HandWrittenNotes/till73/page-007.jpg"><img src="images/HandWrittenNotes/till73/page-007.jpg" alt="till73 p007 handwritten note" width="960"></a>

Explanation: This page is mainly about `CALL 1006H`, return address, `POP H`, and `SP` tracing. Use with the `CALL 1006H` and `POP H` question. Track `SP`, low byte, and high byte separately. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For register pages, keep 8-bit registers and 16-bit register pairs separate. `B-C`, `D-E`, and `H-L` can be used together as address or data pairs, while `PC` and `SP` have special control roles. For branch, call, and return pages, the condition is decided from flags already set by earlier work. The control-transfer instruction tests those flags; it does not create the arithmetic result itself. For stack questions, write `SP` before and after every operation. `PUSH` and `CALL` move the stack downward before storing bytes; `POP` and `RET` read bytes and then move `SP` upward.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p008](images/HandWrittenNotes/till73/page-008.jpg)

<a href="images/HandWrittenNotes/till73/page-008.jpg"><img src="images/HandWrittenNotes/till73/page-008.jpg" alt="till73 p008 handwritten note" width="960"></a>

Explanation: This page is mainly about Stack trace with `PUSH`, `POP`, `DAD`, and register contents. Use with stack trace practice. Draw the stack vertically; pushes move downward and pops move upward. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For stack questions, write `SP` before and after every operation. `PUSH` and `CALL` move the stack downward before storing bytes; `POP` and `RET` read bytes and then move `SP` upward. For trace pages, do not jump from the first instruction to the final answer. Make a row for each instruction and update only the registers, memory bytes, flags, or stack locations that the instruction actually changes.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p009](images/HandWrittenNotes/till73/page-009.jpg)

<a href="images/HandWrittenNotes/till73/page-009.jpg"><img src="images/HandWrittenNotes/till73/page-009.jpg" alt="till73 p009 handwritten note" width="960"></a>

Explanation: This page is mainly about `IN`, `OUT`, signed input loop, `RAL`, and carry test. Use with the signed input loop. `RAL` moves the old sign bit into carry, and `JNC` repeats while that bit is zero. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For flags, do not memorize only the names. Recompute the result, then decide `S`, `Z`, `AC`, `P`, and `CY` from that result; in subtraction, `CY` must be read as borrow. For rotates, draw the accumulator bits and the carry bit before each step. The important distinction is whether the rotation is circular inside the accumulator or passes through `CY`. For delay loops, count the repeated path and the final exit path separately. The last iteration usually has a different branch timing because the conditional jump is not taken.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p010](images/HandWrittenNotes/till73/page-010.jpg)

<a href="images/HandWrittenNotes/till73/page-010.jpg"><img src="images/HandWrittenNotes/till73/page-010.jpg" alt="till73 p010 handwritten note" width="960"></a>

Explanation: This page is mainly about `HLT`, `NOP`, `DI`, `EI`, and `SIM`. Use with `NOP`, `DI`, `EI`, and `SIM`. These are one-byte implied instructions with control effects. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

`SIM` and `RIM` should be read as accumulator-byte formats. One controls masks/serial output, and the other reads interrupt-mask, pending, enable, and serial-input status.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p011](images/HandWrittenNotes/till73/page-011.jpg)

<a href="images/HandWrittenNotes/till73/page-011.jpg"><img src="images/HandWrittenNotes/till73/page-011.jpg" alt="till73 p011 handwritten note" width="960"></a>

Explanation: This page is mainly about `RIM` bit fields, interrupt pending bits, and serial input. Use with `RIM`. This page is the status-byte view: pending interrupt bits, mask bits, interrupt enable, and serial input. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

`SIM` and `RIM` should be read as accumulator-byte formats. One controls masks/serial output, and the other reads interrupt-mask, pending, enable, and serial-input status. For interrupts, keep four separate ideas: who requested service, whether it can be masked, where the CPU jumps, and how the interrupted program returns after the ISR.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

### [till73 p012](images/HandWrittenNotes/till73/page-012.jpg)

<a href="images/HandWrittenNotes/till73/page-012.jpg"><img src="images/HandWrittenNotes/till73/page-012.jpg" alt="till73 p012 handwritten note" width="960"></a>

Explanation: This page is mainly about `SIM` mask bits, `SOD`, `SDE`, and interrupt masks. Use with `SIM`. This page is the control-byte view: mask set enable, interrupt masks, reset `RST 7.5`, and serial output. Read the handwritten page first as the source page: identify the named instructions, signals, registers, or diagrams, then follow the example in the same order it is written.

For signal pages, group each pin by job: address/data transfer, control, status, clock/reset, interrupt, DMA, or serial I/O. This makes the pin diagram readable instead of a list to memorize blindly. `SIM` and `RIM` should be read as accumulator-byte formats. One controls masks/serial output, and the other reads interrupt-mask, pending, enable, and serial-input status. For interrupts, keep four separate ideas: who requested service, whether it can be masked, where the CPU jumps, and how the interrupted program returns after the ISR.

For revision, turn the page into a small working checklist instead of a single memory line. Write the important names from the page, state what each one controls or changes, and then trace the example values step by step. This keeps the explanation tied to the handwritten content while still making the idea usable for exam questions and program traces.

## 1. Conditional Jump Instructions

![Conditional jump instructions table](images/Day%204/day-4-conditional-jump-instructions-table.png)

Conditional jumps change the program counter only if a condition is true. The condition is usually based on a flag set by a previous arithmetic or logical instruction.

Common conditional jumps:

| Instruction | Meaning |
| --- | --- |
| `JC addr` | Jump if carry flag `CY = 1`. |
| `JNC addr` | Jump if carry flag `CY = 0`. |
| `JZ addr` | Jump if zero flag `Z = 1`. |
| `JNZ addr` | Jump if zero flag `Z = 0`. |
| `JM addr` | Jump if sign flag `S = 1`, meaning result has MSB `1`. |
| `JP addr` | Jump if sign flag `S = 0`. |
| `JPE addr` | Jump if parity flag `P = 1`, even parity. |
| `JPO addr` | Jump if parity flag `P = 0`, odd parity. |

All these instructions are branch instructions, and they normally contain a 16-bit target address. If the condition is true, `PC` is loaded with that address. If false, execution continues with the next sequential instruction.

Common trap: a jump instruction checks the existing flag value. It does not calculate the condition by itself. For example, `JZ` only checks whether `Z` is already set by a previous operation.

## 2. `CALL`: Subroutine Entry and Return Address Storage

![CALL instruction stack note](images/Day%204/day-4-call-instruction-stack-note.png)

`CALL addr` transfers control to a subroutine, but it also remembers where to return. It does this by pushing the address of the next instruction onto the stack.

Conceptually:

```text
stack <- address of instruction after CALL
PC <- subroutine address
```

The stack in 8085 grows downward. That means pushes reduce `SP`, and pops increase `SP`.

For a `CALL 1006H`, the CPU fetches the three-byte instruction, computes the return address, stores that return address on the stack, and then loads `PC = 1006H`.

The important mental model:

```text
CALL = PUSH return address + JUMP to subroutine
RET  = POP return address into PC
```

Common trap: `CALL` pushes the address after the call instruction, not the address of the `CALL` opcode itself.

## 3. `RST n`: Restart Instruction

![RST n restart instruction](images/Day%204/day-4-rst-n-restart-instruction.png)

`RST n` is a compact one-byte call to a fixed address. The target address is:

```text
target = 8 x n
```

where `n` is from `0` to `7`.

Examples:

| Instruction | Vector address |
| --- | --- |
| `RST 0` | `0000H` |
| `RST 1` | `0008H` |
| `RST 2` | `0010H` |
| `RST 3` | `0018H` |
| `RST 4` | `0020H` |
| `RST 5` | `0028H` |
| `RST 6` | `0030H` |
| `RST 7` | `0038H` |

Like `CALL`, `RST` stores the return address on the stack and then changes `PC`. It is called a restart because it jumps to a predefined service routine location.

Common trap: `RST 5` does not jump to address `0005H`; it jumps to `5 x 8 = 40 decimal = 0028H`.

## 4. Stack Pointer Operations

![Stack pointer operation note](images/Day%204/day-4-stack-pointer-operation-note.png)

The stack is a memory area used for temporary storage of return addresses, register pairs, and processor status. The stack pointer `SP` holds the address of the top of the stack.

In 8085:

| Operation | Effect on `SP` |
| --- | --- |
| `PUSH rp` | `SP <- SP - 2` after storing two bytes. |
| `POP rp` | `SP <- SP + 2` after reading two bytes. |
| `CALL addr` | Pushes return address, so `SP` decreases by 2. |
| `RET` | Pops return address, so `SP` increases by 2. |

Because stack operations read or write memory, they need extra machine cycles beyond simple opcode fetch. This is why stack-heavy instructions have more T-states than simple register instructions.

Common trap: the stack pointer changes by 2 for 16-bit stack operations, not by 1.

## 5. `PUSH PSW` and `POP PSW`

![PUSH and POP PSW note](images/Day%204/day-4-push-pop-psw-note.png)

`PSW` means **Program Status Word**. In 8085 context, it represents the accumulator plus the flag register:

```text
PSW = A + Flags
```

`PUSH PSW` saves the accumulator and flags on the stack. `POP PSW` restores them. This is important in subroutines and interrupt service routines because temporary code may change `A` and flags. If the caller needs them unchanged, the routine saves and restores them.

The note says `PUSH PSW` and `POP PSW` are similar to other register-pair stack instructions except that all flags are affected/restored as part of the PSW. More precisely:

- `PUSH PSW` stores current `A` and flags.
- `POP PSW` loads `A` and flags from stack data.
- Other `PUSH rp`/`POP rp` instructions work on ordinary register pairs such as `BC`, `DE`, and `HL`.

Common trap: `POP PSW` can change flags because it restores flag bits from memory.

## 6. Stack Tracing Question

![Stack pointer and HL question](images/Day%204/day-4-stack-pointer-hl-question.png)

The question sequence is:

```asm
1000 LXISP,27FFH
1003 CALL 1006H
...
1006 POP H
```

The logic:

1. `LXI SP,27FFH` sets `SP = 27FFH`.
2. `CALL 1006H` pushes the return address onto the stack.
3. A three-byte `CALL` at `1003H` returns to `1006H`.
4. Because `CALL` pushes two bytes, `SP` becomes `27FDH`.
5. The stack now contains the return address bytes.
6. `POP H` loads those two bytes into `L` and `H`, then `SP` becomes `27FFH`.

This kind of question is not about memorizing the answer. It is about tracing `SP`, return address, and byte order.

## 7. Stack Tracing Solution

![Stack pointer and HL solution](images/Day%204/day-4-stack-pointer-hl-question-solution.png)

The solved slide shows the key result:

```text
SP after CALL = 27FDH
return address = 1006H
POP H loads HL = 1006H
SP after POP H = 27FFH
```

The byte order matters. When `POP H` executes, the lower byte goes to `L`, and the higher byte goes to `H`. So if the stack contains `06H` and `10H` as the low and high bytes of the return address, `HL` becomes `1006H`.

Common trap: do not say `HL = 0610H`. The displayed register pair is high byte first: `H` then `L`.

## 8. Signed Input-Port Loop Using `RAL` and `JNC`

![Signed input-port loop question](images/Day%204/day-4-signed-input-port-loop-question.png)

The program is testing the sign bit of an 8-bit two's-complement input value:

```asm
BEGIN: IN PORT
       RAL
       JNC BEGIN
END:   HLT
```

In two's-complement signed numbers:

```text
MSB = 0 -> non-negative number
MSB = 1 -> negative number
```

`IN PORT` reads the input byte into the accumulator. `RAL` rotates the accumulator left through carry. The important effect is that the old `D7` bit of the accumulator moves into the carry flag.

Then:

```text
JNC BEGIN means jump if carry = 0
```

So the program loops while the input sign bit is `0`. It stops when the input sign bit is `1`.

Therefore, the program halts when a **negative number** is read.

Common trap: `RAL` changes the accumulator, but for this question the important result is the carry flag receiving the old MSB.

## 9. `SIM`: Serial Output and Interrupt Mask Control

![SIM SOD and RST 7.5 mask bits](images/Day%204/day-4-sim-sod-rst75-mask-bits.png)

`SIM` means **Set Interrupt Mask**. It uses the accumulator as a control byte. Each bit has a defined meaning:

| Accumulator bit | Meaning in `SIM` |
| --- | --- |
| `D7` | `SOD`: serial output data bit. |
| `D6` | `SDE`: serial data enable. If set, `D7` is sent to `SOD`. |
| `D5` | Unused in common 8085 description. |
| `D4` | Reset `RST 7.5` latch when set. |
| `D3` | `MSE`: mask set enable. Enables mask bits `D2-D0`. |
| `D2` | Mask `RST 7.5`. |
| `D1` | Mask `RST 6.5`. |
| `D0` | Mask `RST 5.5`. |

This is a good example of bit-field programming. The accumulator value is not treated as a normal number; each bit is a control switch.

Common trap: setting `D7` alone does not necessarily output serial data. `SDE` (`D6`) must also enable serial output.

## 10. `NOP`, `DI`, `EI`, `SIM`, and `RIM`

![NOP, DI, EI, SIM, RIM common features](images/Day%204/day-4-nop-di-ei-sim-rim-common-features.png)

These instructions are one-byte instructions with implied behavior:

| Instruction | Meaning |
| --- | --- |
| `NOP` | No operation; useful for delay or placeholder code. |
| `DI` | Disable maskable interrupts. |
| `EI` | Enable maskable interrupts. |
| `SIM` | Set interrupt masks and serial output using accumulator bits. |
| `RIM` | Read interrupt masks/pending status and serial input into accumulator. |

They do not require an explicit operand because the operation is defined by the instruction itself. They also do not perform ordinary arithmetic, so they are not used to update arithmetic result flags.

Common trap: `DI` and `EI` affect interrupt enable state, not the interrupt hardware pins themselves. A masked or disabled interrupt may still physically occur, but the CPU will not service it in the normal way.

## Points To Remember

- Conditional jumps test flags set earlier.
- `CALL` pushes the return address and then jumps.
- `RET` pops the return address back into `PC`.
- Stack grows downward: push decreases `SP`, pop increases `SP`.
- 16-bit stack operations change `SP` by 2.
- `PSW` means accumulator plus flags.
- `RST n` jumps to `8 x n`, not to `n`.
- `RAL` moves old accumulator bit `D7` into carry.
- `SIM` and `RIM` use accumulator bits as control/status fields.
- For stack questions, track `SP`, memory bytes, and low-byte/high-byte order separately.

## Sources

[S1] Intel Corporation, [MCS-80/85 Family User's Manual, January 1983](https://www.bitsavers.org/components/intel/MCS80/MCS80_85_Users_Manual_Jan83.pdf). Used for jump, call, restart, stack, flags, interrupt, `SIM`, `RIM`, and rotate instruction behavior.

[S2] Intel Corporation, [8080/8085 Assembly Language Programming Manual, May 1981](https://www.bitsavers.org/pdf/intel/ISIS_II/9800301-04_8080_8085_Assembly_Language_Programming_Manual_May81.pdf). Used for assembly-language notation, stack programming concepts, and branch/control instruction descriptions.
