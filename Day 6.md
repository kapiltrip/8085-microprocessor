# Day 6: Assignment Questions, Timing, Loops, and Register Tracing

Day 6 covers the May 30 assignment screenshots. The images are mostly exam-style 8085 questions: accumulator rotation, delay loops, T-state counting, register-pair tracing, stack effects, instruction timing, and flag behavior.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [RRC rotate accumulator question](images/Day%206/day-6-rrc-rotate-accumulator-question.png) | Trace `STC`, `MVI`, repeated `RRC`, and final accumulator content. |
| 2 | [Delay loop T-states program question](images/Day%206/day-6-delay-loop-tstates-program-question.png) | Nested loop timing with instruction T-states. |
| 3 | [Delay loop T-states working](images/Day%206/day-6-delay-loop-tstates-working.png) | Working for loop iteration and T-state totals. |
| 4 | [Delay loop time answer](images/Day%206/day-6-delay-loop-time-answer.png) | Convert T-states into delay using clock frequency. |
| 5 | [XCHG register-pair program question](images/Day%206/day-6-xchg-register-pair-program-question.png) | Trace `MOV` and `XCHG` effects on `BC`, `DE`, and `HL`. |
| 6 | [Non-maskable interrupt data-line question](images/Day%206/day-6-non-maskable-interrupt-data-line-question.png) | Data-line value during non-maskable interrupt handling. |
| 7 | [Maximum T-states instruction question](images/Day%206/day-6-maximum-tstates-instruction-question.png) | Compare instruction execution time for `XRI`, `STA`, `CALL`, and `JMP`. |
| 8 | [Direct addressing instruction question](images/Day%206/day-6-direct-addressing-instruction-question.png) | Identify `STA address` as direct addressing. |
| 9 | [DAD and XCHG register-pair question](images/Day%206/day-6-dad-xchg-register-pair-question.png) | Trace `LXI`, `DAD`, and `XCHG`. |
| 10 | [HLT instruction effects question](images/Day%206/day-6-hlt-instruction-effects-question.png) | Effects of `HLT` on program execution and buses. |
| 11 | [XRA, SUI, ANA flags question](images/Day%206/day-6-xra-sui-ana-flags-question.png) | Logical/arithmetic instructions and flag outcomes. |
| 12 | [CMA, INR, memory complement question](images/Day%206/day-6-cma-inr-memory-complement-question.png) | One's-complement versus two's-complement behavior in a program. |
| 13 | [Mask high-order bits question](images/Day%206/day-6-mask-high-order-bits-question.png) | Use `ANI`/`ANA` masking to keep selected nibbles. |
| 14 | [Mask high-order bits solution](images/Day%206/day-6-mask-high-order-bits-solution.png) | Why masking is done through the accumulator. |
| 15 | [Stack PUSH/POP register trace question](images/Day%206/day-6-stack-push-pop-register-trace-question.png) | Trace `SP`, memory contents, `PUSH PSW`, `POP PSW`, and `POP H`. |
| 16 | [CALL/RET stack pointer question](images/Day%206/day-6-call-ret-stack-pointer-question.png) | Correct stack behavior for `CALL` and `RET`. |
| 17 | [Loop T-states total question](images/Day%206/day-6-loop-tstates-total-question.png) | Count T-states for a short decrement-and-jump loop. |
| 18 | [Loop T-cycles iteration question](images/Day%206/day-6-loop-tcycles-iteration-question.png) | Loop count and T-cycle calculation for `ORA`, `DCR`, and `JNZ`. |
| 19 | [Loop T-cycles iteration working](images/Day%206/day-6-loop-tcycles-iteration-working.png) | Working for total T-cycles and number of loop executions. |
| 20 | [Loop time and machine cycles question](images/Day%206/day-6-loop-time-machine-cycles-question.png) | Convert loop cycles into elapsed time and machine cycles. |
| 21 | [Loop time and machine cycles solution](images/Day%206/day-6-loop-time-machine-cycles-solution.png) | Final time and machine-cycle calculation. |
| 22 | [Conditional output port flags question](images/Day%206/day-6-conditional-output-port-flags-question.png) | Trace flags and output in a conditional `JC` program. |
| 23 | [Instruction clock order question](images/Day%206/day-6-instruction-clock-order-question.png) | Order instructions by clock requirement. |

## Handwritten Notes Linked To Day 6

Each handwritten page is shown first as a large full-page image. Click the image or page title to open the high-resolution extracted page, then read the deeper explanation below it.

### [till73 p013](images/HandWrittenNotes/till73/page-013.jpg)

<a href="images/HandWrittenNotes/till73/page-013.jpg"><img src="images/HandWrittenNotes/till73/page-013.jpg" alt="till73 p013 handwritten note" width="960"></a>

Explanation: This page is mainly about `INR M`, binary result, and carry/auxiliary-carry reasoning. Use with `INR M` and binary-result questions. Check whether the result affects `Z`, `S`, `P`, and `AC`. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Flag reasoning:** Do not revise flags as isolated definitions. First compute the 8-bit result, then ask whether the result is zero, whether bit 7 is set, whether parity is even, whether there was a carry from bit 3 to bit 4, and whether there was a carry or borrow out of the byte. This is especially important because in subtraction the carry flag represents borrow.

**Increment and BCD detail:** Increment/decrement instructions look simple but differ in flag behavior and operand size. `INR/DCR` act on an 8-bit register or memory byte and affect normal status flags except carry, while `INX/DCX` act on register pairs and are not ordinary 8-bit ALU flag examples. `DAA` is special because it corrects the accumulator after BCD addition using lower-nibble and carry conditions.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p014](images/HandWrittenNotes/till73/page-014.jpg)

<a href="images/HandWrittenNotes/till73/page-014.jpg"><img src="images/HandWrittenNotes/till73/page-014.jpg" alt="till73 p014 handwritten note" width="960"></a>

Explanation: This page is mainly about Program trace with result, sign, zero, parity, and carry. Use with program trace questions. It shows how to record accumulator, flags, and memory after each instruction. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Core idea:** The page is describing the internal data path of the processor. The accumulator is the main working register, the ALU performs the operation, and the result is not only stored as data but also summarized through flags. Read this as a flow: operand enters, ALU operates, accumulator receives the result, and the flag register records the condition of that result.

**Flag reasoning:** Do not revise flags as isolated definitions. First compute the 8-bit result, then ask whether the result is zero, whether bit 7 is set, whether parity is even, whether there was a carry from bit 3 to bit 4, and whether there was a carry or borrow out of the byte. This is especially important because in subtraction the carry flag represents borrow.

**Trace method:** For trace pages, make a row for every instruction. Update only the register, memory byte, flag, stack location, or program counter value that the instruction actually changes. This slower row-by-row method is the shortest reliable way to avoid losing track of `HL`, `SP`, carry, or memory contents.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p015](images/HandWrittenNotes/till73/page-015.jpg)

<a href="images/HandWrittenNotes/till73/page-015.jpg"><img src="images/HandWrittenNotes/till73/page-015.jpg" alt="till73 p015 handwritten note" width="960"></a>

Explanation: This page is mainly about Memory program practice, `CMA`, `INR`, and complement behavior. Use with `CMA`, `INR`, and complement questions. One's complement flips bits; adding one after complement forms two's complement. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Increment and BCD detail:** Increment/decrement instructions look simple but differ in flag behavior and operand size. `INR/DCR` act on an 8-bit register or memory byte and affect normal status flags except carry, while `INX/DCX` act on register pairs and are not ordinary 8-bit ALU flag examples. `DAA` is special because it corrects the accumulator after BCD addition using lower-nibble and carry conditions.

**Bit-level reading:** Logical pages should be traced bit by bit. `ANA`, `ORA`, and `XRA` combine corresponding bits of the accumulator and operand, while `CMA` flips every accumulator bit without doing subtraction. Whenever the page asks for flags, derive them from the final bit pattern rather than from the name of the instruction alone.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p016](images/HandWrittenNotes/till73/page-016.jpg)

<a href="images/HandWrittenNotes/till73/page-016.jpg"><img src="images/HandWrittenNotes/till73/page-016.jpg" alt="till73 p016 handwritten note" width="960"></a>

Explanation: This page is mainly about Stack/program trace and memory contents. Use with stack and program trace questions. Keep `SP`, memory bytes, and register-pair display separate. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Register reading:** Keep ordinary data registers separate from control registers. `B`, `C`, `D`, `E`, `H`, and `L` are 8-bit working registers, but pairs such as `BC`, `DE`, and `HL` are treated as 16-bit values in many instructions. `PC` points to the next instruction, while `SP` points into the stack, so changing either one changes program flow or stack behavior rather than just data.

**Stack tracking:** Always write `SP` before and after every stack operation. `PUSH` and `CALL` store bytes by moving the stack downward, while `POP` and `RET` read bytes and move `SP` upward. Keep high byte, low byte, register-pair names, and actual memory addresses separate so the stack diagram does not become ambiguous.

**Trace method:** For trace pages, make a row for every instruction. Update only the register, memory byte, flag, stack location, or program counter value that the instruction actually changes. This slower row-by-row method is the shortest reliable way to avoid losing track of `HL`, `SP`, carry, or memory contents.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p017](images/HandWrittenNotes/till73/page-017.jpg)

<a href="images/HandWrittenNotes/till73/page-017.jpg"><img src="images/HandWrittenNotes/till73/page-017.jpg" alt="till73 p017 handwritten note" width="960"></a>

Explanation: This page is mainly about Rotate instructions `RLC`, `RRC`, `RAL`, and `RAR`. Use with rotate questions. Compare `RLC/RRC` circular rotates with `RAL/RAR` rotates through carry. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Flag reasoning:** Do not revise flags as isolated definitions. First compute the 8-bit result, then ask whether the result is zero, whether bit 7 is set, whether parity is even, whether there was a carry from bit 3 to bit 4, and whether there was a carry or borrow out of the byte. This is especially important because in subtraction the carry flag represents borrow.

**Rotate discipline:** For rotate pages, draw the accumulator bits and the carry bit before each step. `RLC/RRC` rotate within the accumulator and copy the outgoing bit to carry, while `RAL/RAR` rotate through carry, so the old carry participates in the new accumulator value. Most wrong answers come from ignoring the initial carry.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p018](images/HandWrittenNotes/till73/page-018.jpg)

<a href="images/HandWrittenNotes/till73/page-018.jpg"><img src="images/HandWrittenNotes/till73/page-018.jpg" alt="till73 p018 handwritten note" width="960"></a>

Explanation: This page is mainly about `XCHG`, `DAD`, register pairs, `PC`, `SP`, and instruction cycles. Use with `XCHG`, `DAD`, `PC`, `SP`, and machine-cycle reasoning. It is a mixed trace sheet. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Register reading:** Keep ordinary data registers separate from control registers. `B`, `C`, `D`, `E`, `H`, and `L` are 8-bit working registers, but pairs such as `BC`, `DE`, and `HL` are treated as 16-bit values in many instructions. `PC` points to the next instruction, while `SP` points into the stack, so changing either one changes program flow or stack behavior rather than just data.

**Trace method:** For trace pages, make a row for every instruction. Update only the register, memory byte, flag, stack location, or program counter value that the instruction actually changes. This slower row-by-row method is the shortest reliable way to avoid losing track of `HL`, `SP`, carry, or memory contents.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p019](images/HandWrittenNotes/till73/page-019.jpg)

<a href="images/HandWrittenNotes/till73/page-019.jpg"><img src="images/HandWrittenNotes/till73/page-019.jpg" alt="till73 p019 handwritten note" width="960"></a>

Explanation: This page is mainly about Rotate trace and interrupt-related register examples. Use with repeated rotate examples. Write each accumulator state in binary; do not jump straight from hex to answer. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Core idea:** The page is describing the internal data path of the processor. The accumulator is the main working register, the ALU performs the operation, and the result is not only stored as data but also summarized through flags. Read this as a flow: operand enters, ALU operates, accumulator receives the result, and the flag register records the condition of that result.

**Rotate discipline:** For rotate pages, draw the accumulator bits and the carry bit before each step. `RLC/RRC` rotate within the accumulator and copy the outgoing bit to carry, while `RAL/RAR` rotate through carry, so the old carry participates in the new accumulator value. Most wrong answers come from ignoring the initial carry.

**Control flow:** Branch, call, and return pages are about the program counter. A conditional jump/call/return tests flags that were already set by previous instructions; it does not calculate the condition itself. `CALL` also stores a return address on the stack, while `RET` restores control by taking that address back from the stack.

**Interrupt logic:** For interrupt pages, keep four questions separate: which source requested service, whether the request can be masked, where the CPU jumps, and how the interrupted program returns. Vectored interrupts already imply the service address, while non-vectored handling needs extra information or an externally supplied instruction. Priority matters only when more than one request is active.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p020](images/HandWrittenNotes/till73/page-020.jpg)

<a href="images/HandWrittenNotes/till73/page-020.jpg"><img src="images/HandWrittenNotes/till73/page-020.jpg" alt="till73 p020 handwritten note" width="960"></a>

Explanation: This page is mainly about Delay loop and total T-state calculation. Use with delay-loop calculations. Separate per-iteration T-states from the final exit iteration. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Timing hierarchy:** Keep three levels separate: an instruction cycle is the complete execution of one instruction, a machine cycle is one bus operation inside that instruction, and a `T`-state is one clock period inside a machine cycle. When the page shows opcode fetch, memory read, memory write, I/O read, or I/O write, it is showing how the processor announces and performs one bus operation at a time.

**Delay calculation:** For delay-loop pages, count the repeated path and the final exit path separately. The conditional jump usually has one timing when taken and another when not taken, so the last iteration cannot blindly use the same total as the previous iterations. First compute one loop body, then multiply by the number of repetitions, then add entry or exit instructions.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p021](images/HandWrittenNotes/till73/page-021.jpg)

<a href="images/HandWrittenNotes/till73/page-021.jpg"><img src="images/HandWrittenNotes/till73/page-021.jpg" alt="till73 p021 handwritten note" width="960"></a>

Explanation: This page is mainly about Flags, interrupts, instruction timing, and direct addressing recap. Use with mixed assignment recaps: flags, interrupt meaning, instruction timing, and direct addressing. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Flag reasoning:** Do not revise flags as isolated definitions. First compute the 8-bit result, then ask whether the result is zero, whether bit 7 is set, whether parity is even, whether there was a carry from bit 3 to bit 4, and whether there was a carry or borrow out of the byte. This is especially important because in subtraction the carry flag represents borrow.

**Timing hierarchy:** Keep three levels separate: an instruction cycle is the complete execution of one instruction, a machine cycle is one bus operation inside that instruction, and a `T`-state is one clock period inside a machine cycle. When the page shows opcode fetch, memory read, memory write, I/O read, or I/O write, it is showing how the processor announces and performs one bus operation at a time.

**Addressing-mode test:** Every addressing-mode page can be solved by asking one question: where is the operand? It may be inside the instruction itself, inside a register, at the memory address written in the instruction, or at a memory address stored in a register pair. Once the operand source is clear, instruction length and machine-cycle count become much easier to justify.

**Interrupt logic:** For interrupt pages, keep four questions separate: which source requested service, whether the request can be masked, where the CPU jumps, and how the interrupted program returns. Vectored interrupts already imply the service address, while non-vectored handling needs extra information or an externally supplied instruction. Priority matters only when more than one request is active.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p022](images/HandWrittenNotes/till73/page-022.jpg)

<a href="images/HandWrittenNotes/till73/page-022.jpg"><img src="images/HandWrittenNotes/till73/page-022.jpg" alt="till73 p022 handwritten note" width="960"></a>

Explanation: This page is mainly about Memory block/address calculation and `DAD`. Use with memory-block and address-calculation questions. Convert sizes to hex before adding. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Memory selection:** For memory pages, divide the address lines into two jobs. Lower address lines select a location inside the chip, while higher address lines are decoded to generate chip select. This explains why capacity calculations, highest-address questions, and ROM/RAM interfacing all depend on counting address lines carefully.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p023](images/HandWrittenNotes/till73/page-023.jpg)

<a href="images/HandWrittenNotes/till73/page-023.jpg"><img src="images/HandWrittenNotes/till73/page-023.jpg" alt="till73 p023 handwritten note" width="960"></a>

Explanation: This page is mainly about `DAD`, `CMP`, `RET`, flag register, and vector/non-vectored interrupt notes. Use with `DAD`, `CMP`, `RET`, and interrupt-type questions. It is a final mixed concept page. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Flag reasoning:** Do not revise flags as isolated definitions. First compute the 8-bit result, then ask whether the result is zero, whether bit 7 is set, whether parity is even, whether there was a carry from bit 3 to bit 4, and whether there was a carry or borrow out of the byte. This is especially important because in subtraction the carry flag represents borrow.

**Arithmetic trace:** For subtraction and comparison, work in 8-bit arithmetic instead of only decimal intuition. A borrow sets carry, two's-complement form represents negative intermediate results, and `CMP` updates flags without storing a new value in the accumulator. This lets the same written work explain both the final result and the conditional jump decision.

**Control flow:** Branch, call, and return pages are about the program counter. A conditional jump/call/return tests flags that were already set by previous instructions; it does not calculate the condition itself. `CALL` also stores a return address on the stack, while `RET` restores control by taking that address back from the stack.

**Interrupt logic:** For interrupt pages, keep four questions separate: which source requested service, whether the request can be masked, where the CPU jumps, and how the interrupted program returns. Vectored interrupts already imply the service address, while non-vectored handling needs extra information or an externally supplied instruction. Priority matters only when more than one request is active.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [till73 p024](images/HandWrittenNotes/till73/page-024.jpg)

<a href="images/HandWrittenNotes/till73/page-024.jpg"><img src="images/HandWrittenNotes/till73/page-024.jpg" alt="till73 p024 handwritten note" width="960"></a>

Explanation: This page is mainly about `XRA`, `SUI`, `ANA`, final accumulator, and flags. Use with `XRA`, `SUI`, `ANA`, and flag tracing. Work in binary for logical instructions and in hex subtraction for `SUI`. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Core idea:** The page is describing the internal data path of the processor. The accumulator is the main working register, the ALU performs the operation, and the result is not only stored as data but also summarized through flags. Read this as a flow: operand enters, ALU operates, accumulator receives the result, and the flag register records the condition of that result.

**Flag reasoning:** Do not revise flags as isolated definitions. First compute the 8-bit result, then ask whether the result is zero, whether bit 7 is set, whether parity is even, whether there was a carry from bit 3 to bit 4, and whether there was a carry or borrow out of the byte. This is especially important because in subtraction the carry flag represents borrow.

**Arithmetic trace:** For subtraction and comparison, work in 8-bit arithmetic instead of only decimal intuition. A borrow sets carry, two's-complement form represents negative intermediate results, and `CMP` updates flags without storing a new value in the accumulator. This lets the same written work explain both the final result and the conditional jump decision.

**Bit-level reading:** Logical pages should be traced bit by bit. `ANA`, `ORA`, and `XRA` combine corresponding bits of the accumulator and operand, while `CMA` flips every accumulator bit without doing subtraction. Whenever the page asks for flags, derive them from the final bit pattern rather than from the name of the instruction alone.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p001](images/HandWrittenNotes/85completed/page-001.jpg)

<a href="images/HandWrittenNotes/85completed/page-001.jpg"><img src="images/HandWrittenNotes/85completed/page-001.jpg" alt="85completed p001 handwritten note" width="960"></a>

Explanation: This page is mainly about Loop/register trace, `ADD`, `MOV`, borrow/carry, and accumulator result. Use with loop/register traces and carry/borrow reasoning. It connects arithmetic results to flags. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Core idea:** The page is describing the internal data path of the processor. The accumulator is the main working register, the ALU performs the operation, and the result is not only stored as data but also summarized through flags. Read this as a flow: operand enters, ALU operates, accumulator receives the result, and the flag register records the condition of that result.

**Flag reasoning:** Do not revise flags as isolated definitions. First compute the 8-bit result, then ask whether the result is zero, whether bit 7 is set, whether parity is even, whether there was a carry from bit 3 to bit 4, and whether there was a carry or borrow out of the byte. This is especially important because in subtraction the carry flag represents borrow.

**Data movement:** For data-transfer instructions, separate the value being moved from the address used to reach it. `MVI` places immediate data, `MOV` transfers between registers or memory through `M`, `LDA/STA` use a direct 16-bit address, and `LHLD/SHLD` move the `HL` pair through consecutive memory locations. That distinction prevents confusing data bytes with address bytes.

**Arithmetic trace:** For subtraction and comparison, work in 8-bit arithmetic instead of only decimal intuition. A borrow sets carry, two's-complement form represents negative intermediate results, and `CMP` updates flags without storing a new value in the accumulator. This lets the same written work explain both the final result and the conditional jump decision.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p002](images/HandWrittenNotes/85completed/page-002.jpg)

<a href="images/HandWrittenNotes/85completed/page-002.jpg"><img src="images/HandWrittenNotes/85completed/page-002.jpg" alt="85completed p002 handwritten note" width="960"></a>

Explanation: This page is mainly about Delay loop T-state calculation and loop iteration count. Use with delay-loop screenshots. It shows how repeated loop counts multiply instruction T-states. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Timing hierarchy:** Keep three levels separate: an instruction cycle is the complete execution of one instruction, a machine cycle is one bus operation inside that instruction, and a `T`-state is one clock period inside a machine cycle. When the page shows opcode fetch, memory read, memory write, I/O read, or I/O write, it is showing how the processor announces and performs one bus operation at a time.

**Delay calculation:** For delay-loop pages, count the repeated path and the final exit path separately. The conditional jump usually has one timing when taken and another when not taken, so the last iteration cannot blindly use the same total as the previous iterations. First compute one loop body, then multiply by the number of repetitions, then add entry or exit instructions.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

### [85completed p003](images/HandWrittenNotes/85completed/page-003.jpg)

<a href="images/HandWrittenNotes/85completed/page-003.jpg"><img src="images/HandWrittenNotes/85completed/page-003.jpg" alt="85completed p003 handwritten note" width="960"></a>

Explanation: This page is mainly about Conditional output, flags, `CALL`, return address, and stack. Use with conditional-output and stack/CALL questions. It ties flags, branch decision, and stack return address together. Read the handwritten page as the primary source first: look at the headings, boxed terms, arrows, tables, and worked values before reading the explanation. The explanation below is meant to unpack the same page, not replace it.

**Flag reasoning:** Do not revise flags as isolated definitions. First compute the 8-bit result, then ask whether the result is zero, whether bit 7 is set, whether parity is even, whether there was a carry from bit 3 to bit 4, and whether there was a carry or borrow out of the byte. This is especially important because in subtraction the carry flag represents borrow.

**Control flow:** Branch, call, and return pages are about the program counter. A conditional jump/call/return tests flags that were already set by previous instructions; it does not calculate the condition itself. `CALL` also stores a return address on the stack, while `RET` restores control by taking that address back from the stack.

**Stack tracking:** Always write `SP` before and after every stack operation. `PUSH` and `CALL` store bytes by moving the stack downward, while `POP` and `RET` read bytes and move `SP` upward. Keep high byte, low byte, register-pair names, and actual memory addresses separate so the stack diagram does not become ambiguous.

How to connect it while revising: start from the exact topic named on the page, then connect it to the closest screenshot or day section. If the page contains a diagram, explain each label in the diagram. If it contains a program or numerical working, trace each instruction or calculation in order and write the changed register, flag, memory byte, address, or signal beside that step.

What to be careful about: do not reduce this page to one sentence. The useful revision value is in the relationships: which signal selects the operation, which register stores the value, which flag records the result, which address is being accessed, and which step happens next. When you can say those relationships aloud, the handwritten page has been understood deeply enough for exam questions.

## Deep Revision Notes

Day 6 is not a new theory chapter as much as it is a solving chapter. The screenshots ask you to combine concepts from earlier days: instruction length, flags, register pairs, stack order, delay loops, and conditional branch timing. The safest method is to build a small trace table for every question.

### 1. Rotate Questions

Use the rotate screenshots with [till73 p017](images/HandWrittenNotes/till73/page-017.jpg), [till73 p019](images/HandWrittenNotes/till73/page-019.jpg), and [Day 6 image 1](images/Day%206/day-6-rrc-rotate-accumulator-question.png).

The four rotate instructions split into two families:

| Instruction | Old bit moved to carry | What enters the emptied bit |
| --- | --- | --- |
| `RLC` | Old `D7` | Old `D7` also enters `D0`. |
| `RRC` | Old `D0` | Old `D0` also enters `D7`. |
| `RAL` | Old `D7` | Old carry enters `D0`. |
| `RAR` | Old `D0` | Old carry enters `D7`. |

The exam mistake is to treat all rotates as circular. Only `RLC` and `RRC` are circular inside the accumulator. `RAL` and `RAR` rotate through carry, so the carry flag is part of the data path.

For any rotate trace, write:

```text
A before = b7 b6 b5 b4 b3 b2 b1 b0
CY before = c
instruction
A after = ...
CY after = ...
```

Then convert the final binary value back to hex. If you skip the binary row, it is very easy to lose the old carry bit.

### 2. Delay Loop And T-State Questions

Use [Day 6 images 2-4](images/Day%206/day-6-delay-loop-tstates-program-question.png), [Day 6 images 17-21](images/Day%206/day-6-loop-tstates-total-question.png), [till73 p020](images/HandWrittenNotes/till73/page-020.jpg), and [85completed p002](images/HandWrittenNotes/85completed/page-002.jpg).

Timing questions usually need two separate steps:

```text
total delay = total T-states x T-state period
T-state period = 1 / clock frequency
```

The important hidden detail is the conditional jump. A conditional jump generally has a different T-state count when the jump is taken versus when it is not taken. In a loop, this means:

```text
total loop T-states =
  repeated iterations where jump is taken
+ final iteration where jump is not taken
```

For a loop like:

```asm
LOOP: DCR C
      JNZ LOOP
```

if `C` starts at `N`, the decrement happens `N` times. `JNZ` is taken `N - 1` times and not taken once. That final not-taken branch is why the last iteration is counted separately.

When nested loops appear, solve from inside outward:

1. Count one full inner-loop execution.
2. Multiply it by the outer loop count.
3. Add setup instructions that run only once.
4. Convert T-states to time using the clock period.

### 3. Register-Pair Trace Questions

Use [Day 6 images 5 and 9](images/Day%206/day-6-xchg-register-pair-program-question.png), [till73 p018](images/HandWrittenNotes/till73/page-018.jpg), and [85completed p001](images/HandWrittenNotes/85completed/page-001.jpg).

For register-pair questions, always track both the pair and the individual registers:

| Instruction | Important effect |
| --- | --- |
| `LXI rp,data16` | Loads a 16-bit immediate value into a register pair. |
| `INX rp` | Adds one to the full 16-bit register pair. |
| `DAD rp` | Adds the selected register pair to `HL`; the result remains in `HL`. |
| `XCHG` | Exchanges `HL` and `DE`; `BC` is not involved. |
| `MOV r1,r2` | Copies one 8-bit register to another; source is unchanged. |

The key rule for `DAD`:

```text
HL <- HL + selected register pair
```

So `DAD B` means `HL <- HL + BC`, not `BC <- BC + HL`. `DAD D` means `HL <- HL + DE`. `DAD H` doubles `HL`.

### 4. Stack, `CALL`, `RET`, `PUSH`, And `POP`

Use [Day 6 images 15-16](images/Day%206/day-6-stack-push-pop-register-trace-question.png), [till73 p016](images/HandWrittenNotes/till73/page-016.jpg), and [85completed p003](images/HandWrittenNotes/85completed/page-003.jpg).

The 8085 stack grows downward. That gives the following mental model:

| Operation | What happens to `SP` | Main data movement |
| --- | --- | --- |
| `PUSH rp` | `SP` decreases by 2 | Register pair goes to stack memory. |
| `POP rp` | `SP` increases by 2 | Two stack bytes come into register pair. |
| `CALL addr` | `SP` decreases by 2 | Return address is pushed, then `PC <- addr`. |
| `RET` | `SP` increases by 2 | Return address is popped into `PC`. |

For `PUSH PSW`, the pair is special:

```text
PSW = accumulator A + flag register
```

For `POP H`, the low byte from the stack goes to `L`, and the high byte goes to `H`. After that, the register pair is displayed as `HL`, high byte first.

The reliable stack-trace method:

1. Write the initial `SP`.
2. Draw two memory boxes below it for each push.
3. Store low and high bytes carefully.
4. Move `SP` only after accounting for the bytes.
5. For pop, read from the current `SP`, then increase `SP`.

### 5. Logical, Masking, And Complement Questions

Use [Day 6 images 11-14](images/Day%206/day-6-xra-sui-ana-flags-question.png), [till73 p015](images/HandWrittenNotes/till73/page-015.jpg), [till73 p024](images/HandWrittenNotes/till73/page-024.jpg), and [till47 p020](images/HandWrittenNotes/till47/page-020.jpg).

Logical instructions should be solved bit by bit:

| Instruction | Meaning | Common use |
| --- | --- | --- |
| `ANA data/r/M` | `A <- A AND operand` | Mask bits; keep only selected positions. |
| `ORA data/r/M` | `A <- A OR operand` | Set selected bits. |
| `XRA data/r/M` | `A <- A XOR operand` | Toggle bits; `XRA A` clears `A`. |
| `CMA` | `A <- one's complement of A` | Flip every bit. |
| `ANI data` | `A <- A AND immediate data` | Mask with a fixed byte. |

For high-order-bit masking, choose a mask with `1`s where bits must be preserved and `0`s where bits must be cleared. Example:

```text
Keep high nibble only:
A    = xxxx yyyy
mask = 1111 0000 = F0H
ANA/ANI F0H -> xxxx 0000
```

For complement questions, separate one's complement and two's complement:

```text
one's complement = flip all bits
two's complement = flip all bits, then add 1
```

### 6. `HLT`, Direct Addressing, And Instruction Timing

Use [Day 6 images 7-10 and 23](images/Day%206/day-6-maximum-tstates-instruction-question.png), [till73 p021](images/HandWrittenNotes/till73/page-021.jpg), and [till73 p022](images/HandWrittenNotes/till73/page-022.jpg).

`HLT` stops normal instruction execution after the processor enters the halt state. It does not mean the chip has no clock or power; it means the processor is waiting in a halted state until a valid reset or interrupt-style event changes execution.

For direct addressing:

```asm
STA 2050H
```

means the 16-bit address is part of the instruction. The accumulator is stored at memory address `2050H`. That is different from register-indirect addressing, where the instruction uses a register pair such as `HL` to hold the address.

For maximum T-state questions, do not guess from instruction name alone. Ask:

1. How many bytes must be fetched?
2. Does it read or write memory?
3. Does it push or pop stack bytes?
4. Does it change `PC` through call/jump/return?
5. Does a conditional branch take the branch or fall through?

That is why stack and call instructions often take more time than simple register operations: they need extra memory cycles.

## Points To Remember

- `CALL` stores the return address on the stack; `RET` restores it by popping the address back.
- `RRC` rotates accumulator bit `D0` into both `D7` and carry.
- `DAD` always updates `HL`, regardless of the source register pair.
- In subtraction, carry means borrow.
- In loop timing, the last conditional jump may use a different T-state count.

## Sources

[S1] Intel Corporation, [MCS-80/85 Family User's Manual, January 1983](https://www.bitsavers.org/components/intel/MCS80/MCS80_85_Users_Manual_Jan83.pdf). Used for instruction timing, machine cycles, stack behavior, register-pair operations, interrupts, flags, and `HLT`.

[S2] Intel Corporation, [8080/8085 Assembly Language Programming Manual, May 1981](https://www.bitsavers.org/pdf/intel/ISIS_II/9800301-04_8080_8085_Assembly_Language_Programming_Manual_May81.pdf). Used for instruction meanings, rotate behavior, logical operations, branch conditions, and programming notation.
