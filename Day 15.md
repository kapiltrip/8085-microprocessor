# Day 15: 8051 Serial Port Operation, SBUF, SCON, Baud Rate, and Timer 1

Day 15 continues the 8051 section from Day 14, but narrows the focus to serial communication. The screenshots cover why the 8051 serial port exists, how it converts data between CPU-parallel form and pin-serial form, how `SBUF` behaves as separate transmit/receive buffers, how the block diagram connects `TXD` and `RXD`, and why `SCON` plus baud-rate generation controls actual serial operation.

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
```

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [Serial port operation overview](images/Day%2015/Screenshot%202026-06-19%20135930.png) | The 8051 serial port converts parallel CPU data to serial output and serial input to parallel CPU data. |
| 2 | [SBUF and SCON software access](images/Day%2015/Screenshot%202026-06-19%20140057.png) | `SBUF` at `99H` is two internal buffers: write for transmit, read for receive. |
| 3 | [Serial port block diagram](images/Day%2015/Screenshot%202026-06-19%20141111.png) | Transmit uses parallel-in serial-out flow to `TXD`; receive uses serial-in parallel-out flow from `RXD`. |
| 4 | [SCON and baud-rate control](images/Day%2015/Screenshot%202026-06-19%20141140.png) | `SCON` at `98H` controls serial mode/status, and Timer 1 can supply variable baud-rate clocking. |

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

### Baud Rate Connection

| Baud-rate case | What to remember |
| --- | --- |
| Fixed baud-rate mode | Clocking is derived from the 8051 timing source in a fixed way. |
| Variable baud-rate mode | Timer 1 is commonly programmed to generate the serial bit clock. |
| Timer dependency | Wrong timer reload values produce wrong serial speed even if `SBUF` and `SCON` code looks correct. |

Baud rate is measured in bits per second, so it is not the same as character rate. A transmitted character often includes framing bits in addition to the data bits, depending on the serial mode. Therefore, when solving baud-rate examples, count the bit timing produced by the timer first, then relate that to the character format.

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
- `SCON` contains serial control bits and serial status flags.
- Serial status bits can be polled by software or used to cause serial interrupt handling.
- Baud rate means serial bit rate.
- Variable baud-rate serial operation commonly depends on Timer 1 setup.
- Timer mistakes can become serial-port mistakes because the serial bit clock may come from Timer 1.

## Sources

[S1] Intel, [MCS-51 Microcontroller Family User's Manual, February 1994](https://bitsavers.trailing-edge.com/components/intel/8051/MCS-51_Users_Manual_Feb94.pdf). Used for 8051 serial-port architecture, `SBUF`, `SCON`, serial pins, and baud-rate/timer relationship.

[S2] Intel, [MCS-51 Instruction Set](https://www.keil.com/dd/docs/datashts/intel/ism51.pdf). Used for SFR/direct-address and instruction-level behavior around serial-register access.
