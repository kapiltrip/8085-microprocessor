# Day 14: 8051 Microcontroller Introduction, Architecture, SFRs, Control Signals, Memory Organization, Pin Diagram, and Addressing Modes

Day 14 begins the 8051 microcontroller section. The screenshots move from the earlier 8085/8086 microprocessor system view into a controller-on-a-chip view: CPU core, memory, I/O ports, timers, serial interface, interrupt/control logic, special function registers, external bus signals, reset, clock input, memory organization, pin mapping, and addressing modes are treated as one integrated embedded-control system.

Use these checks while studying this day:

```text
1. Decide what the 8051 integrates on-chip that an 8085/8086 system often needs externally.
2. Keep program memory, internal data RAM, external data RAM, and SFR space separate.
3. For PSW questions, map each bit to its flag or register-bank function.
4. For stack questions, remember that 8051 SP is 8-bit and pre-increments on PUSH/CALL.
5. For ports, timers, SBUF, and PSEN, separate the programmer-visible SFR or signal name from the underlying hardware block.
6. For external-bus questions, connect Port 0 multiplexing with ALE and the external address latch.
7. For program-memory source questions, use EA together with PSEN and the internal/external code-memory split.
8. For bit-addressing questions, separate the 128 bit-addressable RAM bits from bit-addressable SFR bits.
9. For pin questions, map the port number, pin number, and alternate function together.
10. For addressing-mode questions, distinguish where the operand is located from how the instruction names it.
```

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [8051 microcontroller need](images/Day%2014/Screenshot%202026-06-17%20231904.png) | A microprocessor alone is not a complete microcomputer system. |
| 2 | [Microprocessor versus microcontroller](images/Day%2014/Screenshot%202026-06-17%20233346.png) | Integrated controller system versus external-support processor system. |
| 3 | [Features of 8051](images/Day%2014/Screenshot%202026-06-17%20234554.png) | ROM, RAM, register banks, I/O lines, timers, serial port, and addressability. |
| 4 | [8051 internal architecture](images/Day%2014/Screenshot%202026-06-17%20235301.png) | CPU, ALU, RAM, ROM/EPROM, ports, stack pointer, PC, DPTR, timers, serial, and interrupts. |
| 5 | [PSW bit layout](images/Day%2014/Screenshot%202026-06-18%20000035.png) | `CY`, `AC`, `F0`, `RS1`, `RS0`, `OV`, reserved/user bit, and `P`. |
| 6 | [Register bank select note](images/Day%2014/Screenshot%202026-06-18%20000349.png) | `RS1 RS0` chooses one of four working register banks. |
| 7 | [Stack pointer](images/Day%2014/Screenshot%202026-06-18%20000640.png) | 8-bit SP, reset value `07H`, and first stack location `08H`. |
| 8 | [Program counter basics](images/Day%2014/Screenshot%202026-06-18%20161911.png) | 16-bit PC, reset to `0000H`, and instruction-length-based incrementing. |
| 9 | [Program counter cannot be directly assigned](images/Day%2014/Screenshot%202026-06-18%20162142.png) | Change PC through branch/call/return instructions such as `LJMP`. |
| 10 | [Ports 0 to 3](images/Day%2014/Screenshot%202026-06-18%20162215.png) | Port SFR latches and input/output bit behavior. |
| 11 | [Serial data buffer close view](images/Day%2014/Screenshot%202026-06-18%20162658.png) | `SBUF` write goes to transmit buffer; `SBUF` read comes from receive buffer. |
| 12 | [Serial data buffer full view](images/Day%2014/Screenshot%202026-06-18%20165618.png) | The same transmit/receive split in a cleaner full slide. |
| 13 | [Timer and control registers](images/Day%2014/Screenshot%202026-06-18%20170351.png) | `TH0/TL0`, `TH1/TL1`, and control/status SFR families. |
| 14 | [Register banks overview](images/Day%2014/Screenshot%202026-06-18%20170525.png) | `R0-R7` names refer to the currently selected register bank. |
| 15 | [PSEN program store enable](images/Day%2014/Screenshot%202026-06-18%20170759.png) | Active-low external program-memory read strobe. |
| 16 | [ALE address latch enable](images/Day%2014/Screenshot%202026-06-18%20171231.png) | Latches the low address byte from multiplexed Port 0 during external memory cycles. |
| 17 | [EA external access](images/Day%2014/Screenshot%202026-06-18%20171433.png) | Chooses internal lower-code memory when high, or external program memory only when low. |
| 18 | [RST and oscillator partial view](images/Day%2014/Screenshot%202026-06-18%20171617.png) | Reset requires a high signal for at least two machine cycles; oscillator topic begins. |
| 19 | [RST and on-chip oscillator](images/Day%2014/Screenshot%202026-06-18%20171636.png) | Reset/start-up behavior plus the nominal 12 MHz MCS-51 crystal note. |
| 20 | [RST and oscillator full view](images/Day%2014/Screenshot%202026-06-18%20220817.png) | Full lecture-frame version of the same reset and oscillator points. |
| 21 | [Memory organization overview](images/Day%2014/Screenshot%202026-06-18%20221831.png) | 8051 separates program/code memory and data memory. |
| 22 | [Internal memory map](images/Day%2014/Screenshot%202026-06-18%20224525.png) | Register banks, bit-addressable RAM, general RAM, and SFR address space. |
| 23 | [Register bank close-up](images/Day%2014/Screenshot%202026-06-18%20225137.png) | Lowest 32 RAM bytes hold four banks of eight working registers. |
| 24 | [Bit-addressable RAM](images/Day%2014/Screenshot%202026-06-18%20225648.png) | 128 bit-addressable RAM bits plus bit-addressable SFR bits. |
| 25 | [Bit-addressable RAM continuation](images/Day%2014/Screenshot%202026-06-18%20230055.png) | Bit operations avoid longer read-modify-write software sequences. |
| 26 | [Register banks and direct addressing](images/Day%2014/Screenshot%202026-06-18%20230344.png) | `R0-R7` default to `00H-07H` after reset and are shorter/faster than direct-address forms. |
| 27 | [Special function registers](images/Day%2014/Screenshot%202026-06-18%20230558.png) | Classic SFRs live in the `80H-FFH` direct-address region and expose internal hardware registers. |
| 28 | [External memory expansion](images/Day%2014/Screenshot%202026-06-18%20230840.png) | 64 KB external code and data spaces; Port 0/Port 2 become external memory bus signals. |
| 29 | [External memory address-line annotation](images/Day%2014/Screenshot%202026-06-18%20231120.png) | `64K = 2^16`: Port 0 handles `A0-A7`/`D0-D7`, and Port 2 handles `A8-A15`. |
| 30 | [Working register bank map](images/Day%2014/Screenshot%202026-06-18%20231227.png) | Diagram of Banks 0-3 as four 8-register blocks in `00H-1FH`. |
| 31 | [Annotated working register bank map](images/Day%2014/Screenshot%202026-06-18%20231239.png) | Marked version confirming Bank 0 starts at `00H` and each bank has eight registers. |
| 32 | [General-purpose RAM region](images/Day%2014/Screenshot%202026-06-18%20231338.png) | Internal RAM `30H-7FH` is general-purpose byte storage. |
| 33 | [Working register summary](images/Day%2014/Screenshot%202026-06-18%20231641.png) | Four banks, eight registers each, selected by `RS1 RS0`. |
| 34 | [Bit instructions and general-purpose RAM](images/Day%2014/Screenshot%202026-06-18%20231720.png) | `SETB` and `CLR` operate on bit variables; `30H-7FH` is the main general-purpose RAM region. |
| 35 | [8051 pin diagram](images/Day%2014/Screenshot%202026-06-18%20231941.png) | 40-pin package, power pins, port pin ranges, and alternate external-bus functions. |
| 36 | [External address-line close-up](images/Day%2014/Screenshot%202026-06-18%20232030.png) | Close-up of `64K = 2^16`, `A0-A7` on Port 0, and `A8-A15` on Port 2. |
| 37 | [Pin description: P1, reset, P3 serial](images/Day%2014/Screenshot%202026-06-18%20232113.png) | Port 1 I/O, reset pin, and Port 3 `RXD/TXD` alternate functions. |
| 38 | [Pin description: P3 interrupts/timers, oscillator, P2](images/Day%2014/Screenshot%202026-06-18%20232344.png) | Port 3 interrupt/timer/read-write pins, `XTAL1/XTAL2`, `GND`, and Port 2 I/O. |
| 39 | [8051 addressing modes](images/Day%2014/Screenshot%202026-06-18%20232841.png) | Immediate, register, direct, register-indirect, and indexed addressing. |
| 40 | [Pin description: PSEN, ALE, EA, P0, VCC](images/Day%2014/Screenshot%202026-06-18%20232858.png) | Program-store enable, address latch enable, external access, Port 0, and supply pin. |
| 41 | [Immediate addressing](images/Day%2014/Screenshot%202026-06-18%20232958.png) | Immediate operands are constants encoded in the instruction. |
| 42 | [Immediate addressing examples](images/Day%2014/Screenshot%202026-06-18%20233151.png) | `#data` examples for `A`, registers, `B`, `DPTR`, ports, and immediate range limits. |
| 43 | [Register addressing mode](images/Day%2014/Screenshot%202026-06-18%20233333.png) | Register operands named directly in the instruction mnemonic. |
| 44 | [Handwritten DPTR and register-addressing note](images/Day%2014/day-14-register-addressing-dptr-handwritten-note.jpg) | `MOV DPTR,#4521H` splits into `DPH=45H`, `DPL=21H`; arbitrary register-to-register moves are not a general 8051 form. |
| 45 | [Direct addressing mode](images/Day%2014/Screenshot%202026-06-18%20234503.png) | Internal RAM and SFR locations can be named by direct byte address. |
| 46 | [SFR direct-addressing examples](images/Day%2014/Screenshot%202026-06-18%20234551.png) | SFRs can be accessed by symbolic name or direct address, such as `ACC=E0H` and `B=F0H`. |
| 47 | [SFR address-space caution](images/Day%2014/Screenshot%202026-06-18%20234751.png) | SFR addresses lie in `80H-FFH`, but unused locations are reserved. |
| 48 | [Register-indirect addressing mode](images/Day%2014/Screenshot%202026-06-18%20234938.png) | `R0` or `R1` can hold the address of the data byte being accessed. |
| 49 | [Register-indirect addressing examples](images/Day%2014/Screenshot%202026-06-18%20235110.png) | `@R0/@R1` examples for reading, writing, immediate stores, and direct-to-indirect copies. |
| 50 | [Indexed addressing](images/Day%2014/Screenshot%202026-06-18%20235419.png) | `A+DPTR` or `A+PC` forms a code-memory lookup address for `MOVC`. |
| 51 | [ALE and external-memory port roles](images/Day%2014/Screenshot%202026-06-18%20235805.png) | ALE indicates low-address timing on Port 0; Port 2 joins Port 0 for 16-bit external addressing. |
| 52 | [Port 2 and Port 3 external-memory notes](images/Day%2014/Screenshot%202026-06-19%20000133.png) | Port 2 supplies `A8-A15` during external memory; Port 3 keeps I/O plus alternate control functions. |
| 53 | [Port 3 alternate-function table](images/Day%2014/Screenshot%202026-06-19%20000139.png) | `P3.0-P3.7` grouped as serial, interrupts, timers, and external-memory read/write signals. |
| 54 | [Counters and timers](images/Day%2014/Screenshot%202026-06-19%20000542.png) | Timer/counters T0 and T1 count internal clock pulses or external events. |
| 55 | [Timer interval timing](images/Day%2014/Screenshot%202026-06-19%20001006.png) | Timer overflow flags can synchronize periodic program actions and elapsed-time measurement. |
| 56 | [Event counting and baud clock](images/Day%2014/Screenshot%202026-06-19%20001029.png) | Counter mode counts external transitions; timers can also provide the serial baud-rate clock. |
| 57 | [Timer mode register TMOD](images/Day%2014/Screenshot%202026-06-19%20001407.png) | `TMOD` sets Timer 0 and Timer 1 operating modes and is not bit-addressable. |
| 58 | [TMOD mode table](images/Day%2014/Screenshot%202026-06-19%20001534.png) | `M1 M0` select 13-bit, 16-bit, 8-bit auto-reload, or split timer mode. |
| 59 | [TMOD C/T bit](images/Day%2014/Screenshot%202026-06-19%20002658.png) | `C/T=0` selects internal-clock timer operation; `C/T=1` selects external-input counter operation. |
| 60 | [TMOD GATE bit](images/Day%2014/Screenshot%202026-06-19%20002820.png) | With `GATE=0`, software starts and stops timers through `TR0` and `TR1`. |
| 61 | [GATE software-control slide](images/Day%2014/Screenshot%202026-06-19%20003111.png) | Cleaner full-frame version of the `GATE=0`, `TR0/TR1`, `SETB`, and `CLR` control rule. |
| 62 | [Timer Mode 0: 13-bit timer](images/Day%2014/Screenshot%202026-06-19%20003351.png) | Mode 0 uses `THx` plus five low bits of `TLx` as a 13-bit timer, mainly for 8048 compatibility. |
| 63 | [Timer Mode 1: 16-bit timer](images/Day%2014/Screenshot%202026-06-19%20003638.png) | Mode 1 cascades `THx` and `TLx` as a 16-bit up-counter that sets `TFx` on overflow. |
| 64 | [Timer Mode 2: 8-bit auto-reload](images/Day%2014/Screenshot%202026-06-19%20003804.png) | Mode 2 makes `TLx` the 8-bit timer and uses `THx` as the reload value after overflow. |
| 65 | [Timer Mode 3: split timer](images/Day%2014/Screenshot%202026-06-19%20004024.png) | Mode 3 splits Timer 0 into two 8-bit timers and changes how Timer 1 can be used. |
| 66 | [Timer 1 behavior in Mode 3](images/Day%2014/Screenshot%202026-06-19%20004812.png) | In Mode 3, Timer 1 can still support baud generation or non-interrupt use, but it is detached from normal `TF1` interrupt behavior. |
| 67 | [Timer mode block diagrams](images/Day%2014/Screenshot%202026-06-19%20005107.png) | Register/overflow diagrams for timer modes 0, 1, 2, and 3. |
| 68 | [TCON register bit layout](images/Day%2014/Screenshot%202026-06-19%20005207.png) | `TCON` bits are `TF1 TR1 TF0 TR0 IE1 IT1 IE0 IT0`. |
| 69 | [TCON external interrupt bits](images/Day%2014/Screenshot%202026-06-19%20005656.png) | `IE1/IE0` are external-interrupt flags; `IT1/IT0` select edge-triggered or level-triggered interrupt behavior. |
| 70 | [TCON timer run and overflow bits](images/Day%2014/Screenshot%202026-06-19%20005704.png) | `TF1/TF0` are overflow flags; `TR1/TR0` are timer run-control bits. |
| 71 | [Mode 1 operation checklist](images/Day%2014/Screenshot%202026-06-19%20010208.png) | Mode 1 loads 16-bit `THx/TLx`, starts with `TRx`, counts to `FFFFH`, then sets `TFx` on overflow. |
| 72 | [Timer overflow flag and stop option](images/Day%2014/Screenshot%202026-06-19%20010522.png) | `TF0` and `TF1` can be polled after overflow, and software can stop the timer with `CLR TR0` or `CLR TR1`. |
| 73 | [Timer flag block diagram](images/Day%2014/Screenshot%202026-06-19%20010642.png) | The internal clock is divided, gated by `TRx`, increments the timer registers, and sets `TFx` when the count rolls over. |
| 74 | [Reload and clear after overflow](images/Day%2014/Screenshot%202026-06-19%20010652.png) | Repeated delays require reloading `THx/TLx` and clearing `TFx` after a rollover. |
| 75 | [Time delay generation procedure](images/Day%2014/Screenshot%202026-06-19%20010810.png) | Delay generation is a sequence: configure `TMOD`, load `TH/TL`, start, poll `TFx`, stop, clear `TFx`, then reload. |
| 76 | [Time delay generation clean view](images/Day%2014/Screenshot%202026-06-19%20010922.png) | Cleaner full-frame view of the same reusable timer-delay algorithm. |
| 77 | [Mode 2 characteristics](images/Day%2014/Screenshot%202026-06-19%20011213.png) | Mode 2 is an 8-bit timer where `THx` holds the reload value and `TLx` counts upward to overflow. |
| 78 | [Mode 2 auto-reload diagram](images/Day%2014/Screenshot%202026-06-19%20011239.png) | After `TLx` rolls over, it is automatically reloaded from `THx`; software clears `TFx` and continues. |
| 79 | [Mode 2 delay procedure](images/Day%2014/Screenshot%202026-06-19%20011310.png) | Mode 2 delay loops load `THx` once, poll `TFx`, clear the flag, and return to polling because reload is automatic. |

## Handwritten Notes Linked To Day 14

These eight handwritten pages all belong to Day 14 because they are 8051-specific: they start from the microcontroller system boundary, then move through 8051 features, PSW/register-bank selection, stack and DPTR behavior, port latches, serial buffer, timer/control registers, program-memory control pins, internal memory layout, bit-addressable RAM, SFRs, external-memory expansion, addressing modes, and timer/counter control.

### [8051 handwritten p001](images/HandWrittenNotes/8051-microcontroller-notes/page-001.jpg)

<a href="images/HandWrittenNotes/8051-microcontroller-notes/page-001.jpg"><img src="images/HandWrittenNotes/8051-microcontroller-notes/page-001.jpg" alt="8051 handwritten microcontroller introduction and comparison" width="960"></a>

This first handwritten page frames the 8051 as a microcontroller, not only as a CPU. The notes list ROM, RAM, decoder/control logic, I/O devices, peripheral devices, interrupt control, DMA/control support, and programmable I/O as the surrounding blocks that a microprocessor-based system may need externally. The point is that a microcontroller pulls many of those blocks into one chip so an embedded system can be smaller, more direct, and less dependent on separate interface hardware.

The comparison table is important because it explains why the 8051 feels different from 8085 and 8086 study. A microprocessor system is more flexible and can have more external hardware, a single memory map for code/data in many designs, and more pins dedicated to bus expansion. A microcontroller sacrifices some expansion freedom but gains built-in ROM, RAM, I/O, serial support, timers, counters, and interrupt handling. That is why 8051 questions often ask about port pins, SFRs, timers, and bit-addressability instead of only bus cycles and external decoding.

### [8051 handwritten p002](images/HandWrittenNotes/8051-microcontroller-notes/page-002.jpg)

<a href="images/HandWrittenNotes/8051-microcontroller-notes/page-002.jpg"><img src="images/HandWrittenNotes/8051-microcontroller-notes/page-002.jpg" alt="8051 handwritten features PSW stack DPTR and PC" width="960"></a>

This page condenses the core 8051 feature list: 4 KB on-chip program memory, 128 bytes on-chip data memory, four register banks, 64 KB program and external-memory addressability, one instruction cycle based on the classic 12 MHz timing example, 32 bidirectional I/O lines, multiple operating modes, programmable serial data, 16-bit timer/counters, direct byte access, and bit addressability. Read these as resource limits and access paths, not as disconnected facts.

The right side connects those resources to CPU state. `RS1` and `RS0` in `PSW` choose Bank 0 through Bank 3: `00 -> 00H-07H`, `01 -> 08H-0FH`, `10 -> 10H-17H`, and `11 -> 18H-1FH`. The page also ties stack behavior to `SP=07H` after reset, so stack use begins just above the default register-bank area unless software relocates it. DPTR is highlighted as a 16-bit pointer made of `DPH` and `DPL`, while PC is highlighted as the code-flow register that starts at `0000H` and advances or changes through control-transfer instructions.

### [8051 handwritten p003](images/HandWrittenNotes/8051-microcontroller-notes/page-003.jpg)

<a href="images/HandWrittenNotes/8051-microcontroller-notes/page-003.jpg"><img src="images/HandWrittenNotes/8051-microcontroller-notes/page-003.jpg" alt="8051 handwritten ports SBUF timers and control registers" width="960"></a>

This page is mainly about the software-visible hardware handles. Ports `P0`, `P1`, `P2`, and `P3` are shown as port SFRs with latch behavior. Writing `1` to a port bit stores a high value in the latch; depending on port hardware and external connection, that can release or drive the pin high. The note about using a port pin as input says the latch must first be written high so the external signal can be sensed correctly.

The same page links `SBUF`, timer registers, and control registers. `SBUF` is one SFR name but acts as separate transmit and receive buffers: writing loads the transmit buffer, reading gets the receive buffer. Timer pairs `TH0/TL0` and `TH1/TL1` hold the timer/counter counts. Control registers such as `IE`, `IP`, `TMOD`, `TCON`, `SCON`, and `PCON` are the programmer's way to enable interrupts, set priorities, select timer modes, control timer status, configure serial behavior, and manage power/serial-related control bits.

### [8051 handwritten p004](images/HandWrittenNotes/8051-microcontroller-notes/page-004.jpg)

<a href="images/HandWrittenNotes/8051-microcontroller-notes/page-004.jpg"><img src="images/HandWrittenNotes/8051-microcontroller-notes/page-004.jpg" alt="8051 handwritten PSEN ALE EA reset oscillator and memory organization" width="960"></a>

This page explains the external-control pins that make 8051 memory expansion work. `PSEN` is program store enable, an active-low output used when external program memory is read. `ALE` demultiplexes the low address/data byte on Port 0 by letting external hardware latch the low address before Port 0 becomes a data bus. `EA` selects the code-memory source: high permits internal program memory use on classic parts, while low forces program fetches from external program memory.

The page also connects reset and oscillator behavior to startup. `RST` must be high long enough for reset recognition, after which internal registers load appropriate startup values. The on-chip oscillator and the common 12 MHz example connect directly to runtime and machine-cycle timing. The memory-map notes on the right classify internal RAM into register banks, bit-addressable RAM, general-purpose RAM, and SFR space, which is the core mental model needed before addressing-mode questions make sense.

### [8051 handwritten p005](images/HandWrittenNotes/8051-microcontroller-notes/page-005.jpg)

<a href="images/HandWrittenNotes/8051-microcontroller-notes/page-005.jpg"><img src="images/HandWrittenNotes/8051-microcontroller-notes/page-005.jpg" alt="8051 handwritten bit addressability SFRs external memory and port bus roles" width="960"></a>

This page focuses on bit addressability and special function registers. The note says the 8051 contains 210 bit-addressable locations: 128 bits in internal RAM addresses `20H-2FH`, plus bit-addressable SFR bits. That is why bit instructions can set, clear, and test individual bits without first reading a byte, masking it, and writing it back. For port and flag work, this is a major 8051 advantage.

The page also separates ordinary internal RAM from SFR space. SFRs are in `80H-FFH`, but not every location in that range is implemented. These registers are not general-purpose RAM; they are control/status registers connected to ports, timers, serial hardware, interrupt logic, accumulator/B, PSW, SP, and DPTR bytes. The external-memory notes reinforce that the 8051 can interface 64 KB external code memory and 64 KB external data memory, with Port 0 multiplexing low address/data and Port 2 supplying high address bits during external cycles.

### [8051 handwritten p006](images/HandWrittenNotes/8051-microcontroller-notes/page-006.jpg)

<a href="images/HandWrittenNotes/8051-microcontroller-notes/page-006.jpg"><img src="images/HandWrittenNotes/8051-microcontroller-notes/page-006.jpg" alt="8051 handwritten internal RAM regions and pin roles" width="960"></a>

This page turns the internal RAM map into address ranges. `00H-1FH` contains the four register banks, `20H-2FH` is the bit-addressable RAM area, and `30H-7FH` is general-purpose RAM. The page also repeats that `20H-2FH` gives 16 bytes, or 128 individual bit variables, and that bits can be set or cleared directly. This is the bridge between the memory-map screenshots and the bit-instruction examples such as `SETB` and `CLR`.

The pin notes connect the package to the memory model. Pins `1-8` are Port 1 I/O, pin 9 is active-high reset, pins `10-17` are Port 3 with I/O plus alternate functions, pins `12` and `13` are external interrupt inputs, pin 20 is ground, pins `21-28` are Port 2, pin 29 is `PSEN`, pin 30 is `ALE`, and pins `32-39` are Port 0. The practical takeaway is that port names in programs become physical pins in hardware, but some ports are temporarily consumed by external memory cycles.

### [8051 handwritten p007](images/HandWrittenNotes/8051-microcontroller-notes/page-007.jpg)

<a href="images/HandWrittenNotes/8051-microcontroller-notes/page-007.jpg"><img src="images/HandWrittenNotes/8051-microcontroller-notes/page-007.jpg" alt="8051 handwritten addressing modes and examples" width="960"></a>

This page lists the addressing modes: immediate, register, direct, register-indirect, and indexed. The red note beside immediate addressing correctly states that the data is present directly inside the instruction. Register addressing names a register, direct addressing gives a fixed internal RAM or SFR address, register-indirect addressing uses a register as a pointer, and indexed addressing uses a base such as DPTR or PC plus accumulator offset for table lookup.

The examples are useful because they expose invalid assumptions. `MOV DPTR,#4521H` loads a 16-bit immediate into DPTR, giving `DPH=45H` and `DPL=21H`. `MOV R7,DPL` is marked as not allowed because 8051 does not support arbitrary register-to-register moves in that form. The direct-address examples distinguish moving from a literal address such as `40H`, moving to SFR addresses such as `0F0H`, and using a `#` prefix for immediate constants such as `#55H`.

### [8051 handwritten p008](images/HandWrittenNotes/8051-microcontroller-notes/page-008.jpg)

<a href="images/HandWrittenNotes/8051-microcontroller-notes/page-008.jpg"><img src="images/HandWrittenNotes/8051-microcontroller-notes/page-008.jpg" alt="8051 handwritten I/O ports timers counters TMOD and auto reload" width="960"></a>

The final handwritten page links I/O ports with timer/counter control. It notes that 8051 has four 8-bit I/O ports, each using eight pins. After reset, ports are ready as output latches but must be written high before being used reliably as inputs on classic quasi-bidirectional ports. Port 0 can become `AD0-AD7` during external-memory use, and the lower address byte must be latched because the same pins later carry data.

The timer/counter notes explain why `TMOD` matters. A timer counts internal clock pulses for interval timing; a counter counts external pulses for event counting. Timer/counters are used for internal timing delays, event-frequency counting, and baud-rate generation for the built-in serial port. The page also shows the `M1 M0` mode table: Mode 0 is 13-bit timer mode, Mode 1 is 16-bit timer mode, Mode 2 is 8-bit auto-reload, and Mode 3 is split timer mode. In auto-reload, `THx` holds the reload value and `TLx` is reloaded when overflow occurs.

## Web-Checked Anchor Points

The web cross-checks confirm that the screenshots are following standard 8051-family wording. Microcontrollers integrate the CPU with memory, I/O, timers/counters, and communication peripherals, while general-purpose microprocessors usually need more external support hardware. The 8051-family documentation also confirms separate program and data-memory spaces, the lower 128 bytes of internal RAM, four register banks, bit-addressable memory, direct-only SFR access, an 8-bit stack pointer reset to `07H`, SFR port latches, `SBUF` as separate transmit and receive buffers, `ALE` for demultiplexing the low address byte, `EA` for internal/external code-memory selection, active-low `PSEN` for external program fetches, and active-high reset behavior.

That means this day should be read as a bridge:

```text
8085/8086 mindset: CPU plus external memory, I/O, and support chips.
8051 mindset: CPU plus memory, I/O, timers, serial port, and control SFRs on one chip.
```

## Page-By-Page Explanation

### Page 1: 8051 Microcontroller Need

<a href="images/Day%2014/Screenshot%202026-06-17%20231904.png"><img src="images/Day%2014/Screenshot%202026-06-17%20231904.png" alt="8051 microcontroller need" width="960"></a>

This page sets up why the course moves from microprocessors to microcontrollers. A microprocessor can execute instructions, but a complete working microcomputer system also needs program memory, data memory, I/O interface circuits, address decoding, drivers, timers, interrupt control, and sometimes DMA or serial interface hardware.

The 8051 idea is to integrate many of these embedded-control pieces into one chip. That does not make external hardware impossible, but it changes the default design. For small control systems, the chip already provides RAM, ROM/EPROM option, ports, timer/counters, serial communication, interrupt logic, and control registers.

### Page 2: Microprocessor Versus Microcontroller

<a href="images/Day%2014/Screenshot%202026-06-17%20233346.png"><img src="images/Day%2014/Screenshot%202026-06-17%20233346.png" alt="microprocessor versus microcontroller comparison" width="960"></a>

The table contrasts system boundaries. A microprocessor-centered system is usually more flexible because external memory and I/O can be chosen around the application. The cost is extra chips, address decoding, buses, PCB space, and interface timing.

A microcontroller-centered system is less flexible in raw expansion, but more compact and reliable for embedded control because memory, I/O, timers, and serial hardware are already inside the chip. The important exam phrasing is not "microcontroller is always faster"; it is "microcontroller reduces external hardware for dedicated control applications."

### Page 3: Features Of 8051

<a href="images/Day%2014/Screenshot%202026-06-17%20234554.png"><img src="images/Day%2014/Screenshot%202026-06-17%20234554.png" alt="features of 8051" width="960"></a>

The core original 8051 feature set is the checklist: 4 KB on-chip program memory, 128 bytes on-chip data RAM, four register banks, 64 KB program-memory addressability, 64 KB external data-memory addressability, 32 bidirectional I/O lines arranged as four 8-bit ports, serial communication, two 16-bit timer/counters, and direct byte/bit addressability.

Keep the memory wording precise. The original 8051 has 4 KB on-chip program ROM, but the program address space can extend to 64 KB. The internal data RAM is 128 bytes in the original 8051, while external data memory can also be addressed separately. This separate memory-space idea is one of the main differences from the 8086 unified memory address model studied earlier.

### Page 4: 8051 Internal Architecture

<a href="images/Day%2014/Screenshot%202026-06-17%20235301.png"><img src="images/Day%2014/Screenshot%202026-06-17%20235301.png" alt="8051 internal architecture block diagram" width="960"></a>

Read the architecture diagram in layers. The ALU works with the accumulator, B register, temporary registers, and PSW. The memory side includes internal RAM, RAM address register, program ROM/EPROM, program address register, PC incrementer, program counter, and DPTR. The peripheral side includes port latches/drivers for Ports 0 to 3, timer blocks, serial port, and interrupt logic.

The diagram also shows why SFRs matter. The 8051 is not controlled only by ordinary instructions; it is controlled through memory-mapped special registers such as `P0`, `P1`, `P2`, `P3`, `SP`, `DPL`, `DPH`, `PSW`, `SCON`, `SBUF`, `TMOD`, `TCON`, `IE`, and `IP`. These registers are the software-visible handles for the chip's internal hardware.

### Page 5: PSW Bit Layout

<a href="images/Day%2014/Screenshot%202026-06-18%20000035.png"><img src="images/Day%2014/Screenshot%202026-06-18%20000035.png" alt="8051 PSW bit layout" width="960"></a>

The Program Status Word is an 8-bit SFR at address `D0H`. From MSB to LSB, the common layout is `CY AC F0 RS1 RS0 OV - P`. `CY` is carry, `AC` is auxiliary carry for BCD-style nibble carry, `F0` is a user flag, `RS1` and `RS0` select the active register bank, `OV` is overflow, bit 1 is reserved or derivative-dependent, and `P` is parity.

The parity bit is updated by hardware from the accumulator. It indicates whether the accumulator contains an odd or even number of one bits. The two bank-select bits are especially important because instructions that name `R0` through `R7` do not always refer to addresses `00H-07H`; they refer to whichever register bank PSW currently selects.

### Page 6: Register Bank Select Note

<a href="images/Day%2014/Screenshot%202026-06-18%20000349.png"><img src="images/Day%2014/Screenshot%202026-06-18%20000349.png" alt="8051 register bank select note" width="960"></a>

`RS1 RS0` select the working register bank:

| `RS1 RS0` | Bank | Internal RAM addresses |
| --- | --- | --- |
| `00` | Bank 0 | `00H-07H` |
| `01` | Bank 1 | `08H-0FH` |
| `10` | Bank 2 | `10H-17H` |
| `11` | Bank 3 | `18H-1FH` |

The screenshot appears to show Bank 3 as `18H-17H`, but the manual-correct range is `18H-1FH`. This correction matters because the four banks occupy the lowest 32 bytes of internal RAM: `00H` through `1FH`.

### Page 7: Stack Pointer

<a href="images/Day%2014/Screenshot%202026-06-18%20000640.png"><img src="images/Day%2014/Screenshot%202026-06-18%20000640.png" alt="8051 stack pointer" width="960"></a>

The 8051 stack pointer is 8 bits wide, unlike the 16-bit stack pointers used in 8085 and 8086. After reset, `SP = 07H`. During `PUSH`, `ACALL`, and `LCALL`, the stack pointer is incremented before the byte is stored, so the first default stack byte goes to internal RAM location `08H`.

This default is convenient but risky if the program uses register banks beyond Bank 0. Bank 1 also begins at `08H`, so a program that enables Bank 1 should usually relocate the stack by loading `SP` to a higher safe RAM address, such as after the bit-addressable area or after variables.

### Page 8: Program Counter Basics

<a href="images/Day%2014/Screenshot%202026-06-18%20161911.png"><img src="images/Day%2014/Screenshot%202026-06-18%20161911.png" alt="8051 program counter basics" width="960"></a>

The 8051 program counter is 16 bits, so it can point anywhere in the 64 KB program-memory address space. On reset, execution begins at program address `0000H`. After instruction fetch, the PC advances by the instruction length, so one-byte instructions advance by 1, two-byte instructions by 2, and three-byte instructions by 3.

Do not treat PC incrementing as "always plus one." The PC points to the next instruction byte, not the next instruction number. Variable-length instructions are why the PC must advance by the number of bytes consumed.

### Page 9: Program Counter Cannot Be Directly Assigned

<a href="images/Day%2014/Screenshot%202026-06-18%20162142.png"><img src="images/Day%2014/Screenshot%202026-06-18%20162142.png" alt="8051 program counter direct assignment note" width="960"></a>

The PC is special because the programmer does not write it like a normal SFR. There is no instruction such as `MOV PC,#2430H`. Instead, control-transfer instructions modify it as part of instruction execution.

For example, `LJMP 2430H` loads the program counter with target address `2430H` in effect. Calls also change PC, but they first save a return address on the stack. Returns reload PC from the stack. So the practical rule is: PC changes through sequencing, jumps, calls, returns, interrupts, and reset.

### Page 10: Ports 0 To 3

<a href="images/Day%2014/Screenshot%202026-06-18%20162215.png"><img src="images/Day%2014/Screenshot%202026-06-18%20162215.png" alt="8051 ports 0 to 3" width="960"></a>

`P0`, `P1`, `P2`, and `P3` are SFR latches for the four 8-bit ports. Writing `1` to a port bit drives or releases the corresponding pin high according to that port's electrical structure; writing `0` drives the pin low. When used as input, the pin state is read through the port path.

The deep point is latch versus pin. Some instructions read the latch, modify a bit, and write the latch back. Others read the actual pin state. This distinction prevents mistakes when a pin is externally loaded, being used for alternate functions, or participating in external memory access.

### Page 11: Serial Data Buffer Close View

<a href="images/Day%2014/Screenshot%202026-06-18%20162658.png"><img src="images/Day%2014/Screenshot%202026-06-18%20162658.png" alt="8051 serial data buffer close view" width="960"></a>

`SBUF` looks like one SFR address to software, but internally it represents two different buffers. Writing to `SBUF` loads the transmit buffer and starts serial transmission. Reading from `SBUF` returns data from the receive buffer.

This explains a common confusion: `MOV SBUF,A` and `MOV A,SBUF` do not access the same internal storage cell in opposite directions. The instruction direction selects the transmit or receive side of the serial interface.

### Page 12: Serial Data Buffer Full View

<a href="images/Day%2014/Screenshot%202026-06-18%20165618.png"><img src="images/Day%2014/Screenshot%202026-06-18%20165618.png" alt="8051 serial data buffer full view" width="960"></a>

This repeated slide is worth keeping because it is the cleanest full view of the `SBUF` rule. The transmit buffer is write-only from the programmer's normal perspective, and the receive buffer is read-only. Serial control details such as mode, receive enable, and flags are handled through `SCON`, while `SBUF` is the data byte path.

For revision, connect `SBUF` with the architecture diagram. The serial port block is not just a pin pair; it has a control SFR, data buffers, shift behavior, interrupt flags, and pin-level TXD/RXD activity.

### Page 13: Timer And Control Registers

<a href="images/Day%2014/Screenshot%202026-06-18%20170351.png"><img src="images/Day%2014/Screenshot%202026-06-18%20170351.png" alt="8051 timer and control registers" width="960"></a>

The original 8051 has Timer/Counter 0 and Timer/Counter 1. Each has a high-byte and low-byte register pair: `TH0/TL0` for Timer 0 and `TH1/TL1` for Timer 1. Together, each pair can operate as a 16-bit count path in the appropriate timer mode.

The control-register list is the software control plane around interrupts, timers, serial communication, and power behavior. `IE` enables interrupt sources, `IP` sets interrupt priority, `TMOD` selects timer/counter modes, `TCON` holds timer run/overflow and external interrupt control bits, `SCON` controls serial operation, and `PCON` contains power-control and serial baud-rate related bits.

### Page 14: Register Banks Overview

<a href="images/Day%2014/Screenshot%202026-06-18%20170525.png"><img src="images/Day%2014/Screenshot%202026-06-18%20170525.png" alt="8051 register banks overview" width="960"></a>

The 8051 instruction set frequently names registers as `R0` through `R7`. These are not eight fixed physical addresses in every context. They are aliases for the eight byte locations in the currently selected register bank.

That means `MOV A,R0` can read internal RAM address `00H`, `08H`, `10H`, or `18H` depending on `RS1 RS0` in PSW. This is why the register-bank topic belongs next to PSW. The flag register does not only record arithmetic status; it also selects which fast working-register window the instruction names point to.

### Page 15: PSEN Program Store Enable

<a href="images/Day%2014/Screenshot%202026-06-18%20170759.png"><img src="images/Day%2014/Screenshot%202026-06-18%20170759.png" alt="8051 PSEN program store enable" width="960"></a>

`PSEN` means Program Store Enable. It is an active-low control signal used when the 8051 fetches instruction bytes from external program memory. In a typical external-code-memory system, `PSEN` connects to the output-enable path of the external ROM/EPROM so the selected program byte can drive the data bus during the fetch.

The key separation is code memory versus data memory. `PSEN` is for external program-memory reads. External data memory accesses use the data-memory control path instead, through read/write strobes such as `/RD` and `/WR`. When code is executing from internal ROM on parts that provide it, `PSEN` stays inactive for those internal fetches.

### Page 16: ALE Address Latch Enable

<a href="images/Day%2014/Screenshot%202026-06-18%20171231.png"><img src="images/Day%2014/Screenshot%202026-06-18%20171231.png" alt="8051 ALE address latch enable" width="960"></a>

`ALE` means Address Latch Enable. The slide is connecting the 8051 back to the multiplexed-bus idea already seen in 8085. When Port 0 is used for external memory access, the same eight physical pins carry the low address byte during the first part of the bus cycle and data during the later part of the bus cycle.

The external memory hardware therefore needs a way to capture the address before Port 0 changes role. `ALE` provides that timing signal. During the first half of an external memory cycle, `ALE` pulses so an external latch can store `A0-A7` from Port 0. After that, Port 0 can be reused as the data bus without losing the low address byte. The high address byte normally comes through Port 2 during external memory cycles.

The exam trap is to write that `ALE` carries the address. It does not. Port 0 carries the multiplexed address/data value; `ALE` tells the external latch when the low address byte is valid and should be held.

### Page 17: EA External Access

<a href="images/Day%2014/Screenshot%202026-06-18%20171433.png"><img src="images/Day%2014/Screenshot%202026-06-18%20171433.png" alt="8051 EA external access" width="960"></a>

`EA` means External Access. It is an input pin, not a software SFR bit. It is normally tied either high to `+5 V` or low to ground depending on how the system is meant to fetch program code.

When `EA` is high on a classic 8051 with internal ROM, the controller can execute from internal program memory in the lower 4 KB address region. Program addresses outside the internal range are fetched externally. When `EA` is low, the controller ignores internal program memory for code fetches and executes from external program memory only.

Connect `EA` with `PSEN`: `EA` decides whether internal program memory is eligible for code fetches, while `PSEN` is the active-low read strobe used when external program memory is actually being accessed.

### Page 18: RST And Oscillator Partial View

<a href="images/Day%2014/Screenshot%202026-06-18%20171617.png"><img src="images/Day%2014/Screenshot%202026-06-18%20171617.png" alt="8051 RST and oscillator partial view" width="960"></a>

`RST` is the master reset input for the 8051. The slide states the practical rule: when reset is held high for at least two machine cycles, the controller loads its internal registers with reset values and starts from a known state.

For the pieces already studied, the important reset consequences are `PC = 0000H` and `SP = 07H`. Many SFRs also return to defined reset values so the chip does not begin execution with random interrupt, timer, serial, or port-control state. Reset is therefore not just "restart execution"; it also establishes predictable CPU and peripheral state.

### Page 19: RST And On-Chip Oscillator Inputs

<a href="images/Day%2014/Screenshot%202026-06-18%20171636.png"><img src="images/Day%2014/Screenshot%202026-06-18%20171636.png" alt="8051 reset and on-chip oscillator inputs" width="960"></a>

This fuller capture adds the oscillator point. The 8051 has on-chip oscillator circuitry, but the timing reference is normally provided through the oscillator input pins with an external crystal or clock source. The slide gives the common MCS-51 teaching value of a 12 MHz crystal.

On the classic 8051 timing model, one machine cycle is 12 oscillator periods. With a 12 MHz crystal, that gives a 1 microsecond machine cycle. Modern derivatives can use different oscillator frequencies or faster core timing, so keep the 12 MHz/12-clock statement tied to the classic MCS-51 context unless a specific chip datasheet says otherwise.

### Page 20: RST And Oscillator Full View

<a href="images/Day%2014/Screenshot%202026-06-18%20220817.png"><img src="images/Day%2014/Screenshot%202026-06-18%20220817.png" alt="8051 reset and oscillator full lecture view" width="960"></a>

This screenshot repeats the reset and oscillator content with the video controls visible. It is still useful because it shows the whole slide together: reset is active high and must be sustained long enough for the processor to complete reset sequencing, while the oscillator block supplies the timing foundation for instruction execution, timers, serial baud-rate generation, and bus timing.

Treat reset and oscillator as paired system-start conditions. The oscillator gives the CPU a clock; reset uses clocked machine cycles to force the internal state into a known start-up condition. A reset signal without a valid clock, or a clock without proper reset at power-up, can leave an embedded system unreliable.

### Page 21: Memory Organization Overview

<a href="images/Day%2014/Screenshot%202026-06-18%20221831.png"><img src="images/Day%2014/Screenshot%202026-06-18%20221831.png" alt="8051 memory organization overview" width="960"></a>

The memory-organization slide contrasts general microprocessor systems with microcontroller systems. In many microprocessor-based computers, program files may be stored on disk, loaded into RAM, and executed from the same broad memory space that also holds data. That model fits systems with operating systems, storage, and large RAM.

Small microcontroller systems usually do not have that environment. The control program is expected to reside in nonvolatile program memory, and data storage is handled separately. For that reason, the 8051 uses distinct memory spaces for code and data. Both can be internal or expanded externally, but they are addressed and accessed as separate spaces.

The headline number is `64 KB + 64 KB`: the 8051 can address up to 64 KB of program/code memory and up to 64 KB of external data memory. That does not mean the original chip contains that much memory on-chip. It means the architecture has separate addressability for those spaces.

### Page 22: Internal Memory Map

<a href="images/Day%2014/Screenshot%202026-06-18%20224525.png"><img src="images/Day%2014/Screenshot%202026-06-18%20224525.png" alt="8051 internal memory map" width="960"></a>

This page focuses on the internal data-memory organization. The classic 8051 lower internal RAM is `00H-7FH`, and it is divided into useful regions:

| Range | Region | Main use |
| --- | --- | --- |
| `00H-1FH` | Register banks | Four banks, each containing `R0-R7`. |
| `20H-2FH` | Bit-addressable RAM | 16 bytes that also expose 128 individually addressable bits. |
| `30H-7FH` | General-purpose RAM | Ordinary variable storage and a common place to relocate the stack. |
| `80H-FFH` | SFR direct-address space | Ports, timers, serial, interrupt, accumulator, `B`, `PSW`, `SP`, and other control registers. |

The `80H-FFH` SFR space must be read carefully. It is reached by direct addressing, but not every address in that range is necessarily implemented on every 8051-family device. Unimplemented SFR addresses should not be treated as usable RAM.

### Page 23: Register Bank Close-Up

<a href="images/Day%2014/Screenshot%202026-06-18%20225137.png"><img src="images/Day%2014/Screenshot%202026-06-18%20225137.png" alt="8051 register bank close-up" width="960"></a>

This close-up emphasizes that `00H-1FH` is 32 bytes, not one bank of eight bytes. Those 32 bytes are divided into four register banks:

```text
Bank 0: 00H-07H
Bank 1: 08H-0FH
Bank 2: 10H-17H
Bank 3: 18H-1FH
```

Each bank contains eight byte locations, and the instruction names `R0` through `R7` point into the selected bank. Because the default stack starts at `08H`, using the default stack while also switching into Bank 1 can cause accidental overlap unless the program relocates `SP`.

### Page 24: Bit-Addressable RAM

<a href="images/Day%2014/Screenshot%202026-06-18%20225648.png"><img src="images/Day%2014/Screenshot%202026-06-18%20225648.png" alt="8051 bit-addressable RAM" width="960"></a>

The 8051 supports direct manipulation of individual bits. The internal RAM byte range `20H-2FH` contains 16 bytes. Since each byte has 8 bits, that region gives:

```text
16 bytes x 8 bits = 128 bit-addressable RAM bits
```

The slide also notes additional bit-addressable locations in the SFR area. In practice, only implemented bit-addressable SFRs provide meaningful bits. Typical examples include port SFR bits, `TCON` bits, `SCON` bits, interrupt-enable bits, `PSW` flags, accumulator bits, and `B` register bits.

This feature is one of the reasons 8051 is strong for control work. A program can set, clear, test, AND, or OR a single pin/control/status bit without having to read a whole byte, mask it manually, and write it back in every case. That makes bit-level I/O and flag handling compact and natural.

### Page 25: Bit-Addressable RAM Continuation

<a href="images/Day%2014/Screenshot%202026-06-18%20230055.png"><img src="images/Day%2014/Screenshot%202026-06-18%20230055.png" alt="8051 bit-addressable RAM continuation" width="960"></a>

This continuation explains why bit addressability is more than a memory-map detail. On many ordinary microprocessor systems, changing one bit in a byte requires a read-modify-write sequence: read the byte, apply a mask using AND/OR/XOR, then write the byte back. The 8051 provides instructions that can target individual bits directly, so single-bit inputs, outputs, flags, and control fields are easier to manipulate.

The page repeats the RAM calculation for emphasis. Byte addresses `20H-2FH` cover 16 byte locations, and each byte has 8 bits:

```text
20H-2FH = 16 bytes
16 bytes x 8 bits/byte = 128 general-purpose bit-addressable RAM locations
```

The phrase "general-purpose bit-addressable locations" means these bits are in internal RAM and can be used by the programmer as bit flags. That is separate from bit-addressable SFR bits, which control or report hardware state.

### Page 26: Register Banks And Direct Addressing

<a href="images/Day%2014/Screenshot%202026-06-18%20230344.png"><img src="images/Day%2014/Screenshot%202026-06-18%20230344.png" alt="8051 register banks and direct addressing" width="960"></a>

This late screenshot returns to the register-bank idea from the internal RAM map. The bottom 32 locations of internal memory are the register-bank area. After reset, the default selected bank is Bank 0, so `R0` through `R7` refer to byte addresses `00H-07H`.

The reason the instruction set gives these locations special names is efficiency. Instructions that use `R0-R7` are generally shorter and faster than equivalent instructions that name an internal RAM byte through direct addressing. Therefore, values used frequently in tight code should usually be kept in the selected register bank when possible.

This does not create extra storage beyond internal RAM. The register names are aliases for RAM locations in the active bank. Changing `RS1 RS0` changes which eight RAM bytes the names `R0-R7` refer to.

### Page 27: Special Function Registers

<a href="images/Day%2014/Screenshot%202026-06-18%20230558.png"><img src="images/Day%2014/Screenshot%202026-06-18%20230558.png" alt="8051 special function registers" width="960"></a>

The SFR slide explains why the 8051 is controlled through addresses. The chip has many internal registers, and those registers are made programmer-visible through the direct-address region `80H-FFH`. This lets software access hardware control and status points using normal-looking data-transfer and bit instructions.

The slide says there are 21 special function registers in the classic 8051. Typical examples are `P0`, `P1`, `P2`, `P3`, `SP`, `DPL`, `DPH`, `PCON`, `TCON`, `TMOD`, `TL0`, `TL1`, `TH0`, `TH1`, `SCON`, `SBUF`, `IE`, `IP`, `PSW`, `ACC`, and `B`.

Be precise with the phrase "top of internal RAM." In the programmer's direct-address map, SFRs occupy addresses `80H-FFH`; however, they are not general-purpose RAM bytes. They are hardware registers. Some 8051-family variants also have upper internal RAM, but that upper RAM is normally accessed indirectly, while SFRs are accessed directly.

### Page 28: External Memory Expansion

<a href="images/Day%2014/Screenshot%202026-06-18%20230840.png"><img src="images/Day%2014/Screenshot%202026-06-18%20230840.png" alt="8051 external memory expansion" width="960"></a>

This page ties the memory-organization topic to the actual external bus pins. The MCS-51 architecture supports expansion with up to 64 KB of external code memory and up to 64 KB of external data memory. Extra ROM, RAM, and peripheral interface ICs can be added when the internal resources are not enough.

External peripheral ICs can be placed in the external data-memory space using memory-mapped I/O. In that design, the peripheral is selected by address-decoding hardware and accessed through external data-memory bus cycles, rather than through the four on-chip port SFRs alone.

The hardware cost is that normal port use is reduced during external memory operation. Port 0 is no longer available as ordinary 8-bit I/O because it becomes the multiplexed low address/data bus: `A0-A7` first, then `D0-D7`. `ALE` latches the low address byte at the start of the cycle. Port 2 is usually used for the high address byte `A8-A15`, although exact usage can depend on the access type and address range.

### Page 29: External Memory Address-Line Annotation

<a href="images/Day%2014/Screenshot%202026-06-18%20231120.png"><img src="images/Day%2014/Screenshot%202026-06-18%20231120.png" alt="8051 external memory address-line annotation" width="960"></a>

This cropped annotation gives the address-line reason behind the 64 KB number:

```text
64 KB = 2^16 addressable byte locations
Therefore, a full 64 KB memory space needs 16 address lines: A0-A15.
```

For external memory cycles, the lower eight address lines `A0-A7` appear on Port 0 at the beginning of the cycle. After `ALE` latches those bits externally, Port 0 changes role and carries data lines `D0-D7`. The upper eight address lines `A8-A15` are supplied by Port 2 for full 16-bit external addressing.

This is why external-memory diagrams often label Port 0 as `AD0-AD7`, not simply `A0-A7` or `D0-D7`. The `AD` label means address/data multiplexed.

### Page 30: Working Register Bank Map

<a href="images/Day%2014/Screenshot%202026-06-18%20231227.png"><img src="images/Day%2014/Screenshot%202026-06-18%20231227.png" alt="8051 working register bank map" width="960"></a>

This diagram visualizes the four working-register banks inside the lowest 32 bytes of internal RAM. Each bank has eight registers:

| Bank | `R0` address | `R7` address |
| --- | --- | --- |
| Bank 0 | `00H` | `07H` |
| Bank 1 | `08H` | `0FH` |
| Bank 2 | `10H` | `17H` |
| Bank 3 | `18H` | `1FH` |

The bank selected by `RS1 RS0` becomes the active working-register set. The other banks are still RAM bytes, but the short `R0-R7` register instructions refer only to the currently selected bank. This is why register-bank selection is both a performance feature and a memory-layout responsibility.

### Page 31: Annotated Working Register Bank Map

<a href="images/Day%2014/Screenshot%202026-06-18%20231239.png"><img src="images/Day%2014/Screenshot%202026-06-18%20231239.png" alt="8051 annotated working register bank map" width="960"></a>

This annotated version highlights the lower boundary of Bank 0. Bank 0 is `00H-07H`: `R0` is at `00H`, `R1` is at `01H`, and `R7` is at `07H`. Bank 1 begins immediately after it at `08H`.

The four-bank layout is consecutive and non-overlapping:

```text
00H-07H  Bank 0
08H-0FH  Bank 1
10H-17H  Bank 2
18H-1FH  Bank 3
```

This matters for stack placement. Since reset gives `SP = 07H`, the first default stack push goes to `08H`, exactly where Bank 1 begins. If the program uses Bank 1, Bank 2, or Bank 3, moving the stack into a safer general-purpose RAM area is usually the clean choice.

### Page 32: General-Purpose RAM Region

<a href="images/Day%2014/Screenshot%202026-06-18%20231338.png"><img src="images/Day%2014/Screenshot%202026-06-18%20231338.png" alt="8051 general-purpose RAM region" width="960"></a>

This diagram shifts attention above the bit-addressable region. Internal RAM addresses `30H-7FH` are general-purpose byte storage. They are commonly used for variables, temporary data, buffers, and relocated stack space.

Do not confuse this region with the bit-addressable RAM at `20H-2FH`. Bytes in `30H-7FH` are directly and indirectly addressable as bytes, but they do not provide the special direct bit addresses that `20H-2FH` provides. For ordinary byte variables, `30H-7FH` is often the cleanest internal RAM area because it avoids register-bank and bit-flag reservations.

### Page 33: Working Register Summary

<a href="images/Day%2014/Screenshot%202026-06-18%20231641.png"><img src="images/Day%2014/Screenshot%202026-06-18%20231641.png" alt="8051 working register summary" width="960"></a>

This page condenses the working-register facts into exam form:

| Fact | Value |
| --- | --- |
| Number of register banks | 4 |
| Registers per bank | 8 |
| Register names | `R0, R1, R2, R3, R4, R5, R6, R7` |
| Bank selection bits | `RS1` and `RS0` in `PSW` |
| Bank 0 range | `00H-07H` |
| Bank 1 range | `08H-0FH` |
| Bank 2 range | `10H-17H` |
| Bank 3 range | `18H-1FH` |

The main point is that `R0-R7` are names for the active bank, not separate global registers outside RAM. Any bank can hold data, but only one bank is selected for short register-name instructions at a time.

### Page 34: Bit Instructions And General-Purpose RAM

<a href="images/Day%2014/Screenshot%202026-06-18%20231720.png"><img src="images/Day%2014/Screenshot%202026-06-18%20231720.png" alt="8051 bit instructions and general-purpose RAM" width="960"></a>

This page adds the instruction-level use of bit-addressable memory. The 8051 can address individual bit variables, especially the 128 RAM bit addresses corresponding to internal RAM bytes `20H-2FH`. The bit-address range for that RAM bit area is commonly written as bit addresses `00H-7FH`.

Two basic bit instructions are:

```asm
SETB bit   ; set the addressed bit to 1
CLR  bit   ; clear the addressed bit to 0
```

The lower internal RAM map should be kept precise:

| Range | Size | Main role |
| --- | --- | --- |
| `00H-1FH` | 32 bytes | Register banks. |
| `20H-2FH` | 16 bytes | Bit-addressable RAM, also byte-addressable. |
| `30H-7FH` | 80 bytes | General-purpose RAM for ordinary byte variables, buffers, and stack relocation. |

The slide wording around "80 bytes" and `00H-2FH` is easy to misread. The 80-byte general-purpose region is `30H-7FH`; the earlier `00H-2FH` portion is already assigned to register banks and bit-addressable RAM, though those bytes can still be accessed as internal RAM when the program intentionally uses them that way.

### Page 35: 8051 Pin Diagram

<a href="images/Day%2014/Screenshot%202026-06-18%20231941.png"><img src="images/Day%2014/Screenshot%202026-06-18%20231941.png" alt="8051 pin diagram" width="960"></a>

The pin diagram connects the logical blocks to the physical 40-pin IC package. The basic package facts are:

| Item | Pins |
| --- | --- |
| `P1.0-P1.7` | Pins `1-8` |
| `RST` | Pin `9` |
| `P3.0-P3.7` | Pins `10-17` |
| `XTAL2`, `XTAL1` | Pins `18`, `19` |
| `GND` | Pin `20` |
| `P2.0-P2.7` | Pins `21-28` |
| `PSEN` | Pin `29` |
| `ALE/PROG` | Pin `30` |
| `EA/VPP` | Pin `31` |
| `P0.0-P0.7` | Pins `39-32` |
| `VCC` | Pin `40` |

The four 8-bit ports account for 32 pins, but several pins have alternate roles. Port 0 doubles as `AD0-AD7` during external memory cycles. Port 2 doubles as `A8-A15`. Port 3 carries alternate serial, interrupt, timer, and external data-memory control functions: `RXD`, `TXD`, `INT0`, `INT1`, `T0`, `T1`, `/WR`, and `/RD`.

The practical reading is that "32 I/O lines" is true for simple internal-memory operation, but external memory and alternate peripheral functions consume some of those pins for bus/control work.

### Page 36: External Address-Line Close-Up

<a href="images/Day%2014/Screenshot%202026-06-18%20232030.png"><img src="images/Day%2014/Screenshot%202026-06-18%20232030.png" alt="8051 external address-line close-up" width="960"></a>

This close-up repeats the calculation behind the external address bus:

```text
64 KB = 2^16 byte addresses
A0-A15 = 16 address lines
```

Read the handwritten "port-00" as Port 0. Port 0 carries `A0-A7` first, then `D0-D7` after the address is latched. Port 2 carries `A8-A15`. Together, they provide the 16 address lines needed for a full 64 KB external memory space.

### Page 37: Pin Description - P1, Reset, And P3 Serial Functions

<a href="images/Day%2014/Screenshot%202026-06-18%20232113.png"><img src="images/Day%2014/Screenshot%202026-06-18%20232113.png" alt="8051 pin description for P1 reset and P3 serial" width="960"></a>

This pin-description page starts with Port 1. Pins `1-8` are `P1.0-P1.7`, and each can be used as a general input/output pin. Port 1 is the cleanest basic I/O port in the classic 8051 because it does not carry the external address/data bus roles that Port 0 and Port 2 carry.

Pin `9` is `RST`. A logic high reset input forces the microcontroller into its reset state and initializes the CPU/SFR state. The safe phrasing is "initializes/clears control state according to reset defaults," not "erases all program memory." Reset does not wipe ROM code.

Pins `10-17` are Port 3. They can act as I/O pins, but they also have alternate functions. The first two are serial-port pins:

| Pin | Port bit | Alternate function |
| --- | --- | --- |
| `10` | `P3.0` | `RXD`, serial receive/data input path. |
| `11` | `P3.1` | `TXD`, serial transmit/clock output path. |

In the common UART-style serial modes, `RXD` receives serial data and `TXD` transmits serial data. In the synchronous shift-register mode, the exact data/clock behavior differs, but the important exam identity remains: `P3.0 = RXD` and `P3.1 = TXD`.

### Page 38: Pin Description - P3 Interrupts, Timers, Oscillator, And P2

<a href="images/Day%2014/Screenshot%202026-06-18%20232344.png"><img src="images/Day%2014/Screenshot%202026-06-18%20232344.png" alt="8051 pin description for P3 interrupts timers oscillator and P2" width="960"></a>

This continuation completes most of Port 3's alternate-function list:

| Pin | Port bit/signal | Role |
| --- | --- | --- |
| `12` | `P3.2 / INT0` | External interrupt 0 input. |
| `13` | `P3.3 / INT1` | External interrupt 1 input. |
| `14` | `P3.4 / T0` | External counter/timer 0 input. |
| `15` | `P3.5 / T1` | External counter/timer 1 input. |
| `16` | `P3.6 / WR` | External data-memory write strobe. |
| `17` | `P3.7 / RD` | External data-memory read strobe. |

Pins `18` and `19` are `XTAL2` and `XTAL1`, the oscillator pins used with the crystal/clock circuit. Pin `20` is `GND`. Pins `21-28` are Port 2. If external memory is not being used, Port 2 can operate as general input/output; during external memory access, it usually supplies the high address byte.

### Page 39: 8051 Addressing Modes

<a href="images/Day%2014/Screenshot%202026-06-18%20232841.png"><img src="images/Day%2014/Screenshot%202026-06-18%20232841.png" alt="8051 addressing modes" width="960"></a>

This page starts the instruction-addressing part of the 8051 topic. Addressing mode means the way an instruction identifies its operand. The slide lists five common 8051 addressing modes:

| Addressing mode | Operand idea | Example pattern |
| --- | --- | --- |
| Immediate | Constant data is inside the instruction. | `MOV A,#55H` |
| Register | Operand is one of the active-bank registers. | `MOV A,R2` |
| Direct | Instruction gives an internal RAM or SFR direct address. | `MOV A,30H`, `MOV P1,A` |
| Register indirect | Register holds the address of the operand. | `MOV A,@R0`, `MOV @R1,A` |
| Indexed | Effective code-memory address is formed from a base plus `A`. | `MOVC A,@A+DPTR` |

The difference between register and direct addressing is important in 8051. `MOV A,R0` reads the active bank's `R0`; `MOV A,00H` directly reads internal RAM address `00H`. Those may refer to the same physical byte only when Bank 0 is selected and the direct address is `00H`.

### Page 40: Pin Description - PSEN, ALE, EA, P0, And VCC

<a href="images/Day%2014/Screenshot%202026-06-18%20232858.png"><img src="images/Day%2014/Screenshot%202026-06-18%20232858.png" alt="8051 pin description for PSEN ALE EA P0 and VCC" width="960"></a>

This page completes the pin-description sequence:

| Pin/range | Signal | Correct role |
| --- | --- | --- |
| `29` | `PSEN` | Active-low Program Store Enable for external program-memory fetches. |
| `30` | `ALE` | Address Latch Enable; latches `A0-A7` from multiplexed Port 0. |
| `31` | `EA` | External Access input; selects internal-plus-external code fetch behavior versus external-only code fetch behavior. |
| `32-39` | `P0.7-P0.0` | Port 0 I/O when external memory is not used; `AD0-AD7` during external memory cycles. |
| `40` | `VCC` | Positive supply pin, commonly `+5 V` on classic 8051 parts. |

Use corrected wording for `PSEN`: it is not the external RAM data-write signal. External data-memory writes use `/WR` on `P3.6`, and external data-memory reads use `/RD` on `P3.7`. `PSEN` belongs to external program/code memory fetches.

For `EA`, logic `0` forces external program memory for code fetches. Logic `1` allows internal program memory for the internal code range and external program memory for addresses beyond that range, depending on the exact 8051-family part and memory configuration.

### Page 41: Immediate Addressing

<a href="images/Day%2014/Screenshot%202026-06-18%20232958.png"><img src="images/Day%2014/Screenshot%202026-06-18%20232958.png" alt="8051 immediate addressing" width="960"></a>

Immediate addressing means the source operand is a constant included as part of the instruction itself. The instruction does not go to RAM or a register to fetch that source value; the value is already encoded in the instruction bytes.

In 8051 assembly syntax, immediate operands are written with `#`:

```asm
MOV A,#25H   ; put the constant 25H into A
MOV R4,#62   ; put the constant 62 into R4
```

The constant can be numeric, symbolic, or an expression that the assembler can evaluate. After evaluation, the assembler substitutes the resulting byte or word value into the machine instruction. The destination still matters: an 8-bit destination needs an 8-bit immediate value, while `DPTR` can take a 16-bit immediate value.

### Page 42: Immediate Addressing Examples

<a href="images/Day%2014/Screenshot%202026-06-18%20233151.png"><img src="images/Day%2014/Screenshot%202026-06-18%20233151.png" alt="8051 immediate addressing examples" width="960"></a>

This examples page makes three exam points.

First, `#` marks immediate data:

```asm
MOV A,#12    ; load decimal 12 into A if the assembler treats unsuffixed numbers as decimal
MOV A,#25H   ; load hexadecimal 25H into A
MOV R4,#62   ; load decimal 62 into R4 under the same decimal-default convention
MOV B,#40H   ; load hexadecimal 40H into B
MOV P1,#55H  ; write immediate byte 55H to Port 1 latch
```

Second, notation matters. Many teaching examples treat numbers without `H` as decimal, so `#12` means decimal 12, which is `0CH` in hex. `#12H` would mean hexadecimal 12.

Third, immediate size must fit the destination. `DPTR` is 16 bits, so `MOV DPTR,#4521H` is legal and loads `DPH = 45H`, `DPL = 21H`. The equivalent byte-wise setup is:

```asm
MOV DPL,#21H
MOV DPH,#45H
```

The slide's right-side text appears to mix `4521H` and `4512H`; the consistent result for `MOV DPTR,#4521H` is `DPTR = 4521H`. A value such as decimal `68975` is illegal for `DPTR` because it is greater than `65535`, the maximum unsigned 16-bit value `FFFFH`.

### Page 43: Register Addressing Mode

<a href="images/Day%2014/Screenshot%202026-06-18%20233333.png"><img src="images/Day%2014/Screenshot%202026-06-18%20233333.png" alt="8051 register addressing mode" width="960"></a>

Register addressing names a CPU/register-bank operand directly in the instruction. The source or destination is not written as a memory address; it is written as a register name such as `A`, `B`, `R0-R7`, or another register supported by that instruction form.

Examples from the slide:

```asm
MOV A,R0    ; copy active-bank R0 into A
MOV R2,A    ; copy A into active-bank R2
ADD A,R5    ; add active-bank R5 to A
ADD A,R7    ; add active-bank R7 to A
MOV R6,A    ; save A into active-bank R6
```

The invalid example is also useful:

```asm
MOV DPTR,A  ; invalid
```

`DPTR` is a 16-bit register made from `DPH:DPL`, while `A` is 8-bit. The 8051 does not provide a direct `MOV DPTR,A` instruction. To load `DPTR`, use a 16-bit immediate instruction such as `MOV DPTR,#25F5H`, or write `DPL` and `DPH` separately.

### Page 44: Handwritten DPTR And Register-Addressing Note

<a href="images/Day%2014/day-14-register-addressing-dptr-handwritten-note.jpg"><img src="images/Day%2014/day-14-register-addressing-dptr-handwritten-note.jpg" alt="handwritten 8051 DPTR and register-addressing note" width="720"></a>

This handwritten note reinforces the DPTR example from immediate addressing. If the instruction is:

```asm
MOV DPTR,#4521H
```

then `DPTR` receives the 16-bit value `4521H`, so:

```text
DPH = 45H
DPL = 21H
```

The note also connects register addressing with examples such as:

```asm
ADD A,R5
```

Here `R5` is a register operand from the currently selected register bank, and `A` is the accumulator.

The warning about register-to-register transfer should be read as the usual 8051 rule that arbitrary `MOV Rn,Rm` is not a general instruction form. For example, moving directly from one working register to another is normally done through the accumulator:

```asm
MOV A,R6
MOV R7,A
```

`DPL` and `DPH` are direct-addressable SFRs, so many assemblers can treat a name such as `DPL` as a direct address. The core exam point remains: do not assume every register-looking pair has a direct register-to-register `MOV` encoding.

### Page 45: Direct Addressing Mode

<a href="images/Day%2014/Screenshot%202026-06-18%20234503.png"><img src="images/Day%2014/Screenshot%202026-06-18%20234503.png" alt="8051 direct addressing mode" width="960"></a>

Direct addressing means the instruction contains the byte address of the operand. In 8051, direct addressing can reach the lower 128 bytes of internal RAM and the SFR address space.

Examples from the slide:

```asm
MOV R0,40H   ; copy internal RAM byte 40H into active-bank R0
MOV 56H,A    ; copy A into internal RAM byte 56H
MOV A,04H    ; copy direct address 04H into A
```

The last example is often compared with:

```asm
MOV A,R4
```

These are equivalent only when Bank 0 is selected, because Bank 0 maps `R4` to internal RAM address `04H`. If another register bank is selected, `MOV A,R4` reads that bank's `R4`, while `MOV A,04H` still reads the fixed direct address `04H`.

Direct addressing is also how many SFRs are accessed. For example, a symbolic name such as `P1` or `DPL` is assembled as its direct SFR address.

### Page 46: SFR Direct-Addressing Examples

<a href="images/Day%2014/Screenshot%202026-06-18%20234551.png"><img src="images/Day%2014/Screenshot%202026-06-18%20234551.png" alt="8051 SFR direct-addressing examples" width="960"></a>

This page shows that SFRs can be addressed either by symbolic register name or by direct address. Two useful SFR addresses are:

| SFR | Direct address |
| --- | --- |
| `ACC` / `A` | `E0H` |
| `B` | `F0H` |

So these pairs are equivalent in meaning:

```asm
MOV 0E0H,#55H  ; write 55H to ACC
MOV A,#55H     ; write 55H to A

MOV 0F0H,R0    ; write R0 to B
MOV B,R0       ; write R0 to B
```

Using names is normally clearer, but address form is useful for understanding the SFR map and how assemblers translate symbolic names.

### Page 47: SFR Address-Space Caution

<a href="images/Day%2014/Screenshot%202026-06-18%20234751.png"><img src="images/Day%2014/Screenshot%202026-06-18%20234751.png" alt="8051 SFR address-space caution" width="960"></a>

SFR addresses are in the direct-address range `80H-FFH`, but that does not mean every address in `80H-FFH` is a usable register. Only implemented SFR addresses have defined behavior. Unused addresses are reserved and should not be used by the programmer.

This is why a map or datasheet matters. The classic 8051 defines a standard set of SFRs, while derivatives often add more SFRs for extra peripherals. Code should rely only on addresses defined for the exact device being used.

### Page 48: Register-Indirect Addressing Mode

<a href="images/Day%2014/Screenshot%202026-06-18%20234938.png"><img src="images/Day%2014/Screenshot%202026-06-18%20234938.png" alt="8051 register-indirect addressing mode" width="960"></a>

Register-indirect addressing means the instruction names a register that contains the address of the data byte. The register is a pointer; the operand is at the address stored inside that register.

For many 8051 `MOV` instructions, the indirect pointer register is `R0` or `R1`:

```asm
MOV A,@R0   ; A gets the byte from the internal RAM address held in R0
MOV @R1,A   ; the internal RAM address held in R1 gets A
```

The `@` symbol is the key syntax marker. `MOV A,R0` copies the contents of `R0`; `MOV A,@R0` uses the contents of `R0` as an address and copies the byte from that addressed location.

The slide says the pointed location could be a RAM or SFR address. In the standard 8051 internal data space, `@R0/@R1` indirect addressing is mainly used for internal RAM and upper RAM on derivatives; SFRs are normally reached by direct addressing. Keep that distinction tied to the exact instruction and chip variant.

### Page 49: Register-Indirect Addressing Examples

<a href="images/Day%2014/Screenshot%202026-06-18%20235110.png"><img src="images/Day%2014/Screenshot%202026-06-18%20235110.png" alt="8051 register-indirect addressing examples" width="960"></a>

This page gives concrete `@R0/@R1` examples:

```asm
MOV A,@R0       ; copy the byte at the address held in R0 into A
MOV @R1,#35H    ; store immediate 35H at the address held in R1
MOV add,@R0     ; copy the byte at the address held in R0 into direct location add
MOV @R1,A       ; store A at the address held in R1
MOV @R0,80H     ; copy the byte at direct address 80H into the address held in R0
```

Direct address `80H` is `P0` in the classic SFR map. The exact read behavior for ports can involve latch-versus-pin details, but the addressing-mode point is simpler: the source operand is direct address `80H`, and the destination is the internal RAM location pointed to by `R0`.

### Page 50: Indexed Addressing

<a href="images/Day%2014/Screenshot%202026-06-18%20235419.png"><img src="images/Day%2014/Screenshot%202026-06-18%20235419.png" alt="8051 indexed addressing" width="960"></a>

Indexed addressing forms an effective code-memory address from a base register plus an offset in the accumulator. In 8051, this is commonly used for lookup tables stored in program memory:

```asm
MOVC A,@A+DPTR
MOVC A,@A+PC
```

`MOVC` means move from code memory. For `MOVC A,@A+DPTR`, the code byte is read from program-memory address `DPTR + A` and copied into `A`. For `MOVC A,@A+PC`, the base is the program counter instead of `DPTR`.

This addressing mode is useful for constants such as display-code tables, conversion tables, jump-table support patterns, and other read-only data kept with the program.

### Page 51: ALE And External-Memory Port Roles

<a href="images/Day%2014/Screenshot%202026-06-18%20235805.png"><img src="images/Day%2014/Screenshot%202026-06-18%20235805.png" alt="8051 ALE and external-memory port roles" width="960"></a>

This page summarizes Port 0's multiplexed role. During an external memory cycle, Port 0 first carries the low address byte `A0-A7`; `ALE` pulses so external hardware can latch that address. After the address phase, Port 0 carries data `D0-D7`.

The slide phrases this as:

```text
ALE = 1: Port 0 has A0-A7 address information.
ALE = 0: Port 0 is in the data phase for D0-D7.
```

The practical hardware rule is to use the ALE pulse edge to latch the low address byte. Do not think of `ALE` as carrying address or data itself; it is the timing/control signal for the external latch.

When there is no external memory connection, the ports can be used as ordinary I/O according to their port-specific electrical behavior. When external memory is connected, Port 0 and usually Port 2 are consumed by the external bus: Port 0 provides multiplexed low address/data and Port 2 provides the high address byte for 16-bit external addressing.

### Page 52: Port 2 And Port 3 External-Memory Notes

<a href="images/Day%2014/Screenshot%202026-06-19%20000133.png"><img src="images/Day%2014/Screenshot%202026-06-19%20000133.png" alt="8051 Port 2 and Port 3 external-memory notes" width="960"></a>

This page makes the Port 2 tradeoff explicit. In a system using external memory, Port 2 provides the upper address byte `A8-A15` for the 16-bit external address. During those bus cycles, Port 2 is not available as ordinary I/O because it is part of the memory interface.

Port 3 is different. It can be used as input/output, but it also carries important alternate signals such as serial I/O, external interrupts, timer inputs, and external data-memory `/RD` and `/WR`. The slide also notes that Port 3 does not require external pull-up resistors in the usual classic 8051 port structure.

For exam answers, separate the port roles:

```text
Port 0: multiplexed A0-A7 / D0-D7 during external memory.
Port 2: A8-A15 during external memory.
Port 3: I/O plus alternate serial, interrupt, timer, RD, and WR functions.
```

### Page 53: Port 3 Alternate-Function Table

<a href="images/Day%2014/Screenshot%202026-06-19%20000139.png"><img src="images/Day%2014/Screenshot%202026-06-19%20000139.png" alt="8051 Port 3 alternate-function table" width="960"></a>

This table is the cleanest Port 3 summary:

| Port 3 bit | Alternate function | Pin | Group |
| --- | --- | --- | --- |
| `P3.0` | `RXD` | `10` | Serial communication |
| `P3.1` | `TXD` | `11` | Serial communication |
| `P3.2` | `/INT0` | `12` | External interrupt |
| `P3.3` | `/INT1` | `13` | External interrupt |
| `P3.4` | `T0` | `14` | Timer/counter input |
| `P3.5` | `T1` | `15` | Timer/counter input |
| `P3.6` | `/WR` | `16` | External data-memory write |
| `P3.7` | `/RD` | `17` | External data-memory read |

The overbar on `INT0`, `INT1`, `WR`, and `RD` means active-low signaling. Even when Port 3 pins can work as normal I/O, enabling the corresponding alternate hardware function gives that peripheral control of the pin behavior.

### Page 54: Counters And Timers

<a href="images/Day%2014/Screenshot%202026-06-19%20000542.png"><img src="images/Day%2014/Screenshot%202026-06-19%20000542.png" alt="8051 counters and timers" width="960"></a>

The original 8051 provides two 16-bit up counters, Timer/Counter 0 and Timer/Counter 1. They can reduce processor burden by counting events or generating timing intervals in hardware instead of forcing software loops for everything.

Each block can be programmed in two broad ways:

| Use | What is counted | Typical purpose |
| --- | --- | --- |
| Timer | Internal clock-derived pulses | Time delays, periodic intervals, baud-rate generation. |
| Counter | External pulses on `T0` or `T1` pins | Event counting or pulse/frequency-related counting. |

The slide lists three common uses: interval timing, event counting, and baud-rate generation for the built-in serial port. This connects back to the SFR list: `TH0/TL0`, `TH1/TL1`, `TMOD`, and `TCON` are the programmer-visible controls for these timer/counter blocks.

### Page 55: Timer Interval Timing

<a href="images/Day%2014/Screenshot%202026-06-19%20001006.png"><img src="images/Day%2014/Screenshot%202026-06-19%20001006.png" alt="8051 timer interval timing" width="960"></a>

The 8051 timers/counters can be used as timers for generating delays or as event counters for counting external events. In interval-timing applications, the timer is loaded and allowed to count until it overflows. On overflow, the corresponding timer overflow flag is set.

Software can poll the overflow flag, or an interrupt can be enabled so the overflow automatically requests service. Typical periodic actions include checking input states, updating outputs, maintaining a software clock, or measuring elapsed time such as pulse width.

The important mental model is:

```text
Initial timer value + internal clock ticks -> overflow -> flag/interrupt -> program action
```

### Page 56: Event Counting And Baud Clock

<a href="images/Day%2014/Screenshot%202026-06-19%20001029.png"><img src="images/Day%2014/Screenshot%202026-06-19%20001029.png" alt="8051 event counting and baud clock" width="960"></a>

Event counting counts occurrences rather than measuring elapsed time. In classic 8051 counter mode, external transitions on the timer input pins are counted. The slide describes an event as an external stimulus that provides a `1`-to-`0` transition to a pin on the 8051 IC.

This distinguishes the two modes:

| Mode | Input source | Meaning |
| --- | --- | --- |
| Timer | Internal clock-derived pulses | Measures time. |
| Counter | External transitions on `T0`/`T1` | Counts events. |

The slide also repeats that timers can provide the baud-rate clock for the internal serial port. This is a major practical use of Timer 1 in many classic 8051 serial examples.

### Page 57: Timer Mode Register TMOD

<a href="images/Day%2014/Screenshot%202026-06-19%20001407.png"><img src="images/Day%2014/Screenshot%202026-06-19%20001407.png" alt="8051 timer mode register TMOD" width="960"></a>

`TMOD` is the Timer Mode register. It contains two 4-bit fields: one field controls Timer 0, and the other controls Timer 1. Each field selects the timer/counter operating mode and related operation control.

The slide highlights two practical rules:

```text
TMOD is not bit-addressable.
TMOD is usually initialized by software near the start of the program.
```

Because `TMOD` is not bit-addressable, code normally writes the whole byte or uses read-mask-write logic if only part of it must change. The lower two bits of each timer field select the mode, while the upper two bits specify operation-related control such as timer-versus-counter selection and gate control.

### Page 58: TMOD Mode Table

<a href="images/Day%2014/Screenshot%202026-06-19%20001534.png"><img src="images/Day%2014/Screenshot%202026-06-19%20001534.png" alt="8051 TMOD mode table" width="960"></a>

Each timer's `TMOD` field contains `GATE`, `C/T`, `M1`, and `M0`. The `M1 M0` pair selects the timer mode:

| `M1 M0` | Mode | Meaning |
| --- | --- | --- |
| `00` | Mode 0 | 13-bit timer/counter mode. |
| `01` | Mode 1 | 16-bit timer/counter mode, with `THx` and `TLx` cascaded. |
| `10` | Mode 2 | 8-bit auto-reload mode; `THx` stores the reload value for `TLx`. |
| `11` | Mode 3 | Split timer mode. |

The slide heading appears to have a small label typo in the right-side bit group; the intended per-timer bit pattern is `GATE C/T M1 M0`.

### Page 59: TMOD C/T Bit

<a href="images/Day%2014/Screenshot%202026-06-19%20002658.png"><img src="images/Day%2014/Screenshot%202026-06-19%20002658.png" alt="8051 TMOD C/T bit timer counter selection" width="960"></a>

`C/T` is the timer-or-counter select bit inside each timer's `TMOD` field. When `C/T=0`, the block works as a timer and counts the internal system clock-derived pulses. That is the setting used for time delays, periodic interrupts, software timeouts, and baud-rate generation support.

When `C/T=1`, the same hardware works as a counter and counts transitions arriving from the external timer input pin, `T0` or `T1`. That mode is for counting real external events. The register pair and overflow behavior still matter, but the source of increments changes from the internal clock to the outside pin.

### Page 60: TMOD GATE Bit

<a href="images/Day%2014/Screenshot%202026-06-19%20002820.png"><img src="images/Day%2014/Screenshot%202026-06-19%20002820.png" alt="8051 TMOD GATE bit software timer control" width="960"></a>

`GATE` decides how the timer/counter is allowed to run. When `GATE=0`, the timer is controlled by software through the run-control bits `TR0` and `TR1` in `TCON`. Setting the relevant `TRx` bit starts the timer, and clearing it stops the timer. In assembly form, that often appears as `SETB TR0`, `CLR TR0`, `SETB TR1`, or `CLR TR1`.

When `GATE=1`, hardware gating through the external interrupt pin is added to the start condition. For Day 14, the essential exam point is the simpler software case shown on the slide: with `GATE=0`, the timer starts and stops under program control as long as the corresponding `TRx` bit is set or cleared.

### Page 61: GATE Software-Control Slide

<a href="images/Day%2014/Screenshot%202026-06-19%20003111.png"><img src="images/Day%2014/Screenshot%202026-06-19%20003111.png" alt="8051 GATE software-control slide" width="960"></a>

This is the cleaner full-frame version of the `GATE` explanation. It repeats the main rule: with `GATE=0`, timer start and stop are controlled through software, using `TR0` for Timer 0 and `TR1` for Timer 1. The timer does not need an external gate condition in this mode.

The assembly implication is direct. `SETB TR0` or `SETB TR1` starts the selected timer/counter, while `CLR TR0` or `CLR TR1` stops it. This does not reset the timer count; it only controls whether the counting hardware is allowed to run.

### Page 62: Timer Mode 0, 13-Bit Timer

<a href="images/Day%2014/Screenshot%202026-06-19%20003351.png"><img src="images/Day%2014/Screenshot%202026-06-19%20003351.png" alt="8051 Timer Mode 0 13-bit timer" width="960"></a>

Mode 0 is the old 13-bit timer mode included for compatibility with the 8048 family. It is not commonly preferred in new designs, but it appears in exams because it proves whether the timer-register layout is understood. The timer uses `THx` together with the five least significant bits of `TLx`.

Because only five bits of `TLx` participate, the upper three bits of `TLx` are not part of the 13-bit count. The counting range is therefore smaller than Mode 1's 16-bit range, and the overflow occurs when this 13-bit count wraps around.

### Page 63: Timer Mode 1, 16-Bit Timer

<a href="images/Day%2014/Screenshot%202026-06-19%20003638.png"><img src="images/Day%2014/Screenshot%202026-06-19%20003638.png" alt="8051 Timer Mode 1 16-bit timer" width="960"></a>

Mode 1 uses the combined `THx` and `TLx` registers as a normal 16-bit up-counter. `TLx` is the low byte and `THx` is the high byte, so the count runs from `0000H` through `FFFFH` if it is allowed to continue long enough.

On the transition from `FFFFH` to `0000H`, the timer overflow flag `TFx` is set in `TCON`. The timer can keep counting, but software or an interrupt service routine must notice and clear the flag according to the program design.

### Page 64: Timer Mode 2, 8-Bit Auto-Reload

<a href="images/Day%2014/Screenshot%202026-06-19%20003804.png"><img src="images/Day%2014/Screenshot%202026-06-19%20003804.png" alt="8051 Timer Mode 2 8-bit auto-reload" width="960"></a>

Mode 2 uses `TLx` as the 8-bit timer/counter and `THx` as the reload register. When `TLx` overflows from `FFH` to `00H`, the overflow flag is set and the value from `THx` is automatically loaded back into `TLx`.

This mode is useful when the same interval must repeat many times. Instead of software manually reloading the timer after every overflow, the hardware reloads `TLx`, giving consistent periodic timing once `TMOD` and `THx` are initialized.

### Page 65: Timer Mode 3, Split Timer

<a href="images/Day%2014/Screenshot%202026-06-19%20004024.png"><img src="images/Day%2014/Screenshot%202026-06-19%20004024.png" alt="8051 Timer Mode 3 split timer" width="960"></a>

Mode 3 is the split timer mode. Timer 0 is split into two separate 8-bit timers: `TL0` and `TH0`. Each half can overflow independently, so the chip effectively gains another small timer at the cost of normal Timer 1 interrupt behavior.

The important restriction is that Timer 1 is affected when Timer 0 is in Mode 3. Timer 1 may still be used in limited ways, such as for serial baud-rate generation or non-interrupt timing, but the normal Timer 1 overflow-flag relationship is no longer available in the usual way.

### Page 66: Timer 1 Behavior In Mode 3

<a href="images/Day%2014/Screenshot%202026-06-19%20004812.png"><img src="images/Day%2014/Screenshot%202026-06-19%20004812.png" alt="8051 Timer 1 behavior in Mode 3" width="960"></a>

This page clarifies the Mode 3 side effect on Timer 1. When Timer 0 is in Mode 3, Timer 1 can be switched in and out of its own Mode 3 state, but it is usually not available as a normal interrupt-generating Timer 1 because it is no longer connected to `TF1` in the standard way.

That does not make Timer 1 useless. It can still serve as a serial-port baud-rate generator or be used in program designs that do not require Timer 1 interrupts. For revision, remember Mode 3 as a tradeoff: more 8-bit timing resources from Timer 0, but less normal Timer 1 functionality.

### Page 67: Timer Mode Block Diagrams

<a href="images/Day%2014/Screenshot%202026-06-19%20005107.png"><img src="images/Day%2014/Screenshot%202026-06-19%20005107.png" alt="8051 timer mode block diagrams" width="960"></a>

The diagrams visualize the four timer modes. Mode 0 combines `THx` with five bits of `TLx`; Mode 1 cascades full `TLx` and `THx`; Mode 2 counts in `TLx` and reloads it from `THx`; Mode 3 splits Timer 0 into independent `TL0` and `TH0` 8-bit timers.

Use this page to avoid memorizing the mode table blindly. Ask which register counts, which register reloads, which overflow flag is set, and whether the count is 13-bit, 16-bit, 8-bit auto-reload, or split. That sequence answers most timer-mode questions.

### Page 68: TCON Register Bit Layout

<a href="images/Day%2014/Screenshot%202026-06-19%20005207.png"><img src="images/Day%2014/Screenshot%202026-06-19%20005207.png" alt="8051 TCON register bit layout" width="960"></a>

`TCON` contains both timer-control bits and external-interrupt control/status bits. From bit 7 to bit 0, the layout shown is `TF1 TR1 TF0 TR0 IE1 IT1 IE0 IT0`. The upper half is timer-focused, and the lower half is external-interrupt-focused.

`TR1` and `TR0` start and stop the timers. `TF1` and `TF0` are set by overflow. `IE1` and `IE0` are external-interrupt event flags. `IT1` and `IT0` select whether the external interrupt is edge-triggered or level-triggered.

### Page 69: TCON External Interrupt Bits

<a href="images/Day%2014/Screenshot%202026-06-19%20005656.png"><img src="images/Day%2014/Screenshot%202026-06-19%20005656.png" alt="8051 TCON external interrupt bits" width="960"></a>

This page focuses on the lower `TCON` bits. `IE1` is the external interrupt 1 edge flag and `IE0` is the external interrupt 0 edge flag. They are set when the selected external interrupt event is recognized and are cleared when the processor vectors to the corresponding interrupt service routine.

`IT1` and `IT0` select trigger style. When the bit is set, the corresponding external interrupt is edge-triggered. When it is cleared, the interrupt is level-triggered. These bits are not timer-operation bits, even though they live in the same `TCON` register as the timer flags.

### Page 70: TCON Timer Run And Overflow Bits

<a href="images/Day%2014/Screenshot%202026-06-19%20005704.png"><img src="images/Day%2014/Screenshot%202026-06-19%20005704.png" alt="8051 TCON timer run and overflow bits" width="960"></a>

This page focuses on the upper `TCON` timer bits. `TF1` is set when Timer 1 overflows, and `TF0` is set when Timer 0 overflows. These flags indicate that a timer rolled over from its maximum value back to zero.

`TR1` and `TR0` are run-control bits. Setting `TRx` enables counting for that timer; clearing it halts the timer. Clearing `TRx` does not clear the count register, so stopping a timer and resetting a timer are separate operations.

### Page 71: Mode 1 Operation Checklist

<a href="images/Day%2014/Screenshot%202026-06-19%20010208.png"><img src="images/Day%2014/Screenshot%202026-06-19%20010208.png" alt="8051 Mode 1 operation checklist" width="960"></a>

This page gives the operational sequence for Mode 1. First load the 16-bit initial value into `THx` and `TLx`. Then start the timer by setting `TR0` for Timer 0 or `TR1` for Timer 1. After that, the combined register pair counts upward.

The timer continues until it reaches `FFFFH`. When the next count rolls over to `0000H`, the corresponding timer flag `TFx` is set. That flag is the software-visible sign that the programmed interval has elapsed or that a counter overflow event has occurred.

### Page 72: Timer Overflow Flag And Stop Option

<a href="images/Day%2014/Screenshot%202026-06-19%20010522.png"><img src="images/Day%2014/Screenshot%202026-06-19%20010522.png" alt="8051 timer overflow flag and software stop option" width="960"></a>

This page connects timer overflow to software control. Timer 0 has flag `TF0`; Timer 1 has flag `TF1`. When the selected timer rolls over, the matching flag becomes `1`, and the program can detect that state by polling the flag or by using the timer interrupt path if interrupts are enabled.

The important practical point is that detecting overflow and stopping the timer are separate actions. `TFx` tells the program that overflow happened; `TRx` controls whether counting is allowed to continue. For a polling delay, a common pattern is to wait until `TFx` is set, then execute `CLR TR0` or `CLR TR1` to stop the timer before clearing the flag and preparing the next delay.

### Page 73: Timer Flag Block Diagram

<a href="images/Day%2014/Screenshot%202026-06-19%20010642.png"><img src="images/Day%2014/Screenshot%202026-06-19%20010642.png" alt="8051 timer clock path and overflow flag diagram" width="960"></a>

This cleaner diagram shows the same mechanism as a data path. With `C/T = 0`, the timer uses the internal clock path. On the classic 12-clock 8051 timing model, the oscillator is divided by 12 to create the machine-cycle timing source that increments the timer when the timer is enabled.

The gate in the diagram represents the enable condition. If software control is selected, `TRx` must be set for the timer to count. The timer register pair then increments until it rolls from its maximum value back to zero. That rollover sets `TFx`, which is the flag software checks to know that the programmed count interval has expired.

### Page 74: Reload And Clear After Overflow

<a href="images/Day%2014/Screenshot%202026-06-19%20010652.png"><img src="images/Day%2014/Screenshot%202026-06-19%20010652.png" alt="8051 timer reload and flag clear after rollover" width="960"></a>

This page explains what must happen if the same delay is needed again. In Mode 0 or Mode 1, the timer does not automatically restore the original starting value after overflow. Once the count rolls over, the software must reload `THx` and `TLx` with the initial count for the next interval.

The timer flag must also be cleared. If `TFx` is left as `1`, the next polling loop may immediately think the timer has already overflowed. For repeated delays, the safe sequence is therefore: stop if needed, clear `TFx`, reload the timer registers, start the timer again, and poll for the next overflow.

### Page 75: Time Delay Generation Procedure

<a href="images/Day%2014/Screenshot%202026-06-19%20010810.png"><img src="images/Day%2014/Screenshot%202026-06-19%20010810.png" alt="8051 time delay generation procedure" width="960"></a>

This page turns the timer theory into the standard programming algorithm for generating a delay. First load `TMOD` so the correct timer and mode are selected. Then load `TLx` and `THx` with the initial count value calculated for the desired delay.

After loading the count, start the timer by setting the correct run-control bit. The program then monitors the timer flag with an instruction such as `JNB TFx,target`, meaning it keeps looping while the flag is not set. Once `TFx` becomes high, the delay interval has elapsed, so the program exits the loop, stops the timer, clears `TFx`, and reloads `THx/TLx` if the delay must repeat.

### Page 76: Time Delay Generation Clean View

<a href="images/Day%2014/Screenshot%202026-06-19%20010922.png"><img src="images/Day%2014/Screenshot%202026-06-19%20010922.png" alt="8051 time delay generation clean full-frame view" width="960"></a>

This full-frame version reinforces the exact order of operations. `TMOD` is configured once for the selected timer and mode, but the count registers usually need to be loaded again for every non-auto-reload delay cycle. That is why the final step returns to the loading step instead of directly returning only to the polling loop.

The `JNB TFx,target` step is also important for assembly programs because it shows a polling approach rather than an interrupt-driven approach. The CPU repeatedly checks the flag until hardware sets it. This is simple and common in introductory delay routines, but it occupies the CPU during the wait; interrupt-based timing is preferred when the processor must perform other work during the interval.

### Page 77: Mode 2 Characteristics

<a href="images/Day%2014/Screenshot%202026-06-19%20011213.png"><img src="images/Day%2014/Screenshot%202026-06-19%20011213.png" alt="8051 Mode 2 timer characteristics and operation" width="960"></a>

This page introduces Mode 2 as the 8-bit auto-reload mode. Because the active counter is 8 bits wide, the count range is `00H` through `FFH`. The reload value is stored in `THx`, and that value is copied into `TLx` before the timer begins counting.

After the timer is started with `SETB TR0` or `SETB TR1`, the `TLx` register increments upward. When `TLx` reaches `FFH` and then rolls over to `00H`, the timer flag `TFx` is set. This is different from Mode 1 because the counted width is only 8 bits, so the overflow point is the `TLx` rollover rather than a full `THx:TLx` 16-bit rollover.

### Page 78: Mode 2 Auto-Reload Diagram

<a href="images/Day%2014/Screenshot%202026-06-19%20011239.png"><img src="images/Day%2014/Screenshot%202026-06-19%20011239.png" alt="8051 Mode 2 auto-reload timer diagram" width="960"></a>

This diagram shows why Mode 2 is called auto-reload. `THx` stores the original reload byte, while `TLx` is the byte that actually counts. When `TLx` overflows, hardware copies the value from `THx` back into `TLx` automatically.

For repeated Mode 2 delays, the programmer usually does not reload the original value every cycle. The program still must clear `TFx`, because the overflow flag remains set until software or the interrupt mechanism clears it. The contrast with Mode 1 is the key exam point: Mode 1 normally needs software to reload `THx/TLx`; Mode 2 reloads `TLx` from `THx` automatically.

### Page 79: Mode 2 Delay Procedure

<a href="images/Day%2014/Screenshot%202026-06-19%20011310.png"><img src="images/Day%2014/Screenshot%202026-06-19%20011310.png" alt="8051 Mode 2 time delay generation procedure" width="960"></a>

This page gives the polling delay algorithm specifically for Mode 2. First configure `TMOD` for the selected timer and Mode 2. Then load `THx` with the initial count value, because `THx` is the reload source. After the timer is started, hardware handles the `THx` to `TLx` reload each time `TLx` overflows.

The loop then watches `TFx` using an instruction such as `JNB TFx,target`. When `TFx` goes high, the interval has completed. For another interval, software clears `TFx` and can return to the polling step, because the active counter has already been reloaded from `THx`. This is the main efficiency advantage of Mode 2 in delay and baud-rate style use cases.

## Screenshot Deepening

Day 14 is easiest if the screenshots are grouped into six layers:

| Layer | Screenshots | What to remember |
| --- | --- | --- |
| System boundary | 1-2 | Microcontroller reduces external hardware by integrating memory and peripherals with the CPU. |
| 8051 resources | 3-4 | 4 KB internal program memory, 128 bytes internal RAM, four ports, timers, serial port, interrupts, PSW, SP, PC, and DPTR. |
| CPU state and memory map | 5-9 | PSW selects register banks, SP lives in internal RAM addressing, PC controls program-memory flow. |
| Port and serial contact points | 10-12 | Ports expose parallel I/O and alternate functions; `SBUF` exposes serial transmit/receive data. |
| Timer/control plane | 13, 54-79 | `TH0/TL0`, `TH1/TL1`, `TMOD`, `TCON`, `IE`, `IP`, `SCON`, and `PCON` expose timer, interrupt, serial, and power-control behavior. |
| External control and start-up signals | 15-20, 28-29, 51-52 | `PSEN`, `ALE`, `EA`, `RST`, oscillator inputs, Port 0, and Port 2 control external code fetches, bus demultiplexing, code-source selection, reset, timing, and memory expansion. |
| Internal memory organization | 21-27, 30-34 | Code and data spaces are separate; internal RAM is split into register banks, bit-addressable RAM, and general RAM, with SFRs in direct address space. |
| Physical pin map | 35-38, 53 | The 40-pin package maps ports, power, oscillator, reset, serial pins, interrupts, timers, and external-memory control signals to actual IC pins. |
| Addressing modes | 39, 41-50 | Immediate, register, direct, register-indirect, and indexed modes describe how instructions locate operands. |
| Pin control details | 40 | `PSEN`, `ALE`, `EA`, Port 0, and `VCC` complete the external-memory/pin-description picture. |

The most important change from 8085/8086 study is the address-space model. In 8086, the segment-offset mechanism produces physical memory addresses inside a single 1 MB memory space. In 8051, program memory, internal data memory, external data memory, and SFRs are distinct spaces with different access instructions and addressing rules.

### 8051 Memory And Register Map

| Area | Address/range | Access idea |
| --- | --- | --- |
| Program memory | Up to `0000H-FFFFH` | Holds code and constants; PC points here. |
| Original on-chip program memory | `0000H-0FFFH` on original 8051 | 4 KB internal ROM/EPROM area. |
| Lower internal RAM | `00H-7FH` | 128 bytes, direct and indirect access. |
| Register banks | `00H-1FH` | Four banks of `R0-R7`, chosen by `RS1 RS0`. |
| Bit-addressable RAM | `20H-2FH` | 128 directly addressable bits. |
| General RAM | `30H-7FH` | Ordinary internal data storage and stack area if selected. |
| SFR space | `80H-FFH` direct addresses | Ports, timers, serial, interrupt, PSW, ACC, B, SP, DPTR pieces. |
| External data memory | Up to 64 KB | Accessed through external-memory instructions and bus cycles. |

### External Signal Summary

| Signal/input | Direction | Main role |
| --- | --- | --- |
| `ALE` | Output | Pulses when the low address byte on multiplexed Port 0 should be latched externally. |
| `PSEN` | Output, active low | Enables external program memory during code fetches. |
| `EA` | Input | Selects whether internal program memory can be used, or whether code fetches are external only. |
| `RST` | Input, active high | Forces reset when held high long enough, loading defined start-up values. |
| `XTAL1/XTAL2` | Oscillator pins | Connect to the clock/crystal path that drives the classic 8051 timing base. |
| `P0` during external memory | Bidirectional bus | Carries multiplexed low address and data as `AD0-AD7`. |
| `P2` during external memory | Output/address bus | Usually carries the high address byte `A8-A15`. |

### Timer And Control SFR Summary

| SFR/register | Main role |
| --- | --- |
| `TH0`, `TL0` | High and low bytes of Timer/Counter 0. |
| `TH1`, `TL1` | High and low bytes of Timer/Counter 1. |
| `TMOD` | Selects timer/counter mode and timer versus counter operation. |
| `TCON` | Timer run/overflow bits plus external interrupt control/status bits. |
| `IE` | Enables or disables interrupt sources. |
| `IP` | Sets interrupt priority level. |
| `SCON` | Controls serial mode and serial interrupt flags. |
| `PCON` | Power-control bits and serial baud-rate related control on classic 8051 variants. |

### PSW Quick Table

| Bit | Symbol | Meaning |
| --- | --- | --- |
| `PSW.7` | `CY` | Carry flag and Boolean accumulator for bit operations. |
| `PSW.6` | `AC` | Auxiliary carry, mainly important for BCD adjustment. |
| `PSW.5` | `F0` | General-purpose user flag. |
| `PSW.4` | `RS1` | Register-bank select bit 1. |
| `PSW.3` | `RS0` | Register-bank select bit 0. |
| `PSW.2` | `OV` | Overflow flag for signed arithmetic interpretation. |
| `PSW.1` | `-` | Reserved or derivative-dependent; do not rely on it unless the exact chip manual allows it. |
| `PSW.0` | `P` | Accumulator parity, updated by hardware. |

### PC And SP Contrast

| Register | Width | Reset/default behavior | How software changes it |
| --- | --- | --- | --- |
| `PC` | 16 bits | Starts at `0000H` after reset. | Indirectly through normal sequencing, jumps, calls, returns, interrupts, and reset. |
| `SP` | 8 bits | Starts at `07H` after reset. | Directly writable as SFR `81H`; also changes automatically during stack operations. |

This contrast is exam-heavy. `SP` is a programmer-visible SFR and can be moved. `PC` is not directly assigned as a normal SFR. To send execution to `2430H`, use a branch such as `LJMP 2430H`; do not imagine a direct `PC = 2430H` data-transfer instruction.

### Port And SBUF Mental Model

| Item | Looks like | Actually controls |
| --- | --- | --- |
| `P0-P3` | Four 8-bit SFRs | Output latches, input paths, alternate pin functions, and in some cases external-memory bus roles. |
| `SBUF` write | One SFR write | Loads the serial transmit buffer and starts transmission. |
| `SBUF` read | One SFR read | Reads the serial receive buffer. |
| `SCON` | Serial control SFR | Mode, receive enable, transmit interrupt flag, receive interrupt flag, and related serial control bits. |
| `PSEN` | Active-low output signal | Enables external program memory during code fetches. |

## Points To Remember

- A microprocessor alone is CPU-centered; a microcontroller is system-centered for embedded control.
- Original 8051 has 4 KB on-chip program memory and 128 bytes on-chip data RAM.
- 8051 can address up to 64 KB program memory and up to 64 KB external data memory.
- 32 I/O lines are arranged as four 8-bit ports: `P0`, `P1`, `P2`, and `P3`.
- `PSW = CY AC F0 RS1 RS0 OV - P`.
- `RS1 RS0 = 00, 01, 10, 11` select Banks 0, 1, 2, and 3.
- Bank 3 is `18H-1FH`; the screenshot's `18H-17H` is a slide typo.
- The 8051 stack pointer is 8 bits wide.
- After reset, `SP = 07H`, so the first default stack byte is stored at `08H`.
- `PUSH` and `CALL` pre-increment the stack pointer before storing.
- The 8051 program counter is 16 bits and starts at `0000H` after reset.
- PC advances by instruction length, not always by one.
- PC is changed through jumps, calls, returns, interrupts, reset, and normal sequencing, not by a direct data move into `PC`.
- `P0-P3` are SFR port latches; latch behavior and physical pin behavior must be distinguished.
- Writing `1` to a port bit sets/releases it high; writing `0` drives it low.
- `SBUF` is one software-visible address but two internal buffers.
- Writing `SBUF` loads the transmit buffer; reading `SBUF` reads the receive buffer.
- `SCON` controls serial mode and status; `SBUF` carries the data byte.
- `TH0/TL0` and `TH1/TL1` are the byte pairs for Timer/Counters 0 and 1.
- `TMOD` chooses timer modes; `TCON` holds timer and external-interrupt status/control bits.
- `IE` enables interrupts; `IP` controls interrupt priority.
- T0/T1 can count internal clock pulses as timers or external pulses as counters.
- Timer/counters are commonly used for interval timing, event counting, and serial baud-rate generation.
- Timer overflow flags can be polled or used with interrupts to schedule periodic program actions.
- Counter mode counts external event transitions on timer input pins.
- `TMOD` is not bit-addressable and is usually initialized as a full byte.
- `TMOD` mode bits `M1 M0` select modes 0, 1, 2, and 3.
- `C/T=0` selects timer mode from the internal clock; `C/T=1` selects counter mode from the external `T0` or `T1` input.
- With `GATE=0`, software starts and stops the timers by setting or clearing `TR0` and `TR1`.
- `SETB TRx` starts the selected timer/counter and `CLR TRx` stops it when software control is being used.
- Mode 0 is 13-bit timer/counter mode and mainly remains for compatibility.
- Mode 1 is 16-bit timer/counter mode using `THx:TLx`.
- Mode 2 is 8-bit auto-reload mode, where `TLx` counts and `THx` stores the reload value.
- Mode 3 splits Timer 0 into two 8-bit timers and restricts normal Timer 1 interrupt use.
- `TCON = TF1 TR1 TF0 TR0 IE1 IT1 IE0 IT0`.
- `TF1/TF0` are timer overflow flags; `TR1/TR0` are run-control bits.
- `IE1/IE0` are external-interrupt flags; `IT1/IT0` select edge-triggered or level-triggered external interrupt behavior.
- Polling `TFx` lets software detect that a timer interval has completed.
- `CLR TR0` or `CLR TR1` stops the selected timer, but it does not reload the timer registers or clear the overflow flag.
- After a Mode 0 or Mode 1 overflow, repeat delays require clearing `TFx` and reloading `THx/TLx` with the original count.
- A polling delay routine usually configures `TMOD`, loads `THx/TLx`, starts `TRx`, waits with `JNB TFx,target`, stops the timer, clears `TFx`, and reloads for the next round.
- In Mode 2, `THx` stores the 8-bit reload value and `TLx` is the active 8-bit counter.
- When `TLx` overflows in Mode 2, hardware reloads `TLx` from `THx`; software still clears `TFx`.
- A Mode 2 delay loop can clear `TFx` and return to polling without reloading `THx` every cycle.
- `PSEN` is active low and is used for external program-memory fetches, not ordinary external data-memory reads.
- `ALE` is a latch-timing signal; Port 0 carries the multiplexed low address/data value.
- When Port 0 is used as `AD0-AD7`, `ALE` lets external hardware hold `A0-A7` before Port 0 becomes the data bus.
- `EA` high permits internal program memory use for the lower internal code range on classic 8051 parts.
- `EA` low forces program fetches from external program memory only.
- `RST` is active high and must be held high for at least two machine cycles for reset recognition.
- Reset gives the CPU and SFRs defined start-up values; it is not only a jump to the first instruction.
- Classic MCS-51 examples often use a 12 MHz crystal, giving a 1 microsecond machine cycle on the 12-clock core.
- 8051 code memory and data memory are separate spaces.
- The architecture can address up to 64 KB code memory and up to 64 KB external data memory.
- External RAM, ROM, and peripheral interface ICs can be added through the external memory interface.
- Memory-mapped external peripherals become part of the external data-memory address space.
- During external memory use, Port 0 is consumed as multiplexed low address/data bus `AD0-AD7`.
- Port 2 is usually used for the high address byte during full external memory accesses.
- A 64 KB byte-addressed memory space needs 16 address lines, `A0-A15`.
- `AD0-AD7` means the pins carry low address first and data later in the same bus cycle.
- 8051 is commonly shown as a 40-pin IC with `GND` on pin 20 and `VCC` on pin 40.
- Port 0 is pins `32-39`, Port 1 is pins `1-8`, Port 2 is pins `21-28`, and Port 3 is pins `10-17`.
- Port 3 alternate functions include serial I/O, external interrupts, timer inputs, and `/RD`/`/WR`.
- `P3.0` is `RXD`; `P3.1` is `TXD`.
- `P3.2/P3.3` are external interrupts; `P3.4/P3.5` are timer/counter inputs.
- `P3.6/P3.7` are `/WR` and `/RD` for external data memory.
- Reset initializes CPU/control state but does not erase program ROM.
- Pin 29 `PSEN` is for external program-memory fetches, not external data-memory writes.
- Pin 30 `ALE` latches the low address byte from Port 0.
- Pin 31 `EA` controls whether internal code memory can be used.
- Pin 40 `VCC` is the positive supply pin.
- During external memory cycles, ALE high marks the low-address phase on Port 0; after that Port 0 carries data.
- During external memory cycles, Port 2 supplies `A8-A15` and is not ordinary I/O for that bus use.
- 8051 addressing modes include immediate, register, direct, register-indirect, and indexed.
- Immediate addressing uses `#data`; register addressing uses active-bank `R0-R7`; direct addressing gives an internal/SFR address.
- Register-indirect addressing uses `@R0` or `@R1`; indexed code lookup commonly uses `@A+DPTR` or `@A+PC`.
- Unsuffixed numeric constants may be treated as decimal by the assembler convention used in the slide; `H` marks hexadecimal.
- `DPTR` accepts a 16-bit immediate value up to `FFFFH`.
- `MOV DPTR,A` is invalid; load `DPTR` with a 16-bit immediate or by setting `DPL` and `DPH`.
- `MOV DPTR,#4521H` means `DPH = 45H` and `DPL = 21H`.
- Arbitrary `MOV Rn,Rm` register-to-register copies are not the general 8051 form; use `A` as a temporary when needed.
- Direct addressing names a fixed internal RAM or SFR byte address.
- `MOV A,04H` equals `MOV A,R4` only when Bank 0 is selected.
- `ACC`/`A` has SFR direct address `E0H`; `B` has SFR direct address `F0H`.
- SFR space is `80H-FFH`, but unused addresses in that range are reserved.
- Register-indirect addressing uses `@R0` or `@R1`; the register contents are treated as the target address.
- `MOVC A,@A+DPTR` and `MOVC A,@A+PC` read lookup-table bytes from code memory.
- Internal data RAM `00H-1FH` is register-bank space: four banks times eight registers.
- After reset, Bank 0 is selected, so `R0-R7` refer to addresses `00H-07H`.
- `R0-R7` instructions are normally shorter and faster than equivalent direct-address instructions.
- Internal data RAM `20H-2FH` is 16 bytes, giving 128 bit-addressable RAM bits.
- Internal data RAM `30H-7FH` is general-purpose RAM and is a common safer stack area.
- SFR space is direct-addressed at `80H-FFH`, but not every address in that range is implemented RAM.
- Classic 8051 commonly lists 21 SFRs, including ports, timer registers, serial registers, interrupt registers, `PSW`, `ACC`, `B`, `SP`, and `DPTR` bytes.
- SFRs are hardware control/status registers, not ordinary general-purpose RAM locations.
- Bit-addressable SFR bits are useful for direct control of ports, flags, interrupts, timers, serial state, accumulator bits, and the `B` register.
- Direct bit instructions reduce the need for manual read-mask-write sequences when controlling single-bit inputs, outputs, and flags.
- `SETB bit` sets a bit-addressable location to `1`; `CLR bit` clears it to `0`.

## Sources

[S1] IBM, [Microcontrollers vs. Microprocessors: What's the Difference?](https://www.ibm.com/think/topics/microcontroller-vs-microprocessor). Used for the microcontroller versus microprocessor system-boundary distinction.

[S2] Atmel/Microchip, [Atmel 8051 Microcontrollers Hardware Manual](https://ww1.microchip.com/downloads/en/DeviceDoc/doc4316.pdf). Used for PSW layout, register-bank selection, stack pointer reset behavior, SFR map, ports, timers, and `SBUF` transmit/receive behavior.

[S3] Philips Semiconductors/NXP, [80C51 Family Hardware Description](https://www.pjrc.com/tech/8051/80C51_FAM_HARDWARE_1.pdf). Used for SFR descriptions, port latch behavior, stack pointer behavior, and serial buffer behavior.

[S4] Philips Semiconductors/NXP, [80C51 Family Architecture](https://www.pjrc.com/tech/8051/80C51_FAM_ARCH_1.pdf). Used for internal data-memory organization, lower 128 bytes, register banks, bit-addressable area, SFR space, and external data-memory addressability.

[S5] Intel Corporation, [MCS-51 Microcontroller Family User's Manual, February 1994](https://bitsavers.trailing-edge.com/components/intel/8051/MCS-51_Users_Manual_Feb94.pdf). Used as the Intel-family reference for the 8051 feature set, program/data memory separation, and 64 KB address spaces.
