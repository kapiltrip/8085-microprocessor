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

## Key Study Notes

Timing questions usually need two separate steps:

```text
total delay = total T-states x T-state period
T-state period = 1 / clock frequency
```

For conditional jumps, count the jump differently depending on whether the branch is taken. This is why loops often have one timing value for repeated iterations and another value for the final exit.

For register-pair tracing:

| Instruction | Important effect |
| --- | --- |
| `LXI rp,data16` | Loads a 16-bit immediate value into a register pair. |
| `DAD rp` | Adds a register pair to `HL`; result stays in `HL`. |
| `XCHG` | Exchanges `HL` and `DE`. |
| `PUSH PSW` | Pushes accumulator and flags onto the stack. |
| `POP H` | Loads stack bytes into `L` and `H`, then increments `SP`. |

For bit questions, write the accumulator in binary before applying `RRC`, `RAL`, `ANI`, `ANA`, `CMA`, or `INR`. Most mistakes happen when the carry flag or the high nibble is assumed instead of traced.

## Points To Remember

- `CALL` stores the return address on the stack; `RET` restores it by popping the address back.
- `RRC` rotates accumulator bit `D0` into both `D7` and carry.
- `DAD` always updates `HL`, regardless of the source register pair.
- In subtraction, carry means borrow.
- In loop timing, the last conditional jump may use a different T-state count.
