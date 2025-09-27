# Day 5 – RISC-V Taproot Program

This day covers essential Verilog constructs—`if`, `case`, and loops—with a focus on how they map to hardware and best practices for RTL design.

---

## Table of Contents

- [1. If-Case Constructs – Part 1](#1-if-case-constructs--part-1)
- [2. If-Case Constructs – Part 2](#2-if-case-constructs--part-2)
- [3. If-Case Constructs – Part 3](#3-if-case-constructs--part-3)
- [4. Loop Constructs – Part 1](#4-loop-constructs--part-1)
- [5. Loop Constructs – Part 2](#5-loop-constructs--part-2)
- [6. Lab Session](#6-lab-session)
- [Summary](#summary)

---

## 1. If-Case Constructs – Part 1

### Overview

The `if` construct enables conditional execution in Verilog. In hardware, it creates **priority logic**: conditions are checked one after another, and the first `true` condition sets the output.

- **Implementation:** An `if` ladder maps to a chain of multiplexers.
- **Priority:** The first satisfied condition wins.
- **Risks:** Long `if` chains → deep, slow logic; multiple true conditions → only the first is executed.

**Why is it fine in counters?**
- Counter conditions (e.g., `reset`, `enable`) are usually mutually exclusive.
- Example:
  ```verilog
  always @(posedge clk) begin
      if (reset)
          count <= 0;
      else if (enable)
          count <= count + 1;
  end
  ```
  Here, priority is intentional (`reset` > `enable`).

---

## 2. If-Case Constructs – Part 2

### Case Statement

The `case` statement implements parallel selection—**multiplexer logic**.

- **Implementation:** All branches are evaluated in parallel.
- **Best for:** Mutually exclusive selection (e.g., bus/mux).

**Example:**
```verilog
always @(*) begin
    case (sel)
        2'b00: y = a;
        2'b01: y = b;
        2'b10: y = c;
        default: y = d;
    endcase
end
```

**Caveats:**
- Missing `default` → possible latch inference.
- Overlapping cases → ambiguous hardware.
- Tool directives (`full_case`, `parallel_case`) must be used carefully.

---

## 3. If-Case Constructs – Part 3

### Differences Between `if-elseif-else` and `case`

| Feature              | if-elseif-else                  | case                          |
|----------------------|----------------------------------|-------------------------------|
| Hardware mapping     | Priority logic chain (mux)       | Parallel multiplexer selection|
| Execution order      | Sequential, first true wins      | All conditions evaluated in parallel|
| Use case             | Priority important (e.g., reset) | Mutually exclusive conditions |
| Dangers              | Multiple true conditions, debugging priority | Latch if no default, overlap issues |

---

## 4. Loop Constructs – Part 1

### Synthesis Loops

Loops in HDL **generate hardware** during synthesis—they are not runtime constructs.

- **for loop:** Expands logic statically.
- **while loop:** Rare; may cause issues in synthesis.
- **repeat loop:** Simulation-only.

**Example:**
```verilog
genvar i;
generate
    for (i = 0; i < 8; i = i + 1) begin
        dff d1(.clk(clk), .d(data[i]), .q(q[i]));
    end
endgenerate
```
*Implements 8 flip-flops with one statement.*

---

## 5. Loop Constructs – Part 2

### Simulation Loops

Simulation loops are useful in testbenches.

**Example:**
```verilog
integer i;
initial begin
    for (i = 0; i < 10; i = i + 1) begin
        data = i;
        #10;
    end
end
```
*Applies 10 stimulus values, each with 10 time units delay.*

**Key Points:**
- In **synthesis**: loops unroll into hardware.
- In **simulation**: loops behave as software.

---

## 6. Lab Session

- Practical coding for `if`, `case`, and loops.
- Example: Incomplete if-case (see `incomp_if.v`).
- Implemented small modules and testbenches.
- Verified:
  - `if` generates priority logic.
  - `case` generates parallel muxes.
  - Loops replicate hardware.

---

## Summary

- Use `if` for priority logic (e.g., counter `reset` and `enable`).
- Use `case` for parallel, mutually exclusive choices.
- Loops replicate hardware in synthesis, provide stimulus in simulation.
- Watch out for latches (missing defaults), priority bugs, and ambiguous cases.
