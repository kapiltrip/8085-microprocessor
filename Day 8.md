# Day 8: DMA, I/O Handshaking, and Peripheral Interface Chips

Day 8 covers the May 31 afternoon screenshots. The session moves from programmed I/O handshaking into DMA data transfer and common Intel support chips used with 8085-style systems.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [I/O data transfer handshaking sequence](images/Day%208/day-8-io-data-transfer-handshaking-sequence.png) | CPU and I/O module exchange ready/accepted status signals. |
| 2 | [DMA data transfer scheme](images/Day%208/day-8-dma-data-transfer-scheme.png) | DMA transfers data directly between I/O and memory. |
| 3 | [DMA HOLD/HLDA transfer steps](images/Day%208/day-8-dma-hold-hlda-transfer-steps.png) | DMA controller takes bus control through `HOLD` and `HLDA`. |
| 4 | [Burst mode DMA data transfer](images/Day%208/day-8-burst-mode-dma-data-transfer.png) | Device transfers a block after taking control of the bus. |
| 5 | [Cycle stealing DMA data transfer](images/Day%208/day-8-cycle-stealing-dma-data-transfer.png) | DMA transfers small units while reducing CPU interference. |
| 6 | [Cycle stealing DMA efficiency note](images/Day%208/day-8-cycle-stealing-dma-efficiency-note.png) | Cycle stealing can use bus cycles when CPU is not using the bus. |
| 7 | [I/O mapped port number question](images/Day%208/day-8-io-mapped-port-number-question.png) | I/O mapped devices are identified by 8-bit port numbers. |
| 8 | [Intel 8155 programmable peripheral interface](images/Day%208/day-8-intel-8155-programmable-peripheral-interface.png) | 8155 provides RAM, I/O ports, and a timer. |
| 9 | [Intel 8255 programmable peripheral interface](images/Day%208/day-8-intel-8255-programmable-peripheral-interface.png) | 8255 gives programmable parallel I/O ports. |
| 10 | [Intel 8253 programmable interval timer](images/Day%208/day-8-intel-8253-programmable-interval-timer.png) | 8253 has counters, control word register, and read/write logic. |
| 11 | [Intel 8253 operating modes](images/Day%208/day-8-intel-8253-operating-modes.png) | Timer modes 0 through 5. |
| 12 | [Intel 8257 DMA controller](images/Day%208/day-8-intel-8257-dma-controller.png) | 8257 is a 4-channel programmable DMA controller. |
| 13 | [Intel 8259 programmable interrupt controller](images/Day%208/day-8-intel-8259-programmable-interrupt-controller.png) | 8259 manages multiple interrupt request inputs. |
| 14 | [Intel 8259 PIC features](images/Day%208/day-8-intel-8259-pic-features.png) | 8259 is compatible with 8085/8086/8088 systems. |
| 15 | [Intel 8272 floppy disk controller](images/Day%208/day-8-intel-8272-floppy-disk-controller.png) | 8272 interfaces floppy disk systems to the microprocessor. |
| 16 | [Intel 8275 and 8279 display interfaces](images/Day%208/day-8-intel-8275-8279-display-interfaces.png) | 8275 handles CRT display; 8279 handles keyboard/display interface. |

## Key Study Notes

Programmed I/O keeps the CPU involved in the transfer. DMA reduces CPU involvement by allowing a controller to manage data movement directly between an I/O device and memory.

The basic DMA bus-control handshake is:

```text
DMA controller requests bus with HOLD
8085 completes current bus activity
8085 acknowledges with HLDA
DMA controller transfers data
DMA controller releases HOLD
8085 resumes bus control
```

DMA modes:

| Mode | Idea |
| --- | --- |
| Burst mode | Transfer a block while the DMA controller holds the bus. |
| Cycle stealing | Transfer one byte or a few bytes at a time between CPU bus cycles. |

Peripheral chips in this section:

| Chip | Purpose |
| --- | --- |
| 8155 | RAM, I/O ports, and timer. |
| 8255 | Programmable parallel I/O. |
| 8253 | Programmable interval timer. |
| 8257 | DMA controller. |
| 8259 | Programmable interrupt controller. |
| 8272 | Floppy disk controller. |
| 8275 | CRT controller. |
| 8279 | Keyboard/display interface. |

## Points To Remember

- In I/O mapped I/O on 8085, ports are addressed with 8-bit port numbers.
- DMA lets data bypass normal CPU-controlled transfer loops.
- `HOLD` and `HLDA` are the key 8085 bus request/acknowledge signals for DMA.
- 8255 is for programmable parallel I/O; 8257 is for DMA; 8259 is for interrupts.
