# Day 13: 8086 Rotate, String, Control Transfer, Programs, and I/O

Day 13 continues the 8086 instruction-set sequence. The screenshots cover rotate and shift instructions, string manipulation, jumps, calls, returns, process-control instructions, small assembly programs, and the difference between isolated I/O and memory-mapped I/O.

Use these checks while studying this day:

```text
1. For rotate/shift instructions, decide what enters the vacant bit and what goes into CF.
2. For string instructions, track DS:SI, ES:DI, CX, and DF.
3. For jumps/calls/returns, decide near versus far and direct versus indirect.
4. For programs, trace registers, memory stores, flags, and loop count.
5. For I/O, separate isolated I/O address space from memory address space.
```

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [RCL operation](images/Day%2013/Screenshot%202026-06-16%20150814.png) | Rotate left through carry. |
| 2 | [RCL count and flags](images/Day%2013/Screenshot%202026-06-16%20151006.png) | Destination, count operand, `CL`, `CF`, and `OF`. |
| 3 | [RCL clear continuation](images/Day%2013/Screenshot%202026-06-16%20151018.png) | Same `RCL` rules in a clearer view. |
| 4 | [ROL operation](images/Day%2013/Screenshot%202026-06-16%20151920.png) | Circular rotate left without carry entering the operand. |
| 5 | [ROL examples with pointer](images/Day%2013/Screenshot%202026-06-16%20152435.png) | `ROL AX,1`, `ROL BL,CL`, and memory rotate. |
| 6 | [ROL examples clear view](images/Day%2013/Screenshot%202026-06-16%20152443.png) | Example forms for register and memory operands. |
| 7 | [ROR operation](images/Day%2013/Screenshot%202026-06-16%20152706.png) | Circular rotate right and `CF` update. |
| 8 | [SAL and SHL](images/Day%2013/Screenshot%202026-06-16%20153019.png) | Shift left with zero entering the low bit. |
| 9 | [SAR](images/Day%2013/Screenshot%202026-06-16%20153217.png) | Arithmetic shift right with sign-bit preservation. |
| 10 | [MOVS string instructions](images/Day%2013/Screenshot%202026-06-16%20153317.png) | `MOVS`, `MOVSB`, and `MOVSW`. |
| 11 | [MOVS direction flag behavior](images/Day%2013/Screenshot%202026-06-16%20153457.png) | `SI` and `DI` increment/decrement by byte or word size. |
| 12 | [CMPS string instructions](images/Day%2013/Screenshot%202026-06-16%20154003.png) | Compare string byte/word and update flags. |
| 13 | [CMPS continuation](images/Day%2013/Screenshot%202026-06-16%20154345.png) | `DF`, segment defaults, and repeat prefixes. |
| 14 | [JMP introduction](images/Day%2013/Screenshot%202026-06-16%20154506.png) | Unconditional control transfer. |
| 15 | [Direct JMP example](images/Day%2013/Screenshot%202026-06-16%20155546.png) | `JMP CONTINUE`, near and far direct jumps. |
| 16 | [Indirect near JMP through register](images/Day%2013/Screenshot%202026-06-16%20155645.png) | `JMP BX` replaces `IP` with a register value. |
| 17 | [Indirect memory JMP](images/Day%2013/Screenshot%202026-06-16%20155735.png) | `JMP WORD PTR [BX]` and `JMP DWORD PTR [SI]`. |
| 18 | [JAE JNB JNC](images/Day%2013/Screenshot%202026-06-16%20160110.png) | Unsigned jump when `CF = 0`. |
| 19 | [JNB example continuation](images/Day%2013/Screenshot%202026-06-16%20160429.png) | Fall-through and jump path after compare. |
| 20 | [JB JC JNAE](images/Day%2013/Screenshot%202026-06-16%20160555.png) | Unsigned jump when `CF = 1`. |
| 21 | [JBE JNA](images/Day%2013/Screenshot%202026-06-16%20162819.png) | Unsigned jump when `CF = 1` or `ZF = 1`. |
| 22 | [CALL introduction](images/Day%2013/Screenshot%202026-06-16%20163148.png) | Near and far procedure call concept. |
| 23 | [Far CALL continuation](images/Day%2013/Screenshot%202026-06-16%20164419.png) | Loading `CS:IP` and return address restore. |
| 24 | [CALL MULT and CALL BX](images/Day%2013/Screenshot%202026-06-16%20164504.png) | Direct near call and indirect near call. |
| 25 | [CALL near examples clear view](images/Day%2013/Screenshot%202026-06-16%20171134.png) | Same direct and register-indirect near call forms. |
| 26 | [CALL memory and far procedure](images/Day%2013/Screenshot%202026-06-16%20171341.png) | Indirect near call and direct far call. |
| 27 | [RET introduction](images/Day%2013/Screenshot%202026-06-16%20172029.png) | Near return pops `IP`. |
| 28 | [RET far and RET immediate](images/Day%2013/Screenshot%202026-06-16%20172252.png) | Far return and stack cleanup. |
| 29 | [STC CLC CMC](images/Day%2013/Screenshot%202026-06-16%20172453.png) | Carry-flag process-control instructions. |
| 30 | [STD CLD](images/Day%2013/Screenshot%202026-06-16%20172522.png) | Direction-flag control for string instructions. |
| 31 | [CLI and HLT](images/Day%2013/Screenshot%202026-06-16%20172934.png) | Interrupt flag clear and halt state. |
| 32 | [NOP](images/Day%2013/Screenshot%202026-06-16%20173233.png) | No operation, delay, and placeholder use. |
| 33 | [ESC](images/Day%2013/Screenshot%202026-06-16%20173256.png) | Coprocessor escape instruction. |
| 34 | [Addition and subtraction programs](images/Day%2013/Screenshot%202026-06-16%20173621.png) | Store result of two-number arithmetic. |
| 35 | [Multiplication and division programs](images/Day%2013/Screenshot%202026-06-16%20173807.png) | `MUL`, `DIV`, and result storage. |
| 36 | [Greatest of two and average of numbers](images/Day%2013/Screenshot%202026-06-16%20173829.png) | Compare/store maximum and average an array. |
| 37 | [Greatest and average clear view](images/Day%2013/Screenshot%202026-06-16%20173844.png) | Same programs with more complete code visibility. |
| 38 | [Greatest and average repeated view](images/Day%2013/Screenshot%202026-06-16%20173857.png) | Reinforces branch and loop logic. |
| 39 | [Sum and factorial programs](images/Day%2013/Screenshot%202026-06-16%20174255.png) | Sum `N` numbers and calculate factorial. |
| 40 | [Data transfer quiz](images/Day%2013/Screenshot%202026-06-16%20174625.png) | Identify which instruction is not data transfer. |
| 41 | [I/O address space](images/Day%2013/Screenshot%202026-06-16%20210529.png) | 64K I/O address space, ports, isolated and memory-mapped I/O. |
| 42 | [Advantages of isolated I/O](images/Day%2013/Screenshot%202026-06-16%20211643.png) | Why separate I/O address space is useful. |
| 43 | [Isolated I/O port diagram](images/Day%2013/Screenshot%202026-06-16%20212537.png) | Port-number range and page idea. |
| 44 | [Memory-mapped I/O advantages and disadvantage](images/Day%2013/Screenshot%202026-06-16%20214257.png) | Memory instructions for I/O and memory-space loss. |
| 45 | [Isolated versus memory-mapped I/O](images/Day%2013/Screenshot%202026-06-16%20220122.png) | Side-by-side comparison table. |
| 46 | [Minimum mode interface](images/Day%2013/Screenshot%202026-06-16%20220810.png) | 8086 minimum-mode I/O interface overview. |
| 47 | [Minimum mode I/O signals](images/Day%2013/Screenshot%202026-06-16%20221331.png) | `ALE`, `RD`, `WR`, `M/IO`, `DT/R`, and `DEN`. |
| 48 | [Minimum mode I/O interface diagram](images/Day%2013/Screenshot%202026-06-16%20221703.png) | 8086, I/O interface circuit, and I/O devices. |
| 49 | [Maximum mode interface](images/Day%2013/Screenshot%202026-06-16%20222147.png) | 8288 bus controller and changed I/O control signals. |
| 50 | [IN and OUT instruction forms](images/Day%2013/Screenshot%202026-06-16%20222823.png) | Direct and variable I/O forms using the accumulator. |
| 51 | [IN and OUT instruction introduction clear view](images/Day%2013/Screenshot%202026-06-16%20222948.png) | Same direct/variable I/O idea in a cleaner screenshot. |
| 52 | [IN and OUT operation table](images/Day%2013/Screenshot%202026-06-16%20223017.png) | Formats, operations, and flags for `IN` and `OUT`. |
| 53 | [IN and OUT examples](images/Day%2013/Screenshot%202026-06-16%20223136.png) | Byte and word examples for direct I/O. |
| 54 | [IN and OUT examples clear view](images/Day%2013/Screenshot%202026-06-16%20223211.png) | Same examples with the `DX` range note. |
| 55 | [Output word to 16-bit port example](images/Day%2013/Screenshot%202026-06-16%20223629.png) | Use `DX` for output to port `B000H`. |
| 56 | [Read two byte ports and output a word](images/Day%2013/Screenshot%202026-06-16%20224012.png) | Read adjacent input ports into `AX`, then output the word. |
| 57 | [8086 input bus cycle](images/Day%2013/Screenshot%202026-06-16%20224441.png) | Timing of address, control, and data during I/O read. |
| 58 | [8086 output bus cycle](images/Day%2013/Screenshot%202026-06-16%20224743.png) | Timing idea for I/O write cycle and signal-direction caution. |
| 59 | [Interfacing devices](images/Day%2013/Screenshot%202026-06-16%20224953.png) | Why peripherals need interface circuits. |
| 60 | [Intel 8251 USART](images/Day%2013/Screenshot%202026-06-16%20225324.png) | Serial/parallel conversion for communication. |
| 61 | [Intel 8257 DMA controller](images/Day%2013/Screenshot%202026-06-16%20225828.png) | Four-channel DMA controller block diagram. |
| 62 | [8257 DMA controller summary](images/Day%2013/Screenshot%202026-06-16%20230006.png) | DMA purpose, four channels, and read/write/verify operations. |
| 63 | [8259 interrupt controller introduction](images/Day%2013/Screenshot%202026-06-16%20230119.png) | Programmable interrupt controller for multiple interrupting devices. |
| 64 | [8259 internal block diagram](images/Day%2013/Screenshot%202026-06-16%20230216.png) | Data bus, priority, mask, request, and in-service blocks. |
| 65 | [8275 and 8279 interface devices](images/Day%2013/Screenshot%202026-06-16%20230334.png) | CRT controller and keyboard/display interface overview. |
| 66 | [Input device quiz](images/Day%2013/Screenshot%202026-06-16%20230517.png) | Identify which device lets the processor read external data. |
| 67 | [Read-operation current quiz](images/Day%2013/Screenshot%202026-06-16%20230618.png) | Avoid excessive source or sink current on data lines. |
| 68 | [Read-operation loading quiz](images/Day%2013/Screenshot%202026-06-16%20230708.png) | Use tri-state buffers to prevent bus loading. |
| 69 | [Unsupported operation quiz](images/Day%2013/Screenshot%202026-06-16%20230746.png) | 8086 has no direct packed-BCD multiplication instruction. |
| 70 | [Stack segment size quiz](images/Day%2013/Screenshot%202026-06-16%20230851.png) | 8086 segment size can be up to 64 KB. |
| 71 | [Stack segment marked answer](images/Day%2013/Screenshot%202026-06-16%20231006.png) | Confirms 64 KB as the stack segment maximum. |

## Page-By-Page Explanation

### Page 1: RCL Operation

<a href="images/Day%2013/Screenshot%202026-06-16%20150814.png"><img src="images/Day%2013/Screenshot%202026-06-16%20150814.png" alt="RCL operation" width="960"></a>

`RCL destination,count` rotates left through the carry flag. This is not only a circular movement inside the operand. The old most significant bit moves into `CF`, and the old `CF` moves into the least significant bit.

Think of `CF` as an extra bit attached to the operand. For an 8-bit operand, `RCL` rotates through a 9-bit ring. For a 16-bit operand, it rotates through a 17-bit ring. This is why the incoming carry value matters before the instruction executes.

### Page 2: RCL Count And Flags

<a href="images/Day%2013/Screenshot%202026-06-16%20151006.png"><img src="images/Day%2013/Screenshot%202026-06-16%20151006.png" alt="RCL count and flags" width="960"></a>

The destination for `RCL` can be a register or memory operand. A one-bit rotate can be written with count `1`. For more than one bit, the count is loaded into `CL` and the instruction uses `RCL destination,CL`.

`RCL` affects `CF` and `OF`. For a one-bit rotate, `OF` has defined signed-overflow meaning based on the changed top bits. For multi-bit rotates, `OF` is undefined. `CF` always contains the most recently rotated-out bit.

### Page 3: RCL Clear Continuation

<a href="images/Day%2013/Screenshot%202026-06-16%20151018.png"><img src="images/Day%2013/Screenshot%202026-06-16%20151018.png" alt="RCL clear continuation" width="960"></a>

This page repeats the `RCL` rules in a clearer view. The main point is that only `1` and `CL` are valid count forms for the original 8086 syntax.

Do not write a literal count such as `RCL AX,4` for original 8086 style. Load the count first:

```asm
MOV CL,04H
RCL AX,CL
```

### Page 4: ROL Operation

<a href="images/Day%2013/Screenshot%202026-06-16%20151920.png"><img src="images/Day%2013/Screenshot%202026-06-16%20151920.png" alt="ROL operation" width="960"></a>

`ROL destination,count` rotates left inside the operand. The old most significant bit moves into the least significant bit, and a copy of that outgoing bit is also placed into `CF`.

The difference from `RCL` is the incoming bit. `ROL` does not bring the old carry into the operand. `RCL` does. Therefore, `ROL` is a pure circular rotate of the operand, while `RCL` is a rotate through carry.

### Page 5: ROL Examples With Pointer

<a href="images/Day%2013/Screenshot%202026-06-16%20152435.png"><img src="images/Day%2013/Screenshot%202026-06-16%20152435.png" alt="ROL examples with pointer" width="960"></a>

The examples show the valid shapes of `ROL`. `ROL AX,1` rotates the word in `AX` left by one bit. `MOV CL,04H` followed by `ROL BL,CL` rotates the byte in `BL` left by four bit positions.

`ROL FACTOR[BX],1` rotates a memory operand at effective address `offset FACTOR + BX`. The operand size must be known to the assembler from the variable declaration or from an explicit `BYTE PTR` or `WORD PTR`.

### Page 6: ROL Examples Clear View

<a href="images/Day%2013/Screenshot%202026-06-16%20152443.png"><img src="images/Day%2013/Screenshot%202026-06-16%20152443.png" alt="ROL examples clear view" width="960"></a>

This clearer page reinforces the same examples. The key trace rule is: every bit moves left, old `MSB` becomes new `LSB`, and `CF` receives a copy of the old `MSB`.

For multi-bit `ROL`, `CF` ends with the last bit rotated out of the most significant position. Earlier carry values from the same instruction are overwritten.

### Page 7: ROR Operation

<a href="images/Day%2013/Screenshot%202026-06-16%20152706.png"><img src="images/Day%2013/Screenshot%202026-06-16%20152706.png" alt="ROR operation" width="960"></a>

`ROR destination,count` rotates right inside the operand. The old least significant bit moves into the most significant bit, and a copy of that bit is placed into `CF`.

Again, this is circular rotate, not shift. No zero is inserted. No old carry is inserted. For multi-bit rotates, `CF` contains the last bit moved out of the least significant position.

### Page 8: SAL And SHL

<a href="images/Day%2013/Screenshot%202026-06-16%20153019.png"><img src="images/Day%2013/Screenshot%202026-06-16%20153019.png" alt="SAL and SHL" width="960"></a>

`SAL` and `SHL` are two names for the same operation on 8086. They shift all bits left, move the old most significant bit into `CF`, and insert `0` into the least significant bit.

This differs from `ROL` because the old `MSB` does not wrap around into `LSB`. For unsigned values, a left shift by one is like multiplying by 2 if no significant bit is lost. For signed values, `OF` after a one-bit shift is important because the sign may have changed incorrectly.

### Page 9: SAR

<a href="images/Day%2013/Screenshot%202026-06-16%20153217.png"><img src="images/Day%2013/Screenshot%202026-06-16%20153217.png" alt="SAR" width="960"></a>

`SAR destination,count` shifts right arithmetically. The least significant bit moves into `CF`, and the old most significant bit is copied back into the most significant position.

That copied sign bit is the whole purpose of `SAR`. It preserves the sign of signed two's-complement numbers while shifting right. For signed numbers, `SAR` is the shift form related to division by powers of two, while `SHR` would insert zero and is mainly for unsigned values.

### Page 10: MOVS String Instructions

<a href="images/Day%2013/Screenshot%202026-06-16%20153317.png"><img src="images/Day%2013/Screenshot%202026-06-16%20153317.png" alt="MOVS string instructions" width="960"></a>

`MOVS`, `MOVSB`, and `MOVSW` copy a byte or word from a source string to a destination string. The source offset is in `SI` and normally uses `DS`; the destination offset is in `DI` and uses `ES`.

`MOVSB` moves one byte. `MOVSW` moves one word. When repeated with a repeat prefix, `CX` provides the count of elements to move. The instruction itself does not affect flags.

### Page 11: MOVS Direction Flag Behavior

<a href="images/Day%2013/Screenshot%202026-06-16%20153457.png"><img src="images/Day%2013/Screenshot%202026-06-16%20153457.png" alt="MOVS direction flag behavior" width="960"></a>

After each string move, `SI` and `DI` are automatically adjusted. If `DF = 0`, they increment: by 1 after a byte move and by 2 after a word move.

If `DF = 1`, they decrement by the same amount. This is why `CLD` is usually placed before forward string processing, and `STD` is used only when the string must be processed backward.

### Page 12: CMPS String Instructions

<a href="images/Day%2013/Screenshot%202026-06-16%20154003.png"><img src="images/Day%2013/Screenshot%202026-06-16%20154003.png" alt="CMPS string instructions" width="960"></a>

`CMPS`, `CMPSB`, and `CMPSW` compare a byte or word from one string with a byte or word from another string. The source element is at `DS:SI`; the destination element is at `ES:DI`.

The comparison is performed like subtraction for flags, but the operands are not overwritten. `AF`, `CF`, `OF`, `PF`, `SF`, and `ZF` are affected. After the comparison, `SI` and `DI` are adjusted according to `DF`.

### Page 13: CMPS Continuation

<a href="images/Day%2013/Screenshot%202026-06-16%20154345.png"><img src="images/Day%2013/Screenshot%202026-06-16%20154345.png" alt="CMPS continuation" width="960"></a>

This page highlights direction and segment rules. If `DF` is set, `SI` and `DI` move backward. If `DF` is reset, they move forward. The size of the movement is 1 for byte strings and 2 for word strings.

`CMPS` can be combined with `REPE` or `REPNE` to compare multiple elements. `REPE CMPSB` keeps comparing while elements are equal, while `REPNE CMPSB` keeps comparing while they are not equal.

### Page 14: JMP Introduction

<a href="images/Day%2013/Screenshot%202026-06-16%20154506.png"><img src="images/Day%2013/Screenshot%202026-06-16%20154506.png" alt="JMP introduction" width="960"></a>

`JMP` is an unconditional control-transfer instruction. It fetches the next instruction from the target location instead of from the next sequential address.

If the target is in the same segment, only `IP` changes; this is a near jump. If the target is in another segment, both `CS` and `IP` change; this is a far jump. This near/far distinction controls how much address information the instruction must supply.

### Page 15: Direct JMP Example

<a href="images/Day%2013/Screenshot%202026-06-16%20155546.png"><img src="images/Day%2013/Screenshot%202026-06-16%20155546.png" alt="Direct JMP example" width="960"></a>

`JMP CONTINUE` is a direct jump because the destination label is encoded by the assembler as part of the instruction. For a near jump, the encoded displacement changes `IP` so execution continues at the label.

For a far direct jump, the instruction includes both a new offset and a new segment value. In that case, the processor replaces both `IP` and `CS`.

### Page 16: Indirect Near JMP Through Register

<a href="images/Day%2013/Screenshot%202026-06-16%20155645.png"><img src="images/Day%2013/Screenshot%202026-06-16%20155645.png" alt="Indirect near JMP through register" width="960"></a>

`JMP BX` is an indirect near jump. The new value of `IP` comes from `BX`, and `CS` remains unchanged.

This is indirect because the target address is not written as a label or immediate displacement in the instruction. The target is contained in a register at run time.

### Page 17: Indirect Memory JMP

<a href="images/Day%2013/Screenshot%202026-06-16%20155735.png"><img src="images/Day%2013/Screenshot%202026-06-16%20155735.png" alt="Indirect memory JMP" width="960"></a>

`JMP WORD PTR [BX]` is an indirect near jump through memory. The word stored at `DS:BX` is loaded into `IP`, and `CS` stays the same.

`JMP DWORD PTR [SI]` is an indirect far jump. The memory doubleword supplies both the new offset and the new segment. The first word becomes `IP`; the next word becomes `CS`.

### Page 18: JAE, JNB, And JNC

<a href="images/Day%2013/Screenshot%202026-06-16%20160110.png"><img src="images/Day%2013/Screenshot%202026-06-16%20160110.png" alt="JAE JNB JNC" width="960"></a>

`JAE`, `JNB`, and `JNC` are aliases based on the same flag test: jump if `CF = 0`. After `CMP AX,4371H`, this means unsigned `AX` is above or equal to `4371H`.

These are unsigned-condition jumps. Do not use them for signed greater-than or less-than logic; signed comparisons require jumps such as `JGE` or `JL`.

### Page 19: JNB Example Continuation

<a href="images/Day%2013/Screenshot%202026-06-16%20160429.png"><img src="images/Day%2013/Screenshot%202026-06-16%20160429.png" alt="JNB example continuation" width="960"></a>

This continuation shows the fall-through path after a comparison. If `CF = 0`, `JNB NEXT` transfers control to `NEXT`. If `CF = 1`, the jump is not taken and the next instruction, such as `ADD AL,BL`, executes.

The key habit is to read the conditional jump as a test of existing flags. The jump does not perform the comparison; the previous `CMP` or arithmetic instruction created the flag state.

### Page 20: JB, JC, And JNAE

<a href="images/Day%2013/Screenshot%202026-06-16%20160555.png"><img src="images/Day%2013/Screenshot%202026-06-16%20160555.png" alt="JB JC JNAE" width="960"></a>

`JB`, `JC`, and `JNAE` all jump when `CF = 1`. After an unsigned `CMP`, this means the destination operand was below the source operand.

For example, after `CMP AX,4371H`, `JB NEXT` jumps if unsigned `AX < 4371H`. If `CF = 0`, the jump has no effect and execution continues with the next instruction.

### Page 21: JBE And JNA

<a href="images/Day%2013/Screenshot%202026-06-16%20162819.png"><img src="images/Day%2013/Screenshot%202026-06-16%20162819.png" alt="JBE JNA" width="960"></a>

`JBE` and `JNA` jump when unsigned `destination <= source` after a comparison. The flag condition is:

```text
CF = 1 or ZF = 1
```

`CF = 1` covers below; `ZF = 1` covers equal. If both `CF` and `ZF` are zero, the destination is unsigned above the source, so `JBE` is not taken.

### Page 22: CALL Introduction

<a href="images/Day%2013/Screenshot%202026-06-16%20163148.png"><img src="images/Day%2013/Screenshot%202026-06-16%20163148.png" alt="CALL introduction" width="960"></a>

`CALL` transfers control to a procedure and saves a return address on the stack. A near call is within the same code segment, so it saves only the offset of the next instruction and then loads a new `IP`.

A far call transfers to another segment. It must save enough information to return across segments, so it saves both the return offset and return segment, then loads a new `CS:IP`.

### Page 23: Far CALL Continuation

<a href="images/Day%2013/Screenshot%202026-06-16%20164419.png"><img src="images/Day%2013/Screenshot%202026-06-16%20164419.png" alt="Far CALL continuation" width="960"></a>

This page explains the far call path. The processor loads `CS` with the segment base of the target procedure and loads `IP` with the offset of the first instruction in that target segment.

At the end of a far procedure, a far return restores both saved values from the stack. That is why far calls and far returns change the stack by more bytes than near calls and near returns.

### Page 24: CALL MULT And CALL BX

<a href="images/Day%2013/Screenshot%202026-06-16%20164504.png"><img src="images/Day%2013/Screenshot%202026-06-16%20164504.png" alt="CALL MULT and CALL BX" width="960"></a>

`CALL MULT` is a direct near call. The assembler computes the displacement to the procedure named `MULT` and encodes it in the instruction.

`CALL BX` is an indirect near call. `BX` already contains the offset of the procedure's first instruction. The processor pushes the return offset and replaces `IP` with the value in `BX`.

### Page 25: CALL Near Examples Clear View

<a href="images/Day%2013/Screenshot%202026-06-16%20171134.png"><img src="images/Day%2013/Screenshot%202026-06-16%20171134.png" alt="CALL near examples clear view" width="960"></a>

This page repeats the direct and register-indirect near call examples in a clearer view. In both cases, `CS` remains the same because the procedure is in the same segment.

The return address is always the address of the instruction after the call. The called procedure does not need to know who called it; it only needs `RET` to pop the saved address.

### Page 26: CALL Memory And Far Procedure

<a href="images/Day%2013/Screenshot%202026-06-16%20171341.png"><img src="images/Day%2013/Screenshot%202026-06-16%20171341.png" alt="CALL memory and far procedure" width="960"></a>

`CALL WORD PTR [BX]` is an indirect near call. The word at `DS:BX` supplies the new `IP`. The procedure is still inside the current `CS`.

`CALL DIVIDE` is shown as a far procedure call when `DIVIDE` is declared in another segment, such as with `PROC FAR`. A far call changes both `CS` and `IP`, and the matching return must restore both.

### Page 27: RET Introduction

<a href="images/Day%2013/Screenshot%202026-06-16%20172029.png"><img src="images/Day%2013/Screenshot%202026-06-16%20172029.png" alt="RET introduction" width="960"></a>

`RET` returns execution from a procedure to the instruction after the call that invoked it. For a near procedure, `RET` pops one word from the stack into `IP`.

This works because `CALL` pushed the return offset before jumping to the procedure. `RET` does not affect flags; it is a control-transfer and stack operation.

### Page 28: RET Far And RET Immediate

<a href="images/Day%2013/Screenshot%202026-06-16%20172252.png"><img src="images/Day%2013/Screenshot%202026-06-16%20172252.png" alt="RET far and RET immediate" width="960"></a>

For a far return, the processor pops the return offset into `IP` and then pops the return segment into `CS`. This restores the caller's full `CS:IP`.

`RET n` additionally increments `SP` by `n` after popping the return address. This is used to remove parameters that were passed on the stack. For example, `RET 6` clears six parameter bytes after the return address is restored.

### Page 29: STC, CLC, And CMC

<a href="images/Day%2013/Screenshot%202026-06-16%20172453.png"><img src="images/Day%2013/Screenshot%202026-06-16%20172453.png" alt="STC CLC CMC" width="960"></a>

These process-control instructions modify only the carry flag. `STC` sets `CF = 1`, `CLC` clears `CF = 0`, and `CMC` complements the current carry flag.

They are often used before rotate-through-carry or multi-precision arithmetic. Since they do not affect other flags, other status flags keep their previous values.

### Page 30: STD And CLD

<a href="images/Day%2013/Screenshot%202026-06-16%20172522.png"><img src="images/Day%2013/Screenshot%202026-06-16%20172522.png" alt="STD CLD" width="960"></a>

`STD` sets the direction flag, making string instructions process backward by decrementing `SI` and `DI`. `CLD` clears the direction flag, making string instructions process forward by incrementing `SI` and `DI`.

These instructions do not affect other flags. Since string instructions depend heavily on `DF`, it is good practice to execute `CLD` before forward string operations instead of assuming the flag already has the desired value.

### Page 31: CLI And HLT

<a href="images/Day%2013/Screenshot%202026-06-16%20172934.png"><img src="images/Day%2013/Screenshot%202026-06-16%20172934.png" alt="CLI and HLT" width="960"></a>

`CLI` clears the interrupt flag, `IF = 0`. When `IF` is clear, maskable interrupts through `INTR` are disabled. Non-maskable interrupts are not controlled by `IF`.

`HLT` stops instruction fetching and execution until an interrupt or reset condition resumes control. In 8086, the processor enters a halt state; it does not mean program memory is erased or the machine is powered off.

### Page 32: NOP

<a href="images/Day%2013/Screenshot%202026-06-16%20173233.png"><img src="images/Day%2013/Screenshot%202026-06-16%20173233.png" alt="NOP" width="960"></a>

`NOP` performs no data operation. It consumes time and advances the instruction pointer to the next instruction.

It is used for small delays, alignment, patch space, and placeholder code. Since it changes no register or memory value and affects no flags, it is safe when the goal is only to spend cycles or reserve space.

### Page 33: ESC

<a href="images/Day%2013/Screenshot%202026-06-16%20173256.png"><img src="images/Day%2013/Screenshot%202026-06-16%20173256.png" alt="ESC" width="960"></a>

`ESC` is used to pass instructions to a coprocessor such as the 8087 math coprocessor. The 8086 fetches the instruction bytes, and the coprocessor observes the bus and interprets the coprocessor-specific operation.

In many cases, the 8086 itself treats `ESC` as having no ordinary CPU-side effect except possible memory operand access. The useful operation is performed by the coprocessor.

### Page 34: Addition And Subtraction Programs

<a href="images/Day%2013/Screenshot%202026-06-16%20173621.png"><img src="images/Day%2013/Screenshot%202026-06-16%20173621.png" alt="Addition and subtraction programs" width="960"></a>

The addition program loads a value into `AX`, adds `BX`, stores the result at memory offset `8000H` through `SI`, and stops with `INT 03`. The subtraction program follows the same storage pattern but uses `SUB AX,BX`.

The screenshots are demonstration code, so `BX` must be assumed to contain the second operand before `ADD` or `SUB`, unless the full program initializes it elsewhere. The important idea is the result path: arithmetic result in `AX`, memory pointer in `SI`, then `MOV [SI],AX`.

### Page 35: Multiplication And Division Programs

<a href="images/Day%2013/Screenshot%202026-06-16%20173807.png"><img src="images/Day%2013/Screenshot%202026-06-16%20173807.png" alt="Multiplication and division programs" width="960"></a>

The multiplication program loads `AX = 0005H`, `BX = 0003H`, then executes `MUL BX`. Because `BX` is a word operand, the unsigned product is placed in `DX:AX`. For this small product, `AX` contains the low word result, and it is stored at `[SI]`.

The division program uses `DIV BX`. For word division, the dividend is `DX:AX`, quotient goes to `AX`, and remainder goes to `DX`. In a complete robust program, `DX` should be cleared before unsigned division when the dividend is meant to be only `AX`, for example `XOR DX,DX`.

### Page 36: Greatest Of Two And Average Of Numbers

<a href="images/Day%2013/Screenshot%202026-06-16%20173829.png"><img src="images/Day%2013/Screenshot%202026-06-16%20173829.png" alt="Greatest of two and average of numbers" width="960"></a>

The greatest-of-two program compares `AX` and `BX` using `CMP AX,BX`. `JNC LABEL1` means jump if no carry, which after an unsigned compare means `AX >= BX`. The program then stores one of the two values through `SI`.

The average program initializes `AX = 0000H`, points `SI` to the source list at `8000H`, points `DI` to result address `8020H`, and uses `CX = 5` as the count. It adds each word from `[SI]`, advances `SI` by two bytes for the next word, loops with `LOOP`, divides by `CX`, and stores the result at `[DI]`. A careful version should preserve the divisor count before `LOOP` reduces `CX` to zero.

### Page 37: Greatest And Average Clear View

<a href="images/Day%2013/Screenshot%202026-06-16%20173844.png"><img src="images/Day%2013/Screenshot%202026-06-16%20173844.png" alt="Greatest and average clear view" width="960"></a>

This page gives a clearer view of the same two routines. The maximum routine is an unsigned comparison routine because it uses carry-based logic (`JNC`).

In the average routine, two `INC SI` instructions are used because each word is two bytes. That is the same reason `MOVSW` changes `SI` by two in string operations.

### Page 38: Greatest And Average Repeated View

<a href="images/Day%2013/Screenshot%202026-06-16%20173857.png"><img src="images/Day%2013/Screenshot%202026-06-16%20173857.png" alt="Greatest and average repeated view" width="960"></a>

This repeated view reinforces two separate ideas: flag-based branching and counted looping. `CMP` creates flags; `JNC` reads the carry flag. `LOOP` decrements `CX` and jumps while `CX` is not zero.

When writing the average program yourself, keep the element count in a separate register or memory location before `LOOP` destroys `CX`, then use that saved count as the divisor.

### Page 39: Sum And Factorial Programs

<a href="images/Day%2013/Screenshot%202026-06-16%20174255.png"><img src="images/Day%2013/Screenshot%202026-06-16%20174255.png" alt="Sum and factorial programs" width="960"></a>

The sum program reads the count from `[SI]`, clears `AX`, uses `BX` as the running index/value, repeatedly increments `BX`, adds it to `AX`, compares `BX` with `CX`, and loops until the count is reached. The result is stored through `DI`.

The factorial program loads the number into `BX`, starts `AX = 0001H`, repeatedly multiplies by `BX`, decrements `BX`, and loops until `BX` becomes zero. Since `MUL BX` produces `DX:AX`, large factorials can overflow `AX`; this demo stores only the low word in `AX`.

### Page 40: Data Transfer Quiz

<a href="images/Day%2013/Screenshot%202026-06-16%20174625.png"><img src="images/Day%2013/Screenshot%202026-06-16%20174625.png" alt="Data transfer quiz" width="960"></a>

The question asks which instruction is not a data copy or transfer instruction. `MOV`, `PUSH`, and `POP` all move data between registers, memory, and stack.

`DAS` is different. It is Decimal Adjust after Subtraction, an arithmetic-adjust instruction for packed BCD subtraction results in `AL`. So `DAS` is not a data-transfer instruction.

### Page 41: I/O Address Space

<a href="images/Day%2013/Screenshot%202026-06-16%20210529.png"><img src="images/Day%2013/Screenshot%202026-06-16%20210529.png" alt="I/O address space" width="960"></a>

The 8086 has an isolated I/O address space of 65,536 byte addresses, from `0000H` to `FFFFH`. Each I/O address is called a port.

I/O can be organized in two main ways. In isolated I/O, port addresses are separate from memory addresses and are accessed by special I/O instructions such as `IN` and `OUT`. In memory-mapped I/O, device registers occupy addresses in the normal memory address space.

### Page 42: Advantages Of Isolated I/O

<a href="images/Day%2013/Screenshot%202026-06-16%20211643.png"><img src="images/Day%2013/Screenshot%202026-06-16%20211643.png" alt="Advantages of isolated I/O" width="960"></a>

The first advantage is that the full memory address space remains available for memory, because I/O ports live in a separate address space.

The second advantage is clarity and instruction specialization. `IN` and `OUT` are specifically designed for I/O transfers, so the program makes a clear distinction between memory access and device access.

### Page 43: Isolated I/O Port Diagram

<a href="images/Day%2013/Screenshot%202026-06-16%20212537.png"><img src="images/Day%2013/Screenshot%202026-06-16%20212537.png" alt="Isolated I/O port diagram" width="960"></a>

The diagram shows isolated I/O port numbering from `0000H` through `FFFFH`. That is 64K port addresses.

The note about pages is a way to group the 16-bit port space: 256 ports per 8-bit page and 256 such pages. Immediate I/O instructions can directly encode only an 8-bit port number, while the `DX` form can address the full 16-bit port range.

### Page 44: Memory-Mapped I/O Advantages And Disadvantage

<a href="images/Day%2013/Screenshot%202026-06-16%20214257.png"><img src="images/Day%2013/Screenshot%202026-06-16%20214257.png" alt="Memory-mapped I/O advantages and disadvantage" width="960"></a>

In memory-mapped I/O, device registers are treated as memory locations. This means normal memory instructions such as `MOV`, `ADD`, `AND`, `XCHG`, and `SUB` can operate on I/O locations.

The advantage is flexibility: many instructions and registers can be used. The disadvantage is that part of the memory address space is consumed by device registers, and memory-style accesses may be slower than special I/O instructions in isolated I/O systems.

### Page 45: Isolated Versus Memory-Mapped I/O

<a href="images/Day%2013/Screenshot%202026-06-16%20220122.png"><img src="images/Day%2013/Screenshot%202026-06-16%20220122.png" alt="Isolated versus memory-mapped I/O" width="960"></a>

This comparison table summarizes the two approaches. Isolated I/O uses a separate I/O address space; memory-mapped I/O uses part of the normal memory address space.

Isolated I/O uses limited, special instructions such as `IN`, `OUT`, `INS`, and `OUTS`. Memory-mapped I/O can use normal memory-reference instructions. Isolated I/O preserves memory space and can be faster for I/O-specific transfers, while memory-mapped I/O offers richer instruction choices but reduces available memory address space.

### Page 46: Minimum Mode Interface

<a href="images/Day%2013/Screenshot%202026-06-16%20220810.png"><img src="images/Day%2013/Screenshot%202026-06-16%20220810.png" alt="Minimum mode interface" width="960"></a>

This page moves from the software view of I/O into the hardware interface view. In minimum mode, the 8086 itself provides the bus-control signals needed by the interface circuitry. The structure is similar to memory interfacing because the processor still places address information on the bus, activates read/write control, and transfers data over the data bus.

The I/O devices can be LEDs, switches, keyboards, serial ports, printer ports, or other peripherals. Data moves between the MPU and these devices through an I/O interface circuit. Because `AD0-AD7` are multiplexed address/data lines, address information must be latched before the same lines are reused for data.

### Page 47: Minimum Mode I/O Signals

<a href="images/Day%2013/Screenshot%202026-06-16%20221331.png"><img src="images/Day%2013/Screenshot%202026-06-16%20221331.png" alt="Minimum mode I/O signals" width="960"></a>

This page names the main minimum-mode signals. `ALE` pulses high to tell external latch circuitry to capture the address. Active-low `RD` tells the interface an input read is in progress, and active-low `WR` tells it an output write is in progress.

`M/IO` distinguishes memory transfers from I/O transfers; in this slide, logic 0 marks an I/O operation. `DT/R` sets the data-bus direction for read or write, and `DEN` enables the external data-bus interface. These signals let the interface know when to latch an address, when to drive data, and when to receive data.

### Page 48: Minimum Mode I/O Interface Diagram

<a href="images/Day%2013/Screenshot%202026-06-16%20221703.png"><img src="images/Day%2013/Screenshot%202026-06-16%20221703.png" alt="Minimum mode I/O interface diagram" width="960"></a>

The diagram shows the 8086 MPU connected to an I/O interface circuit. Address/data lines, `ALE`, `RD`, `WR`, `M/IO`, `DT/R`, `DEN`, and `BHE` reach the interface, and the interface connects to multiple I/O devices.

The `MN/MX` pin is tied for minimum mode, so the 8086 directly generates the bus-control signals. The I/O interface decodes the address, uses the control signals to determine read or write direction, and selects the correct I/O device.

### Page 49: Maximum Mode Interface

<a href="images/Day%2013/Screenshot%202026-06-16%20222147.png"><img src="images/Day%2013/Screenshot%202026-06-16%20222147.png" alt="Maximum mode interface" width="960"></a>

In maximum mode, the 8086 does not directly provide all the same control signals. An 8288 bus controller produces the bus-control signals for the I/O subsystem.

The slide lists the signal changes: `IORC` replaces `RD` for I/O read control, `IOWC` and `AIOWC` replace `WR` for I/O write control, `DEN` polarity/meaning is handled through the bus-controller interface, and `M/IO` is no longer needed in the same direct form because the bus controller creates separate I/O read/write controls.

### Page 50: IN And OUT Instruction Forms

<a href="images/Day%2013/Screenshot%202026-06-16%20222823.png"><img src="images/Day%2013/Screenshot%202026-06-16%20222823.png" alt="IN and OUT instruction forms" width="960"></a>

This page returns to the software instruction view. `IN` and `OUT` have direct I/O forms and variable I/O forms. The direct form encodes an 8-bit port number in the instruction. The variable form uses `DX`, so it can access the full 16-bit I/O port range.

All basic `IN` and `OUT` data transfers use the accumulator. A byte transfer uses `AL`; a word transfer uses `AX`. So the device communicates with the MPU through `AL` or `AX`, while the port number is either immediate or supplied through `DX`.

### Page 51: IN And OUT Instruction Introduction Clear View

<a href="images/Day%2013/Screenshot%202026-06-16%20222948.png"><img src="images/Day%2013/Screenshot%202026-06-16%20222948.png" alt="IN and OUT instruction introduction clear view" width="960"></a>

This page is the same `IN`/`OUT` introduction without the bottom table visible. It emphasizes the two instruction classes: direct I/O and variable I/O.

Direct I/O puts the port number in the instruction. Variable I/O takes the port number from `DX`. Either form can transfer a byte or a word, but the data endpoint inside the processor is still the accumulator: `AL` for byte data and `AX` for word data.

### Page 52: IN And OUT Operation Table

<a href="images/Day%2013/Screenshot%202026-06-16%20223017.png"><img src="images/Day%2013/Screenshot%202026-06-16%20223017.png" alt="IN and OUT operation table" width="960"></a>

The table summarizes the exact formats. Input direct is `IN Acc, Port`, meaning accumulator receives data from the selected port. Input variable is `IN Acc, DX`, meaning the port address is in `DX`.

For output, direct form is `OUT Port, Acc`, and variable form is `OUT DX, Acc`. In both cases, the port receives data from the accumulator. `IN` and `OUT` do not affect flags, so any later conditional jump must depend on flags set by earlier instructions, not by the I/O transfer itself.

### Page 53: IN And OUT Examples

<a href="images/Day%2013/Screenshot%202026-06-16%20223136.png"><img src="images/Day%2013/Screenshot%202026-06-16%20223136.png" alt="IN and OUT examples" width="960"></a>

The examples show byte and word I/O. `IN AL,0C8H` reads one byte from port `0C8H` into `AL`. `OUT 3BH,AL` writes the byte in `AL` to port `3BH`.

For word transfers, the 8086 uses two adjacent byte ports. `IN AX,34H` reads a word through ports `34H` and `35H`, and `OUT 2CH,AX` writes a word through ports `2CH` and `2DH`. For variable-port I/O, load the 16-bit port address into `DX` before executing `IN` or `OUT`.

### Page 54: IN And OUT Examples Clear View

<a href="images/Day%2013/Screenshot%202026-06-16%20223211.png"><img src="images/Day%2013/Screenshot%202026-06-16%20223211.png" alt="IN and OUT examples clear view" width="960"></a>

This page repeats the same examples with the full variable-port note visible. The key range is:

```text
DX can hold 0000H through FFFFH
```

That is why `DX` is required for ports outside the direct 8-bit immediate range. The accumulator remains the data register: `AL` for byte I/O and `AX` for word I/O.

### Page 55: Output Word To 16-Bit Port Example

<a href="images/Day%2013/Screenshot%202026-06-16%20223629.png"><img src="images/Day%2013/Screenshot%202026-06-16%20223629.png" alt="Output word to 16-bit port example" width="960"></a>

The task is to output `FFFFH` to I/O port address `B000H`. Since `B000H` is a 16-bit port address, the direct immediate form is not enough; original 8086 direct `OUT` encodes only an 8-bit port number.

The correct variable-port sequence is:

```asm
MOV AX,0FFFFH
MOV DX,0B000H
OUT DX,AX
```

`AX` holds the word data, and `DX` holds the 16-bit port address. The word output uses port `B000H` for the low byte and the next port for the high byte.

### Page 56: Read Two Byte Ports And Output A Word

<a href="images/Day%2013/Screenshot%202026-06-16%20224012.png"><img src="images/Day%2013/Screenshot%202026-06-16%20224012.png" alt="Read two byte ports and output a word" width="960"></a>

The input side uses two adjacent byte-wide ports, `A9H` and `AAH`, as one word input. `IN AX,A9H` reads a word into `AX`, with the low byte from port `A9H` and the high byte from port `AAH`.

For the output side, the same 16-bit port-address rule applies. If the intended output port is `B000H`, use:

```asm
IN  AX,0A9H
MOV DX,0B000H
OUT DX,AX
```

If the written address is truly `B0000H`, it is outside the 8086 I/O address range of `0000H` to `FFFFH`, so it cannot be addressed as a normal 8086 I/O port. In that case the question likely has a typographical extra zero.

### Page 57: 8086 Input Bus Cycle

<a href="images/Day%2013/Screenshot%202026-06-16%20224441.png"><img src="images/Day%2013/Screenshot%202026-06-16%20224441.png" alt="8086 input bus cycle" width="960"></a>

This timing diagram shows one 8086 I/O read bus cycle. During `T1`, the processor outputs address information on the multiplexed address/data lines, and `ALE` pulses so external latch circuitry can capture that address before the bus is reused for data.

During the middle of the cycle, `M/IO` identifies the transfer as I/O, active-low `RD` indicates a read operation, and `DEN` enables the data bus interface. The input device then drives data onto the data bus during the data phase, so the processor can sample it before the bus cycle ends.

### Page 58: 8086 Output Bus Cycle

<a href="images/Day%2013/Screenshot%202026-06-16%20224743.png"><img src="images/Day%2013/Screenshot%202026-06-16%20224743.png" alt="8086 output bus cycle" width="960"></a>

This page is labelled as an output bus cycle. The main timing idea is the same first step: during `T1`, the processor outputs the I/O address and pulses `ALE` so the address can be latched before the multiplexed bus is reused.

For a true I/O write cycle, the processor should drive data onto the data bus and the active-low `WR` signal should indicate the write operation. The visible screenshot still shows read-style labels such as `RD` and `DATA IN`, so treat it as a timing-shape reminder but remember the correction: input uses `RD` and device-to-CPU data flow; output uses `WR` and CPU-to-device data flow.

### Page 59: Interfacing Devices

<a href="images/Day%2013/Screenshot%202026-06-16%20224953.png"><img src="images/Day%2013/Screenshot%202026-06-16%20224953.png" alt="Interfacing devices" width="960"></a>

This page explains why interface devices are needed. A microprocessor communicates with the outside world through peripherals or I/O devices, but those devices may not produce or accept data in the exact electrical, timing, or format style the processor bus expects.

An input interface converts peripheral data into a processor-compatible format. An output interface converts processor data into the format required by the peripheral. Interface circuits simplify system design by handling selection, timing, buffering, and format conversion between the CPU bus and external devices.

### Page 60: Intel 8251 USART

<a href="images/Day%2013/Screenshot%202026-06-16%20225324.png"><img src="images/Day%2013/Screenshot%202026-06-16%20225324.png" alt="Intel 8251 USART" width="960"></a>

The Intel 8251 is a programmable communication interface, also called a USART: universal synchronous/asynchronous receiver/transmitter. It is compatible with 8085, 8086, and 8088 systems.

Its job is serial communication. On transmission, it accepts parallel data from the microprocessor and converts it into serial data. On reception, it accepts serial data, converts it into parallel format, and provides that parallel data to the CPU.

### Page 61: Intel 8257 DMA Controller

<a href="images/Day%2013/Screenshot%202026-06-16%20225828.png"><img src="images/Day%2013/Screenshot%202026-06-16%20225828.png" alt="Intel 8257 DMA controller" width="960"></a>

The Intel 8257 is a programmable DMA controller. DMA means direct memory access: data can move between memory and an I/O device without the CPU executing one instruction for every byte.

The diagram shows four DMA channels, each with address and count registers. `DRQ0-DRQ3` are DMA request inputs from devices, and `DACK0-DACK3` acknowledge the selected device. The controller also has bus-control signals such as `HRQ` and `HLDA` to request and receive bus ownership from the processor, plus memory and I/O read/write controls for the actual transfer.

### Page 62: 8257 DMA Controller Summary

<a href="images/Day%2013/Screenshot%202026-06-16%20230006.png"><img src="images/Day%2013/Screenshot%202026-06-16%20230006.png" alt="8257 DMA controller summary" width="960"></a>

This page explains where DMA is preferred. DMA is useful when a fast I/O device needs to transfer a block of data to memory, or memory needs to transfer a block of data to a fast I/O device. If the CPU handled every byte using normal instructions, it would waste time on repeated input/output and memory-transfer operations.

In a DMA transfer, the device and memory exchange data directly under control of the DMA controller. The 8257 has four channels, so four I/O devices can be connected. Each channel has a 16-bit address register and a 16-bit byte-count register. Before enabling a channel, the program initializes these registers so the DMA controller knows the starting memory address and how many bytes to transfer.

The listed operations are read, write, and verify. In DMA read/write, data is transferred between memory and an I/O device. In verify, the controller can perform the address/count sequencing without actual data movement, which is useful for checking or timing certain operations.

### Page 63: 8259 Interrupt Controller Introduction

<a href="images/Day%2013/Screenshot%202026-06-16%20230119.png"><img src="images/Day%2013/Screenshot%202026-06-16%20230119.png" alt="8259 interrupt controller introduction" width="960"></a>

The 8259 is a programmable interrupt controller. It is used when several I/O devices need to interrupt the processor, especially when those interrupt requests must be organized through a limited number of processor interrupt inputs.

Instead of wiring many devices directly and handling priority externally, the 8259 accepts interrupt requests, decides which request should be served first, and then sends one interrupt request to the microprocessor. When the processor acknowledges the interrupt, the 8259 helps provide the correct interrupt information so the CPU can jump to the proper service routine.

The page notes that the 8259 is a single-chip controller, comes as a 28-pin dual in-line package, uses NMOS technology, and is compatible with 8085, 8086, and 8088 systems. The important exam idea is its role: it manages multiple interrupt sources in a programmable priority structure.

### Page 64: 8259 Internal Block Diagram

<a href="images/Day%2013/Screenshot%202026-06-16%20230216.png"><img src="images/Day%2013/Screenshot%202026-06-16%20230216.png" alt="8259 internal block diagram" width="960"></a>

The block diagram shows how the 8259 receives, prioritizes, masks, and services interrupt requests. `IR0` through `IR7` are interrupt request inputs from external devices. These requests first enter the interrupt request register, or `IRR`, which records pending interrupts.

The interrupt mask register, or `IMR`, decides which requests are allowed and which are temporarily blocked. A masked request is ignored until it is unmasked. After masking, the priority resolver selects the highest-priority pending request. When an interrupt is accepted for service, the in-service register, or `ISR`, records that the selected interrupt is currently being handled.

The control logic communicates with the processor using `INT` and active-low `INTA`. `INT` tells the CPU that an interrupt request exists, and `INTA` is the CPU's acknowledge signal. The data bus buffer connects the 8259 to `D0-D7`, while the read/write logic allows the CPU to initialize and read the 8259 through control words and status reads. The cascade buffer/comparator supports expanding the interrupt system by connecting multiple 8259 chips.

### Page 65: 8275 And 8279 Interface Devices

<a href="images/Day%2013/Screenshot%202026-06-16%20230334.png"><img src="images/Day%2013/Screenshot%202026-06-16%20230334.png" alt="8275 and 8279 interface devices" width="960"></a>

This page lists two more programmable interface chips used with microprocessor systems. The Intel 8275 is a programmable CRT controller. Its role is to interface a CRT or raster-scan display with the microcomputer, so the CPU does not have to directly generate all display timing and control signals.

The Intel 8279 is a programmable keyboard/display interface. It has a keyboard section and a display section. The keyboard section handles keyboard interfacing, while the display section drives alphanumeric displays or indicator lights. The main idea is the same as the other interface chips: specialized hardware sits between the processor and a peripheral so the CPU can use programmed control instead of directly managing every signal detail.

### Page 66: Input Device Quiz

<a href="images/Day%2013/Screenshot%202026-06-16%20230517.png"><img src="images/Day%2013/Screenshot%202026-06-16%20230517.png" alt="Input device quiz" width="960"></a>

This question asks which device enables the microprocessor to read data from external devices. In microprocessor terminology, reading external data means accepting input into the system.

Among the options, `joystick` is the clearest input device. A printer and display are output devices because the processor sends data to them. A reader can be an input-related device in general wording, but in this MCQ set the expected distinction is input versus output, so joystick is the best answer.

### Page 67: Read-Operation Current Quiz

<a href="images/Day%2013/Screenshot%202026-06-16%20230618.png"><img src="images/Day%2013/Screenshot%202026-06-16%20230618.png" alt="Read-operation current quiz" width="960"></a>

This question is about bus loading during a read operation. During a read, the selected external device drives the data bus and the processor samples that data. The data lines should not be forced to source or sink excessive current, because that can overload the bus driver, distort logic levels, or create contention if more than one device tries to drive the bus.

The marked answer is option `(c) sourced or sinked from data lines`. In better wording: during a read operation, care must be taken that excessive current is neither sourced from nor sunk into the data lines. Only the selected device should drive the bus, and all non-selected devices should remain electrically disconnected from the bus output path.

### Page 68: Read-Operation Loading Quiz

<a href="images/Day%2013/Screenshot%202026-06-16%20230708.png"><img src="images/Day%2013/Screenshot%202026-06-16%20230708.png" alt="Read-operation loading quiz" width="960"></a>

This question asks which device is used to avoid loading during a read operation. The marked answer is `(d) tri-state buffer`.

A tri-state buffer has three output states: logic `0`, logic `1`, and high impedance. The high-impedance state is the important one here. When a peripheral is not selected, its tri-state buffer output is effectively disconnected from the data bus, so it does not load the bus or fight with another device. When the peripheral is selected for reading, the buffer is enabled and can safely drive the data lines.

### Page 69: Unsupported Operation Quiz

<a href="images/Day%2013/Screenshot%202026-06-16%20230746.png"><img src="images/Day%2013/Screenshot%202026-06-16%20230746.png" alt="Unsupported operation quiz" width="960"></a>

This question asks what the 8086 does not support. The 8086 supports normal arithmetic operations, logical operations, and several decimal-adjust instructions used around BCD arithmetic.

The unsupported item here is `direct BCD packed multiplication`. The 8086 has binary multiply instructions such as `MUL` and `IMUL`, and it has BCD adjustment instructions for addition/subtraction style decimal correction, but it does not provide a single direct instruction for multiplying packed BCD operands. Such a task must be handled by a software routine.

### Page 70: Stack Segment Size Quiz

<a href="images/Day%2013/Screenshot%202026-06-16%20230851.png"><img src="images/Day%2013/Screenshot%202026-06-16%20230851.png" alt="Stack segment size quiz" width="960"></a>

This question asks the maximum memory block size of the 8086 stack segment. In 8086 segmentation, each segment is addressed by a 16-bit offset, so a segment can cover `0000H` through `FFFFH`.

That is `10000H` bytes, or 64 KB. Therefore the stack segment may have a maximum size of `64 K bytes`, assuming the stack is allowed to use the full segment range.

### Page 71: Stack Segment Marked Answer

<a href="images/Day%2013/Screenshot%202026-06-16%20231006.png"><img src="images/Day%2013/Screenshot%202026-06-16%20231006.png" alt="Stack segment marked answer" width="960"></a>

This page repeats the stack-segment size question and marks `64 K bytes` as the answer. The reason is the same: 8086 offsets are 16-bit values, so one segment can address `2^16` byte positions.

For the stack, `SS` gives the segment base and `SP`/`BP` provide offsets inside that segment. Since the offset range is `0000H` to `FFFFH`, the maximum block covered by the stack segment is 64 KB.

## Deep Revision Tables

### Rotate And Shift Summary

| Instruction | What enters vacant bit | What goes to `CF` | Main use |
| --- | --- | --- | --- |
| `RCL` | Old `CF` enters low bit | Old `MSB` | Multi-bit rotate through carry. |
| `ROL` | Old `MSB` enters low bit | Old `MSB` | Circular rotate left. |
| `ROR` | Old `LSB` enters high bit | Old `LSB` | Circular rotate right. |
| `SHL` / `SAL` | `0` enters low bit | Old `MSB` | Logical left shift, multiply-like operation. |
| `SAR` | Old `MSB` is copied | Old `LSB` | Signed right shift. |

### Unsigned Jump Summary

| Jump names | Flag test | Meaning after `CMP dest,src` |
| --- | --- | --- |
| `JAE`, `JNB`, `JNC` | `CF = 0` | `dest >= src` unsigned. |
| `JB`, `JC`, `JNAE` | `CF = 1` | `dest < src` unsigned. |
| `JBE`, `JNA` | `CF = 1` or `ZF = 1` | `dest <= src` unsigned. |

### Near And Far Control Transfer

| Instruction type | What changes | What is stored/restored |
| --- | --- | --- |
| Near `JMP` | `IP` | No return address. |
| Far `JMP` | `CS` and `IP` | No return address. |
| Near `CALL` | `IP` | Push return `IP`. |
| Far `CALL` | `CS` and `IP` | Push return `CS:IP`. |
| Near `RET` | `IP` | Pop return `IP`. |
| Far `RET` | `CS` and `IP` | Pop return `IP` and `CS`. |

## Points To Remember

- `RCL` uses carry as part of the rotation path; `ROL` does not.
- `SHL`/`SAL` insert zero into the low bit; `SAR` preserves the sign bit.
- `MOVS` uses `DS:SI` as source and `ES:DI` as destination.
- `DF = 0` moves string pointers forward; `DF = 1` moves them backward.
- `CMP` sets flags; conditional jumps only read those flags.
- `JAE/JNB/JNC` mean unsigned no carry.
- `JB/JC/JNAE` mean unsigned carry.
- `JBE/JNA` means unsigned below or equal.
- Near control transfers change only `IP`; far transfers change `CS:IP`.
- `CALL` saves a return address; `JMP` does not.
- `RET n` removes stack parameters after restoring the return address.
- `STC`, `CLC`, and `CMC` modify only carry.
- `STD` and `CLD` control string direction.
- `DAS` is not a data-transfer instruction.
- Isolated I/O uses separate port space; memory-mapped I/O uses memory addresses for devices.
- In minimum mode, the 8086 directly provides I/O bus-control signals.
- In maximum mode, the 8288 bus controller creates the I/O control signals.
- Direct `IN`/`OUT` uses an 8-bit port number; variable `IN`/`OUT` uses `DX`.
- 8257 handles DMA transfers so fast devices can exchange blocks with memory without CPU involvement for each byte.
- 8259 handles multiple interrupt requests, applies masks and priority, and presents a controlled interrupt request to the CPU.
- 8275 supports CRT/raster display interfacing; 8279 supports keyboard and display interfacing.

## Sources

[S1] Intel Corporation, [The 8086 Family User's Manual, October 1979](https://www.ardent-tool.com/CPU/docs/Intel/808x/manuals/9800722-03.pdf). Used for 8086 rotate/shift, string, control-transfer, process-control, and I/O instruction behavior.

[S2] Intel Corporation, [iAPX 86,88 User's Manual, August 1981](https://www.dosdays.co.uk/media/intel/1981_iAPX_86_88_Users_Manual.pdf). Used for 8086 programming model, stack behavior, segmentation, I/O organization, and instruction details.
