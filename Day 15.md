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
9. For common Mode 1 serial programs, `TMOD=20H`, a suitable `TH1` reload, `SCON=50H`, and `TR1=1` are the core setup chain.
```

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

## Sources

[S1] Intel, [MCS-51 Microcontroller Family User's Manual, February 1994](https://bitsavers.trailing-edge.com/components/intel/8051/MCS-51_Users_Manual_Feb94.pdf). Used for 8051 serial-port architecture, `SBUF`, `SCON`, serial pins, and baud-rate/timer relationship.

[S2] Intel, [MCS-51 Instruction Set](https://www.keil.com/dd/docs/datashts/intel/ism51.pdf). Used for SFR/direct-address and instruction-level behavior around serial-register access.
