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

## Screenshot Deepening

Day 14 is easiest if the screenshots are grouped into six layers:

| Layer | Screenshots | What to remember |
| --- | --- | --- |
| System boundary | 1-2 | Microcontroller reduces external hardware by integrating memory and peripherals with the CPU. |
| 8051 resources | 3-4 | 4 KB internal program memory, 128 bytes internal RAM, four ports, timers, serial port, interrupts, PSW, SP, PC, and DPTR. |
| CPU state and memory map | 5-9 | PSW selects register banks, SP lives in internal RAM addressing, PC controls program-memory flow. |
| Port and serial contact points | 10-12 | Ports expose parallel I/O and alternate functions; `SBUF` exposes serial transmit/receive data. |
| Timer/control plane | 13 | `TH0/TL0`, `TH1/TL1`, `TMOD`, `TCON`, `IE`, `IP`, `SCON`, and `PCON` expose timer, interrupt, serial, and power-control behavior. |
| External control and start-up signals | 15-20, 28-29 | `PSEN`, `ALE`, `EA`, `RST`, oscillator inputs, Port 0, and Port 2 control external code fetches, bus demultiplexing, code-source selection, reset, timing, and memory expansion. |
| Internal memory organization | 21-27, 30-34 | Code and data spaces are separate; internal RAM is split into register banks, bit-addressable RAM, and general RAM, with SFRs in direct address space. |
| Physical pin map | 35-38 | The 40-pin package maps ports, power, oscillator, reset, serial pins, interrupts, timers, and external-memory control signals to actual IC pins. |
| Addressing modes | 39, 41-44 | Immediate, register, direct, register-indirect, and indexed modes describe how instructions locate operands. |
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
- 8051 addressing modes include immediate, register, direct, register-indirect, and indexed.
- Immediate addressing uses `#data`; register addressing uses active-bank `R0-R7`; direct addressing gives an internal/SFR address.
- Register-indirect addressing uses `@R0` or `@R1`; indexed code lookup commonly uses `@A+DPTR` or `@A+PC`.
- Unsuffixed numeric constants may be treated as decimal by the assembler convention used in the slide; `H` marks hexadecimal.
- `DPTR` accepts a 16-bit immediate value up to `FFFFH`.
- `MOV DPTR,A` is invalid; load `DPTR` with a 16-bit immediate or by setting `DPL` and `DPH`.
- `MOV DPTR,#4521H` means `DPH = 45H` and `DPL = 21H`.
- Arbitrary `MOV Rn,Rm` register-to-register copies are not the general 8051 form; use `A` as a temporary when needed.
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
