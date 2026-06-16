# 8085 Microprocessor Learning Notes

This repository is organized as a study-day learning log. Each completed day has one Markdown notes file and a matching image folder under `images/`. The handwritten PDFs have also been rendered into page images and mapped in [Handwritten Notes Index.md](Handwritten%20Notes%20Index.md).

## Day Index

| Day | Date from screenshots | Status | Notes | Image folder | Main coverage |
| --- | --- | --- | --- | --- | --- |
| Day 1 | 2026-05-13 | Complete | [Day 1.md](Day%201.md) | [images/Day 1](images/Day%201/) | Microprocessor basics, 8085 pins/signals, buses, `ALE`, machine cycles, serial I/O. |
| Day 2 | 2026-05-26 | Complete | [Day 2.md](Day%202.md) | [images/Day 2](images/Day%202/) | Machine cycles, timing diagrams, address decoding, memory/I/O mapping, instruction formats. |
| Day 3 | 2026-05-27 | Complete | [Day 3.md](Day%203.md) | [images/Day 3](images/Day%203/) | `MVI M`, `LHLD`, `XCHG`, register-pair tracing, `ORI`, `XRA A`. |
| Day 4 | 2026-05-28 | Complete | [Day 4.md](Day%204.md) | [images/Day 4](images/Day%204/) | Conditional jumps, `CALL`, `RST`, stack pointer, `PSW`, `SIM/RIM`, signed input test. |
| Day 5 | 2026-05-29 | Complete | [Day 5.md](Day%205.md) | [images/Day 5](images/Day%205/) | `SUB/SBB`, `CMP`, memory placement of instruction bytes, `ORA`, `RRC`, `RAL`. |
| Day 6 | 2026-05-30 | Complete | [Day 6.md](Day%206.md) | [images/Day 6](images/Day%206/) | Assignment questions on `HLT`, rotates, T-states, delay loops, stack effects, flags, and instruction timing. |
| Day 7 | 2026-05-31 | Complete | [Day 7.md](Day%207.md) | [images/Day 7](images/Day%207/) | Interrupt service flow, `DI/EI`, `TRAP`, `RST 7.5/6.5/5.5`, `INTR`, vector table, priority, and daisy chaining. |
| Day 8 | 2026-05-31 | Complete | [Day 8.md](Day%208.md) | [images/Day 8](images/Day%208/) | I/O handshaking, DMA transfer modes, `HOLD/HLDA`, and Intel 8155/8255/8253/8257/8259/8272/8275/8279 support chips. |
| Day 9 | 2026-06-01, 2026-06-10 | Complete | [Day 9.md](Day%209.md) | [images/Day 9](images/Day%209/) | 8086 pin configuration, min/max mode signals, BIU, EU, block diagram, and general-purpose register roles. |
| Day 10 | 2026-06-10 | Complete | [Day 10.md](Day%2010.md) | [images/Day 10](images/Day%2010/) | 8086 flags, addressing modes, hardware/software interrupts, interrupt vector table, data transfer, arithmetic, logical, branch, loop, flag, string, and repeat instructions. |
| Day 11 | 2026-06-12 | Complete | [Day 11.md](Day%2011.md) | [images/Day 11](images/Day%2011/) | 8086 `XCHG`, `LEA`, `LES`, and `PUSH` instructions, including effective addresses, far pointers, and stack word rules. |
| Day 12 | 2026-06-15, 2026-06-16 | Complete | [Day 12.md](Day%2012.md) | [images/Day 12](images/Day%2012/) | 8086 instruction-set deep dive: `MOV`, `XCHG`, `LEA`, `IN`, `ADD/ADC`, `SUB/SBB`, `MUL/IMUL`, `DIV/IDIV`, `INC/DEC`, BCD/ASCII adjust, `OR`, and `CMP`. |

## Handwritten Notes

The folder [images/HandWrittenNotes](images/HandWrittenNotes/) contains rendered page images from the handwritten PDFs in [HandWrittenNotes](HandWrittenNotes/). Every page is mapped to a day and topic in [Handwritten Notes Index.md](Handwritten%20Notes%20Index.md), where the pages are also shown larger with explanations after each image. The relevant handwritten pages are embedded directly inside the day files.

## Image Cleanup

Unrelated screenshots should be moved to `C:\Users\kapil\OneDrive\Desktop\To Review` for manual review instead of being deleted directly. The local `.image-review/` folder is ignored by git and is only used for temporary contact sheets while classifying screenshots.

## Study Order

1. Read the day's Markdown file first.
2. Use the image index at the top of the file to jump to the exact screenshot.
3. Study the explanation below each image.
4. End with the "Points To Remember" section.
5. Re-solve the examples without looking at the answer.

## Source Basis

The notes use the screenshots as the primary course sequence and cross-check technical behavior against Intel's original 8085/MCS-85 documentation:

- Intel Corporation, [MCS-80/85 Family User's Manual, January 1983](https://www.bitsavers.org/components/intel/MCS80/MCS80_85_Users_Manual_Jan83.pdf)
- Intel Corporation, [8080/8085 Assembly Language Programming Manual, May 1981](https://www.bitsavers.org/pdf/intel/ISIS_II/9800301-04_8080_8085_Assembly_Language_Programming_Manual_May81.pdf)
- Intel Corporation, [The 8086 Family User's Manual, October 1979](https://www.ardent-tool.com/CPU/docs/Intel/808x/manuals/9800722-03.pdf)
- Intel Corporation, [iAPX 86,88 User's Manual, August 1981](https://www.dosdays.co.uk/media/intel/1981_iAPX_86_88_Users_Manual.pdf)

Day 1 also contains additional source links for background topics such as CPU basics, microcontroller versus microprocessor, Intel 4004/8008 history, and architecture terms.
