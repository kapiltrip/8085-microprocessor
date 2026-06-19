# Day 15: 8051 Serial Port, 8096 Overview, And 8051 Instruction Practice

Day 15 continues the 8051 section from Day 14. It starts with serial communication: why the 8051 serial port exists, how it converts data between CPU-parallel form and pin-serial form, how `SBUF` behaves as separate transmit/receive buffers, how the block diagram connects `TXD` and `RXD`, and why `SCON` plus baud-rate generation controls actual serial operation. The later screenshots extend the same day into practical transmit/receive loops, a short Intel 8096/MCS-96 overview, 8051 architecture MCQs, and 8051 instruction-set revision covering data movement, arithmetic, logic, rotate, branch, call, return, stack, and flag behavior.

Use these checks while studying this day:

```text
1. Separate CPU data movement from bit-by-bit serial shifting.
2. Remember that software sees SBUF at 99H, but transmit and receive are separate internal buffers.
3. Writing SBUF starts transmit-side work; reading SBUF gets received data.
4. TXD is P3.1 and RXD is P3.0.
5. SCON at 98H is bit-addressable and mixes control bits with status flags.
6. Serial status can be polled by software or used to request an interrupt.
7. Baud rate means bit rate, not byte rate.
8. Some serial modes use a fixed clock, while variable baud-rate modes commonly use Timer 1.
9. For common Mode 1 serial programs, `TMOD=20H`, a suitable `TH1` reload, `SCON=50H`, and `TR1=1` are the core setup chain.
10. For MCQs, separate the course answer from the architecture reason: pin count, memory size, SFR behavior, stack reset value, and flag rules all come from fixed 8051 hardware definitions.
11. For instruction-set slides, always identify the real destination, source address space, and flag effect before memorizing the mnemonic.
```

## Handwritten Notes Linked To Day 15

These handwritten pages continue directly from the Day 14 8051 notebook pages. The first pages finish timer/counter control because Timer 1 is the normal source for many 8051 serial baud-rate examples. The middle pages match the Day 15 serial-port screenshots: `SBUF`, `SCON`, serial modes, `SMOD`, baud-rate generation, and transmit/receive polling. The last pages collect MCQs and 8051 instruction-set practice, so they belong with the Day 15 screenshot set instead of being left as a separate PDF.

### [8051 serial handwritten p001](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-001.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-001.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-001.jpg" alt="8051 handwritten timer counter C T GATE TR and mode 0 page 001" width="960"></a>

This page finishes the `TMOD` control ideas needed before serial baud-rate study. `C/T=0` makes the unit a timer that counts the internal machine-cycle clock; `C/T=1` makes it a counter that counts external transitions on the timer input pin. `GATE=0` lets software control running through `TR0` or `TR1`; `GATE=1` adds the external interrupt pin as a hardware enable condition, so software run control is no longer the only condition.

The Mode 0 note is a useful correction anchor: classic 8051 Mode 0 is a 13-bit timer mode kept for compatibility with the 8048 family. Only the lower five bits of `TLx` participate with `THx`, so the count range is not a normal 16-bit `0000H` to `FFFFH` range. This distinction matters because later serial baud-rate work usually uses Timer 1 Mode 2, not Mode 0.

### [8051 serial handwritten p002](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-002.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-002.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-002.jpg" alt="8051 handwritten timer modes 1 2 3 auto reload split timer page 002" width="960"></a>

This page compares the timer modes. Mode 1 is the simple 16-bit mode: `THx` and `TLx` are cascaded, the timer counts up to `FFFFH`, then rollover to `0000H` sets the overflow flag. Mode 2 is the 8-bit auto-reload mode: `TLx` is the active counter and `THx` stores the reload value that is copied back into `TLx` after overflow.

Mode 3 is the special split-timer mode. It splits Timer 0 into two separate 8-bit timers using `TL0` and `TH0`; Timer 0 effectively consumes both timer overflow/run-control resources. That is why Timer 1 behaves unusually when Timer 0 is in Mode 3: Timer 1 can still be useful, especially for serial baud generation, but it does not operate with the ordinary independent `TF1` interrupt pattern in the same way.

### [8051 serial handwritten p003](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-003.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-003.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-003.jpg" alt="8051 handwritten TCON flags run bits and external interrupt bits page 003" width="960"></a>

This page maps `TCON` as `TF1 TR1 TF0 TR0 IE1 IT1 IE0 IT0`. The upper half is timer-oriented: `TF1` and `TF0` are overflow flags, while `TR1` and `TR0` start or stop Timer 1 and Timer 0. The lower half belongs to external interrupt control: `IE1/IE0` are external interrupt flags and `IT1/IT0` select edge-triggered versus level-triggered operation.

The important reasoning step is to separate "run control" from "overflow event." Setting `TR1=1` only permits Timer 1 to run; it does not mean a delay or baud tick has already completed. `TF1=1` is the event produced after rollover. That same event can be polled in delay loops, used for interrupts, or used internally as part of serial baud-rate generation.

### [8051 serial handwritten p004](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-004.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-004.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-004.jpg" alt="8051 handwritten timer mode 1 delay polling and JNB TFx page 004" width="960"></a>

This page turns timer hardware into a delay algorithm. Load the initial count into `THx/TLx`, start the timer by setting `TRx`, wait while the overflow flag is not set, then stop or clear/reload according to the loop design. The note `JNB TFx` is the polling form: stay in the wait loop until the timer flag becomes `1`.

The page also explains why repeated delays require deliberate reload and flag handling. In Mode 1, the timer rolls over from `FFFFH` to `0000H`; if software wants the same delay again, it must reload the original starting count and clear `TFx`. In Mode 2 auto-reload, `THx` reloads `TLx` automatically, which is exactly why Mode 2 is convenient for repeated overflow timing and serial baud-rate generation.

### [8051 serial handwritten p005](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-005.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-005.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-005.jpg" alt="8051 handwritten serial port SBUF SCON baud clock page 005" width="960"></a>

This page introduces the serial port as a hardware conversion block. The CPU handles bytes in parallel, but `TXD` and `RXD` carry bits serially. The serial block therefore needs transmit shifting, receive shifting, a baud clock, and programmer-visible control/status bits.

`SBUF` is the key software trap on this page. The address `99H` names the serial buffer SFR, but internally there are two buffers behind that name: a transmit buffer loaded by writing `SBUF`, and a receive buffer read by reading `SBUF`. `SCON` at `98H` configures the mode and reports `TI/RI` status. Timer 1 is shown because variable baud-rate modes commonly derive their bit timing from Timer 1 overflow.

### [8051 serial handwritten p006](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-006.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-006.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-006.jpg" alt="8051 handwritten SCON bits serial flags and modes page 006" width="960"></a>

This page expands `SCON`. `SM0` and `SM1` select the serial mode, `SM2` supports multiprocessor filtering in selected modes, `REN` enables reception, `TB8` and `RB8` carry or store the ninth data bit in 9-bit modes, and `TI/RI` are transmit and receive flags. It is both a control register and a status register.

The practical study rule is that `TI` and `RI` must be cleared by software after service. Hardware sets `TI` when a transmit event completes and sets `RI` when a receive event completes. If code polls those flags but never clears them, the next loop iteration may falsely think a new event happened.

### [8051 serial handwritten p007](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-007.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-007.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-007.jpg" alt="8051 handwritten serial modes mode 0 mode 1 mode 2 mode 3 page 007" width="960"></a>

This page classifies the four serial modes. Mode 0 is an 8-bit shift-register mode with a fixed oscillator-derived rate. Mode 1 is the standard 8-bit UART mode with start bit, eight data bits, and stop bit, and it normally uses variable baud timing. Modes 2 and 3 are 9-bit UART modes; Mode 2 uses a fixed oscillator-derived baud rate and Mode 3 uses variable baud timing.

The important correction is that "serial mode" includes both frame format and baud source. Mode 1 and Mode 3 are often tied to Timer 1 because their baud rate is programmable. Mode 2 has the extra ninth bit but fixed baud options. Mode 0 does not use ordinary asynchronous start/stop framing; it behaves like a shift-register transfer.

### [8051 serial handwritten p008](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-008.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-008.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-008.jpg" alt="8051 handwritten baud rate timer 1 SMOD calculation page 008" width="960"></a>

This page starts baud-rate calculation. For the classic 8051, the machine-cycle rate is commonly `fosc/12`; with an `11.0592 MHz` crystal that gives `921.6 kHz`. The serial baud path then divides timer overflow timing further depending on serial mode and `SMOD`.

The page matters because the TH1 reload value is not arbitrary. In Timer 1 Mode 2, `TH1` determines how often `TL1` overflows, and the serial port derives baud from that overflow frequency. The common textbook values work cleanly with `11.0592 MHz` because that crystal divides neatly into standard UART baud rates.

### [8051 serial handwritten p009](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-009.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-009.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-009.jpg" alt="8051 handwritten PCON SMOD baud rate doubling page 009" width="960"></a>

This page ties `PCON` to serial timing. `SMOD` is `PCON.7`; when set, it doubles the supported serial baud rate in the relevant modes. After reset, `SMOD=0`, so do not assume doubled baud unless software explicitly sets the bit.

The fixed-rate modes and variable-rate modes should be kept separate. Mode 0 has a fixed shift rate tied to the oscillator. Mode 2 has oscillator-derived fixed rates, commonly described as `fosc/64` or doubled to `fosc/32` through `SMOD`. Mode 1 and Mode 3 use Timer 1-derived baud in common setups, so `TH1`, `TR1`, and `SMOD` all belong in the calculation.

### [8051 serial handwritten p010](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-010.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-010.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-010.jpg" alt="8051 handwritten TH1 reload baud rate examples page 010" width="960"></a>

This page continues the Timer 1 baud-rate chain. The reload value in `TH1` controls how many counts occur before overflow, and overflow rate feeds the serial divider. Values such as `FDH`, `FAH`, `F4H`, and `E8H` are negative reload offsets in 8-bit arithmetic: `FDH` means `-3`, `FAH` means `-6`, `F4H` means `-12`, and `E8H` means `-24`.

The clean way to revise these examples is to write three stages: oscillator to machine-cycle clock, Timer 1 overflow frequency from `TH1`, then serial divider plus `SMOD`. If one stage is skipped, baud questions become memorized table entries instead of a repeatable calculation.

### [8051 serial handwritten p011](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-011.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-011.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-011.jpg" alt="8051 handwritten SM2 multiprocessor communication PCON bits page 011" width="960"></a>

This page explains the ninth-bit and multiprocessor use case. In modes 2 and 3, a transmitted frame has an extra bit from `TB8`, and a received frame stores that bit in `RB8`. `SM2` can use that bit to decide whether received frames should raise service attention, which is useful when one master sends address frames and data frames to multiple slaves.

The `PCON` sketch reinforces that `SMOD` is the top bit and that the lower power-control bits are a different topic. For serial study, focus on the fact that `SMOD` doubles baud in the supported modes while `SM2` controls receive filtering behavior in multiprocessor serial communication.

### [8051 serial handwritten p012](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-012.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-012.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-012.jpg" alt="8051 handwritten serial receive timing and RI page 012" width="960"></a>

This page turns receive hardware into a sequence. A falling edge on `RXD` marks the start bit, the receiver samples each data bit at the baud-rate timing, the stop bit is checked, and then the received byte becomes available through receive `SBUF`. At that point `RI` is set.

The safest program pattern is therefore `wait for RI`, `read SBUF`, `store the byte somewhere safe`, and `clear RI`. The order matters because the receive buffer is small. If software waits too long and another character arrives, the older byte can be lost or overwritten depending on timing and device behavior.

### [8051 serial handwritten p013](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-013.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-013.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-013.jpg" alt="8051 handwritten serial transmit and receive polling steps page 013" width="960"></a>

This page pairs transmit and receive programming. For transmit, the byte must be placed in `SBUF`; the hardware adds the start bit, shifts out the data bits through `TXD`, sends the stop bit, and sets `TI`. Software then clears `TI` before using the flag for the next byte.

For receive, `RXD` supplies the incoming serial frame and `RI` tells software that a full character has arrived. The page is important because both directions share the name `SBUF` but not the same internal buffer. Writing `SBUF` is transmit-side work; reading `SBUF` is receive-side work.

### [8051 serial handwritten p014](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-014.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-014.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-014.jpg" alt="8051 handwritten receive via RXD 8096 features and 8051 MCQs page 014" width="960"></a>

This page mixes a serial receive recap, the 8096 comparison, and 8051 MCQ facts. The receive recap is the same UART framing chain: start bit first, 8-bit character one bit at a time, stop bit, then `RI=1`. That is why serial receive code must watch `RI` rather than checking `RXD` directly for a whole byte.

The 8096 notes should be studied as a family comparison. 8051 is the 8-bit MCS-51 controller with 40-pin classic package facts, `DPTR`, SFRs, ports, timers, and bit-addressable control style. 8096 belongs to the 16-bit MCS-96 line, with control-oriented features such as higher-speed math, ADC/PWM support, richer I/O, and full-duplex serial support.

### [8051 serial handwritten p015](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-015.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-015.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-015.jpg" alt="8051 handwritten reset values MCQs clock calculation SBUF transmit page 015" width="960"></a>

This page collects high-yield MCQ anchors. After reset, the classic stack pointer is `07H`, Port 1 latch reads as `FFH`, and `PSW=00H` selects Bank 0 through `RS1 RS0 = 00`. SFR address facts also appear: `P0` is `80H`, `TCON` is `88H`, `SCON` is `98H`, and `SBUF` is `99H`.

The clock example is useful because it ties timing to the oscillator. With `11.0592 MHz`, the classic machine-cycle clock is `fosc/12 = 921.6 kHz`, so one machine cycle is about `1.085 us`. The page also notes that transmitting a byte through `TXD` starts by loading `SBUF`, because software writes the transmit buffer and the serial hardware performs the bit-by-bit output.

### [8051 serial handwritten p016](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-016.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-016.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-016.jpg" alt="8051 handwritten MOV MOVC MOVX external memory page 016" width="960"></a>

This page begins the instruction-set revision. Plain `MOV dest,source` moves bytes inside allowed 8051 internal data/SFR/register forms and normally does not change flags unless the destination is a flag or carry bit in a bit-specific form. The note "both destination and source must be in internal RAM" should be read as a simplified class rule for ordinary byte `MOV`; SFRs and registers are also part of the directly accessible internal data space.

`MOVC` and `MOVX` are the real distinction. `MOVC A,@A+DPTR` or `MOVC A,@A+PC` reads code memory into `A`, so it is for lookup tables stored in program space. `MOVX` accesses external data memory (`XRAM`) through `@DPTR` or `@R0/@R1`; external cycles use Port 0, Port 2, and `ALE`/read-write signals depending on the addressing form.

### [8051 serial handwritten p017](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-017.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-017.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-017.jpg" alt="8051 handwritten SWAP XCH PUSH POP ADD ADDC SUBB page 017" width="960"></a>

This page groups accumulator-centered operations. `SWAP A` exchanges the high and low nibbles inside `A`; it does not move bytes between registers. `XCH A,operand` exchanges the accumulator with an allowed byte operand, so after the instruction both locations have changed.

The stack notes must be corrected to the 8051's 8-bit internal stack model: `PUSH direct` increments `SP` first, then stores the byte into internal RAM at the new `SP` address; `POP direct` reads from the current stack location, then decrements `SP`. `ADD`, `ADDC`, `SUBB`, and the later arithmetic pages are accumulator-centered: the result goes to `A`, and flags such as `CY`, `AC`, `OV`, and `P` must be derived from the operation.

### [8051 serial handwritten p018](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-018.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-018.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-018.jpg" alt="8051 handwritten MUL DIV INC DEC logic rotate jump page 018" width="960"></a>

This page continues arithmetic and logic. `MUL AB` produces a 16-bit product with low byte in `A` and high byte in `B`. `DIV AB` places quotient in `A` and remainder in `B`; valid division clears carry and overflow, while divide-by-zero sets overflow. `INC` and `DEC` modify the target but do not behave like 8085-style flag-updating arithmetic for carry.

The logic and rotate entries should be learned by destination. `ANL`, `ORL`, and `XRL` perform bitwise masking/combining. `RL` and `RR` rotate inside `A`; `RLC` and `RRC` rotate through carry. `JMP @A+DPTR` is indexed code flow, not data movement: the target address is formed by adding `A` to `DPTR`.

### [8051 serial handwritten p019](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-019.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-019.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-019.jpg" alt="8051 handwritten jump branch DJNZ JZ JNZ SJMP AJMP page 019" width="960"></a>

This page is the control-transfer map. `JC` branches on carry. `DJNZ` decrements first and jumps if the result is not zero. `JZ` and `JNZ` test the accumulator value directly, which is important because the 8051 does not use an 8085-style zero flag in the same way.

The range rules matter. `SJMP` is a relative short jump, so it reaches only nearby code. `AJMP` is an absolute jump limited to the same 2 KB code block. `LJMP` is the full long jump form, though the page mostly focuses on the shorter exam forms. For every branch mnemonic, identify both the condition and the address range.

### [8051 serial handwritten p020](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-020.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-020.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-020.jpg" alt="8051 handwritten AJMP 11 bit same 2 KB block page 020" width="960"></a>

This page focuses on `AJMP`. The effective destination uses an 11-bit target field from the instruction, while the upper bits come from the current program-counter region. That is why `AJMP` can only jump within the same 2 KB code block.

The practical rule is that `AJMP` is compact but local. It is not the same as `SJMP`, which is relative to the current PC, and it is not the same as `LJMP`, which can reach the full 64 KB code space. If an exam question gives a far target outside the current 2 KB block, `AJMP` is not the correct form.

### [8051 serial handwritten p021](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-021.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-021.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-021.jpg" alt="8051 handwritten LCALL ACALL RET RETI stack page 021" width="960"></a>

This page connects calls to stack behavior. `LCALL` can call anywhere in the 64 KB code space and pushes the return address before loading the new PC. `ACALL` is shorter but limited to the same 2 KB block rule as `AJMP`. In both cases, return depends on the saved PC bytes on the stack.

`RET` pops the saved program counter and resumes the caller. `RETI` also pops the saved program counter, but it additionally tells the interrupt controller that interrupt service has completed. Use `RET` for normal subroutines and `RETI` for interrupt service routines; using the wrong one can leave interrupt priority/service state incorrect.

### [8051 serial handwritten p022](images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-022.jpg)

<a href="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-022.jpg"><img src="images/HandWrittenNotes/8051-serial-port-programming-instruction-set-mcqs/page-022.jpg" alt="8051 handwritten final instruction MCQs CJNE flags logical operations page 022" width="960"></a>

This last page is a quick MCQ drill. It revises operand rules such as accumulator destination for arithmetic, indirect access through `@R0/@R1`, and the distinction between immediate data and an address. When a question gives a mnemonic, first identify the destination and address space before choosing the answer.

The flag notes are the main depth point. `CJNE` branches when operands are not equal and sets carry when the first operand is less than the second in unsigned comparison. Logical byte operations usually do not update the PSW math flags. `DA A` is only meaningful after BCD addition. These are the kinds of details that prevent MCQ answers from being chosen by mnemonic memory alone.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [Serial port operation overview](images/Day%2015/Screenshot%202026-06-19%20135930.png) | The 8051 serial port converts parallel CPU data to serial output and serial input to parallel CPU data. |
| 2 | [SBUF and SCON software access](images/Day%2015/Screenshot%202026-06-19%20140057.png) | `SBUF` at `99H` is two internal buffers: write for transmit, read for receive. |
| 3 | [Serial port block diagram](images/Day%2015/Screenshot%202026-06-19%20141111.png) | Transmit uses parallel-in serial-out flow to `TXD`; receive uses serial-in parallel-out flow from `RXD`. |
| 4 | [SCON and baud-rate control](images/Day%2015/Screenshot%202026-06-19%20141140.png) | `SCON` at `98H` controls serial mode/status, and Timer 1 can supply variable baud-rate clocking. |
| 5 | [SCON bit layout](images/Day%2015/Screenshot%202026-06-19%20141838.png) | `SCON = SM0 SM1 SM2 REN TB8 RB8 TI RI`; mode bits, receive enable, ninth-bit fields, and serial flags. |
| 6 | [SCON simple-use note](images/Day%2015/Screenshot%202026-06-19%20142407.png) | For ordinary serial communication, keep `SM2`, `TB8`, and `RB8` cleared unless using multiprocessor or 9-bit features. |
| 7 | [Receiver enable initialization](images/Day%2015/Screenshot%202026-06-19%20142934.png) | `REN` must be set to receive characters; `MOV SCON,#10H` sets `REN` in a clean byte, while `SETB REN` sets only that bit. |
| 8 | [Serial interrupt flags](images/Day%2015/Screenshot%202026-06-19%20143037.png) | `RI` marks receive-buffer full; `TI` marks transmit-buffer empty/ready; hardware sets both and software clears them. |
| 9 | [Serial modes of operation](images/Day%2015/Screenshot%202026-06-19%20143340.png) | `SM0/SM1` select four serial modes; three are asynchronous framed modes and one is shift-register mode. |
| 10 | [Mode 0: 8-bit shift register](images/Day%2015/Screenshot%202026-06-19%20143616.png) | Mode 0 uses 8-bit shift-register operation, LSB first, with `RXD` as data and `TXD` as shift clock. |
| 11 | [Mode 1: 8-bit UART variable baud](images/Day%2015/Screenshot%202026-06-19%20144240.png) | Mode 1 sends a 10-bit frame: start bit, eight data bits LSB first, and stop bit, with baud rate tied to Timer 1 overflow. |
| 12 | [Mode 1 transmit and receive timing](images/Day%2015/Screenshot%202026-06-19%20145103.png) | Mode 1 bit period is set by baud rate; `TI` is set at the stop bit, and receive timing aligns after a `1`-to-`0` start transition on `RXD`. |
| 13 | [Mode 1 receive sampling and false start detection](images/Day%2015/Screenshot%202026-06-19%20145830.png) | The receiver samples near the middle of the bit time and rejects noise if the start bit is not still low after the validation count. |
| 14 | [Mode 1 receive completion sequence](images/Day%2015/Screenshot%202026-06-19%20150358.png) | On valid reception, the stop bit is stored in `RB8`, the eight data bits load receive `SBUF`, and `RI` is set. |
| 15 | [Mode 2: 9-bit UART fixed baud](images/Day%2015/Screenshot%202026-06-19%20150953.png) | Mode 2 sends an 11-bit frame with a programmable ninth bit; `TB8` supplies it on transmit and `RB8` stores it on receive. |
| 16 | [Mode 2 clean view](images/Day%2015/Screenshot%202026-06-19%20151141.png) | Cleaner/cropped view of Mode 2 fixed-baud behavior, ninth-bit handling, and oscillator-derived baud-rate choices. |
| 17 | [Mode 3: 9-bit UART variable baud](images/Day%2015/Screenshot%202026-06-19%20151657.png) | Mode 3 is like Mode 2's 9-bit UART frame, but its baud rate is programmable and timer-provided. |
| 18 | [Multiprocessor communications](images/Day%2015/Screenshot%202026-06-19%20152848.png) | In modes 2 and 3, `SM2` can use the ninth bit in `RB8` to filter which received frames raise the serial interrupt. |
| 19 | [Fixed baud rates in modes 0 and 2](images/Day%2015/Screenshot%202026-06-19%20153807.png) | Mode 0 baud is `fosc/12`; Mode 2 defaults to `fosc/64` after reset, with oscillator-derived fixed timing. |
| 20 | [PCON effect on baud rate](images/Day%2015/Screenshot%202026-06-19%20153942.png) | Baud rate can be increased by using a higher oscillator frequency or by changing the baud-rate bit in `PCON`. |
| 21 | [PCON register and SMOD](images/Day%2015/Screenshot%202026-06-19%20154147.png) | `PCON` is an 8-bit register; `SMOD` resets to `0` and can be set by software to double baud rate. |
| 22 | [SMOD baud-rate doubling path](images/Day%2015/Screenshot%202026-06-19%20154527.png) | `SMOD` is `PCON.7`; setting it doubles baud rate in modes 1, 2, and 3, including Mode 2 `fosc/64` to `fosc/32`. |
| 23 | [TH1 reload baud-rate comparison](images/Day%2015/Screenshot%202026-06-19%20154541.png) | Common `TH1` reload values show how `SMOD=1` doubles the baud rate compared with `SMOD=0`. |
| 24 | [SMOD divider diagram](images/Day%2015/Screenshot%202026-06-19%20154610.png) | With an 11.0592 MHz crystal, the timing path shows `SMOD=1` using divide-by-16 and `SMOD=0` using divide-by-32. |
| 25 | [Mode 1 serial programming setup](images/Day%2015/Screenshot%202026-06-19%20155615.png) | A practical setup sequence: `TMOD=20H` puts Timer 1 in Mode 2 auto-reload, `TH1` selects baud, `SCON=50H` selects Mode 1 with receive enabled, and `TR1=1` starts Timer 1. |
| 26 | [Mode 1 setup clean view](images/Day%2015/Screenshot%202026-06-19%20155809.png) | Cleaner view of the same Mode 1 programming checklist: Timer 1 mode setup, `TH1` reload, `SCON=50H`, then `TR1`. |
| 27 | [Transmit loop continuation](images/Day%2015/Screenshot%202026-06-19%20160842.png) | `CLR TI`, write the next byte to `SBUF`, poll `TI`, then repeat for the next character. |
| 28 | [TXD transmit frame steps](images/Day%2015/Screenshot%202026-06-19%20161013.png) | The byte written to `SBUF` becomes a framed serial stream: start bit, data bits, stop bit, then `TI`. |
| 29 | [TI and SBUF overwrite caution](images/Day%2015/Screenshot%202026-06-19%20161504.png) | Polling `TI` prevents software from loading a new byte before the previous byte has finished shifting. |
| 30 | [Receive loop continuation](images/Day%2015/Screenshot%202026-06-19%20162237.png) | Poll `RI`, move received `SBUF` data to a safe location, then wait for the next received character. |
| 31 | [RXD receive frame steps](images/Day%2015/Screenshot%202026-06-19%20162516.png) | Reception detects a start bit, samples eight data bits, accepts the stop bit, and sets `RI`. |
| 32 | [RXD receive frame clean view](images/Day%2015/Screenshot%202026-06-19%20162941.png) | Cleaner view of the receive-side frame sequence and the need to read `SBUF` before overwrite. |
| 33 | [8096 architectural features](images/Day%2015/Screenshot%202026-06-19%20163026.png) | Intel 8096 is introduced as a high-performance 16-bit MCS-96-family microcontroller. |
| 34 | [8096 applications and modes](images/Day%2015/Screenshot%202026-06-19%20163452.png) | 8096 targets motor/control applications, includes ADC capability, and can operate in single-chip or expanded mode. |
| 35 | [8096 memory, ADC, and ports](images/Day%2015/Screenshot%202026-06-19%20163538.png) | 8096 notes on single-chip/expanded operation, multiplexed bus, on-chip RAM, ADC, I/O ports, and arithmetic. |
| 36 | [8096 PWM, serial, interrupts, timers](images/Day%2015/Screenshot%202026-06-19%20163803.png) | 8096 has PWM, full-duplex serial support, interrupt sources, and 16-bit timers. |
| 37 | [MCQ: 8051 pin count](images/Day%2015/Screenshot%202026-06-19%20164259.png) | Classic 8051 package answer: 40 pins. |
| 38 | [MCQ: 8051 address bus](images/Day%2015/Screenshot%202026-06-19%20164305.png) | 8051 can address 64 KB, so the address bus is treated as 16-bit and unidirectional. |
| 39 | [MCQ: pin count clean view](images/Day%2015/Screenshot%202026-06-19%20164319.png) | Repeat/clean view of the 40-pin 8051 answer. |
| 40 | [MCQ: TMOD and PCON](images/Day%2015/Screenshot%202026-06-19%20164327.png) | `TMOD` and `PCON` are control registers, not serial data buffers. |
| 41 | [MCQ: internal RAM and ROM](images/Day%2015/Screenshot%202026-06-19%20164337.png) | Classic 8051 has 128 bytes internal RAM and 4 KB internal program ROM. |
| 42 | [MCQ: DPTR meaning](images/Day%2015/Screenshot%202026-06-19%20164414.png) | `DPTR` means Data Pointer. |
| 43 | [MCQ: number of SFRs](images/Day%2015/Screenshot%202026-06-19%20164417.png) | Common classic-8051 exam count: 21 special function registers. |
| 44 | [MCQ: interrupt priority levels](images/Day%2015/Screenshot%202026-06-19%20164432.png) | Classic 8051 interrupt priority is commonly taught as two levels: low and high. |
| 45 | [MCQ: 8051 bit width](images/Day%2015/Screenshot%202026-06-19%20164506.png) | 8051 is an 8-bit microcontroller. |
| 46 | [MCQ: multifunctional port](images/Day%2015/Screenshot%202026-06-19%20164520.png) | Port 3 is multifunctional because its pins have serial, interrupt, timer, and external-memory alternate functions. |
| 47 | [MCQ: number of 8-bit ports](images/Day%2015/Screenshot%202026-06-19%20164638.png) | 8051 has four 8-bit ports: P0, P1, P2, and P3. |
| 48 | [MCQ: flags are in PSW](images/Day%2015/Screenshot%202026-06-19%20164644.png) | CPU flags are stored in the Program Status Word. |
| 49 | [MCQ: delay-loop filler](images/Day%2015/Screenshot%202026-06-19%20164649.png) | `NOP` wastes one instruction slot without changing machine state. |
| 50 | [MCQ: unconditional jump](images/Day%2015/Screenshot%202026-06-19%20164702.png) | `SJMP` is an unconditional short jump; the slide option appears to contain a spelling error. |
| 51 | [MCQ: reset interrupt priority](images/Day%2015/Screenshot%202026-06-19%20164729.png) | In the default polling order, external interrupt 0 has the highest priority among the listed sources. |
| 52 | [MCQ: reset interrupt priority clean view](images/Day%2015/Screenshot%202026-06-19%20164810.png) | Repeat/clean view of the external interrupt 0 priority answer. |
| 53 | [MCQ: code memory limit](images/Day%2015/Screenshot%202026-06-19%20164822.png) | 8051-family code/program memory access is limited to 64 KB in the classic architecture. |
| 54 | [MCQ: code memory limit clean view](images/Day%2015/Screenshot%202026-06-19%20164844.png) | Repeat/clean view of the 64 KB program-memory answer. |
| 55 | [MCQ: two 16-bit registers](images/Day%2015/Screenshot%202026-06-19%20164900.png) | The usual teaching answer is `PC` and `DPTR`; note that `PC` is not a directly addressed SFR. |
| 56 | [MCQ: 16-bit registers clean view](images/Day%2015/Screenshot%202026-06-19%20164912.png) | Repeat/clean view of the `PC` and `DPTR` answer. |
| 57 | [MCQ: reset state](images/Day%2015/Screenshot%202026-06-19%20165015.png) | Reset gives `SP=07H`, port latches high such as `P1=FFH`, and `RS1 RS0=00`. |
| 58 | [MCQ: invalid MOV form](images/Day%2015/Screenshot%202026-06-19%20165222.png) | `MOV 80H,92H` is invalid as byte-direct transfer because `92H` is a bit address, not an implemented byte SFR. |
| 59 | [MCQ: invalid MOV annotated](images/Day%2015/Screenshot%202026-06-19%20165639.png) | Annotated view of the invalid `MOV 80H,92H` choice and the SFR/bit-address distinction. |
| 60 | [MCQ: Mode 1 serial communication](images/Day%2015/Screenshot%202026-06-19%20165748.png) | 8051 serial Mode 1 is the standard 8-bit UART mode with variable baud. |
| 61 | [MCQ: power-up stack pointer](images/Day%2015/Screenshot%202026-06-19%20165802.png) | On reset, `SP=07H`; the first pushed byte goes to `08H`. |
| 62 | [MCQ: TXD data source](images/Day%2015/Screenshot%202026-06-19%20175327.png) | To transmit through `TXD`, software writes the byte to `SBUF`. |
| 63 | [MCQ: 11.0592 MHz timer clock](images/Day%2015/Screenshot%202026-06-19%20175332.png) | With classic 12-clock timing, 11.0592 MHz gives 921.6 kHz machine-cycle frequency and about 1.085 microseconds period. |
| 64 | [MCQ: math flags count](images/Day%2015/Screenshot%202026-06-19%20175550.png) | The commonly tested arithmetic flags are `CY`, `AC`, `OV`, and `P`: four flags. |
| 65 | [MCQ: bit set/reset mode](images/Day%2015/Screenshot%202026-06-19%20175658.png) | Bit set/reset mode belongs to the 8255 PPI. |
| 66 | [MCQ: field-device interface module](images/Day%2015/Screenshot%202026-06-19%20175725.png) | Field devices are normally treated as connected through input modules when their states are sensed by the controller. |
| 67 | [MCQ: error detection code](images/Day%2015/Screenshot%202026-06-19%20175919.png) | CRC is used in protocols for detecting transmission errors. |
| 68 | [MOVC instruction](images/Day%2015/Screenshot%202026-06-19%20180533.png) | `MOVC` reads code memory into the accumulator using `A+DPTR` or `A+PC`. |
| 69 | [MOVX instruction](images/Day%2015/Screenshot%202026-06-19%20181115.png) | `MOVX` moves data between accumulator and external data memory. |
| 70 | [MOVX @Ri and @DPTR details](images/Day%2015/Screenshot%202026-06-19%20181253.png) | `@R0/@R1` gives an 8-bit external address through P0; `@DPTR` gives a 16-bit external address through P0/P2. |
| 71 | [SWAP A](images/Day%2015/Screenshot%202026-06-19%20181927.png) | `SWAP A` exchanges the accumulator's low and high nibbles. |
| 72 | [XCH](images/Day%2015/Screenshot%202026-06-19%20181952.png) | `XCH A,operand` exchanges accumulator contents with the selected byte operand. |
| 73 | [PUSH operation](images/Day%2015/Screenshot%202026-06-19%20182109.png) | `PUSH direct` increments `SP` first, then stores the direct byte at the new stack location. |
| 74 | [PUSH clean view](images/Day%2015/Screenshot%202026-06-19%20182151.png) | Repeat/clean view of the 8051 pre-increment stack push rule. |
| 75 | [ADD and ADDC](images/Day%2015/Screenshot%202026-06-19%20182346.png) | `ADD` ignores incoming carry; `ADDC` includes the carry flag in the addition. |
| 76 | [SUBB](images/Day%2015/Screenshot%202026-06-19%20182600.png) | `SUBB` subtracts operand and carry/borrow from the accumulator. |
| 77 | [Blank transition capture](images/Day%2015/Screenshot%202026-06-19%20182928.png) | This capture has no technical slide content; it is kept only for sequence completeness. |
| 78 | [MUL AB](images/Day%2015/Screenshot%202026-06-19%20182938.png) | `MUL AB` multiplies unsigned `A` and `B`; low byte goes to `A`, high byte to `B`. |
| 79 | [DIV AB](images/Day%2015/Screenshot%202026-06-19%20183225.png) | `DIV AB` divides unsigned `A` by `B`; quotient goes to `A`, remainder to `B`. |
| 80 | [INC register](images/Day%2015/Screenshot%202026-06-19%20183345.png) | `INC` adds one and wraps `FFH` to `00H` without setting carry. |
| 81 | [INC register clean view](images/Day%2015/Screenshot%202026-06-19%20214533.png) | Repeat/clean view of `INC` rollover and carry behavior. |
| 82 | [INC DPTR partial view](images/Day%2015/Screenshot%202026-06-19%20214551.png) | `INC DPTR` increments the 16-bit data pointer. |
| 83 | [INC DPTR rollover](images/Day%2015/Screenshot%202026-06-19%20214615.png) | `INC DPTR` rolls `FFFFH` to `0000H` and does not set carry. |
| 84 | [INC register final view](images/Day%2015/Screenshot%202026-06-19%20214638.png) | Final clean view of the `INC register` rule. |
| 85 | [ORL](images/Day%2015/Screenshot%202026-06-19%20214929.png) | `ORL` performs bitwise OR and stores the result in the destination operand. |
| 86 | [ANL](images/Day%2015/Screenshot%202026-06-19%20215019.png) | `ANL` performs bitwise AND and is useful for masking. |
| 87 | [XRL](images/Day%2015/Screenshot%202026-06-19%20215113.png) | `XRL` performs bitwise exclusive OR. |
| 88 | [CLR](images/Day%2015/Screenshot%202026-06-19%20215303.png) | `CLR` clears accumulator, carry, or an addressed bit depending on the operand form. |
| 89 | [RL A](images/Day%2015/Screenshot%202026-06-19%20215520.png) | `RL A` rotates accumulator bits left without using carry. |
| 90 | [RR A](images/Day%2015/Screenshot%202026-06-19%20215619.png) | `RR A` rotates accumulator bits right without using carry. |
| 91 | [RRC A](images/Day%2015/Screenshot%202026-06-19%20220250.png) | `RRC A` rotates accumulator right through the carry flag. |
| 92 | [JMP @A+DPTR](images/Day%2015/Screenshot%202026-06-19%20220302.png) | Indirect jump target is calculated as `DPTR + A`. |
| 93 | [JC reladdr](images/Day%2015/Screenshot%202026-06-19%20220542.png) | `JC` branches to a relative address if carry is set. |
| 94 | [DJNZ](images/Day%2015/Screenshot%202026-06-19%20220745.png) | `DJNZ` decrements and branches if the new value is not zero. |
| 95 | [JZ](images/Day%2015/Screenshot%202026-06-19%20220904.png) | `JZ` branches if accumulator is zero; the slide syntax appears to accidentally show `JNZ`. |
| 96 | [JNZ](images/Day%2015/Screenshot%202026-06-19%20220958.png) | `JNZ` branches if accumulator is not zero. |
| 97 | [SJMP](images/Day%2015/Screenshot%202026-06-19%20221112.png) | `SJMP` is an unconditional relative branch within signed 8-bit offset range. |
| 98 | [SJMP clean view](images/Day%2015/Screenshot%202026-06-19%20221745.png) | Cleaner view of the `SJMP reladdr` range rule. |
| 99 | [AJMP](images/Day%2015/Screenshot%202026-06-19%20222211.png) | `AJMP` gives an absolute jump within the same 2 KB code block. |
| 100 | [AJMP clean view](images/Day%2015/Screenshot%202026-06-19%20222725.png) | Cleaner view of the `AJMP code address` rule. |
| 101 | [AJMP bit layout](images/Day%2015/Screenshot%202026-06-19%20223951.png) | Annotated view showing how the 11-bit destination is formed. |
| 102 | [AJMP annotated repeat](images/Day%2015/Screenshot%202026-06-19%20223955.png) | Repeat/annotated view of the 2 KB-page restriction. |
| 103 | [ACALL 2 KB block rule](images/Day%2015/Screenshot%202026-06-19%20225104.png) | `ACALL` also affects only 11 PC bits, so the target must be in the same 2 KB block. |
| 104 | [RET](images/Day%2015/Screenshot%202026-06-19%20225255.png) | `RET` returns from subroutine by popping the stored PC bytes from the stack. |
| 105 | [RETI](images/Day%2015/Screenshot%202026-06-19%20225357.png) | `RETI` returns from interrupt and re-enables interrupt-priority service logic. |
| 106 | [JNB](images/Day%2015/Screenshot%202026-06-19%20225536.png) | `JNB bit,reladdr` branches if the addressed bit is zero. |
| 107 | [JBC](images/Day%2015/Screenshot%202026-06-19%20225558.png) | `JBC bit,reladdr` branches if the bit is set and clears that bit before branching. |
| 108 | [MCQ: battery use](images/Day%2015/Screenshot%202026-06-19%20225617.png) | Microcontrollers suit battery use because of low power consumption. |
| 109 | [MCQ: indirect operator](images/Day%2015/Screenshot%202026-06-19%20225643.png) | Register-indirect addressing uses the `@` operator. |
| 110 | [MCQ: ADD destination](images/Day%2015/Screenshot%202026-06-19%20225703.png) | 8051 `ADD` destination is always accumulator. |
| 111 | [MCQ: two's-complement program](images/Day%2015/Screenshot%202026-06-19%20225742.png) | `MOV A,R0`, `CPL A`, `INC A` forms the two's complement of `R0` in `A`. |
| 112 | [MCQ: indirect RAM to accumulator](images/Day%2015/Screenshot%202026-06-19%20225819.png) | `MOV A,@Ri` transfers indirectly addressed internal RAM into accumulator. |
| 113 | [MCQ: ADD destination clean view](images/Day%2015/Screenshot%202026-06-19%20225835.png) | Repeat/clean view of accumulator-only destination for `ADD`. |
| 114 | [MCQ: DA A condition](images/Day%2015/Screenshot%202026-06-19%20225838.png) | `DA A` adjustment depends on BCD digit range plus `AC`/`CY`; the MCQ answer is usually "either CY or AC is 1." |
| 115 | [MCQ: DA A condition clean view](images/Day%2015/Screenshot%202026-06-19%20225857.png) | Cleaner view of the decimal-adjust MCQ. |
| 116 | [MCQ: SUBB A,R4](images/Day%2015/Screenshot%202026-06-19%20225924.png) | `SUBB A,R4` performs `A = A - R4 - CY`. |
| 117 | [MCQ: DIV flag options](images/Day%2015/Screenshot%202026-06-19%20225931.png) | The reliable rule is: valid `DIV AB` clears carry and overflow; auxiliary carry is not the meaningful flag. |
| 118 | [MCQ: DIV flag clean view](images/Day%2015/Screenshot%202026-06-19%20225952.png) | Cleaner view of the division flag MCQ. |
| 119 | [MCQ: DIV flag marked view](images/Day%2015/Screenshot%202026-06-19%20230007.png) | The marked course answer says `CY=0, AC=0`; cross-check with official flag behavior while revising. |
| 120 | [MCQ: signed overflow](images/Day%2015/Screenshot%202026-06-19%20230029.png) | Signed overflow is detected from mismatch between carry into and carry out of the sign bit. |
| 121 | [MCQ: signed overflow annotated](images/Day%2015/Screenshot%202026-06-19%20230114.png) | Annotated view of the D6/D7 carry relationship behind `OV`. |
| 122 | [MCQ: unsigned addition flag](images/Day%2015/Screenshot%202026-06-19%20230121.png) | Unsigned addition mainly uses carry `CY`. |
| 123 | [MCQ: unsigned addition flag marked](images/Day%2015/Screenshot%202026-06-19%20230129.png) | Marked answer confirming `CY` for unsigned addition. |
| 124 | [MCQ: logic instructions and PSW](images/Day%2015/Screenshot%202026-06-19%20230153.png) | Byte logical operations `ANL`, `ORL`, and `XRL` are normally taught as not changing PSW flags. |
| 125 | [MCQ: logic instructions clean view](images/Day%2015/Screenshot%202026-06-19%20230201.png) | Cleaner view of the "no effect on PSW flags" MCQ. |
| 126 | [MCQ: ANL uses](images/Day%2015/Screenshot%202026-06-19%20230233.png) | `ANL` both ANDs values and masks selected bits, so all listed uses apply. |
| 127 | [MCQ: ANL answer marked](images/Day%2015/Screenshot%202026-06-19%20230253.png) | Marked answer confirming all listed uses of `ANL`. |
| 128 | [MCQ: CJNE behavior](images/Day%2015/Screenshot%202026-06-19%20230310.png) | `CJNE` jumps when operands are not equal and sets carry if first operand is less than second. |
| 129 | [MCQ: CJNE clean view](images/Day%2015/Screenshot%202026-06-19%20230332.png) | Cleaner view of the `CJNE` carry-comparison rule. |
| 130 | [MCQ: ADD operand rule](images/Day%2015/Screenshot%202026-06-19%20230359.png) | `ADD A,source` keeps accumulator as destination; source may be register, direct memory/SFR, indirect, or immediate. |

## Page Notes

### Page 1: Serial Port Operation Overview

<a href="images/Day%2015/Screenshot%202026-06-19%20135930.png"><img src="images/Day%2015/Screenshot%202026-06-19%20135930.png" alt="8051 serial port operation overview" width="960"></a>

This page introduces the on-chip serial port as a data-conversion block. Inside the CPU, data is naturally handled as an 8-bit parallel value. On a serial line, that same value must be sent one bit at a time. The transmit side therefore performs parallel-to-serial conversion, while the receive side performs serial-to-parallel conversion before software reads the received byte.

The page also highlights full-duplex operation. Full duplex means transmit and receive can proceed at the same time, because the 8051 has separate serial pins and internal paths for output and input. Receive buffering matters because the receiver can hold a received character while the next character is arriving, giving software time to read the first byte before it is overwritten or lost.

### Page 2: SBUF And SCON Software Access

<a href="images/Day%2015/Screenshot%202026-06-19%20140057.png"><img src="images/Day%2015/Screenshot%202026-06-19%20140057.png" alt="8051 SBUF and SCON software access" width="960"></a>

This page gives the key programmer view: serial communication is accessed mainly through `SBUF` and `SCON`. `SBUF` is at direct SFR address `99H`, but it is not one simple read/write storage byte internally. The same software address maps to two different hardware buffers depending on the operation.

Writing to `SBUF` loads the transmit buffer, so the serial hardware can shift that byte out. Reading `SBUF` reads the receive buffer, so software gets the byte that arrived from the serial input path. This explains why `SBUF` questions should always be answered with the operation direction: `MOV SBUF,A` and `MOV A,SBUF` touch different internal serial buffers even though they name the same SFR address.

### Page 3: Serial Port Block Diagram

<a href="images/Day%2015/Screenshot%202026-06-19%20141111.png"><img src="images/Day%2015/Screenshot%202026-06-19%20141111.png" alt="8051 serial port block diagram" width="960"></a>

This block diagram makes the two `SBUF` paths visible. On the transmit side, the CPU writes a byte from the internal bus into the write-only transmit `SBUF`. That byte is then shifted out one bit at a time on `TXD`, which is the alternate function of `P3.1`. This is the parallel-in serial-out path, often remembered as PISO.

On the receive side, serial bits arrive on `RXD`, the alternate function of `P3.0`. The receiver shift register collects those serial bits into a parallel byte and then places the received byte into the read-only receive `SBUF`. This is the serial-in parallel-out path, or SIPO. The separate baud-rate clocks in the diagram show that serial communication is timing-sensitive: both transmitter and receiver must agree on the bit timing.

### Page 4: SCON And Baud-Rate Control

<a href="images/Day%2015/Screenshot%202026-06-19%20141140.png"><img src="images/Day%2015/Screenshot%202026-06-19%20141140.png" alt="8051 SCON and baud-rate control" width="960"></a>

This page introduces the serial control register `SCON` at SFR address `98H`. `SCON` is bit-addressable, which is important because serial programming often sets or clears individual control bits and tests individual status flags. Its control bits select serial operating behavior, while its status bits indicate transmit-complete or receive-complete events.

The page also connects serial operation to baud-rate generation. Baud rate is the serial bit rate. Some 8051 serial modes use a fixed rate derived from the oscillator, while variable baud-rate operation commonly uses Timer 1 as the baud-rate clock source. That is why Day 14's timer material directly supports Day 15's serial-port material: timer setup can determine how fast serial bits are transmitted and sampled.

### Page 5: SCON Bit Layout

<a href="images/Day%2015/Screenshot%202026-06-19%20141838.png"><img src="images/Day%2015/Screenshot%202026-06-19%20141838.png" alt="8051 SCON bit layout" width="960"></a>

This page gives the bit-level view of `SCON`: `SM0 SM1 SM2 REN TB8 RB8 TI RI`. The slide text says `SCON` is at `99H`, but that address belongs to `SBUF`; `SCON` is the bit-addressable serial control SFR at `98H`. Keeping this correction explicit avoids mixing up the two central serial SFRs.

`SM0` and `SM1` select the serial mode. `SM2` supports multiprocessor communication behavior in the modes where that feature applies. `REN` enables or disables reception. `TB8` and `RB8` carry the ninth transmit/receive bit in 9-bit serial modes. `TI` is the transmit interrupt flag and `RI` is the receive interrupt flag; hardware sets these flags when the corresponding serial event completes, and software clears them after servicing.

### Page 6: SCON Simple-Use Note

<a href="images/Day%2015/Screenshot%202026-06-19%20142407.png"><img src="images/Day%2015/Screenshot%202026-06-19%20142407.png" alt="8051 SCON simple-use note" width="960"></a>

This note is a shortcut for ordinary serial-port setup. If the program is not using multiprocessor communication and is not using 9-bit serial data, then `SM2`, `TB8`, and `RB8` should be kept `0`. That leaves the program focused on the mode-select bits, receive enable, and the transmit/receive flags.

The screenshot appears to write `TB5` and `RB5`, but in the `SCON` register the relevant bits are `TB8` and `RB8`. `TB8` is the ninth transmitted data bit and `RB8` is the ninth received data bit in 9-bit modes. For common 8-bit UART-style operation, those ninth-bit fields are not part of the data character, so clearing them avoids accidental 9-bit/multiprocessor behavior.

### Page 7: Receiver Enable Initialization

<a href="images/Day%2015/Screenshot%202026-06-19%20142934.png"><img src="images/Day%2015/Screenshot%202026-06-19%20142934.png" alt="8051 receiver enable initialization" width="960"></a>

This page explains how receiving is enabled in software. `REN` is `SCON.4`, so its bit value is `00010000B`, or `10H`. Writing `MOV SCON,#10H` sets `REN` and clears the other `SCON` bits, which is useful only when that complete control-byte value matches the intended mode and flag state.

The second method, `SETB REN`, is more targeted. Because `SCON` is bit-addressable, software can set the receive-enable bit without disturbing `SM0`, `SM1`, `SM2`, `TB8`, `RB8`, `TI`, or `RI`. This distinction is important in real programs: full-byte initialization is convenient at startup, while bit operations are safer when other `SCON` bits already carry meaningful state.

### Page 8: Serial Interrupt Flags

<a href="images/Day%2015/Screenshot%202026-06-19%20143037.png"><img src="images/Day%2015/Screenshot%202026-06-19%20143037.png" alt="8051 serial interrupt flags RI and TI" width="960"></a>

This page explains the two serial event flags. `RI` is the receive interrupt flag. Hardware sets it when a character has been received and the receive buffer is ready for software to read. In a polling program, the CPU repeatedly checks `RI`; in an interrupt-driven program, the serial interrupt can be used so the CPU does not have to poll continuously.

`TI` is the transmit interrupt flag. Hardware sets it when the transmitter reaches the point where software may treat the transmit buffer as ready for the next byte. Before sending another character, software should check that the transmitter is ready, then write the next byte to `SBUF`. Both `RI` and `TI` are cleared by software after the corresponding event has been handled.

### Page 9: Serial Modes Of Operation

<a href="images/Day%2015/Screenshot%202026-06-19%20143340.png"><img src="images/Day%2015/Screenshot%202026-06-19%20143340.png" alt="8051 serial modes of operation" width="960"></a>

This page connects the flag logic to transmit sequencing. If a previous character is still being sent, software should wait until transmission is finished before loading the next character. In practical polling code, that means checking `TI` before writing the next byte to `SBUF`, then clearing `TI` after acknowledging that transmit event.

The page also introduces the four serial modes selected by `SM0` and `SM1` in `SCON`. Three modes are asynchronous serial modes, where each character is framed by start/stop timing. The remaining mode uses the serial port as a shift register. Therefore `SM0/SM1` are not just labels; they define the frame structure, bit count, and baud-rate source assumptions for the serial transfer.

### Page 10: Mode 0 8-Bit Shift Register

<a href="images/Day%2015/Screenshot%202026-06-19%20143616.png"><img src="images/Day%2015/Screenshot%202026-06-19%20143616.png" alt="8051 serial Mode 0 8-bit shift register" width="960"></a>

This page gives the details for serial Mode 0. Mode 0 is selected by clearing both serial mode bits: `SM0=0` and `SM1=0`. In this mode, the serial port behaves like an 8-bit shift register rather than a normal UART-style framed asynchronous link.

The pin roles are different from the usual `TXD` transmit-data and `RXD` receive-data expectation. In Mode 0, serial data enters and exits through `RXD`, while `TXD` outputs the shift clock. Eight bits are shifted least-significant bit first, and the bit rate is fixed at one-twelfth of the on-chip oscillator frequency. Transmission starts when software writes to `SBUF`; reception starts when `REN=1` and `RI=0`, so clearing `RI` is part of preparing for another received byte.

### Page 11: Mode 1 8-Bit UART With Variable Baud

<a href="images/Day%2015/Screenshot%202026-06-19%20144240.png"><img src="images/Day%2015/Screenshot%202026-06-19%20144240.png" alt="8051 serial Mode 1 8-bit UART variable baud" width="960"></a>

This page gives the standard 8-bit UART-style serial mode. Mode 1 sends or receives 10 bits for each character: a start bit that is always `0`, eight data bits transmitted least-significant bit first, and a stop bit that is always `1`. Data is transmitted on `TXD` and received on `RXD`.

Mode 1 uses variable baud-rate operation. In the classic 8051 setup, the baud-rate timing is derived from Timer 1 overflow behavior and the serial-port divider path. Writing to `SBUF` initiates a transmit request, but the actual serial shifting waits for the baud-rate timing path. On receive, the stop bit can be reflected in `RB8`, so `RB8` is not a ninth data bit in Mode 1; it records the received stop-bit state.

### Page 12: Mode 1 Transmit And Receive Timing

<a href="images/Day%2015/Screenshot%202026-06-19%20145103.png"><img src="images/Day%2015/Screenshot%202026-06-19%20145103.png" alt="8051 Mode 1 transmit and receive timing" width="960"></a>

This page adds the timing detail behind Mode 1. On transmit, the shifted frame appears on `TXD` beginning with the start bit, followed by the eight data bits, then the stop bit. The period of each bit is the reciprocal of the programmed baud rate, so timer programming directly controls how long each bit remains on the line.

`TI` is set when the stop bit appears on `TXD`, marking that the transmit event has reached its completion point. On receive, the hardware watches `RXD` for a `1`-to-`0` transition, which is the falling edge into the start bit. The divide-by-16 receive timing path is reset/aligned to that incoming stream so the receiver samples the following bits at the correct positions inside each bit period.

### Page 13: Mode 1 Receive Sampling And False Start Detection

<a href="images/Day%2015/Screenshot%202026-06-19%20145830.png"><img src="images/Day%2015/Screenshot%202026-06-19%20145830.png" alt="8051 Mode 1 receive sampling and false start detection" width="960"></a>

This page explains why the receiver does not accept every falling edge as a real character. After the first `1`-to-`0` transition on `RXD`, the incoming bit stream is sampled relative to the 16-count receive timing path. Sampling near the middle of a bit time is important because the signal is most stable away from the transition edges.

The false-start check requires the line to still be in the `0` state after the validation delay. If the line is not still low, the receiver treats the original edge as noise instead of a valid start bit. It then resets the receive logic and returns to the idle state, waiting for the next `1`-to-`0` transition.

### Page 14: Mode 1 Receive Completion Sequence

<a href="images/Day%2015/Screenshot%202026-06-19%20150358.png"><img src="images/Day%2015/Screenshot%202026-06-19%20150358.png" alt="8051 Mode 1 receive completion sequence" width="960"></a>

This page gives the final receive-side sequence after a valid Mode 1 frame has been sampled. The stop bit is clocked into `RB8`, the eight received data bits are loaded into the receive side of `SBUF`, and the receive interrupt flag `RI` is set.

This order matters in software. Once `RI=1`, the program can read `SBUF` to get the eight data bits. If the program also cares about the received stop-bit state, it can inspect `RB8`. After handling the character, software clears `RI` so the receiver can report the next completed character cleanly.

### Page 15: Mode 2 9-Bit UART With Fixed Baud

<a href="images/Day%2015/Screenshot%202026-06-19%20150953.png"><img src="images/Day%2015/Screenshot%202026-06-19%20150953.png" alt="8051 serial Mode 2 9-bit UART fixed baud" width="960"></a>

This page introduces Mode 2, the 9-bit UART mode with fixed baud-rate selection. Using the standard `SCON` bit naming from the 8051 manuals, Mode 2 is selected by `SM0=1` and `SM1=0`, which is the `SM0 SM1 = 10` combination. Some slides write the pair in the opposite textual order, so keep the actual `SCON.7=SM0` and `SCON.6=SM1` bit positions in mind.

Mode 2 transmits or receives 11 bits per character: one start bit, eight data bits, one programmable ninth bit, and one stop bit. On transmit, the ninth bit is taken from `TB8`, so software can use it as a parity bit or an address/data marker. On receive, the incoming ninth bit is placed into `RB8`. The baud rate is fixed relative to the oscillator, commonly selected as either `1/32` or `1/64` of the oscillator frequency depending on the baud-rate doubling configuration.

### Page 16: Mode 2 Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20151141.png"><img src="images/Day%2015/Screenshot%202026-06-19%20151141.png" alt="8051 serial Mode 2 fixed baud clean view" width="960"></a>

This cleaner view repeats the Mode 2 essentials and is useful for revision. Mode 2 is the fixed-baud 9-bit UART mode: start bit, eight ordinary data bits, a programmable ninth bit, and a stop bit. The ninth bit is the reason `TB8` and `RB8` matter in this mode.

The fixed-baud line is the contrast with Mode 3. Mode 2 does not use the same variable Timer 1 baud setup as Mode 1 or Mode 3. Its baud rate is oscillator-derived, with the common fixed choices controlled by the serial baud-rate doubling configuration.

### Page 17: Mode 3 9-Bit UART With Variable Baud

<a href="images/Day%2015/Screenshot%202026-06-19%20151657.png"><img src="images/Day%2015/Screenshot%202026-06-19%20151657.png" alt="8051 serial Mode 3 9-bit UART variable baud" width="960"></a>

This page completes the four-mode picture. Mode 3 uses the same 9-bit UART frame idea as Mode 2: start bit, eight data bits, programmable ninth bit, and stop bit. The difference is baud-rate generation. Mode 3 uses a programmable baud rate supplied by the timer path, so it is the 9-bit counterpart to Mode 1's variable-baud behavior.

The summary line "modes 1, 2, and 3 are very similar" should be read carefully. They are similar because they are asynchronous framed modes. They differ in data-bit count and baud-rate source: Mode 1 is 8 data bits with variable baud, Mode 2 is 9 data bits with fixed baud, and Mode 3 is 9 data bits with variable baud.

### Page 18: Multiprocessor Communications

<a href="images/Day%2015/Screenshot%202026-06-19%20152848.png"><img src="images/Day%2015/Screenshot%202026-06-19%20152848.png" alt="8051 multiprocessor communications using SM2 and RB8" width="960"></a>

This page explains why the ninth bit exists in modes 2 and 3. In these modes, the ninth received bit is placed in `RB8`. If `SM2` is enabled, the serial interrupt behavior can be filtered so that a received frame activates service only when `RB8=1`. That gives software a way to distinguish special frames from ordinary data frames.

The common use case is a master/slave network of multiple 8051-based devices. A master can transmit an address or attention frame with the ninth bit set, causing slaves to notice and check whether they are being addressed. Ordinary data frames can use the ninth bit cleared, allowing non-addressed slaves to ignore them at the interrupt level.

### Page 19: Fixed Baud Rates In Modes 0 And 2

<a href="images/Day%2015/Screenshot%202026-06-19%20153807.png"><img src="images/Day%2015/Screenshot%202026-06-19%20153807.png" alt="8051 fixed baud rates in serial modes 0 and 2" width="960"></a>

This page separates fixed baud-rate modes from timer-programmable baud-rate modes. In Mode 0, the baud rate is always the oscillator frequency divided by 12. If the oscillator is 12 MHz, Mode 0 shifts at 1 MHz. This matches Mode 0's shift-register nature rather than UART-style timer baud generation.

Mode 2 is also fixed relative to the oscillator. After reset, its baud rate defaults to oscillator frequency divided by 64. With baud-rate doubling configured, the other common Mode 2 value is oscillator frequency divided by 32. This is why Mode 2 is fixed-baud while Mode 3, although also 9-bit, is variable-baud.

### Page 20: PCON Effect On Baud Rate

<a href="images/Day%2015/Screenshot%202026-06-19%20153942.png"><img src="images/Day%2015/Screenshot%202026-06-19%20153942.png" alt="8051 PCON effect on baud rate" width="960"></a>

This page explains why baud rate is not only a timer setting. The oscillator frequency is the base timing source, so a higher-frequency crystal can increase the available baud rates. The power control register `PCON` also contains a baud-rate-related bit, commonly called `SMOD`, that can double the serial baud rate in the modes where that feature applies.

For revision, separate the three layers: oscillator frequency is the raw time base, Timer 1 or fixed dividers shape the serial clock depending on the mode, and `PCON.SMOD` can apply a doubling factor. If a baud-rate calculation is wrong, check all three layers before assuming the `SBUF` or `SCON` program is wrong.

### Page 21: PCON Register And SMOD

<a href="images/Day%2015/Screenshot%202026-06-19%20154147.png"><img src="images/Day%2015/Screenshot%202026-06-19%20154147.png" alt="8051 PCON register and SMOD bit" width="960"></a>

This page gives the `PCON` bit layout used for the baud-rate discussion. `PCON` is an 8-bit register whose high bit is `SMOD`; the lower named bits shown include `GF1`, `GF0`, `PD`, and `IDL`. For this serial-port topic, `SMOD` is the important bit because it controls baud-rate doubling.

After reset, `SMOD=0`. Software can set `SMOD=1` to double the serial baud rate in the supported serial modes. That means the same timer reload or oscillator-derived base can produce two different serial bit rates depending on `SMOD`.

### Page 22: SMOD Baud-Rate Doubling Path

<a href="images/Day%2015/Screenshot%202026-06-19%20154527.png"><img src="images/Day%2015/Screenshot%202026-06-19%20154527.png" alt="8051 SMOD baud-rate doubling path" width="960"></a>

This page gives the practical effect of `SMOD`. `SMOD` is bit 7 of `PCON`. When software sets it, the serial baud rate is doubled in serial modes 1, 2, and 3. In Mode 2 specifically, the fixed baud rate changes from the reset/default `fosc/64` case to the doubled `fosc/32` case.

The diagram uses the common 11.0592 MHz crystal because it divides cleanly into standard serial rates. The oscillator first feeds the 8051 timing path, then the serial baud path divides that timing further. With `SMOD=1`, the divider is effectively smaller, so the resulting baud-rate clock is doubled compared with `SMOD=0`.

### Page 23: TH1 Reload Baud-Rate Comparison

<a href="images/Day%2015/Screenshot%202026-06-19%20154541.png"><img src="images/Day%2015/Screenshot%202026-06-19%20154541.png" alt="8051 TH1 reload baud-rate comparison for SMOD" width="960"></a>

This table shows common Timer 1 reload values for variable baud-rate modes. The decimal values are negative reload offsets represented in 8-bit two's-complement form: `-3` is `FDH`, `-6` is `FAH`, `-12` is `F4H`, and `-24` is `E8H`. Loading these values into `TH1` changes how often Timer 1 overflows.

The table also shows the doubling effect of `SMOD`. For the same `TH1` reload, `SMOD=1` doubles the baud rate compared with `SMOD=0`: `FDH` gives 9600 versus 19200, `FAH` gives 4800 versus 9600, `F4H` gives 2400 versus 4800, and `E8H` gives 1200 versus 2400.

### Page 24: SMOD Divider Diagram

<a href="images/Day%2015/Screenshot%202026-06-19%20154610.png"><img src="images/Day%2015/Screenshot%202026-06-19%20154610.png" alt="8051 SMOD divider diagram" width="960"></a>

This diagram gives the same baud-rate doubling idea as a clock path. With an 11.0592 MHz crystal, the classic 12-clock timing path gives a machine-cycle frequency of 921.6 kHz. The serial baud path then divides that timing. `SMOD=0` uses the lower baud result, while `SMOD=1` doubles it.

The numbers shown beside the divider path are useful checkpoints: dividing 921.6 kHz by 32 gives 28.8 kHz, while dividing by 16 gives 57.6 kHz. Those are intermediate clocks used in the serial baud-rate generation path, not necessarily the final named UART baud in every mode. The main lesson is that `SMOD` halves the divider and therefore doubles the resulting serial timing.

### Page 25: Mode 1 Serial Programming Setup

<a href="images/Day%2015/Screenshot%202026-06-19%20155615.png"><img src="images/Day%2015/Screenshot%202026-06-19%20155615.png" alt="8051 programming sequence for Mode 1 serial byte transfer" width="960"></a>

This page converts the baud-rate discussion into a programming sequence for character-byte transfer. `TMOD=20H` means Timer 1 is configured in Mode 2, the 8-bit auto-reload timer mode. That mode is commonly used for 8051 serial baud generation because `TH1` can hold a reload value and the timer can overflow repeatedly at a steady rate without software reloading it after every overflow.

`TH1` then receives the reload byte chosen for the required baud rate. The exact value depends on oscillator frequency, `SMOD`, and the target baud rate. This is why the previous table of `TH1` values matters: it is not an isolated table; it supplies the reload byte used by the serial initialization program.

`SCON=50H` is the serial-control setup for a common Mode 1 receive/transmit program. In binary, `50H` is `01010000B`: `SM0=0`, `SM1=1` selects Mode 1, `SM2=0` disables multiprocessor filtering, `REN=1` enables reception, and `TB8/RB8/TI/RI` start clear. Finally, setting `TR1=1` starts Timer 1, so the baud-rate clock begins running and the serial port has the timing source needed for actual transfer.

### Page 26: Mode 1 Setup Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20155809.png"><img src="images/Day%2015/Screenshot%202026-06-19%20155809.png" alt="cleaner 8051 Mode 1 serial setup checklist" width="960"></a>

This page is a cleaner view of the same programming checklist, so use it as the final practical memory hook for Mode 1 serial initialization. The order is important: first configure Timer 1 behavior with `TMOD=20H`, then load `TH1` with the required baud-rate reload, then configure the serial control register with `SCON=50H`, and finally start Timer 1 with `TR1=1`.

The checklist also shows the separation of responsibilities. `TMOD` and `TH1` belong to baud-rate generation. `SCON` belongs to serial mode and receiver enable. `TR1` belongs to timer start/stop control in `TCON`. A correct serial program needs all three areas to agree.

### Page 27: Transmit Loop Continuation

<a href="images/Day%2015/Screenshot%202026-06-19%20160842.png"><img src="images/Day%2015/Screenshot%202026-06-19%20160842.png" alt="8051 transmit loop continuation with CLR TI SBUF and JNB TI" width="960"></a>

This continuation completes the software transmit loop. `CLR TI` makes sure the old transmit-complete event is not mistaken for the new byte. Then the byte is written to `SBUF`, and the program waits with a test such as `JNB TI,xx` until hardware sets `TI` again. The loop is not waiting for `SBUF` storage alone; it is waiting for the serial frame to reach the transmit-complete point.

### Page 28: TXD Transmit Frame Steps

<a href="images/Day%2015/Screenshot%202026-06-19%20161013.png"><img src="images/Day%2015/Screenshot%202026-06-19%20161013.png" alt="8051 TXD transmit frame steps" width="960"></a>

This page shows the hardware side after software writes `SBUF`. In Mode 1, the byte is not sent as one parallel value. The serial unit sends a start bit, then the eight data bits one at a time, then a stop bit. `TI` is raised during the stop-bit phase, so `TI=1` means the previous character has reached the point where software can load or prepare the next character.

### Page 29: TI Flag And SBUF Overwrite Caution

<a href="images/Day%2015/Screenshot%202026-06-19%20161504.png"><img src="images/Day%2015/Screenshot%202026-06-19%20161504.png" alt="8051 TI flag and SBUF overwrite caution" width="960"></a>

This slide explains why polling `TI` is not optional in a simple transmit program. If software writes another byte to `SBUF` too early, the transmit path can lose the unfinished part of the previous byte. The correct pattern is: wait for `TI`, clear `TI`, write the next `SBUF` byte, then wait again. In interrupt-driven code the ISR replaces the polling loop, but the same flag discipline is still required.

### Page 30: Receive Loop Continuation

<a href="images/Day%2015/Screenshot%202026-06-19%20162237.png"><img src="images/Day%2015/Screenshot%202026-06-19%20162237.png" alt="8051 receive loop continuation with JNB RI" width="960"></a>

The receive loop mirrors the transmit loop. `RI` is tested with an instruction such as `JNB RI,xx`; while `RI=0`, no complete character is ready. When hardware sets `RI`, the receive `SBUF` contains a full byte and software should move it to RAM, a register, or another safe destination. After service, software clears `RI` and waits for the next character.

### Page 31: RXD Receive Frame Steps

<a href="images/Day%2015/Screenshot%202026-06-19%20162516.png"><img src="images/Day%2015/Screenshot%202026-06-19%20162516.png" alt="8051 RXD receive frame steps" width="960"></a>

This page is the receive-side version of the serial frame. The receiver detects the start bit first, then samples the eight data bits, then accepts the stop bit. When the stop bit validates the end of the frame, hardware sets `RI=1`. The important timing idea is that software does not read each incoming bit; the serial hardware collects the bits and presents the completed byte through receive `SBUF`.

### Page 32: RXD Receive Frame Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20162941.png"><img src="images/Day%2015/Screenshot%202026-06-19%20162941.png" alt="cleaner 8051 RXD receive frame steps" width="960"></a>

This cleaner view reinforces the overwrite risk on receive. `RI=1` means a byte is already waiting in receive `SBUF`. If the program ignores it for too long and another character arrives, the old byte can be overwritten or lost. For polling programs, the safe habit is: wait for `RI`, read `SBUF` immediately, store the value, then clear `RI`.

### Page 33: 8096 Architectural Features

<a href="images/Day%2015/Screenshot%202026-06-19%20163026.png"><img src="images/Day%2015/Screenshot%202026-06-19%20163026.png" alt="Intel 8096 architectural features" width="960"></a>

This page shifts briefly from 8051 to the Intel 8096, part of the MCS-96 family. The key contrast is scale: 8051 is an 8-bit controller, while 8096 is taught here as a higher-performance 16-bit microcontroller for control applications. Its register-to-register orientation and stronger math/I/O capability make it more suitable for high-speed embedded control than the classic 8051 baseline.

### Page 34: 8096 Applications And Modes

<a href="images/Day%2015/Screenshot%202026-06-19%20163452.png"><img src="images/Day%2015/Screenshot%202026-06-19%20163452.png" alt="Intel 8096 applications ADC and operating modes" width="960"></a>

The slide connects 8096 to motor control, axis control, robotics, and data-acquisition systems. The 10-bit ADC matters because control systems often measure analog signals before computing an output. The two operating modes have the same broad meaning as many microcontroller families: single-chip mode emphasizes on-chip resources, while expanded mode lets the system reach external memory or devices through a bus.

### Page 35: 8096 Memory, ADC, And I/O Ports

<a href="images/Day%2015/Screenshot%202026-06-19%20163538.png"><img src="images/Day%2015/Screenshot%202026-06-19%20163538.png" alt="Intel 8096 memory ADC I/O ports and arithmetic features" width="960"></a>

This page lists system resources rather than one instruction behavior. The useful revision method is to group them: memory mode, analog input, digital I/O, and arithmetic. The multiplexed bus in expanded mode is the same design idea seen on other Intel processors: pins are reused for address/data phases so external hardware must latch the address at the correct time.

### Page 36: 8096 PWM, Serial, Interrupts, And Timers

<a href="images/Day%2015/Screenshot%202026-06-19%20163803.png"><img src="images/Day%2015/Screenshot%202026-06-19%20163803.png" alt="Intel 8096 PWM serial interrupts and timers" width="960"></a>

This page explains why 8096 is a control-oriented microcontroller. PWM output is useful for motor and power control, full-duplex serial handles communication, interrupt sources let events preempt the main program, and 16-bit timers support timing measurement or waveform generation. The difference from 8051 is not only bit width; it is the richer on-chip control peripheral set.

### Page 37: MCQ - 8051 Pin Count

<a href="images/Day%2015/Screenshot%202026-06-19%20164259.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164259.png" alt="8051 pin count MCQ" width="960"></a>

The classic 8051 is commonly taught in a 40-pin DIP package. This number matters because many pin-function MCQs are built around the four 8-bit ports, power pins, oscillator pins, reset, `EA`, `ALE`, and `PSEN`.

### Page 38: MCQ - 8051 Address Bus

<a href="images/Day%2015/Screenshot%202026-06-19%20164305.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164305.png" alt="8051 address bus width MCQ" width="960"></a>

The answer is 16-bit unidirectional address bus. The 8051 can address 64 KB of code memory and 64 KB of external data memory, so it needs 16 address bits. Address flow is from controller to memory/device; the data bus is the one that becomes bidirectional during data transfer.

### Page 39: MCQ - Pin Count Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20164319.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164319.png" alt="8051 40 pin MCQ clean view" width="960"></a>

This repeat view reinforces the same 40-pin answer. When revising, do not memorize the package count alone; connect it to `P0-P3`, external memory signals, oscillator pins, reset, and supply pins.

### Page 40: MCQ - TMOD And PCON

<a href="images/Day%2015/Screenshot%202026-06-19%20164327.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164327.png" alt="TMOD PCON control register MCQ" width="960"></a>

`TMOD` and `PCON` are control registers. `TMOD` selects timer/counter modes and gate/counter behavior. `PCON` includes power-control bits and `SMOD`, the serial baud-rate doubling bit. Neither is the serial data transmitter or receiver; `SBUF` is the serial data register.

### Page 41: MCQ - Internal RAM And ROM

<a href="images/Day%2015/Screenshot%202026-06-19%20164337.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164337.png" alt="8051 internal RAM and ROM MCQ" width="960"></a>

The classic 8051 answer is 128 bytes of internal RAM and 4 KB of internal ROM/program memory. Keep the memory spaces separate: internal data RAM is used by registers, bit-addressable RAM, stack, and general RAM; program memory stores code.

### Page 42: MCQ - DPTR Meaning

<a href="images/Day%2015/Screenshot%202026-06-19%20164414.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164414.png" alt="DPTR data pointer MCQ" width="960"></a>

`DPTR` stands for Data Pointer. It is a 16-bit pointer made from `DPH` and `DPL`. It is heavily used for external data access with `MOVX @DPTR` and code-table lookup with `MOVC A,@A+DPTR`.

### Page 43: MCQ - Number Of SFRs

<a href="images/Day%2015/Screenshot%202026-06-19%20164417.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164417.png" alt="8051 special function register count MCQ" width="960"></a>

The usual classroom answer is 21 special function registers in the classic 8051. The deeper point is that the direct-address range `80H-FFH` is not all ordinary RAM. Implemented addresses in that range map to hardware registers such as ports, `SP`, timer registers, `SCON`, `SBUF`, `IE`, `IP`, `PSW`, `ACC`, and `B`.

### Page 44: MCQ - Interrupt Priority Levels

<a href="images/Day%2015/Screenshot%202026-06-19%20164432.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164432.png" alt="8051 interrupt priority levels MCQ" width="960"></a>

Classic 8051 interrupt priority is commonly taught as two programmable levels: low and high. If two enabled interrupts have the same priority, the internal polling order decides which is serviced first. That is different from the priority-level setting itself.

### Page 45: MCQ - 8051 Bit Width

<a href="images/Day%2015/Screenshot%202026-06-19%20164506.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164506.png" alt="8051 8 bit microcontroller MCQ" width="960"></a>

8051 is an 8-bit microcontroller because its accumulator, ALU data path, and ordinary register operations are centered on 8-bit data. It still has 16-bit registers such as `DPTR` and `PC`, but those do not make the core a 16-bit controller.

### Page 46: MCQ - Multifunctional Port

<a href="images/Day%2015/Screenshot%202026-06-19%20164520.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164520.png" alt="8051 multifunctional port MCQ" width="960"></a>

Port 3 is the multifunctional port. Its pins have alternate functions: `P3.0/RXD`, `P3.1/TXD`, external interrupts, timer inputs, and external memory read/write control signals. Port 0 and Port 2 also have external-memory bus roles, but the standard MCQ wording usually expects Port 3 because each pin has a named alternate control function.

### Page 47: MCQ - Number Of 8-Bit Ports

<a href="images/Day%2015/Screenshot%202026-06-19%20164638.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164638.png" alt="8051 four 8 bit ports MCQ" width="960"></a>

The answer is four: `P0`, `P1`, `P2`, and `P3`. Together they provide 32 port pins, although some pins are reused for alternate functions during serial, interrupt, timer, or external-memory operation.

### Page 48: MCQ - Flags Stored In PSW

<a href="images/Day%2015/Screenshot%202026-06-19%20164644.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164644.png" alt="8051 flags stored in PSW MCQ" width="960"></a>

The flags are stored in `PSW`, the Program Status Word. For arithmetic and logic revision, focus on `CY`, `AC`, `OV`, and `P`. `PSW` also contains register-bank select bits, so it controls both status reporting and active register-bank selection.

### Page 49: MCQ - NOP In Delay Loops

<a href="images/Day%2015/Screenshot%202026-06-19%20164649.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164649.png" alt="NOP timing loop MCQ" width="960"></a>

`NOP` is used to waste a fixed instruction time without changing registers, memory, or flags. In practical delay loops, most delay comes from loop instructions, but `NOP` is useful when a small timing adjustment is needed.

### Page 50: MCQ - Unconditional Jump

<a href="images/Day%2015/Screenshot%202026-06-19%20164702.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164702.png" alt="8051 unconditional jump MCQ" width="960"></a>

The unconditional jump option is `SJMP`; the slide appears to show it with a typo. `CJNE`, `JNZ`, and `JZ` are conditional because they depend on comparison result or accumulator state.

### Page 51: MCQ - Reset Interrupt Priority

<a href="images/Day%2015/Screenshot%202026-06-19%20164729.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164729.png" alt="8051 highest interrupt priority after reset MCQ" width="960"></a>

Among the listed choices, external interrupt 0 is the highest in the default polling order after reset. Do not confuse this with programmable priority levels. If priority bits are left at reset defaults, all interrupts are at the same programmable priority level, and the internal polling order breaks ties.

### Page 52: MCQ - Reset Interrupt Priority Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20164810.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164810.png" alt="8051 highest interrupt priority clean MCQ" width="960"></a>

This repeat view reinforces the same distinction: priority-level configuration and default polling order are related but not identical. The exam answer here is external interrupt 0.

### Page 53: MCQ - Code Memory Limit

<a href="images/Day%2015/Screenshot%202026-06-19%20164822.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164822.png" alt="8051 code memory 64K MCQ" width="960"></a>

The classic architecture has a 16-bit program counter, so it can address up to 64 KB of code memory. The slide uses "opcode" loosely; the reliable wording is that no classic 8051 code fetch can go beyond the 64 KB program-memory address space.

### Page 54: MCQ - Code Memory Limit Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20164844.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164844.png" alt="8051 code memory 64K clean MCQ" width="960"></a>

This repeat view again points to 64 KB. Remember the reason: `PC` is 16 bits, so the maximum addressable code range is `0000H-FFFFH`.

### Page 55: MCQ - Two 16-Bit Registers

<a href="images/Day%2015/Screenshot%202026-06-19%20164900.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164900.png" alt="8051 PC and DPTR 16 bit registers MCQ" width="960"></a>

The usual answer is `PC` and `DPTR`. `DPTR` is software visible as `DPH:DPL`. `PC` is 16-bit but is changed by sequencing, jumps, calls, returns, interrupts, and reset; it is not normally accessed with a direct `MOV PC,...` instruction.

### Page 56: MCQ - 16-Bit Registers Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20164912.png"><img src="images/Day%2015/Screenshot%202026-06-19%20164912.png" alt="8051 PC DPTR 16 bit registers clean MCQ" width="960"></a>

This cleaner view repeats the `PC` and `DPTR` answer. The trap is `SP`: it is an 8-bit SFR in the classic 8051, not a 16-bit stack pointer.

### Page 57: MCQ - Reset State

<a href="images/Day%2015/Screenshot%202026-06-19%20165015.png"><img src="images/Day%2015/Screenshot%202026-06-19%20165015.png" alt="8051 reset state SP P1 register bank MCQ" width="960"></a>

On reset, `SP=07H`, `PSW=00H`, and the active register bank is Bank 0 because `RS1=0` and `RS0=0`. Port latches are reset high, so `P1=FFH` is the usual reset-state answer. The first stack push increments `SP` to `08H` before storing data.

### Page 58: MCQ - Invalid MOV Form

<a href="images/Day%2015/Screenshot%202026-06-19%20165222.png"><img src="images/Day%2015/Screenshot%202026-06-19%20165222.png" alt="8051 invalid MOV direct address MCQ" width="960"></a>

The invalid form is `MOV 80H,92H`. `80H` is byte address `P0`, but `92H` is a bit address inside the bit-addressable SFR region, corresponding to a Port 1 bit. A byte `MOV direct,direct` needs byte direct addresses; it cannot use a bit address as the source.

### Page 59: MCQ - Invalid MOV Annotated

<a href="images/Day%2015/Screenshot%202026-06-19%20165639.png"><img src="images/Day%2015/Screenshot%202026-06-19%20165639.png" alt="8051 invalid MOV annotated MCQ" width="960"></a>

The annotations make the address-space trap visible. `80H` is `P0`; `88H` is `TCON`; `82H` is `DPL`; `90H` is `P1`. But `92H` is not the byte address of an SFR. It is a bit address, so it belongs with bit instructions such as `SETB 92H`, not with byte `MOV direct,direct`.

### Page 60: MCQ - Mode 1 Serial Communication

<a href="images/Day%2015/Screenshot%202026-06-19%20165748.png"><img src="images/Day%2015/Screenshot%202026-06-19%20165748.png" alt="8051 Mode 1 standard UART MCQ" width="960"></a>

Mode 1 is the standard 8-bit UART mode: start bit, eight data bits, stop bit, and variable baud rate. It is not the multiprocessor mode and not the shift-register mode. Multiprocessor ninth-bit behavior belongs mainly with modes 2 and 3.

### Page 61: MCQ - Power-Up Stack Pointer

<a href="images/Day%2015/Screenshot%202026-06-19%20165802.png"><img src="images/Day%2015/Screenshot%202026-06-19%20165802.png" alt="8051 stack pointer power up MCQ" width="960"></a>

On power-up/reset, the stack pointer contains `07H`. Because 8051 stack push pre-increments `SP`, the first pushed byte is stored at internal RAM address `08H`. If a question asks "value of SP," answer `07H`; if it asks "first stack storage location," answer `08H`.

### Page 62: MCQ - TXD Data Source

<a href="images/Day%2015/Screenshot%202026-06-19%20175327.png"><img src="images/Day%2015/Screenshot%202026-06-19%20175327.png" alt="8051 TXD transfer source SBUF MCQ" width="960"></a>

To transmit a byte through `TXD`, software writes the byte to `SBUF`. `SCON` configures the serial port, but it does not hold the outgoing data byte. The accumulator may be the source in code such as `MOV SBUF,A`, but the serial hardware transmits from the transmit side of `SBUF`.

### Page 63: MCQ - 11.0592 MHz Timer Clock

<a href="images/Day%2015/Screenshot%202026-06-19%20175332.png"><img src="images/Day%2015/Screenshot%202026-06-19%20175332.png" alt="8051 11.0592 MHz machine cycle frequency MCQ" width="960"></a>

For the classic 12-clock 8051, the timer tick frequency is `fosc/12`. With `fosc = 11.0592 MHz`, that gives `921.6 kHz`. The period is the reciprocal: about `1.085 microseconds`. This crystal is common because it divides cleanly into standard serial baud rates.

### Page 64: MCQ - Math Flags Count

<a href="images/Day%2015/Screenshot%202026-06-19%20175550.png"><img src="images/Day%2015/Screenshot%202026-06-19%20175550.png" alt="8051 math flags count MCQ" width="960"></a>

The arithmetic/status flags usually counted for math questions are `CY`, `AC`, `OV`, and `P`, giving four. `F0` is a user flag and `RS1/RS0` select register banks, so they are part of `PSW` but not ordinary arithmetic-result flags.

### Page 65: MCQ - Bit Set/Reset Mode

<a href="images/Day%2015/Screenshot%202026-06-19%20175658.png"><img src="images/Day%2015/Screenshot%202026-06-19%20175658.png" alt="8255 bit set reset mode MCQ" width="960"></a>

Bit set/reset mode belongs to the Intel 8255 programmable peripheral interface. It lets software set or reset individual bits of Port C. This should not be confused with the 8051's own bit-addressable RAM/SFR feature; both involve individual bits, but they are different devices and programming models.

### Page 66: MCQ - Field-Device Interface Module

<a href="images/Day%2015/Screenshot%202026-06-19%20175725.png"><img src="images/Day%2015/Screenshot%202026-06-19%20175725.png" alt="field devices input module MCQ" width="960"></a>

The expected answer here is input modules, because field devices such as switches and sensors are sensed by the controller through input interface modules. In a real control system, actuators use output modules, so always read the direction implied by the question.

### Page 67: MCQ - CRC Error Detection

<a href="images/Day%2015/Screenshot%202026-06-19%20175919.png"><img src="images/Day%2015/Screenshot%202026-06-19%20175919.png" alt="CRC transmission error detection MCQ" width="960"></a>

CRC means cyclic redundancy check/code and is used to detect transmission errors. The sender appends check bits calculated from the message, and the receiver recalculates to see whether the received frame is likely corrupted.

### Page 68: MOVC Instruction

<a href="images/Day%2015/Screenshot%202026-06-19%20180533.png"><img src="images/Day%2015/Screenshot%202026-06-19%20180533.png" alt="8051 MOVC code memory instruction" width="960"></a>

`MOVC` means move code byte to accumulator. It reads from code/program memory, not internal RAM and not external data memory. The effective code address is formed as `A + DPTR` or `A + PC`, which makes `MOVC` useful for lookup tables stored in program memory.

### Page 69: MOVX Instruction

<a href="images/Day%2015/Screenshot%202026-06-19%20181115.png"><img src="images/Day%2015/Screenshot%202026-06-19%20181115.png" alt="8051 MOVX external data memory instruction" width="960"></a>

`MOVX` accesses external data memory, often called XRAM. This is different from `MOVC`, which reads code memory. In `MOVX`, the accumulator is the data endpoint, while the address is supplied by `DPTR`, `R0`, or `R1` depending on the instruction form.

### Page 70: MOVX @Ri And @DPTR Details

<a href="images/Day%2015/Screenshot%202026-06-19%20181253.png"><img src="images/Day%2015/Screenshot%202026-06-19%20181253.png" alt="8051 MOVX Ri and DPTR addressing details" width="960"></a>

This page gives the practical bus distinction. `MOVX @R0,A` or `MOVX @R1,A` uses an 8-bit external address, so Port 0 supplies the low address/data path and Port 2 is not part of the address. `MOVX @DPTR,A` uses a 16-bit external address, so Port 0 carries low address/data and Port 2 supplies the high address byte.

### Page 71: SWAP A

<a href="images/Day%2015/Screenshot%202026-06-19%20181927.png"><img src="images/Day%2015/Screenshot%202026-06-19%20181927.png" alt="8051 SWAP A instruction" width="960"></a>

`SWAP A` exchanges the accumulator's two nibbles: bits `7-4` move to `3-0`, and bits `3-0` move to `7-4`. This is useful in BCD, hexadecimal digit handling, and nibble-oriented display or conversion code.

### Page 72: XCH

<a href="images/Day%2015/Screenshot%202026-06-19%20181952.png"><img src="images/Day%2015/Screenshot%202026-06-19%20181952.png" alt="8051 XCH instruction" width="960"></a>

`XCH A,operand` exchanges the accumulator with the selected byte operand. The accumulator is always one side of the exchange. This is different from a general register-to-register swap instruction; the 8051 instruction form is accumulator-centered.

### Page 73: PUSH Operation

<a href="images/Day%2015/Screenshot%202026-06-19%20182109.png"><img src="images/Day%2015/Screenshot%202026-06-19%20182109.png" alt="8051 PUSH operation stack rule" width="960"></a>

`PUSH direct` first increments `SP`, then stores the byte from the direct address into internal RAM at the new stack location. This pre-increment behavior is why reset `SP=07H` causes the first pushed byte to go to `08H`.

### Page 74: PUSH Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20182151.png"><img src="images/Day%2015/Screenshot%202026-06-19%20182151.png" alt="8051 PUSH clean view" width="960"></a>

This repeat view reinforces the stack direction. The 8051 stack grows upward in internal RAM as `SP` increases. A badly placed stack can overwrite register-bank RAM, bit-addressable RAM, or variables, so stack placement is a real programming decision.

### Page 75: ADD And ADDC

<a href="images/Day%2015/Screenshot%202026-06-19%20182346.png"><img src="images/Day%2015/Screenshot%202026-06-19%20182346.png" alt="8051 ADD and ADDC instruction" width="960"></a>

`ADD A,source` adds the source byte to the accumulator. `ADDC A,source` does the same but also includes the current carry flag. That makes `ADDC` essential for multi-byte addition: add the low byte first, then use `ADDC` on higher bytes so the carry propagates.

### Page 76: SUBB

<a href="images/Day%2015/Screenshot%202026-06-19%20182600.png"><img src="images/Day%2015/Screenshot%202026-06-19%20182600.png" alt="8051 SUBB subtract with borrow instruction" width="960"></a>

`SUBB A,source` performs `A = A - source - CY`. In subtraction, `CY` represents borrow. If a borrow is needed, carry is set; if no borrow is needed, carry is cleared. For multi-byte subtraction, subtract the low byte first and then use `SUBB` for higher bytes so the borrow propagates.

### Page 77: Blank Transition Capture

<a href="images/Day%2015/Screenshot%202026-06-19%20182928.png"><img src="images/Day%2015/Screenshot%202026-06-19%20182928.png" alt="blank transition capture" width="960"></a>

This image is effectively a blank transition capture. It has no new technical content, but it is referenced here so the image sequence remains complete and every added file is accounted for.

### Page 78: MUL AB

<a href="images/Day%2015/Screenshot%202026-06-19%20182938.png"><img src="images/Day%2015/Screenshot%202026-06-19%20182938.png" alt="8051 MUL AB instruction" width="960"></a>

`MUL AB` multiplies the unsigned contents of accumulator `A` and register `B`. The 16-bit product is split: the low byte goes to `A`, and the high byte goes to `B`. Carry is cleared. Overflow indicates that the product is larger than 8 bits, which means `B` is not zero after multiplication.

### Page 79: DIV AB

<a href="images/Day%2015/Screenshot%202026-06-19%20183225.png"><img src="images/Day%2015/Screenshot%202026-06-19%20183225.png" alt="8051 DIV AB instruction" width="960"></a>

`DIV AB` divides unsigned `A` by unsigned `B`. After a valid divide, `A` receives the quotient and `B` receives the remainder. Carry is cleared. Overflow is used to report divide-by-zero; for a valid nonzero divisor, overflow is cleared.

### Page 80: INC Register

<a href="images/Day%2015/Screenshot%202026-06-19%20183345.png"><img src="images/Day%2015/Screenshot%202026-06-19%20183345.png" alt="8051 INC register instruction" width="960"></a>

`INC` adds one to the addressed register, direct byte, indirect byte, accumulator, or `DPTR` depending on the form. For an 8-bit operand, `FFH + 1` rolls over to `00H`. Unlike addition, this rollover does not set carry.

### Page 81: INC Register Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20214533.png"><img src="images/Day%2015/Screenshot%202026-06-19%20214533.png" alt="8051 INC register clean view" width="960"></a>

This cleaner view repeats the same rule: `INC` can wrap an 8-bit value but does not treat the wrap as a carry-out event. That distinction is a frequent MCQ trap because students expect `FFH + 1` to behave like `ADD`.

### Page 82: INC DPTR Partial View

<a href="images/Day%2015/Screenshot%202026-06-19%20214551.png"><img src="images/Day%2015/Screenshot%202026-06-19%20214551.png" alt="8051 INC DPTR partial view" width="960"></a>

`INC DPTR` increments the 16-bit data pointer as one unsigned 16-bit value. It is not the same as incrementing only `DPL`; if `DPL` overflows, the carry into `DPH` is handled internally as part of the 16-bit increment.

### Page 83: INC DPTR Rollover

<a href="images/Day%2015/Screenshot%202026-06-19%20214615.png"><img src="images/Day%2015/Screenshot%202026-06-19%20214615.png" alt="8051 INC DPTR rollover" width="960"></a>

If `DPTR=FFFFH`, `INC DPTR` rolls it to `0000H`. Carry is still not set. This makes `INC DPTR` useful for table or memory traversal, but code that needs to detect wraparound must test `DPTR` separately.

### Page 84: INC Register Final View

<a href="images/Day%2015/Screenshot%202026-06-19%20214638.png"><img src="images/Day%2015/Screenshot%202026-06-19%20214638.png" alt="8051 INC instruction final view" width="960"></a>

This final view reinforces the 8-bit `INC` behavior. Use it as a quick contrast table: `ADD` affects arithmetic flags, while `INC` changes the operand but does not set carry on rollover.

### Page 85: ORL

<a href="images/Day%2015/Screenshot%202026-06-19%20214929.png"><img src="images/Day%2015/Screenshot%202026-06-19%20214929.png" alt="8051 ORL instruction" width="960"></a>

`ORL` performs bitwise OR. A result bit becomes 1 if either corresponding operand bit is 1. This is useful for setting selected bits while leaving other bits unchanged, for example `ORL P1,#mask`.

### Page 86: ANL

<a href="images/Day%2015/Screenshot%202026-06-19%20215019.png"><img src="images/Day%2015/Screenshot%202026-06-19%20215019.png" alt="8051 ANL instruction" width="960"></a>

`ANL` performs bitwise AND. A result bit stays 1 only if both input bits are 1. This is the standard masking operation: AND with `0` clears selected bits, while AND with `1` preserves selected bits.

### Page 87: XRL

<a href="images/Day%2015/Screenshot%202026-06-19%20215113.png"><img src="images/Day%2015/Screenshot%202026-06-19%20215113.png" alt="8051 XRL instruction" width="960"></a>

`XRL` performs bitwise exclusive OR. A result bit becomes 1 only when the two input bits differ. This is useful for toggling selected bits when one operand is a mask.

### Page 88: CLR

<a href="images/Day%2015/Screenshot%202026-06-19%20215303.png"><img src="images/Day%2015/Screenshot%202026-06-19%20215303.png" alt="8051 CLR instruction" width="960"></a>

`CLR` clears the specified destination. `CLR A` clears the accumulator, `CLR C` clears carry, and `CLR bit` clears a bit-addressable RAM or SFR bit. The operand type matters because 8051 has both byte and bit instruction forms.

### Page 89: RL A

<a href="images/Day%2015/Screenshot%202026-06-19%20215520.png"><img src="images/Day%2015/Screenshot%202026-06-19%20215520.png" alt="8051 RL A instruction" width="960"></a>

`RL A` rotates the accumulator left without carry. Bit 7 wraps into bit 0. The carry flag is not used as an extra bit in this operation, so it differs from `RLC A`.

### Page 90: RR A

<a href="images/Day%2015/Screenshot%202026-06-19%20215619.png"><img src="images/Day%2015/Screenshot%202026-06-19%20215619.png" alt="8051 RR A instruction" width="960"></a>

`RR A` rotates the accumulator right without carry. Bit 0 wraps into bit 7. It is a circular movement inside the accumulator only.

### Page 91: RRC A

<a href="images/Day%2015/Screenshot%202026-06-19%20220250.png"><img src="images/Day%2015/Screenshot%202026-06-19%20220250.png" alt="8051 RRC A instruction" width="960"></a>

`RRC A` rotates right through carry. Bit 0 moves into `CY`, and the old carry moves into bit 7. This is useful when shifting multi-byte values through carry, because carry becomes the link between bytes.

### Page 92: JMP @A+DPTR

<a href="images/Day%2015/Screenshot%202026-06-19%20220302.png"><img src="images/Day%2015/Screenshot%202026-06-19%20220302.png" alt="8051 JMP at A plus DPTR instruction" width="960"></a>

`JMP @A+DPTR` is an indexed indirect jump. The target address is computed by adding the unsigned value in `A` to `DPTR`. This is useful for jump tables stored in code memory where accumulator selects which table entry to use.

### Page 93: JC

<a href="images/Day%2015/Screenshot%202026-06-19%20220542.png"><img src="images/Day%2015/Screenshot%202026-06-19%20220542.png" alt="8051 JC jump if carry set instruction" width="960"></a>

`JC reladdr` branches if carry is set. It does not create the carry condition; it only tests the current `CY` value left by an earlier arithmetic, compare-like, rotate-through-carry, or bit operation.

### Page 94: DJNZ

<a href="images/Day%2015/Screenshot%202026-06-19%20220745.png"><img src="images/Day%2015/Screenshot%202026-06-19%20220745.png" alt="8051 DJNZ instruction" width="960"></a>

`DJNZ operand,reladdr` decrements the operand and branches if the new value is not zero. It is a compact loop instruction. If the operand starts at `00H`, decrementing wraps to `FFH`, so the branch is taken because the new value is not zero.

### Page 95: JZ

<a href="images/Day%2015/Screenshot%202026-06-19%20220904.png"><img src="images/Day%2015/Screenshot%202026-06-19%20220904.png" alt="8051 JZ instruction" width="960"></a>

`JZ reladdr` branches if the accumulator is zero. The slide text appears to show `JNZ` in the syntax line, but the operation name and function are `JZ`. The test is on accumulator value, not on the PSW zero flag, because 8051 does not have a dedicated zero flag like 8085.

### Page 96: JNZ

<a href="images/Day%2015/Screenshot%202026-06-19%20220958.png"><img src="images/Day%2015/Screenshot%202026-06-19%20220958.png" alt="8051 JNZ instruction" width="960"></a>

`JNZ reladdr` branches if accumulator is not zero. Together, `JZ` and `JNZ` replace many zero-flag style checks from other processors by testing `A` directly.

### Page 97: SJMP

<a href="images/Day%2015/Screenshot%202026-06-19%20221112.png"><img src="images/Day%2015/Screenshot%202026-06-19%20221112.png" alt="8051 SJMP instruction" width="960"></a>

`SJMP reladdr` is an unconditional short jump. Its target is relative to the address after the `SJMP` instruction and must fit in an 8-bit signed offset, usually described as `-128` to `+127` bytes from the following instruction.

### Page 98: SJMP Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20221745.png"><img src="images/Day%2015/Screenshot%202026-06-19%20221745.png" alt="8051 SJMP clean view" width="960"></a>

This cleaner view repeats the relative-address rule. `SJMP` is compact and position-relative; it does not contain a full 16-bit address.

### Page 99: AJMP

<a href="images/Day%2015/Screenshot%202026-06-19%20222211.png"><img src="images/Day%2015/Screenshot%202026-06-19%20222211.png" alt="8051 AJMP instruction" width="960"></a>

`AJMP code_address` is an absolute jump within a 2 KB block. It uses an 11-bit destination, so it can replace only the lower 11 bits of the program counter while the upper page bits come from the current code area.

### Page 100: AJMP Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20222725.png"><img src="images/Day%2015/Screenshot%202026-06-19%20222725.png" alt="8051 AJMP clean view" width="960"></a>

This view restates the same rule in exam language: `AJMP` is absolute, but not anywhere in 64 KB. It is absolute only inside the reachable 2 KB page.

### Page 101: AJMP Bit Layout

<a href="images/Day%2015/Screenshot%202026-06-19%20223951.png"><img src="images/Day%2015/Screenshot%202026-06-19%20223951.png" alt="8051 AJMP bit layout explanation" width="960"></a>

The annotations show how the 11-bit destination is assembled. The second instruction byte supplies the lower 8 bits, and opcode bits supply the upper 3 bits of the 11-bit target. The untouched high PC bits keep the jump within the same 2 KB block.

### Page 102: AJMP Annotated Repeat

<a href="images/Day%2015/Screenshot%202026-06-19%20223955.png"><img src="images/Day%2015/Screenshot%202026-06-19%20223955.png" alt="8051 AJMP annotated repeat" width="960"></a>

This repeat view is useful because it connects the mnemonic to actual instruction encoding. If an `AJMP` target is outside the same 2 KB block, the assembler must use a different jump form such as `LJMP`.

### Page 103: ACALL 2 KB Block Rule

<a href="images/Day%2015/Screenshot%202026-06-19%20225104.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225104.png" alt="8051 ACALL 2KB block rule" width="960"></a>

`ACALL` has the same 11-bit target limitation idea as `AJMP`, but it is a call rather than a jump. It stores the return address on the stack, then branches to a subroutine in the same 2 KB block. For calls outside that block, use `LCALL`.

### Page 104: RET

<a href="images/Day%2015/Screenshot%202026-06-19%20225255.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225255.png" alt="8051 RET instruction" width="960"></a>

`RET` returns from a subroutine by restoring the program counter from the stack. The reliable way to revise this is with a stack picture: call instructions push the return address bytes, `SP` points to the top byte, and `RET` pops the saved bytes back into `PC` so execution continues after the call.

### Page 105: RETI

<a href="images/Day%2015/Screenshot%202026-06-19%20225357.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225357.png" alt="8051 RETI instruction" width="960"></a>

`RETI` also restores the saved program counter, but it additionally tells the interrupt-control logic that the interrupt service routine has ended. It does not automatically restore accumulator, PSW, or general registers; an ISR must preserve anything it changes.

### Page 106: JNB

<a href="images/Day%2015/Screenshot%202026-06-19%20225536.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225536.png" alt="8051 JNB instruction" width="960"></a>

`JNB bit,reladdr` means jump if bit not set. It tests a bit-addressable RAM bit or bit-addressable SFR bit. This instruction is one reason 8051 is strong in control applications: individual status/control bits can be tested directly.

### Page 107: JBC

<a href="images/Day%2015/Screenshot%202026-06-19%20225558.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225558.png" alt="8051 JBC instruction" width="960"></a>

`JBC bit,reladdr` means jump if bit set and clear the bit. If the tested bit is 1, the branch happens and the bit is cleared before branching. If the bit is 0, execution continues and the bit is unchanged. This is useful for event flags that should be consumed once.

### Page 108: MCQ - Battery Use

<a href="images/Day%2015/Screenshot%202026-06-19%20225617.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225617.png" alt="microcontrollers battery low power MCQ" width="960"></a>

Microcontrollers are commonly used in battery-powered systems because they can provide low-power operation with CPU, memory, I/O, timers, and serial support integrated on one chip. The best answer is low power consumption.

### Page 109: MCQ - Indirect Operator

<a href="images/Day%2015/Screenshot%202026-06-19%20225643.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225643.png" alt="8051 indirect addressing operator MCQ" width="960"></a>

The register-indirect operator is `@`. Examples are `MOV A,@R0`, `MOV @R1,A`, and `MOVX A,@DPTR`. The `#` symbol means immediate data, not indirect addressing.

### Page 110: MCQ - ADD Destination

<a href="images/Day%2015/Screenshot%202026-06-19%20225703.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225703.png" alt="8051 ADD destination accumulator MCQ" width="960"></a>

For 8051 `ADD`, the destination is always accumulator: `ADD A,source`. The source can be a register, direct byte, indirect byte, or immediate constant, but the result returns to `A`.

### Page 111: MCQ - Two's-Complement Program

<a href="images/Day%2015/Screenshot%202026-06-19%20225742.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225742.png" alt="8051 two's complement program MCQ" width="960"></a>

The program loads `R0` into `A`, complements every bit with `CPL A`, then adds one with `INC A`. Complement plus one is the two's-complement operation, so the program finds the two's complement of the value originally in `R0`.

### Page 112: MCQ - Indirect RAM To Accumulator

<a href="images/Day%2015/Screenshot%202026-06-19%20225819.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225819.png" alt="8051 MOV A at Ri indirect RAM MCQ" width="960"></a>

`MOV A,@Ri` is the indirect internal-RAM-to-accumulator form, where `Ri` means `R0` or `R1`. The register contains the internal RAM address; the memory byte at that address is copied into `A`.

### Page 113: MCQ - ADD Destination Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20225835.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225835.png" alt="8051 ADD destination accumulator clean MCQ" width="960"></a>

This repeat view reinforces the accumulator-centered design of 8051 arithmetic. Many arithmetic instructions write back to `A`; they do not allow arbitrary register destinations.

### Page 114: MCQ - DA A Condition

<a href="images/Day%2015/Screenshot%202026-06-19%20225838.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225838.png" alt="8051 DA A condition MCQ" width="960"></a>

`DA A` adjusts the accumulator after BCD addition. The low nibble is corrected by adding `06H` when the low digit is invalid or `AC=1`; the high digit is corrected by adding `60H` when the high digit is invalid or `CY=1`. The MCQ's simplified answer is "either CY or AC is 1," but the full rule also depends on nibble values greater than 9.

### Page 115: MCQ - DA A Condition Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20225857.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225857.png" alt="8051 DA A condition clean MCQ" width="960"></a>

This cleaner view repeats the decimal-adjust trap. For exam solving, compute the binary addition first, then apply the lower-nibble and upper-nibble BCD correction tests.

### Page 116: MCQ - SUBB A,R4

<a href="images/Day%2015/Screenshot%202026-06-19%20225924.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225924.png" alt="8051 SUBB A R4 MCQ" width="960"></a>

`SUBB A,R4` means accumulator minus `R4` minus carry/borrow. If the question ignores carry, the closest simplified answer is `A - R4`, but the exact operation is `A = A - R4 - CY`.

### Page 117: MCQ - DIV Flag Options

<a href="images/Day%2015/Screenshot%202026-06-19%20225931.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225931.png" alt="8051 DIV flags MCQ options" width="960"></a>

For reliable revision, use the official instruction behavior: valid `DIV AB` clears carry and clears overflow; divide-by-zero sets overflow. Auxiliary carry is not the meaningful division flag in the Intel instruction table. If the class answer says `CY=0, AC=0`, remember that the robust concept is really `CY=0` and `OV=0` for a valid divide.

### Page 118: MCQ - DIV Flag Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20225952.png"><img src="images/Day%2015/Screenshot%202026-06-19%20225952.png" alt="8051 DIV flags clean MCQ" width="960"></a>

This view repeats the division-flag options. The safe exam correction is to write beside it: `DIV AB`: quotient in `A`, remainder in `B`, `CY=0`, `OV=0` if divisor is nonzero, `OV=1` if divisor is zero.

### Page 119: MCQ - DIV Flag Marked View

<a href="images/Day%2015/Screenshot%202026-06-19%20230007.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230007.png" alt="8051 DIV flags marked MCQ" width="960"></a>

The marked course answer points to `CY=0, AC=0`. Keep that as the class MCQ memory if needed, but cross-check your conceptual answer against the instruction reference: carry is cleared and overflow is the divide-validity flag.

### Page 120: MCQ - Signed Overflow

<a href="images/Day%2015/Screenshot%202026-06-19%20230029.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230029.png" alt="8051 signed overflow MCQ" width="960"></a>

For signed 8-bit addition/subtraction, overflow is about the sign bit, not just unsigned carry. The hardware idea is carry into bit 7 compared with carry out of bit 7. If they differ, `OV=1`.

### Page 121: MCQ - Signed Overflow Annotated

<a href="images/Day%2015/Screenshot%202026-06-19%20230114.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230114.png" alt="8051 signed overflow annotated MCQ" width="960"></a>

The handwritten annotation points to the D6/D7 boundary. That is the right mental model: carry from bit 6 into bit 7 and carry out of bit 7 determine signed overflow. Unsigned carry and signed overflow are different flags because they answer different questions.

### Page 122: MCQ - Unsigned Addition Flag

<a href="images/Day%2015/Screenshot%202026-06-19%20230121.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230121.png" alt="8051 unsigned addition carry MCQ" width="960"></a>

For unsigned addition, the important flag is `CY`. It tells whether the result exceeded 8 bits. `OV` is for signed interpretation, and `AC` is mainly for BCD/low-nibble correction.

### Page 123: MCQ - Unsigned Addition Flag Marked

<a href="images/Day%2015/Screenshot%202026-06-19%20230129.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230129.png" alt="8051 unsigned addition carry marked MCQ" width="960"></a>

The marked answer confirms `CY`. A quick example: `F0H + 20H = 110H`, so the stored byte is `10H` and carry records the ninth bit.

### Page 124: MCQ - Logic Instructions And PSW

<a href="images/Day%2015/Screenshot%202026-06-19%20230153.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230153.png" alt="8051 logical instructions no PSW effect MCQ" width="960"></a>

For byte logical operations, `ANL`, `ORL`, and `XRL` are usually taught as not changing the PSW flags. Nuance: bit operations that explicitly use carry, such as `ANL C,bit`, do affect `CY` because carry is the destination.

### Page 125: MCQ - Logic Instructions Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20230201.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230201.png" alt="8051 logical instructions no flag effect clean MCQ" width="960"></a>

This repeat view reinforces the byte-operation rule. Do not import 8085 habits blindly: in 8051, many byte logical instructions do not update flags the way some other processors do.

### Page 126: MCQ - ANL Uses

<a href="images/Day%2015/Screenshot%202026-06-19%20230233.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230233.png" alt="8051 ANL uses MCQ" width="960"></a>

`ANL` is used both to AND two values and to mask selected bits. Because masking is just a purposeful AND operation, "all of the mentioned" is the best answer when both options are listed.

### Page 127: MCQ - ANL Answer Marked

<a href="images/Day%2015/Screenshot%202026-06-19%20230253.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230253.png" alt="8051 ANL uses marked answer MCQ" width="960"></a>

The marked answer confirms all listed uses. A practical example is `ANL A,#0FH`, which masks away the high nibble and keeps only the low nibble.

### Page 128: MCQ - CJNE Behavior

<a href="images/Day%2015/Screenshot%202026-06-19%20230310.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230310.png" alt="8051 CJNE behavior MCQ" width="960"></a>

`CJNE` means compare and jump if not equal. It branches when the two operands differ. It also sets carry if the first operand is less than the second operand in unsigned comparison, and clears carry otherwise. The carry behavior is separate from the branch decision.

### Page 129: MCQ - CJNE Clean View

<a href="images/Day%2015/Screenshot%202026-06-19%20230332.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230332.png" alt="8051 CJNE clean view MCQ" width="960"></a>

This cleaner view is a good place to write the exact correction: `if op1 != op2, jump`; `CY=1` when `op1 < op2`; `CY=0` when `op1 >= op2`.

### Page 130: MCQ - ADD Operand Rule

<a href="images/Day%2015/Screenshot%202026-06-19%20230359.png"><img src="images/Day%2015/Screenshot%202026-06-19%20230359.png" alt="8051 ADD operand rule MCQ" width="960"></a>

For 8051 addition, accumulator is the destination and the source can be register, direct memory/SFR, register-indirect memory, or immediate data. That is why the best answer is the option that keeps accumulator as destination and allows multiple source forms.

## Serial Port Deepening

### Software View

| Register/bit area | Address | What software does with it |
| --- | ---: | --- |
| `SBUF` write path | `99H` | Load a byte for transmission. |
| `SBUF` read path | `99H` | Read the byte received by the serial receiver. |
| `SCON` | `98H` | Select serial mode, enable reception, and hold serial status flags. |
| `TXD` | `P3.1` | Serial transmit output pin. |
| `RXD` | `P3.0` | Serial receive input pin. |

The `SBUF` address is the main trap. In ordinary RAM thinking, one address normally means one read/write byte. In 8051 serial hardware, the operation direction selects the internal buffer. A write feeds the transmitter; a read takes data from the receiver. This is why the same SFR name can be correct in both transmit and receive code.

### Serial Direction Model

| Direction | CPU-side format | Hardware conversion | Pin-side format |
| --- | --- | --- | --- |
| Transmit | Parallel byte on internal bus | Parallel-in serial-out shifting | Serial bits on `TXD` |
| Receive | Parallel byte read from receive `SBUF` | Serial-in parallel-out shifting | Serial bits from `RXD` |

Full duplex is possible because transmit and receive paths are separate. The CPU can load transmit data while the receive path is collecting incoming bits. The receive-buffering note in the screenshot should be understood as protection against immediate data loss: software still must read the receive buffer in time, but it does not have to read every bit as it arrives.

### SCON Mental Model

| SCON role | What it controls or reports |
| --- | --- |
| Mode selection | Chooses the serial-port operating mode. |
| Receive enable | Allows the receiver to accept serial input. |
| Status flags | Indicate transmit completion or receive completion. |
| Interrupt link | Serial status can be polled or used with serial interrupt handling. |

`SCON` combines control and status. Control bits are written by software to choose behavior. Status flags are set by hardware when serial events finish, and software tests or clears them according to the program structure. This is the same general pattern seen in timers: configuration bits define behavior, while flags report events.

### SCON Bit Quick Table

| Bit | Symbol | Main meaning |
| --- | --- | --- |
| `SCON.7` | `SM0` | Serial mode select bit. |
| `SCON.6` | `SM1` | Serial mode select bit. |
| `SCON.5` | `SM2` | Multiprocessor-communication control in applicable modes. |
| `SCON.4` | `REN` | Receive enable. |
| `SCON.3` | `TB8` | Ninth transmit data bit in 9-bit modes. |
| `SCON.2` | `RB8` | Ninth received data bit in 9-bit modes. |
| `SCON.1` | `TI` | Transmit interrupt flag. |
| `SCON.0` | `RI` | Receive interrupt flag. |

The two most frequently tested status flags are `TI` and `RI`. `TI` tells software the transmitter has reached the completion point for the frame, and `RI` tells software a character has been received. Both flags must be cleared by software after handling; otherwise a polling loop or interrupt routine may repeatedly think the same old event is still new.

For basic serial programs, a common mental starting point is: select the required mode with `SM0/SM1`, set `REN` if reception is needed, keep `SM2=0`, keep `TB8=0` and `RB8=0` unless using 9-bit communication, then handle `TI` and `RI` as event flags.

### Serial Mode Selection

| `SM0` | `SM1` | Mode idea |
| --- | --- | --- |
| `0` | `0` | Mode 0: 8-bit shift-register operation; `RXD` carries data and `TXD` outputs shift clock. |
| `0` | `1` | Mode 1: 8-bit UART-style asynchronous operation with start/stop framing and variable baud rate. |
| `1` | `0` | Mode 2: 9-bit UART operation with fixed oscillator-derived baud rate. |
| `1` | `1` | Mode 3: 9-bit UART operation with variable timer-provided baud rate. |

The screenshot's "three asynchronous modes" line is the key high-level memory hook: modes 1, 2, and 3 are framed asynchronous communication modes, while mode 0 is the shift-register mode. Once the mode is chosen, the remaining setup must match it: `REN` for reception, `TB8/RB8` relevance for 9-bit modes, and baud-rate generation for modes that need a programmable bit clock.

Mode 0 is useful to remember separately because it does not behave like the later asynchronous UART-style modes. It shifts exactly 8 bits, sends/receives the least-significant bit first, uses `TXD` as a clock output, and can be used for simple serial expansion of I/O capability.

Mode 1 is the common 8-bit UART case: one start bit, eight data bits, and one stop bit. The frame is 10 bits long even though the data payload is 8 bits. This distinction matters for baud-rate calculations because baud counts transmitted bits, not just data bytes.

Mode 1 receive logic depends on edge detection and timing alignment. The start bit begins with a high-to-low transition on `RXD`; after that, the serial timing path aligns sampling to the incoming bit stream. If the baud-rate setup is wrong, framing and sampling errors can occur even when the `SBUF` and `SCON` code is syntactically correct.

False start-bit detection protects the receiver from noise spikes. A short low pulse can look like the beginning of a start bit, but if the line is not still low at the validation point, the hardware rejects it and goes back to idle. This is why serial reception is about both bit values and bit timing.

For a valid Mode 1 receive frame, completion has three visible software effects: `RB8` receives the stop-bit state, receive `SBUF` receives the eight data bits, and `RI` becomes `1`. The program should treat `RI` as the signal that the received byte is ready to be consumed.

Mode 2 is the point where `TB8` and `RB8` become actual ninth-bit fields rather than ordinary 8-bit UART leftovers. This ninth bit is useful for parity-like schemes or multiprocessor/address-mark communication patterns. The important difference from Mode 1 is that Mode 2 has an 11-bit frame and fixed baud-rate choices tied to the oscillator path.

Mode 3 keeps the 11-bit frame structure of Mode 2 but changes the baud-rate source to the programmable timer path. A clean way to compare the asynchronous modes is: Mode 1 means 8-bit variable-baud UART, Mode 2 means 9-bit fixed-baud UART, and Mode 3 means 9-bit variable-baud UART.

The multiprocessor feature belongs with modes 2 and 3 because those modes carry a ninth bit. `SM2` uses that ninth-bit information to reduce unnecessary serial service. Conceptually, `RB8=1` can mark an address/control frame, while `RB8=0` can mark ordinary data.

### Receiver Enable Setup

| Instruction | Effect | When it is appropriate |
| --- | --- | --- |
| `MOV SCON,#10H` | Writes the whole `SCON` byte with only `REN` set. | Early initialization when all other `SCON` bits are meant to be zero. |
| `SETB REN` | Sets only `SCON.4`. | Safer after mode bits or flags already have meaningful values. |

`REN` must be set for reception. If `REN=0`, the receiver is disabled even if the baud-rate source and pins are otherwise configured. For transmit-only programs, `REN` may be left cleared; for any program that expects incoming characters, enabling `REN` is part of serial-port initialization.

### Serial Flag Service Pattern

| Flag | Set by | Meaning | Software response |
| --- | --- | --- | --- |
| `RI` | Hardware | A character has been received; receive buffer is ready. | Read `SBUF`, then clear `RI`. |
| `TI` | Hardware | Transmission reached the ready/completion point for the next byte. | Write next `SBUF` byte when needed, then clear `TI`. |

Polling and interrupts are two ways to react to the same flags. Polling repeatedly tests `RI` or `TI` in software. Interrupt-driven serial I/O lets the serial hardware request service when the flag event occurs, but the service routine still has to identify which flag caused the serial event and clear the handled flag.

### Baud Rate Connection

| Baud-rate case | What to remember |
| --- | --- |
| Fixed baud-rate mode | Clocking is derived from the 8051 timing source in a fixed way. |
| Variable baud-rate mode | Timer 1 is commonly programmed to generate the serial bit clock. |
| Timer dependency | Wrong timer reload values produce wrong serial speed even if `SBUF` and `SCON` code looks correct. |

Baud rate is measured in bits per second, so it is not the same as character rate. A transmitted character often includes framing bits in addition to the data bits, depending on the serial mode. Therefore, when solving baud-rate examples, count the bit timing produced by the timer first, then relate that to the character format.

For mode comparison, keep these fixed/variable rules visible: Mode 0 is fixed at `fosc/12`; Mode 2 is fixed at `fosc/64` by default or `fosc/32` with baud-rate doubling; Modes 1 and 3 use variable baud-rate timing from the timer path.

Baud rate can also be increased at the source or by control configuration. A higher oscillator increases clock-derived baud rates. Setting the baud-rate doubling bit in `PCON` changes the serial baud-rate divisor in the supported modes. This is why `PCON` belongs in serial-port setup even though it is not itself `SBUF` or `SCON`.

`PCON` should not be treated as a serial data register. It is a power/control register, but its `SMOD` bit affects serial timing. After reset, `SMOD` is clear; setting it is an intentional software choice to double the baud rate.

Common Timer 1 reload examples are easier if the reload byte is read as an 8-bit negative offset. `TH1=FDH` means `-3`, `TH1=FAH` means `-6`, `TH1=F4H` means `-12`, and `TH1=E8H` means `-24`. Smaller magnitude reloads overflow more often and produce higher baud rates.

The practical Mode 1 setup joins four registers/bits in order. `TMOD=20H` prepares Timer 1 as an 8-bit auto-reload timer. `TH1` stores the baud-rate reload value. `SCON=50H` chooses Mode 1 and enables reception. `TR1=1` starts Timer 1, which starts the baud-rate timing path. If any one of these is missing, the serial code may look correct but fail: no Timer 1 mode, no reload, no serial mode/receive enable, or no running timer.

### Transmit And Receive Loop Discipline

| Direction | Software waits for | Data register action | Flag action | Main mistake to avoid |
| --- | --- | --- | --- | --- |
| Transmit | `TI=1` after the previous character completes | Write the next byte to transmit `SBUF` | Clear `TI` before/after acknowledging the transmit event according to the program pattern | Do not overwrite transmit `SBUF` before the previous frame has completed. |
| Receive | `RI=1` after a complete character arrives | Read receive `SBUF` and store the byte safely | Clear `RI` after reading/storing the byte | Do not leave a received byte in `SBUF` until the next character overwrites it. |

The simple polling loops in these screenshots are intentionally repetitive because serial communication is event-driven. `SBUF` is the data doorway, but `TI` and `RI` are the event signs. A program that writes and reads `SBUF` without respecting `TI` and `RI` is not synchronized with the serial hardware.

### 8096 Context

The 8096 screenshots should be studied as a contrast with the 8051, not as a replacement for the 8051 material. The 8051 is an 8-bit controller with strong bit manipulation, small internal RAM, four ports, timers, and a serial port. The 8096 belongs to Intel's MCS-96 line and is presented as a higher-performance 16-bit control microcontroller with stronger math, ADC/PWM support, richer interrupt/event handling, and control-system applications such as motor or axis control.

The reason this matters for exams is classification. If a question asks about `SBUF`, `SCON`, `PSW`, `DPTR`, bit-addressable RAM, or `MOVC/MOVX`, it is 8051 territory. If it asks about the 8096's 16-bit control orientation, ADC/PWM, or MCS-96 family role, do not answer using 8051 limits.

### 8051 Architecture MCQ Spine

| Fact | Correct anchor |
| --- | --- |
| Package | Classic 8051 is commonly taught as 40-pin. |
| CPU/data width | 8-bit microcontroller. |
| Address reach | 16-bit addressing gives 64 KB code memory and 64 KB external data-memory spaces. |
| Internal memory | 128 bytes internal data RAM and 4 KB on-chip ROM in the classic 8051. |
| Ports | Four 8-bit ports, with Port 3 carrying many named alternate functions. |
| Reset state | `SP=07H`, port latches high, `PSW=00H`, register Bank 0 selected. |
| Main 16-bit registers | `PC` and `DPTR`; `DPTR` is software-visible as `DPH:DPL`, while `PC` is changed by control flow. |
| Status flags | Important math flags are `CY`, `AC`, `OV`, and `P` in `PSW`. |

Use this table before solving MCQs. Most wrong options come from mixing byte and bit address spaces, mixing program and data memory spaces, or importing 8085-style assumptions such as a zero flag into 8051 questions.

### Instruction-Set Revision Map

| Group | Instructions from the screenshots | Core rule |
| --- | --- | --- |
| Code/external memory | `MOVC`, `MOVX` | `MOVC` reads code memory; `MOVX` accesses external data memory. |
| Byte exchange and nibbles | `SWAP`, `XCH` | `SWAP` changes nibbles inside `A`; `XCH` exchanges `A` with another byte operand. |
| Stack/subroutine | `PUSH`, `ACALL`, `RET`, `RETI` | `PUSH` pre-increments `SP`; calls save return address; `RET/RETI` restore `PC`. |
| Arithmetic | `ADD`, `ADDC`, `SUBB`, `MUL`, `DIV`, `INC`, `DA A` | Destination is usually `A`; flag behavior must be read instruction-by-instruction. |
| Logic and rotate | `ANL`, `ORL`, `XRL`, `CLR`, `RL`, `RR`, `RRC` | Logic changes bits by masks; rotate either stays inside `A` or passes through carry. |
| Branch and bit branch | `JMP @A+DPTR`, `JC`, `DJNZ`, `JZ`, `JNZ`, `SJMP`, `AJMP`, `JNB`, `JBC`, `CJNE` | First identify what condition is tested, then identify whether the target is relative, 2 KB absolute, or indexed. |

The safest way to study these instruction slides is to write three columns beside every mnemonic: destination, source/address space, and flags affected. This prevents common mistakes such as treating `MOVC` and `MOVX` as the same kind of memory access, treating `ADD` as if any register can be the destination, or treating `SJMP` and `AJMP` as if they use the same address range.

### Flag And Correction Notes

- `DIV AB`: official flag behavior centers on `CY` and `OV`. A valid divide clears carry and overflow; divide-by-zero sets overflow. Treat any `AC`-based MCQ wording as a class simplification, not the architectural rule.
- `CJNE`: the branch happens when operands are not equal. Carry is set when the first operand is less than the second operand in unsigned comparison.
- `JZ` and `JNZ`: these test accumulator value directly. The 8051 does not have the same zero flag model as 8085.
- `DA A`: BCD correction depends on invalid BCD nibbles and the `AC`/`CY` flags from the preceding addition.
- `INC`: rollover does not set carry, including the special `INC DPTR` 16-bit form.
- Byte `ANL`/`ORL`/`XRL`: normally no PSW flag change; carry-targeted bit forms are different because `C` is the destination.

## Points To Remember

- The 8051 serial port is on-chip and can support several operating modes.
- Serial output converts a CPU parallel byte into serial bits.
- Serial input converts serial bits into a CPU-readable parallel byte.
- Full duplex means transmit and receive can happen simultaneously.
- `SBUF` is at SFR address `99H`.
- Writing `SBUF` loads the transmit buffer.
- Reading `SBUF` reads the receive buffer.
- The transmit and receive `SBUF` buffers are separate internal registers behind one software-visible SFR address.
- `TXD` is `P3.1`; `RXD` is `P3.0`.
- `SCON` is at SFR address `98H` and is bit-addressable.
- The screenshot that labels `SCON` as `99H` should be corrected: `SBUF = 99H`, `SCON = 98H`.
- `SCON = SM0 SM1 SM2 REN TB8 RB8 TI RI`.
- `SM0/SM1` select serial mode; `REN` enables reception.
- For normal 8-bit, non-multiprocessor use, keep `SM2=0`, `TB8=0`, and `RB8=0`.
- `REN` is `SCON.4`, so `MOV SCON,#10H` sets only `REN` in a fresh `SCON` byte.
- `SETB REN` sets the receive-enable bit without rewriting the rest of `SCON`.
- `TI` is the transmit flag and `RI` is the receive flag; software clears both after service.
- `RI=1` means received data is ready in the receive buffer.
- `TI=1` means the transmitter is ready/completed for the next transmit action.
- A serial interrupt can be caused by receive or transmit flag activity, so software must check which flag is set.
- Wait for transmit completion before writing the next transmit character to `SBUF`.
- `SM0/SM1` choose one of four serial modes.
- Modes 1, 2, and 3 are asynchronous framed modes; Mode 0 is shift-register style operation.
- Mode 0 is selected with `SM0=0` and `SM1=0`.
- In Mode 0, `RXD` carries serial data and `TXD` supplies the shift clock.
- Mode 0 shifts 8 bits LSB first at a fixed oscillator-derived rate.
- Mode 1 is an 8-bit UART mode with a 10-bit frame: start bit, eight data bits, stop bit.
- Mode 1 sends data LSB first and uses variable baud-rate timing derived through the Timer 1/serial divider path.
- In Mode 1 reception, `RB8` can hold the received stop-bit state.
- In Mode 1, each bit period is `1 / baud rate`.
- In Mode 1 transmit, `TI` is set when the stop bit appears on `TXD`.
- In Mode 1 receive, a `1`-to-`0` transition on `RXD` indicates the start bit and lets receive timing align.
- Mode 1 receive samples the incoming stream near the middle of the bit timing window.
- False start-bit detection rejects noise if the line is not still low after the validation count.
- At the end of a valid Mode 1 receive, `RB8` holds the stop bit, receive `SBUF` holds the eight data bits, and `RI` is set.
- Mode 2 is selected by the standard `SM0 SM1 = 10` bit combination.
- Mode 2 uses an 11-bit frame: start bit, eight data bits, programmable ninth bit, stop bit.
- In Mode 2, transmit ninth bit comes from `TB8`; receive ninth bit is stored in `RB8`.
- Mode 2 baud rate is fixed relative to the oscillator, commonly `fosc/32` or `fosc/64` depending on baud-rate doubling.
- Mode 3 uses the same 9-bit frame idea as Mode 2 but uses programmable timer-provided baud rate.
- Modes 1, 2, and 3 are asynchronous framed modes; Mode 0 is the outlier shift-register mode.
- `SM2` enables multiprocessor filtering behavior in the 9-bit serial modes.
- With `SM2` use, `RB8=1` can mark a frame that should activate serial service, such as an address frame in a master/slave setup.
- Mode 0 baud rate is fixed at `fosc/12`; with a 12 MHz oscillator, that is 1 MHz.
- Mode 2 baud rate defaults to `fosc/64` after reset and can commonly be doubled to `fosc/32`.
- Baud rate can be increased by using a higher-frequency oscillator.
- `PCON` contains the serial baud-rate doubling control used in supported modes.
- `SMOD` is the baud-rate doubling bit in `PCON`.
- After reset, `SMOD=0`; setting `SMOD=1` doubles the supported serial baud rate.
- For common Timer 1 baud examples, `TH1=FDH`, `FAH`, `F4H`, and `E8H` correspond to reload offsets `-3`, `-6`, `-12`, and `-24`.
- With the same `TH1` reload, `SMOD=1` doubles the baud rate compared with `SMOD=0`.
- With an 11.0592 MHz crystal, the classic machine-cycle frequency is 921.6 kHz.
- `TMOD=20H` configures Timer 1 in Mode 2, the 8-bit auto-reload mode often used for serial baud generation.
- `TH1` holds the reload value that controls Timer 1 overflow rate for variable-baud serial modes.
- `SCON=50H` means `SM0=0`, `SM1=1`, and `REN=1`: Mode 1 with receive enabled and ordinary 8-bit UART framing.
- `TR1=1` starts Timer 1, so the serial baud-rate timing path can run.
- `SCON` contains serial control bits and serial status flags.
- Serial status bits can be polled by software or used to cause serial interrupt handling.
- Baud rate means serial bit rate.
- Variable baud-rate serial operation commonly depends on Timer 1 setup.
- Timer mistakes can become serial-port mistakes because the serial bit clock may come from Timer 1.
- `TI` and `RI` are synchronization flags; `SBUF` should be accessed in step with those flags.
- 8051 polling transmit code commonly waits for `TI`, clears/acknowledges it, writes `SBUF`, and repeats.
- 8051 polling receive code waits for `RI`, reads `SBUF`, stores the byte safely, clears `RI`, and repeats.
- Classic 8051 facts used in the MCQs: 40 pins, 8-bit core, four 8-bit ports, 128 bytes internal RAM, 4 KB ROM, and 64 KB code-memory reach.
- `SP=07H` after reset; the first pushed byte goes to `08H`.
- Port 3 is multifunctional because of `RXD`, `TXD`, interrupt, timer, and external-memory alternate functions.
- `MOVC` reads code memory; `MOVX` accesses external data memory.
- `MOVX @R0/@R1` uses an 8-bit external address; `MOVX @DPTR` uses a 16-bit external address.
- `ADD`, `ADDC`, and `SUBB` are accumulator-centered arithmetic instructions.
- `DA A` is for BCD adjustment after addition, not for arbitrary binary correction.
- `MUL AB` places the low product byte in `A` and high product byte in `B`.
- `DIV AB` places quotient in `A` and remainder in `B`; valid division clears carry and overflow.
- `INC` rollover does not set carry.
- `CJNE` jumps on not-equal and sets carry when the first operand is less than the second.
- `SJMP` is relative; `AJMP` and `ACALL` are limited to the same 2 KB code block.
- `RET` returns from a subroutine; `RETI` returns from interrupt and releases interrupt-priority service state.
- 8096/MCS-96 is a 16-bit Intel microcontroller family topic, separate from the 8-bit 8051 architecture.

## Sources

[S1] Intel, [MCS-51 Microcontroller Family User's Manual, February 1994](https://bitsavers.trailing-edge.com/components/intel/8051/MCS-51_Users_Manual_Feb94.pdf). Used for 8051 serial-port architecture, `SBUF`, `SCON`, serial pins, and baud-rate/timer relationship.

[S2] Intel, [MCS-51 Instruction Set](https://www.keil.com/dd/docs/datashts/intel/ism51.pdf). Used for SFR/direct-address and instruction-level behavior around serial-register access.

[S3] Intel, [1991 Intel 16-Bit Embedded Controller Handbook](https://bitsavers.trailing-edge.com/components/intel/_dataBooks/1991_Intel_16-Bit_Embedded_Controller_Handbook.pdf). Used for the MCS-96/8096 context, 16-bit embedded-controller family framing, and control-oriented peripheral comparison.

[S4] Intel, [8255A Programmable Peripheral Interface](https://aturing.umcs.maine.edu/~meadow/courses/cos335/Intel8255A.pdf). Used to verify the 8255 bit set/reset mode point.
