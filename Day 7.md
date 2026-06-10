# Day 7: Interrupts, Vectoring, Priority, and Bus Arbitration

Day 7 covers the May 31 interrupt screenshots from the early session. The topic moves from the basic interrupt service flow into 8085 interrupt pins, masking, vector addresses, triggering type, and priority.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [Interrupt service routine flow](images/Day%207/day-7-interrupt-service-routine-flow.png) | Main program checks and services interrupt requests through an ISR. |
| 2 | [Disable interrupt DI](images/Day%207/day-7-disable-interrupt-di.png) | `DI` disables maskable interrupts by resetting interrupt enable. |
| 3 | [TRAP non-maskable interrupt](images/Day%207/day-7-trap-non-maskable-interrupt.png) | `TRAP` is non-maskable, vectored, and highest priority. |
| 4 | [RST 7.5 interrupt](images/Day%207/day-7-rst-75-interrupt.png) | `RST 7.5` is maskable, vectored, and edge sensitive. |
| 5 | [INTR acknowledge sequence](images/Day%207/day-7-intr-acknowledge-sequence.png) | INTR uses interrupt acknowledge and external opcode placement. |
| 6 | [Interrupt vector table](images/Day%207/day-7-interrupt-vector-table.png) | Interrupt vectors point to ISR locations in memory. |
| 7 | [Interrupt priority vector table](images/Day%207/day-7-interrupt-priority-vector-table.png) | Compare maskable, vectored, priority, ISR address, and triggering method. |
| 8 | [Maskable vectored interrupt process](images/Day%207/day-7-maskable-vectored-interrupt-process.png) | 8085 checks interrupts and branches to vector locations. |
| 9 | [Interrupt mask flip-flop diagram](images/Day%207/day-7-interrupt-mask-flip-flop-diagram.png) | Mask bits and edge storage for `RST 7.5`, `RST 6.5`, and `RST 5.5`. |
| 10 | [Triggering levels: edge and level](images/Day%207/day-7-triggering-levels-edge-level.png) | `RST 7.5` is edge triggered; `RST 6.5` and `RST 5.5` are level sensitive. |
| 11 | [Bus arbitration daisy chaining question](images/Day%207/day-7-bus-arbitration-daisy-chaining-question.png) | Daisy chaining assigns priority by device position on the bus-grant line. |
| 12 | [Non-maskable interrupt question](images/Day%207/day-7-non-maskable-interrupt-question.png) | Non-maskable interrupts are used for unrecoverable events. |
| 13 | [Interrupt handler question](images/Day%207/day-7-interrupt-handler-question.png) | The interrupt handler performs service work and returns to the interrupted program. |
| 14 | [Non-maskable interrupt answer](images/Day%207/day-7-non-maskable-interrupt-answer.png) | Reinforces non-maskable interrupt use cases. |

## Key Study Notes

8085 interrupt priority is:

```text
TRAP > RST 7.5 > RST 6.5 > RST 5.5 > INTR
```

The main split is between vectored and non-vectored interrupts:

| Interrupt | Maskable | Vectored | Triggering |
| --- | --- | --- | --- |
| `TRAP` | No | Yes | Edge and level |
| `RST 7.5` | Yes | Yes | Positive edge |
| `RST 6.5` | Yes | Yes | Level |
| `RST 5.5` | Yes | Yes | Level |
| `INTR` | Yes | No | Level |

`DI` disables maskable interrupts. `EI` enables them again. `TRAP` is not affected by `DI`.

`INTR` is special because it is not vectored. External hardware must place an instruction, often a `RST` or `CALL`, on the data bus during interrupt acknowledge.

## Points To Remember

- `TRAP` has the highest priority and cannot be masked.
- `RST 7.5` is edge sensitive and can remember a pending edge.
- `RST 6.5` and `RST 5.5` must remain high until recognized.
- The interrupt handler/ISR must return control to the interrupted program.
- Daisy chaining gives priority according to physical order on the grant line.
