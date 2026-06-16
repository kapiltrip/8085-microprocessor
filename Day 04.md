# Day 04: Branching, Stack, Interrupt Control, and Bit-Level I/O

Day 04 is about control flow and stack-based execution. Earlier days explained how the 8085 fetches and executes ordinary instructions. This day explains how execution changes direction through conditional jumps, `CALL`, `RST`, stack operations, interrupt control, and bit-level serial/interrupt masking through `SIM` and `RIM`.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [Conditional jump instructions table](images/Day%2004/day-4-conditional-jump-instructions-table.png) | Branching based on carry, zero, sign, parity, and other flags. |
| 2 | [CALL instruction stack note](images/Day%2004/day-4-call-instruction-stack-note.png) | `CALL` saves return address on stack and jumps to subroutine. |
| 3 | [RST n restart instruction](images/Day%2004/day-4-rst-n-restart-instruction.png) | `RST n` is a one-byte call to a fixed vector address. |
| 4 | [Stack pointer operation note](images/Day%2004/day-4-stack-pointer-operation-note.png) | Stack operations use extra machine cycles for memory writes/reads. |
| 5 | [PUSH and POP PSW note](images/Day%2004/day-4-push-pop-psw-note.png) | `PUSH PSW` and `POP PSW` save/restore accumulator and flags. |
| 6 | [Stack pointer and HL question](images/Day%2004/day-4-stack-pointer-hl-question.png) | Program tracing before final stack solution. |
| 7 | [Stack pointer and HL solution](images/Day%2004/day-4-stack-pointer-hl-question-solution.png) | Solved effect of `CALL` and `POP H` on `SP` and `HL`. |
| 8 | [Signed input-port loop question](images/Day%2004/day-4-signed-input-port-loop-question.png) | Using `RAL` and `JNC` to test sign bit from an input port. |
| 9 | [SIM SOD and RST 7.5 mask bits](images/Day%2004/day-4-sim-sod-rst75-mask-bits.png) | Accumulator bit fields used by `SIM`. |
| 10 | [NOP, DI, EI, SIM, RIM common features](images/Day%2004/day-4-nop-di-ei-sim-rim-common-features.png) | One-byte implied instructions with no flags affected. |

## Handwritten Notes Linked To Day 04

Each handwritten page is shown first as a large full-page image. The explanation below the image adds the technical layer: instruction behavior, bus cycles, flags, timing, address formation, or hardware reason behind the note.

### [till73 p001](images/HandWrittenNotes/till73/page-001.jpg)

<a href="images/HandWrittenNotes/till73/page-001.jpg"><img src="images/HandWrittenNotes/till73/page-001.jpg" alt="till73 p001 handwritten note" width="960"></a>

Technical explanation: `CALL` is both a control-transfer instruction and a stack operation. The CPU fetches the target address, pushes the return address on the stack, and loads `PC` with the target. `RET` pops the saved address back into `PC`. The return address is the address of the next instruction after the call, not the call instruction's own address.

Branch instructions test existing state; they do not create the condition themselves. Conditional jumps, calls, and returns read flag bits left by earlier instructions. Timing can differ between taken and not-taken branches because the processor may or may not load the target address into `PC`. This is why loop timing must count the last failed branch separately.

### [till73 p002](images/HandWrittenNotes/till73/page-002.jpg)

<a href="images/HandWrittenNotes/till73/page-002.jpg"><img src="images/HandWrittenNotes/till73/page-002.jpg" alt="till73 p002 handwritten note" width="960"></a>

Technical explanation: `CALL` is both a control-transfer instruction and a stack operation. The CPU fetches the target address, pushes the return address on the stack, and loads `PC` with the target. `RET` pops the saved address back into `PC`. The return address is the address of the next instruction after the call, not the call instruction's own address.

Branch instructions test existing state; they do not create the condition themselves. Conditional jumps, calls, and returns read flag bits left by earlier instructions. Timing can differ between taken and not-taken branches because the processor may or may not load the target address into `PC`. This is why loop timing must count the last failed branch separately.

The 8085 stack grows toward lower memory addresses. On push-like operations, `SP` is decremented before bytes are stored; on pop-like operations, bytes are read and then `SP` is incremented. For register pairs, keep high and low bytes separate because the final memory layout determines what a later `POP` or `RET` reconstructs.

### [till73 p003](images/HandWrittenNotes/till73/page-003.jpg)

<a href="images/HandWrittenNotes/till73/page-003.jpg"><img src="images/HandWrittenNotes/till73/page-003.jpg" alt="till73 p003 handwritten note" width="960"></a>

Technical explanation: `CALL` is both a control-transfer instruction and a stack operation. The CPU fetches the target address, pushes the return address on the stack, and loads `PC` with the target. `RET` pops the saved address back into `PC`. The return address is the address of the next instruction after the call, not the call instruction's own address.

Branch instructions test existing state; they do not create the condition themselves. Conditional jumps, calls, and returns read flag bits left by earlier instructions. Timing can differ between taken and not-taken branches because the processor may or may not load the target address into `PC`. This is why loop timing must count the last failed branch separately.

### [till73 p004](images/HandWrittenNotes/till73/page-004.jpg)

<a href="images/HandWrittenNotes/till73/page-004.jpg"><img src="images/HandWrittenNotes/till73/page-004.jpg" alt="till73 p004 handwritten note" width="960"></a>

Technical explanation: `CALL` is both a control-transfer instruction and a stack operation. The CPU fetches the target address, pushes the return address on the stack, and loads `PC` with the target. `RET` pops the saved address back into `PC`. The return address is the address of the next instruction after the call, not the call instruction's own address.

Branch instructions test existing state; they do not create the condition themselves. Conditional jumps, calls, and returns read flag bits left by earlier instructions. Timing can differ between taken and not-taken branches because the processor may or may not load the target address into `PC`. This is why loop timing must count the last failed branch separately.

For program traces, keep a state table. Each row should list only what the current instruction changes: registers, flags, memory, `PC`, `SP`, or stack bytes. This prevents the common mistake of updating a value but forgetting the pointer that determines where the next memory access will happen.

### [till73 p005](images/HandWrittenNotes/till73/page-005.jpg)

<a href="images/HandWrittenNotes/till73/page-005.jpg"><img src="images/HandWrittenNotes/till73/page-005.jpg" alt="till73 p005 handwritten note" width="960"></a>

Technical explanation: `CALL` is both a control-transfer instruction and a stack operation. The CPU fetches the target address, pushes the return address on the stack, and loads `PC` with the target. `RET` pops the saved address back into `PC`. The return address is the address of the next instruction after the call, not the call instruction's own address.

Branch instructions test existing state; they do not create the condition themselves. Conditional jumps, calls, and returns read flag bits left by earlier instructions. Timing can differ between taken and not-taken branches because the processor may or may not load the target address into `PC`. This is why loop timing must count the last failed branch separately.

The 8085 stack grows toward lower memory addresses. On push-like operations, `SP` is decremented before bytes are stored; on pop-like operations, bytes are read and then `SP` is incremented. For register pairs, keep high and low bytes separate because the final memory layout determines what a later `POP` or `RET` reconstructs.

### [till73 p006](images/HandWrittenNotes/till73/page-006.jpg)

<a href="images/HandWrittenNotes/till73/page-006.jpg"><img src="images/HandWrittenNotes/till73/page-006.jpg" alt="till73 p006 handwritten note" width="960"></a>

Technical explanation: the 8085 stack grows toward lower memory addresses. On push-like operations, `SP` is decremented before bytes are stored; on pop-like operations, bytes are read and then `SP` is incremented. For register pairs, keep high and low bytes separate because the final memory layout determines what a later `POP` or `RET` reconstructs.

`XTHL` exchanges `L` and `H` with the two bytes at the top of the stack, while `SPHL` copies `HL` into `SP`. They are not arithmetic instructions. Their value is that they let software manipulate stack-resident 16-bit values, temporary addresses, or return-address-like data without ordinary byte-by-byte loads.

### [till73 p007](images/HandWrittenNotes/till73/page-007.jpg)

<a href="images/HandWrittenNotes/till73/page-007.jpg"><img src="images/HandWrittenNotes/till73/page-007.jpg" alt="till73 p007 handwritten note" width="960"></a>

Technical explanation: `CALL` is both a control-transfer instruction and a stack operation. The CPU fetches the target address, pushes the return address on the stack, and loads `PC` with the target. `RET` pops the saved address back into `PC`. The return address is the address of the next instruction after the call, not the call instruction's own address.

Branch instructions test existing state; they do not create the condition themselves. Conditional jumps, calls, and returns read flag bits left by earlier instructions. Timing can differ between taken and not-taken branches because the processor may or may not load the target address into `PC`. This is why loop timing must count the last failed branch separately.

The 8085 stack grows toward lower memory addresses. On push-like operations, `SP` is decremented before bytes are stored; on pop-like operations, bytes are read and then `SP` is incremented. For register pairs, keep high and low bytes separate because the final memory layout determines what a later `POP` or `RET` reconstructs.

### [till73 p008](images/HandWrittenNotes/till73/page-008.jpg)

<a href="images/HandWrittenNotes/till73/page-008.jpg"><img src="images/HandWrittenNotes/till73/page-008.jpg" alt="till73 p008 handwritten note" width="960"></a>

Technical explanation: `XCHG` swaps `HL` with `DE`; it does not touch memory. `DAD rp` adds a 16-bit register pair to `HL` and stores the 16-bit result in `HL`, with carry out recorded in `CY`. When tracing these, combine each pair as a 16-bit number first, then split the result back into high and low registers.

The 8085 stack grows toward lower memory addresses. On push-like operations, `SP` is decremented before bytes are stored; on pop-like operations, bytes are read and then `SP` is incremented. For register pairs, keep high and low bytes separate because the final memory layout determines what a later `POP` or `RET` reconstructs.

For program traces, keep a state table. Each row should list only what the current instruction changes: registers, flags, memory, `PC`, `SP`, or stack bytes. This prevents the common mistake of updating a value but forgetting the pointer that determines where the next memory access will happen.

### [till73 p009](images/HandWrittenNotes/till73/page-009.jpg)

<a href="images/HandWrittenNotes/till73/page-009.jpg"><img src="images/HandWrittenNotes/till73/page-009.jpg" alt="till73 p009 handwritten note" width="960"></a>

Technical explanation: rotate instructions are best traced as eight accumulator bits plus a separate carry. `RLC` moves bit 7 into bit 0 and into `CY`; `RRC` moves bit 0 into bit 7 and into `CY`. `RAL` and `RAR` rotate through carry, so the old carry becomes part of the new accumulator and the outgoing accumulator bit becomes the new carry.

`IN` and `OUT` move bytes through the accumulator but do not by themselves create signed arithmetic meaning. If the program uses `RAL` and then tests carry, the sign bit or selected bit is being moved into `CY` deliberately. The branch decision is therefore based on the rotated bit, not on a fresh comparison instruction.

### [till73 p010](images/HandWrittenNotes/till73/page-010.jpg)

<a href="images/HandWrittenNotes/till73/page-010.jpg"><img src="images/HandWrittenNotes/till73/page-010.jpg" alt="till73 p010 handwritten note" width="960"></a>

Technical explanation: `SIM` and `RIM` treat the accumulator as a packed control/status byte. For `SIM`, bits control interrupt masks, mask-set enable, `RST 7.5` reset, serial data enable, and serial output data. For `RIM`, bits report mask status, interrupt enable, pending interrupt requests, and serial input data. Label each bit before interpreting the byte.

`NOP`, `HLT`, `DI`, `EI`, and `SIM` are control-oriented instructions. `NOP` only consumes time, `HLT` stops execution until interrupt/reset, `DI/EI` change interrupt enable state, and `SIM` uses accumulator bits as control fields. Their operands are implied by CPU control logic rather than fetched as ordinary data bytes.

### [till73 p011](images/HandWrittenNotes/till73/page-011.jpg)

<a href="images/HandWrittenNotes/till73/page-011.jpg"><img src="images/HandWrittenNotes/till73/page-011.jpg" alt="till73 p011 handwritten note" width="960"></a>

Technical explanation: `SIM` and `RIM` treat the accumulator as a packed control/status byte. For `SIM`, bits control interrupt masks, mask-set enable, `RST 7.5` reset, serial data enable, and serial output data. For `RIM`, bits report mask status, interrupt enable, pending interrupt requests, and serial input data. Label each bit before interpreting the byte.

8085 serial I/O is bit-oriented. `SID` is the serial input data pin and `SOD` is the serial output data pin. They are controlled through `RIM` and `SIM`, not through normal parallel `IN` and `OUT` port transfers. Serial transfer moves one bit under software control; normal I/O ports move an 8-bit byte through the data bus.

### [till73 p012](images/HandWrittenNotes/till73/page-012.jpg)

<a href="images/HandWrittenNotes/till73/page-012.jpg"><img src="images/HandWrittenNotes/till73/page-012.jpg" alt="till73 p012 handwritten note" width="960"></a>

Technical explanation: `SIM` and `RIM` treat the accumulator as a packed control/status byte. For `SIM`, bits control interrupt masks, mask-set enable, `RST 7.5` reset, serial data enable, and serial output data. For `RIM`, bits report mask status, interrupt enable, pending interrupt requests, and serial input data. Label each bit before interpreting the byte.

8085 serial I/O is bit-oriented. `SID` is the serial input data pin and `SOD` is the serial output data pin. They are controlled through `RIM` and `SIM`, not through normal parallel `IN` and `OUT` port transfers. Serial transfer moves one bit under software control; normal I/O ports move an 8-bit byte through the data bus.

## 1. Conditional Jump Instructions

![Conditional jump instructions table](images/Day%2004/day-4-conditional-jump-instructions-table.png)

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

![CALL instruction stack note](images/Day%2004/day-4-call-instruction-stack-note.png)

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

![RST n restart instruction](images/Day%2004/day-4-rst-n-restart-instruction.png)

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

![Stack pointer operation note](images/Day%2004/day-4-stack-pointer-operation-note.png)

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

![PUSH and POP PSW note](images/Day%2004/day-4-push-pop-psw-note.png)

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

![Stack pointer and HL question](images/Day%2004/day-4-stack-pointer-hl-question.png)

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

![Stack pointer and HL solution](images/Day%2004/day-4-stack-pointer-hl-question-solution.png)

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

![Signed input-port loop question](images/Day%2004/day-4-signed-input-port-loop-question.png)

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

![SIM SOD and RST 7.5 mask bits](images/Day%2004/day-4-sim-sod-rst75-mask-bits.png)

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

![NOP, DI, EI, SIM, RIM common features](images/Day%2004/day-4-nop-di-ei-sim-rim-common-features.png)

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

## Research Deep Dive: Stack Discipline and Control Flow

Day 04 is where program flow stops being purely sequential. The CPU must remember where to return after `CALL`, `RST`, interrupts, and stack operations. That is why stack tracing must be exact.

### What `CALL` Really Saves

If a three-byte instruction:

```asm
CALL 2500H
```

starts at `2000H`, the next instruction address is:

```text
2003H
```

That return address is what gets pushed on the stack, not `2000H` and not `2500H`.

The stack grows downward. In 8085 stack discussions, treat the return address as two separate bytes:

```text
high byte = 20H
low byte  = 03H
```

When tracing, do not just write "PC saved." Write the actual bytes and the final `SP`.

### `RST n` As A One-Byte Call

`RST n` is best understood as a compact call to a fixed vector:

```text
restart address = n x 8
```

Examples:

| Instruction | Restart address |
| --- | --- |
| `RST 1` | `0008H` |
| `RST 5` | `0028H` |
| `RST 7.5` interrupt | `003CH` |

The software instruction form `RST n` is one byte, so it is useful when a small vector call is needed. Hardware interrupts use the same restart-address idea for vectored interrupt service.

### Stack Safety Rule

Every push-like action must be matched by a compatible pop-like action. If a subroutine pushes `B`, `D`, and `H`, then it should usually pop them in reverse order:

```asm
PUSH B
PUSH D
PUSH H
...
POP H
POP D
POP B
RET
```

That reverse order is not style; it is required because the stack is last-in, first-out.

### `SIM` And `RIM` Are Packed Bit Interfaces

`SIM` and `RIM` are not normal arithmetic instructions. They treat the accumulator as eight individual control/status bits.

Practical method:

1. Write the accumulator in binary.
2. Label bits `D7` through `D0`.
3. Interpret each bit according to the `SIM` or `RIM` table.
4. Do not treat the byte as a decimal number.

This same bit-field idea appears later in peripheral control words for chips such as the 8255 and 8259.

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
