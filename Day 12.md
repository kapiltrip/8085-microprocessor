# Day 12: 8086 Instruction Set Deep Dive

Day 12 continues the 8086 instruction-set work from Day 10 and Day 11. The screenshots go deeper into data-transfer instructions, effective-address calculation, port I/O, arithmetic, multiplication, division, increment/decrement, decimal/ASCII adjust instructions, logical `OR`, and `CMP`.

The main study rule for this day:

```text
For every 8086 instruction, ask five questions:
1. What is the operation?
2. What is the operand size: byte or word?
3. Where are the operands: register, memory, immediate, port, or implicit register?
4. Which segment is used for memory?
5. Which flags are changed, cleared, undefined, or untouched?
```

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [MOV examples and `BP` addressing](images/Day%2012/Screenshot%202026-06-15%20161545.png) | Immediate, direct memory, register, register-indirect, segment-register, and word store forms of `MOV`. |
| 2 | [XCHG and LEA](images/Day%2012/Screenshot%202026-06-15%20162837.png) | `XCHG` swaps equal-size operands; `LEA` loads an offset, not memory data. |
| 3 | [IN instruction, immediate and DX port forms](images/Day%2012/Screenshot%202026-06-15%20170903.png) | `IN AL,port`, `IN AX,port`, and the role of `DX` for 16-bit port addresses. |
| 4 | [IN instruction with annotations](images/Day%2012/Screenshot%202026-06-15%20171252.png) | Immediate port examples and the 0000H-FFFFH range possible through `DX`. |
| 5 | [MOV examples with physical-address annotation](images/Day%2012/Screenshot%202026-06-15%20171949.png) | `DS:437AH`, `RESULT[BP]`, and the correct segment-offset interpretation. |
| 6 | [ADD, ADC, SUB, SBB introduction](images/Day%2012/Screenshot%202026-06-15%20172546.png) | Addition with memory operands and carry, then subtraction instruction forms. |
| 7 | [SUB and SBB flag effects](images/Day%2012/Screenshot%202026-06-15%20223644.png) | `SUB`, `SBB`, borrow through `CF`, and affected flags. |
| 8 | [Byte array subtraction example](images/Day%2012/Screenshot%202026-06-15%20225034.png) | `DB` array indexing, `PRICES[BX]`, and byte subtraction from memory. |
| 9 | [Word array subtraction example](images/Day%2012/Screenshot%202026-06-15%20230050.png) | `DW` array indexing, word offsets, little-endian storage, and `3000H - 0004H`. |
| 10 | [MUL result and flags](images/Day%2012/Screenshot%202026-06-15%20230824.png) | Unsigned multiply result placement and `CF/OF` rule. |
| 11 | [MUL result and flags with annotation](images/Day%2012/Screenshot%202026-06-15%20230928.png) | Upper byte/word decides `CF` and `OF`; other flags are undefined. |
| 12 | [MUL byte and word examples](images/Day%2012/Screenshot%202026-06-15%20231037.png) | `MUL BH`, `MUL CX`, and `MUL BYTE PTR [BX]`. |
| 13 | [MUL examples repeated](images/Day%2012/Screenshot%202026-06-15%20231041.png) | Reinforces unsigned byte/word multiply conventions. |
| 14 | [MUL examples clear view](images/Day%2012/Screenshot%202026-06-15%20231259.png) | One-operand `MUL` and why size controls hidden registers. |
| 15 | [IMUL source rule](images/Day%2012/Screenshot%202026-06-15%20231849.png) | Signed multiply, sign extension, result placement, and `CF/OF` rule. |
| 16 | [IMUL and CBW examples](images/Day%2012/Screenshot%202026-06-15%20232308.png) | Signed byte-to-word extension and sample `IMUL` instructions. |
| 17 | [IMUL examples repeated](images/Day%2012/Screenshot%202026-06-15%20233238.png) | Same signed multiply examples, focused on `CBW`. |
| 18 | [IMUL examples final view](images/Day%2012/Screenshot%202026-06-15%20233837.png) | Signed multiplicand loading and result in `AX` or `DX:AX`. |
| 19 | [DIV quotient overflow and flags](images/Day%2012/Screenshot%202026-06-15%20234500.png) | Unsigned division quotient/remainder, type 0 interrupt, and undefined flags. |
| 20 | [IDIV signed division](images/Day%2012/Screenshot%202026-06-15%20234819.png) | Signed `DX:AX` dividend, signed quotient/remainder, and type 0 interrupt. |
| 21 | [INC examples](images/Day%2012/Screenshot%202026-06-16%20000249.png) | Increment byte/word register or memory operands. |
| 22 | [DEC instruction](images/Day%2012/Screenshot%202026-06-16%20000734.png) | Decrement byte/word destination and flag behavior without changing `CF`. |
| 23 | [DAA after BCD addition](images/Day%2012/Screenshot%202026-06-16%20000946.png) | Packed BCD correction after addition in `AL`. |
| 24 | [AAS after ASCII subtraction](images/Day%2012/Screenshot%202026-06-16%20001921.png) | ASCII digit subtraction and unpacked BCD adjustment. |
| 25 | [AAM after multiply](images/Day%2012/Screenshot%202026-06-16%20002303.png) | Adjust product of unpacked BCD digits into two decimal digits in `AX`. |
| 26 | [AAD before division](images/Day%2012/Screenshot%202026-06-16%20002501.png) | Convert unpacked BCD digits in `AH:AL` into binary in `AL`. |
| 27 | [AAD with board annotation](images/Day%2012/Screenshot%202026-06-16%20003032.png) | ASCII/BCD adjustment before division and flag behavior. |
| 28 | [OR instruction examples](images/Day%2012/Screenshot%202026-06-16%20003601.png) | Bitwise OR, destination update, and logical flag behavior. |
| 29 | [CMP instruction](images/Day%2012/Screenshot%202026-06-16%20004203.png) | Compare by subtraction without saving the result. |

## 1. MOV: Copying Data Without Changing Flags

![MOV examples and BP addressing](images/Day%2012/Screenshot%202026-06-15%20161545.png)

![MOV examples with physical-address annotation](images/Day%2012/Screenshot%202026-06-15%20171949.png)

`MOV destination, source` copies data from source to destination. It does not destroy the source and it does not affect flags.

The screenshot shows several forms:

| Instruction | Meaning | Important detail |
| --- | --- | --- |
| `MOV CX,037AH` | Copy immediate word `037AH` into `CX`. | Destination is 16-bit, so the immediate operand is a word. |
| `MOV BL,[437AH]` | Copy the byte at offset `437AH` in `DS` into `BL`. | Destination `BL` decides this is a byte memory read. |
| `MOV AX,BX` | Copy word register `BX` into word register `AX`. | Register-to-register word copy. |
| `MOV DL,[BX]` | Copy byte from memory at offset in `BX` into `DL`. | `BX` uses `DS` by default. |
| `MOV DS,BX` | Copy word from `BX` into segment register `DS`. | Segment registers are 16-bit. |
| `MOV RESULT[BP],AX` | Store `AX` into memory at offset `RESULT + BP`. | Because `BP` is used, the default segment is `SS`. |

### Direct Memory Example

For:

```asm
MOV BL,[437AH]
```

the offset is directly written in the instruction. If:

```text
DS = 2000H
offset = 437AH
```

then the physical address is:

```text
physical address = DS x 10H + offset
                 = 2000H x 10H + 437AH
                 = 20000H + 437AH
                 = 2437AH
```

The instruction copies one byte from physical address `2437AH` into `BL`.

### `BP` Uses `SS` By Default

For:

```asm
MOV RESULT[BP],AX
```

the effective offset is:

```text
EA = offset RESULT + BP
```

Because `BP` appears in the effective address, the default segment is `SS`, not `DS`. The slide says:

```text
Physical address = EA + SS
```

The exact 8086 formula is:

```text
physical address = SS x 10H + EA
```

Since `AX` is a word, the 8086 stores two bytes. The 8086 is little-endian:

| Memory location | Stored byte |
| --- | --- |
| `SS:EA` | `AL` |
| `SS:EA + 1` | `AH` |

This low-byte-first rule matters for stack, word arrays, `MOV`, `PUSH`, `CALL`, and interrupt vectors.

## 2. XCHG and LEA

![XCHG and LEA](images/Day%2012/Screenshot%202026-06-15%20162837.png)

### `XCHG`

`XCHG destination, source` exchanges two operands of the same size.

Examples:

| Instruction | Meaning |
| --- | --- |
| `XCHG AX,DX` | Exchange the 16-bit word in `AX` with the 16-bit word in `DX`. |
| `XCHG BL,CH` | Exchange the byte in `BL` with the byte in `CH`. |
| `XCHG AL,PRICES[BX]` | Exchange `AL` with the byte at memory offset `PRICES + BX`. |

Rules:

- both operands must have the same size;
- memory-to-memory exchange is not allowed;
- at least one operand must be a register;
- `XCHG` does not affect flags.

For:

```asm
XCHG AL,PRICES[BX]
```

the effective offset is:

```text
EA = offset PRICES + BX
```

Because `BX` is used, the default segment is `DS`. The memory byte at `DS:EA` is exchanged with `AL`.

### `LEA`

`LEA` means **Load Effective Address**.

General form:

```asm
LEA register, memory_operand
```

`LEA` calculates the effective offset of the source memory expression and loads that offset into the destination register. It does not read the memory data.

Example:

```asm
LEA SI,PRICES[BX]
```

Meaning:

```text
SI <- offset PRICES + BX
```

This is different from:

```asm
MOV SI,PRICES[BX]
```

That `MOV` would read the word stored in memory. `LEA` only loads the address calculation result.

Important points:

- destination must be a 16-bit general-purpose register;
- source must be a memory addressing expression;
- no memory contents are fetched;
- no flags are affected;
- segment value is not loaded, only the offset is loaded.

`LEA` is useful for pointer setup, array address calculation, and avoiding separate `ADD` instructions when building an offset.

## 3. IN: Port Input to `AL` or `AX`

![IN instruction, immediate and DX port forms](images/Day%2012/Screenshot%202026-06-15%20170903.png)

![IN instruction with annotations](images/Day%2012/Screenshot%202026-06-15%20171252.png)

`IN` reads data from an I/O port into the accumulator.

The destination is fixed by operand size:

| Instruction form | Data size | Destination |
| --- | --- | --- |
| `IN AL,port` | 8-bit input | `AL` |
| `IN AX,port` | 16-bit input | `AX` |

Examples from the screenshots:

```asm
IN AL,0C8H
IN AX,34H
```

Mean:

| Instruction | Meaning |
| --- | --- |
| `IN AL,0C8H` | Read one byte from port `0C8H` into `AL`. |
| `IN AX,34H` | Read one word from port `34H` into `AX`. |

There are two port-addressing forms:

| Form | Port address range | Example |
| --- | ---: | --- |
| Immediate port | `00H` to `FFH` | `IN AL,0C8H` |
| Variable port through `DX` | `0000H` to `FFFFH` | `MOV DX,03F8H` then `IN AL,DX` |

For the variable-port form, the port address must already be in `DX`:

```asm
MOV DX,03F8H
IN  AL,DX
```

Since `DX` is 16-bit, up to:

```text
2^16 = 65536
```

I/O port addresses are possible in that mode.

Important points:

- `IN` does not affect flags;
- only `AL` or `AX` can receive the data;
- memory is not involved because this is I/O-port space;
- immediate port numbers are only 8-bit, but `DX` can hold a 16-bit port number.

## 4. ADD, ADC, SUB, and SBB

![ADD, ADC, SUB, SBB introduction](images/Day%2012/Screenshot%202026-06-15%20172546.png)

![SUB and SBB flag effects](images/Day%2012/Screenshot%202026-06-15%20223644.png)

Arithmetic instructions operate on byte or word operands. The destination receives the result.

### Addition

Examples from the screenshot:

```asm
ADD DX,[SI]
ADC AL,PRICES[BX]
ADD AL,PRICES[BX]
```

Meaning:

| Instruction | Operation |
| --- | --- |
| `ADD DX,[SI]` | `DX <- DX + word at DS:SI` |
| `ADC AL,PRICES[BX]` | `AL <- AL + byte at DS:(PRICES + BX) + CF` |
| `ADD AL,PRICES[BX]` | `AL <- AL + byte at DS:(PRICES + BX)` |

`ADC` means **add with carry**. It is used for multi-byte or multi-word addition. The carry from a lower part can be included in the higher part.

Example idea:

```asm
ADD AX,low_word
ADC DX,high_word
```

Here `ADC` includes the carry generated by the lower-word addition.

### Subtraction

General forms:

```asm
SUB destination,source
SBB destination,source
```

Operations:

```text
SUB: destination <- destination - source
SBB: destination <- destination - source - CF
```

Examples from the screenshot:

| Instruction | Operation |
| --- | --- |
| `SUB CX,BX` | `CX <- CX - BX` |
| `SBB CH,AL` | `CH <- CH - AL - CF` |
| `SUB AX,3427H` | `AX <- AX - 3427H` |
| `SBB BX,[3427H]` | `BX <- BX - word at DS:3427H - CF` |

`SBB` means **subtract with borrow**. In subtraction, the carry flag `CF` represents a borrow.

Arithmetic flag effects for `ADD`, `ADC`, `SUB`, `SBB`, and `CMP`:

| Flag | Meaning in these instructions |
| --- | --- |
| `CF` | Unsigned carry or borrow. |
| `OF` | Signed overflow. |
| `SF` | Sign bit of result. |
| `ZF` | Result is zero. |
| `PF` | Even parity of low byte of result. |
| `AF` | Carry or borrow between bit 3 and bit 4. |

Do not use only one flag for all comparisons:

- unsigned arithmetic depends mainly on `CF` and `ZF`;
- signed arithmetic depends mainly on `SF`, `OF`, and `ZF`.

## 5. Array Addressing Examples: `DB` Versus `DW`

![Byte array subtraction example](images/Day%2012/Screenshot%202026-06-15%20225034.png)

### Byte Array: `DB`

The screenshot gives:

```asm
PRICES DB 10H,20H,30H,40H
MOV BX,0002H
SUB PRICES[BX],04H
```

If `PRICES` starts at offset `1000H`, the bytes are placed like this:

| Offset | Value |
| --- | --- |
| `1000H` | `10H` |
| `1001H` | `20H` |
| `1002H` | `30H` |
| `1003H` | `40H` |

The effective offset is:

```text
EA = offset PRICES + BX
EA = 1000H + 0002H
EA = 1002H
```

So:

```asm
SUB PRICES[BX],04H
```

subtracts `04H` from the byte at `1002H`:

```text
30H - 04H = 2CH
```

The new byte stored at `PRICES + 2` is `2CH`.

Because the destination is memory, the result is written back to memory. The immediate source `04H` is not changed.

![Word array subtraction example](images/Day%2012/Screenshot%202026-06-15%20230050.png)

### Word Array: `DW`

The screenshot gives:

```asm
PRICES DW 1000H,2000H,3000H,4000H
MOV BX,0004H
SUB PRICES[BX],0004H
```

`DW` means each element is one word, so each element occupies two bytes. If `PRICES` starts at offset `1000H`, the word elements begin at:

| Word element | Starting offset | Value |
| --- | --- | --- |
| 1st | `1000H` | `1000H` |
| 2nd | `1002H` | `2000H` |
| 3rd | `1004H` | `3000H` |
| 4th | `1006H` | `4000H` |

With:

```asm
MOV BX,0004H
```

the effective offset is:

```text
EA = offset PRICES + BX
EA = 1000H + 0004H
EA = 1004H
```

So the selected word is the third word, `3000H`.

The subtraction is:

```text
3000H - 0004H = 2FFCH
```

The board's final line appears to write `1002H` near the selected value, but with `BX = 0004H` the correct selected offset is `1004H`. The table on the right side of the screenshot also shows the correct word spacing: `1000H`, `1002H`, `1004H`, `1006H`.

### Little-Endian Detail For `DW`

The word `3000H` stored at offset `1004H` is physically stored as:

| Offset | Byte |
| --- | --- |
| `1004H` | `00H` |
| `1005H` | `30H` |

After the result `2FFCH`, memory becomes:

| Offset | Byte |
| --- | --- |
| `1004H` | `FCH` |
| `1005H` | `2FH` |

That low-byte-first rule is the same rule used by `PUSH`, `CALL`, and interrupt-vector entries.

## 6. Unsigned Multiplication: `MUL`

![MUL result and flags](images/Day%2012/Screenshot%202026-06-15%20230824.png)

![MUL result and flags with annotation](images/Day%2012/Screenshot%202026-06-15%20230928.png)

`MUL source` performs unsigned multiplication. It has only one explicit operand. The other operand is implicit.

| Source size | Implicit multiplicand | Product destination |
| --- | --- | --- |
| 8-bit source | `AL` | `AX` |
| 16-bit source | `AX` | `DX:AX` |

So:

```asm
MUL BH
```

means:

```text
AX <- AL x BH
```

And:

```asm
MUL CX
```

means:

```text
DX:AX <- AX x CX
```

For a word product in `DX:AX`:

| Register | Holds |
| --- | --- |
| `DX` | High word of product |
| `AX` | Low word of product |

### `MUL` Flags

For unsigned multiplication:

| Condition | `CF` | `OF` |
| --- | --- | --- |
| Upper half of product is zero | `0` | `0` |
| Upper half of product is nonzero | `1` | `1` |

The upper half means:

| Multiply size | Upper half checked |
| --- | --- |
| `AL x r/m8 -> AX` | `AH` |
| `AX x r/m16 -> DX:AX` | `DX` |

After `MUL`, these flags are undefined:

```text
AF, PF, SF, ZF
```

So do not write a program that depends on `ZF` or `SF` immediately after `MUL`.

![MUL byte and word examples](images/Day%2012/Screenshot%202026-06-15%20231037.png)

![MUL examples repeated](images/Day%2012/Screenshot%202026-06-15%20231041.png)

![MUL examples clear view](images/Day%2012/Screenshot%202026-06-15%20231259.png)

Examples from the screenshots:

| Instruction | Meaning |
| --- | --- |
| `MUL BH` | Multiply `AL` by `BH`; result in `AX`. |
| `MUL CX` | Multiply `AX` by `CX`; high word in `DX`, low word in `AX`. |
| `MUL BYTE PTR [BX]` | Multiply `AL` by byte at `DS:BX`; result in `AX`. |

`BYTE PTR` is needed when the assembler cannot infer memory operand size from a register.

### Byte With Word Warning

The slide warns that if you want to multiply a byte with a word, you cannot simply mix sizes. `MUL` does not have a two-explicit-operand byte-by-word form in basic 8086 syntax.

For unsigned data:

```asm
MOV AL,byte_value
MOV AH,00H
MUL word_value
```

or:

```asm
MOV AL,byte_value
XOR AH,AH
MUL word_value
```

This zero-extends `AL` into `AX`.

Do not use `CBW` for unsigned zero extension. `CBW` copies the sign bit of `AL` into every bit of `AH`. That is correct for signed values, but wrong for unsigned values where the high byte must become `00H`.

## 7. Signed Multiplication: `IMUL`

![IMUL source rule](images/Day%2012/Screenshot%202026-06-15%20231849.png)

`IMUL source` performs signed multiplication in the original one-operand 8086 form.

| Source size | Implicit signed multiplicand | Product destination |
| --- | --- | --- |
| 8-bit source | `AL` | `AX` |
| 16-bit source | `AX` | `DX:AX` |

The source can be a register or memory operand.

### `IMUL` Flags

For signed multiplication, `CF` and `OF` show whether the product needs the full destination size.

| Condition | `CF` | `OF` |
| --- | --- | --- |
| Upper half is only sign extension of lower half | `0` | `0` |
| Upper half contains significant product bits | `1` | `1` |

For byte multiply:

```text
AL x r/m8 -> AX
```

`CF` and `OF` clear if `AH` is just the sign extension of `AL`.

For word multiply:

```text
AX x r/m16 -> DX:AX
```

`CF` and `OF` clear if `DX` is just the sign extension of `AX`.

After `IMUL`, these flags are undefined:

```text
AF, PF, SF, ZF
```

![IMUL and CBW examples](images/Day%2012/Screenshot%202026-06-15%20232308.png)

![IMUL examples repeated](images/Day%2012/Screenshot%202026-06-15%20233238.png)

![IMUL examples final view](images/Day%2012/Screenshot%202026-06-15%20233837.png)

Examples from the screenshots:

| Instruction | Meaning |
| --- | --- |
| `IMUL BH` | Multiply signed byte in `AL` by signed byte in `BH`; result in `AX`. |
| `IMUL AX` | Multiply signed `AX` by signed `AX`; result in `DX:AX`. |
| `MOV CX,MULTIPLIER` | Load signed word from memory into `CX`. |
| `MOV AL,MULTIPLICAND` | Load signed byte from memory into `AL`. |

### `CBW` and `CWD`

For signed widening:

| Instruction | Meaning |
| --- | --- |
| `CBW` | Convert byte to word: sign-extend `AL` into `AX`. |
| `CWD` | Convert word to doubleword: sign-extend `AX` into `DX:AX`. |

If `AL = F6H`, that is `-10` as a signed byte. After:

```asm
CBW
```

the value becomes:

```text
AX = FFF6H
```

That is still `-10`, now as a signed word.

This is why the slide says `CBW` is correct for signed byte-to-word conversion, but not for unsigned byte-to-word conversion.

## 8. Division: `DIV` and `IDIV`

![DIV quotient overflow and flags](images/Day%2012/Screenshot%202026-06-15%20234500.png)

### Unsigned `DIV`

`DIV source` performs unsigned division. The dividend is implicit and depends on source size.

| Source size | Dividend | Quotient | Remainder |
| --- | --- | --- | --- |
| 8-bit source | `AX` | `AL` | `AH` |
| 16-bit source | `DX:AX` | `AX` | `DX` |

For word division:

```asm
DIV CX
```

means:

```text
AX <- quotient of DX:AX / CX
DX <- remainder of DX:AX / CX
```

The screenshot notes:

```text
After the division, AX will contain the 16-bit quotient
and DX will contain the 16-bit remainder.
```

That is the 16-bit divisor form.

### Type 0 Interrupt Conditions

The 8086 generates a type 0 interrupt if:

- divisor is zero;
- quotient is too large for the destination register.

For unsigned division:

| Division form | Quotient must fit in |
| --- | --- |
| `AX / r/m8` | `AL`, so maximum `FFH` |
| `DX:AX / r/m16` | `AX`, so maximum `FFFFH` |

After `DIV`, all flags are undefined. Do not test flags after `DIV` to decide whether the quotient is zero or negative.

![IDIV signed division](images/Day%2012/Screenshot%202026-06-15%20234819.png)

### Signed `IDIV`

`IDIV source` performs signed division.

| Source size | Signed dividend | Signed quotient | Signed remainder |
| --- | --- | --- | --- |
| 8-bit source | `AX` | `AL` | `AH` |
| 16-bit source | `DX:AX` | `AX` | `DX` |

For 16-bit signed division:

```text
DX:AX / r/m16 -> AX quotient, DX remainder
```

The remainder has the same sign as the dividend.

Before signed division, prepare the dividend correctly:

| Desired dividend size | Preparation |
| --- | --- |
| Signed byte in `AL` divided by byte | Use `CBW` to form signed `AX`. |
| Signed word in `AX` divided by word | Use `CWD` to form signed `DX:AX`. |

For a 16-bit signed quotient, the result must fit in:

```text
-32768 to +32767
```

The slide gives the positive limit `+32767 (7FFFH)`. The exact lower limit for a signed 16-bit value is:

```text
-32768 = 8000H
```

The 8086 generates type 0 if the signed quotient is outside that range.

After `IDIV`, all flags are undefined.

## 9. INC and DEC

![INC examples](images/Day%2012/Screenshot%202026-06-16%20000249.png)

`INC destination` adds 1 to a byte or word destination.

Examples from the screenshot:

| Instruction | Meaning |
| --- | --- |
| `INC BL` | `BL <- BL + 1` |
| `INC CX` | `CX <- CX + 1` |
| `INC BYTE PTR [BX]` | Increment the byte at `DS:BX`. |
| `INC WORD PTR [BX]` | Increment the word at `DS:BX`. |

For memory operands, `BYTE PTR` or `WORD PTR` may be needed so the assembler knows the operand size.

![DEC instruction](images/Day%2012/Screenshot%202026-06-16%20000734.png)

`DEC destination` subtracts 1 from a byte or word destination.

Examples:

| Instruction | Meaning |
| --- | --- |
| `DEC CL` | `CL <- CL - 1` |
| `DEC BP` | `BP <- BP - 1` |

Flag behavior for both `INC` and `DEC`:

| Flag | Behavior |
| --- | --- |
| `AF` | Updated. |
| `OF` | Updated. |
| `SF` | Updated. |
| `PF` | Updated. |
| `ZF` | Updated. |
| `CF` | Not affected. |

The `CF` rule is important. If:

```asm
DEC CL
```

changes `CL` from `00H` to `FFH`, `CF` is still not changed. The wraparound happens, but `DEC` does not report it through carry/borrow.

This is different from:

```asm
SUB CL,01H
```

`SUB` would update `CF`.

## 10. Decimal and ASCII Adjust Instructions

These instructions exist because binary arithmetic and decimal digit arithmetic are not the same thing.

Packed BCD:

```text
One byte stores two decimal digits.
Example: 45 decimal as packed BCD = 45H
```

Unpacked BCD:

```text
One byte stores one decimal digit in the low nibble.
Example: digit 5 = 05H
```

ASCII digit:

```text
One byte stores the character code.
Example: character '5' = 35H
```

### `DAA`: Decimal Adjust After BCD Addition

![DAA after BCD addition](images/Day%2012/Screenshot%202026-06-16%20000946.png)

`DAA` adjusts the result in `AL` after adding two packed BCD numbers.

It does not perform addition by itself. The addition must already have placed its result in `AL`.

Example idea:

```asm
MOV AL,45H
ADD AL,38H
DAA
```

Binary addition gives:

```text
45H + 38H = 7DH
```

But `7DH` is not valid packed BCD because `D` is not a decimal digit. `DAA` corrects it to valid packed BCD:

```text
45 + 38 = 83 decimal -> 83H packed BCD
```

Correction logic:

| Condition | Correction |
| --- | --- |
| Low nibble of `AL` > 9, or `AF = 1` | Add `06H` to `AL`. |
| High nibble of `AL` > 9, or `CF = 1` after addition/correction | Add `60H` to `AL`. |

Flag notes:

- `CF` and `AF` show decimal correction/carry behavior;
- `SF`, `ZF`, and `PF` are updated from the adjusted result;
- `OF` is undefined.

### `AAS`: ASCII Adjust After Subtraction

![AAS after ASCII subtraction](images/Day%2012/Screenshot%202026-06-16%20001921.png)

ASCII codes for digits:

| Digit | ASCII code |
| --- | --- |
| `0` | `30H` |
| `1` | `31H` |
| `2` | `32H` |
| ... | ... |
| `9` | `39H` |

`AAS` is used after subtracting ASCII decimal digits. It adjusts the result into an unpacked BCD digit form.

Example idea:

```asm
MOV AL,35H   ; ASCII '5'
SUB AL,32H   ; subtract ASCII '2'
AAS
```

The adjusted result represents digit `3` as unpacked BCD:

```text
AL = 03H
```

Important: after `AAS`, the result is not automatically ASCII. If the digit must be displayed as ASCII, convert it back:

```asm
OR AL,30H
```

or:

```asm
ADD AL,30H
```

Flag notes for `AAS`:

- `AF` and `CF` show whether adjustment/borrow was needed;
- `OF`, `SF`, `ZF`, and `PF` are undefined.

### `AAM`: ASCII Adjust After Multiply

![AAM after multiply](images/Day%2012/Screenshot%202026-06-16%20002303.png)

Before multiplying ASCII digits, remove the ASCII upper nibble. For example:

```text
'7' = 37H
unpacked BCD 7 = 07H
```

The upper nibble can be masked:

```asm
AND AL,0FH
```

After multiplying two unpacked decimal digits, the binary product is in `AL`. `AAM` adjusts that product into two unpacked decimal digits in `AX`.

Default 8086 `AAM` behavior:

```text
AH <- AL / 10
AL <- AL mod 10
```

Example:

```text
7 x 8 = 56
```

After multiplication:

```text
AL = 38H   ; 56 decimal in binary
```

After:

```asm
AAM
```

the result is:

```text
AH = 05H
AL = 06H
```

So `AX` holds two unpacked BCD digits: `05H` and `06H`.

Flag notes from the screenshot:

- `PF`, `SF`, and `ZF` are updated;
- `AF`, `CF`, and `OF` are undefined.

### `AAD`: ASCII Adjust Before Division

![AAD before division](images/Day%2012/Screenshot%202026-06-16%20002501.png)

![AAD with board annotation](images/Day%2012/Screenshot%202026-06-16%20003032.png)

`AAD` prepares two unpacked BCD digits in `AH` and `AL` for division.

Default 8086 behavior:

```text
AL <- AH x 10 + AL
AH <- 00H
```

Example:

```text
AH = 04H
AL = 02H
```

This represents decimal `42` as two unpacked digits. After:

```asm
AAD
```

the result is:

```text
AL = 2AH   ; 42 decimal
AH = 00H
```

Then `DIV` can divide this binary value by an unpacked BCD byte.

After the BCD division:

| Register | Meaning |
| --- | --- |
| `AL` | Unpacked BCD quotient |
| `AH` | Unpacked BCD remainder |

Flag notes from the screenshot:

- `PF`, `SF`, and `ZF` are updated;
- `AF`, `CF`, and `OF` are undefined.

### Adjust Instruction Summary

| Instruction | Use it after/before | Operand location | Result idea |
| --- | --- | --- | --- |
| `DAA` | After packed BCD addition | `AL` | Corrects `AL` to packed BCD. |
| `AAS` | After ASCII/unpacked digit subtraction | `AL`, may adjust `AH` | Leaves unpacked BCD digit in `AL`. |
| `AAM` | After multiplying two unpacked digits | Product in `AL` | Tens digit in `AH`, ones digit in `AL`. |
| `AAD` | Before dividing unpacked decimal digits | Digits in `AH:AL` | Binary number in `AL`, `AH = 0`. |

## 11. Logical OR

![OR instruction examples](images/Day%2012/Screenshot%202026-06-16%20003601.png)

`OR destination, source` performs bitwise OR.

Operation:

```text
destination <- destination OR source
```

The source is not changed.

Examples from the screenshot:

| Instruction | Meaning |
| --- | --- |
| `OR AH,CL` | `AH <- AH OR CL`; `CL` unchanged. |
| `OR BP,SI` | `BP <- BP OR SI`; `SI` unchanged. |
| `OR SI,BP` | `SI <- SI OR BP`; `BP` unchanged. |
| `OR BL,80H` | Set bit 7 of `BL` to 1. |

For:

```asm
OR BL,80H
```

binary `80H` is:

```text
1000 0000B
```

So bit 7 of `BL` becomes 1, while other bits stay as they were.

Rules:

- source and destination must be the same size;
- source and destination cannot both be memory operands;
- destination can be register or memory;
- source can be register, memory, or immediate, depending on the form.

Flag behavior:

| Flag | Behavior after `OR` |
| --- | --- |
| `CF` | Cleared to `0`. |
| `OF` | Cleared to `0`. |
| `SF` | Updated from result sign bit. |
| `ZF` | Updated if result is zero. |
| `PF` | Updated from parity of low byte of result. |
| `AF` | Undefined. |

Use `OR` when you want to set selected bits to 1 without disturbing the other bits.

Common bit-operation pattern:

| Goal | Instruction type |
| --- | --- |
| Set bits | `OR` with mask bits set to 1. |
| Clear bits | `AND` with mask bits set to 0. |
| Toggle bits | `XOR` with mask bits set to 1. |
| Test bits without storing result | `TEST`. |

## 12. CMP: Compare Without Storing Result

![CMP instruction](images/Day%2012/Screenshot%202026-06-16%20004203.png)

`CMP destination, source` compares two operands by internally subtracting:

```text
destination - source
```

The subtraction result is not stored. Only flags are updated.

Allowed operand ideas from the screenshot:

| Operand | Can be |
| --- | --- |
| Source | Immediate, register, or memory. |
| Destination | Register or memory. |

Restriction:

```text
source and destination cannot both be memory locations
```

Example:

```asm
CMP AX,BX
```

means:

```text
Set flags as if AX - BX was performed.
AX is not changed.
BX is not changed.
```

### Reading Flags After `CMP`

For equality:

| Condition | Flag result |
| --- | --- |
| `destination == source` | `ZF = 1` |
| `destination != source` | `ZF = 0` |

For unsigned comparison:

| Condition after `CMP dest,src` | Flag meaning |
| --- | --- |
| `dest < src` | `CF = 1` |
| `dest == src` | `ZF = 1` |
| `dest > src` | `CF = 0` and `ZF = 0` |

For signed comparison:

| Condition after `CMP dest,src` | Flag meaning |
| --- | --- |
| `dest < src` | `SF != OF` |
| `dest >= src` | `SF = OF` |
| `dest > src` | `ZF = 0` and `SF = OF` |
| `dest <= src` | `ZF = 1` or `SF != OF` |

This is why 8086 has different conditional jumps for signed and unsigned comparisons. The same `CMP` instruction can be followed by different jumps depending on whether the numbers are meant to be signed or unsigned.

Examples:

| Meaning | Unsigned jump | Signed jump |
| --- | --- | --- |
| Equal | `JE` / `JZ` | `JE` / `JZ` |
| Not equal | `JNE` / `JNZ` | `JNE` / `JNZ` |
| Greater than | `JA` | `JG` |
| Less than | `JB` | `JL` |
| Greater or equal | `JAE` | `JGE` |
| Less or equal | `JBE` | `JLE` |

## Deep Revision Tables

### Hidden Register Table

Many 8086 instructions use registers that are not written as explicit operands.

| Instruction | Hidden register rule |
| --- | --- |
| `IN AL,port` | Input byte always goes to `AL`. |
| `IN AX,port` | Input word always goes to `AX`. |
| `MUL r/m8` | `AL` is implicit; product goes to `AX`. |
| `MUL r/m16` | `AX` is implicit; product goes to `DX:AX`. |
| `IMUL r/m8` | signed `AL` is implicit; product goes to `AX`. |
| `IMUL r/m16` | signed `AX` is implicit; product goes to `DX:AX`. |
| `DIV r/m8` | dividend is `AX`; quotient `AL`, remainder `AH`. |
| `DIV r/m16` | dividend is `DX:AX`; quotient `AX`, remainder `DX`. |
| `IDIV r/m8` | signed dividend is `AX`; quotient `AL`, remainder `AH`. |
| `IDIV r/m16` | signed dividend is `DX:AX`; quotient `AX`, remainder `DX`. |
| `DAA` | adjusts `AL`. |
| `AAS` | adjusts `AL` and may change `AH`. |
| `AAM` | converts product in `AL` into `AH:AL`. |
| `AAD` | converts digits in `AH:AL` into binary in `AL`. |

### Segment Default Table

| Effective address uses | Default segment |
| --- | --- |
| `BX`, `SI`, `DI`, displacement-only | `DS` |
| `BP` | `SS` |
| Stack through `SP`/`BP` | `SS` |
| Instruction fetch | `CS:IP` |
| String source | usually `DS:SI` |
| String destination | `ES:DI` |

The physical address formula is always:

```text
physical address = segment x 10H + offset
```

### Flag Effect Summary

| Instruction group | Flag behavior |
| --- | --- |
| `MOV`, `XCHG`, `LEA`, `IN` | No flags affected. |
| `ADD`, `ADC`, `SUB`, `SBB`, `CMP` | `AF`, `CF`, `OF`, `PF`, `SF`, `ZF` updated. |
| `INC`, `DEC` | `AF`, `OF`, `PF`, `SF`, `ZF` updated; `CF` not affected. |
| `MUL` | `CF` and `OF` defined by upper half of unsigned product; `AF`, `PF`, `SF`, `ZF` undefined. |
| `IMUL` | `CF` and `OF` defined by signed product fit; `AF`, `PF`, `SF`, `ZF` undefined. |
| `DIV`, `IDIV` | All flags undefined. |
| `DAA` | `CF`/`AF` reflect decimal correction; `SF`, `ZF`, `PF` updated; `OF` undefined. |
| `AAS` | `CF`/`AF` reflect adjustment; `OF`, `SF`, `ZF`, `PF` undefined. |
| `AAM`, `AAD` | `PF`, `SF`, `ZF` updated; `AF`, `CF`, `OF` undefined. |
| `OR` | `CF = 0`, `OF = 0`, `SF/ZF/PF` updated, `AF` undefined. |

## Points To Remember

- `MOV` copies; it does not change flags.
- Direct memory addressing such as `[437AH]` normally uses `DS`.
- Addressing with `BP` normally uses `SS`.
- Exact physical address formula is `segment x 10H + offset`.
- 8086 stores words little-endian: low byte first, high byte second.
- `XCHG` swaps equal-size operands and cannot exchange memory with memory.
- `LEA` loads an offset address, not the data at that address.
- `IN` can read only into `AL` or `AX`.
- Immediate port form is 8-bit; `DX` port form can address `0000H-FFFFH`.
- `ADC` includes `CF`; `SBB` subtracts `CF` as a borrow.
- `DB` arrays advance one byte per element; `DW` arrays advance two bytes per element.
- `MUL r/m8` uses `AL` and produces `AX`.
- `MUL r/m16` uses `AX` and produces `DX:AX`.
- `IMUL` uses signed interpretation and sign-extension rules.
- `CBW` is for signed byte-to-word conversion, not unsigned zero extension.
- `DIV` and `IDIV` generate type 0 interrupt on divide by zero or quotient overflow.
- After `DIV` or `IDIV`, flags are undefined.
- `INC` and `DEC` do not affect `CF`.
- `DAA` is for packed BCD addition results in `AL`.
- `AAS`, `AAM`, and `AAD` are for ASCII/unpacked decimal adjustment workflows.
- `OR` is useful for setting bits.
- `CMP` subtracts only for flags; it does not store the result.
- Signed and unsigned comparisons use different jumps after the same `CMP`.

## Sources

[S1] Intel Corporation, [The 8086 Family User's Manual, October 1979](https://www.ardent-tool.com/CPU/docs/Intel/808x/manuals/9800722-03.pdf). Used for 8086 instruction behavior, implicit operands, addressing, flag effects, multiplication/division, and decimal-adjust instructions.

[S2] Intel Corporation, [iAPX 86,88 User's Manual, August 1981](https://www.dosdays.co.uk/media/intel/1981_iAPX_86_88_Users_Manual.pdf). Used for 8086 programming model, segment-offset addressing, port I/O, arithmetic/logical instructions, and instruction-set details.
