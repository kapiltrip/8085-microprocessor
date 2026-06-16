# Day 11: 8086 XCHG, LEA, LES, and PUSH Instructions

Day 11 continues the 8086 instruction-set discussion from Day 10. The screenshots focus on data-transfer and address-related instructions: exchanging register or memory operands, loading an effective address, loading a far pointer into `ES` and a register, and pushing word-sized data onto the stack.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [XCHG and LEA instructions](images/Day%2011/Screenshot%202026-06-12%20163213.png) | `XCHG` swaps operands; `LEA` loads an offset address without changing flags. |
| 2 | [LES and PUSH instructions](images/Day%2011/Screenshot%202026-06-12%20163257.png) | `LES` loads a register and `ES` from memory; `PUSH` stores a word on the stack. |

## 1. XCHG and LEA Instructions

![XCHG and LEA instructions](images/Day%2011/Screenshot%202026-06-12%20163213.png)

### XCHG

`XCHG` exchanges the contents of two operands. The operands must be the same size.

Examples from the screenshot:

```asm
XCHG AX, DX
XCHG BL, CH
XCHG AL, PRICES[BX]
```

Meaning:

| Instruction | Operation |
| --- | --- |
| `XCHG AX, DX` | Exchange the word in `AX` with the word in `DX`. |
| `XCHG BL, CH` | Exchange the byte in `BL` with the byte in `CH`. |
| `XCHG AL, PRICES[BX]` | Exchange `AL` with the byte stored at offset `PRICES + BX` in the data segment. |

For the memory example, the square brackets mean that the operand is the memory byte at the computed effective address. `PRICES[BX]` uses `BX` as an index/base value added to the offset of `PRICES`.

Important rule:

```text
XCHG cannot exchange two memory operands directly.
```

At least one operand must be a register.

### LEA

`LEA` means **Load Effective Address**.

General form:

```asm
LEA register, source
```

`LEA` calculates the offset address of the source operand and loads that offset into the destination register. It does not load the data stored at that address.

Example idea:

```asm
LEA SI, PRICES[BX]
```

Meaning:

```text
SI <- offset address of PRICES[BX]
```

`LEA` is useful when a program needs an address for later memory access. It does not affect any flag.

## 2. LES and PUSH Instructions

![LES and PUSH instructions](images/Day%2011/Screenshot%202026-06-12%20163257.png)

### LES

`LES` means **Load Register and ES**.

General form:

```asm
LES register, memory
```

This instruction treats the memory operand as a far pointer made of two consecutive words:

| Memory word | Loaded into |
| --- | --- |
| First word | Specified register |
| Next word | `ES` segment register |

So `LES` loads both an offset and a segment value. It is used when the program needs a pointer of the form:

```text
ES:register
```

Important points:

```text
LES uses a memory operand.
LES does not affect any flag.
```

### PUSH

`PUSH` places a word onto the stack.

In 8086, the stack grows downward. So a push first reduces `SP`, then stores the word at the new stack location.

Example operations from the screenshot:

| Instruction | Operation |
| --- | --- |
| `PUSH BX` | `SP <- SP - 2`, then copy `BX` to the stack. |
| `PUSH DS` | `SP <- SP - 2`, then copy `DS` to the stack. |
| `PUSH BL` | Illegal, because `BL` is only an 8-bit register. |

The 8086 stack stores words, so `PUSH` works with 16-bit operands such as general-purpose word registers, segment registers, or memory words. Byte registers like `BL`, `AL`, and `CH` cannot be pushed directly.

## Research Deep Dive: Address Versus Contents, Far Pointers, and Stack Bytes

Day 11 has a small number of screenshots, but the concepts are central to 8086 programming.

### `LEA` Does Address Arithmetic Only

Compare these two instructions:

```asm
LEA SI,PRICES[BX]
MOV SI,PRICES[BX]
```

If:

```text
offset PRICES = 1000H
BX = 0004H
memory word at DS:1004H = 2222H
```

then:

| Instruction | Result |
| --- | --- |
| `LEA SI,PRICES[BX]` | `SI = 1004H` |
| `MOV SI,PRICES[BX]` | `SI = 2222H` |

This is the cleanest way to remember `LEA`: it loads the calculated offset, not the memory contents.

### `LES` Loads A Far Pointer

`LES register,memory` reads four bytes from memory:

| Memory bytes | Loaded into |
| --- | --- |
| first word | destination register |
| second word | `ES` |

Example memory layout:

| Offset | Byte |
| --- | --- |
| `2000H` | `56H` |
| `2001H` | `34H` |
| `2002H` | `9AH` |
| `2003H` | `78H` |

For:

```asm
LES DI,[2000H]
```

the result is:

```text
DI = 3456H
ES = 789AH
```

The bytes are low-byte first inside each word. After this, `ES:DI` points to the far memory location `789AH:3456H`.

### `PUSH` Byte Order Example

Suppose:

```text
SS = 3000H
SP = 0100H
AX = 1234H
```

After:

```asm
PUSH AX
```

the stack pointer becomes:

```text
SP = 00FEH
```

The word is stored low byte first at the new stack location:

| Logical stack address | Byte |
| --- | --- |
| `SS:00FEH` | `34H` |
| `SS:00FFH` | `12H` |

Physical location of the first byte:

```text
3000H x 10H + 00FEH = 300FEH
```

This example connects `PUSH` to segmentation, downward stack growth, and little-endian word storage.

## Points To Remember

| Instruction | Main use | Key point |
| --- | --- | --- |
| `XCHG` | Exchange two operands | Operands must be the same size; memory-to-memory exchange is not allowed. |
| `LEA` | Load an effective offset address | Loads the address, not the contents of memory. |
| `LES` | Load far pointer into `ES:register` | First memory word goes to the register; next word goes to `ES`. |
| `PUSH` | Save a word on stack | `SP` decreases by 2 before the word is stored. |

## Sources

[S1] Intel Corporation, [The 8086 Family User's Manual, October 1979](https://www.ardent-tool.com/CPU/docs/Intel/808x/manuals/9800722-03.pdf). Used for 8086 data-transfer instructions, effective-address calculation, stack operation, and segment-register behavior.

[S2] Intel Corporation, [iAPX 86,88 User's Manual, August 1981](https://www.dosdays.co.uk/media/intel/1981_iAPX_86_88_Users_Manual.pdf). Used for far pointer, `LES`, stack, and programming-model details.
