# Day 9: 8086 Pin Signals, BIU/EU, and Register Organization

Day 9 collects the relevant 8086 introduction screenshots from June 1 and the continuation screenshots from June 10. This is the point where the notes move from 8085 support topics into 8086 architecture.

## Image Index

| No. | Image | Main idea |
| --- | --- | --- |
| 1 | [8086 pin configuration](images/Day%209/day-9-8086-pin-configuration.png) | 8086 pinout with address/data/status/control signals. |
| 2 | [8086 min/max mode signals](images/Day%209/day-9-8086-min-max-mode-signals.png) | Compare minimum-mode and maximum-mode signal meanings. |
| 3 | [8086 internal architecture](images/Day%209/day-9-8086-internal-architecture.png) | 8086 is divided into BIU and EU. |
| 4 | [8086 block diagram](images/Day%209/day-9-8086-block-diagram.png) | Block diagram showing registers, bus interface, ALU, and control logic. |
| 5 | [8086 bus interface unit](images/Day%209/day-9-8086-bus-interface-unit.png) | BIU handles address/data transfer, queue fetch, memory, and I/O access. |
| 6 | [8086 execution unit](images/Day%209/day-9-8086-execution-unit.png) | EU receives opcodes from the queue, decodes, and executes instructions. |
| 7 | [8086 general-purpose register uses](images/Day%209/day-9-8086-general-purpose-register-uses.png) | AX, BX, CX, and DX have general and special-purpose roles. |

## Key Study Notes

The 8086 has two major internal sections:

| Unit | Main role |
| --- | --- |
| BIU | Handles bus operations, address generation, instruction fetching, and the instruction queue. |
| EU | Decodes and executes instructions using the ALU, flags, and general-purpose registers. |

The split allows the BIU to fetch upcoming instruction bytes while the EU executes the current instruction. This is the basic idea behind the 8086 instruction queue.

General-purpose registers also have common special uses:

| Register | Common special role |
| --- | --- |
| `AX` | Accumulator. |
| `BX` | Base register for address formation. |
| `CX` | Counter, often used in loops and string operations. |
| `DX` | Data register, often used in I/O and extended arithmetic. |

Minimum mode is used for smaller single-processor systems. Maximum mode is used when the system needs more external bus-control support, such as multiprocessor or coprocessor-style configurations.

## Points To Remember

- 8086 has a 16-bit data bus and a 20-bit address bus.
- BIU fetches instructions and performs bus transfers.
- EU decodes and executes instructions.
- The instruction queue helps overlap fetch and execute work.
- `AX`, `BX`, `CX`, and `DX` are general-purpose but also have common special-purpose meanings.
