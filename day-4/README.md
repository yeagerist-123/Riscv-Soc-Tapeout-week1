# Day 4: Gate Level Simulation (GLS), Blocking vs Non-Blocking, and Synthesis-Simulation Mismatch

Welcome to Day 4 of the RISC-V Taproot program! Today, we focus on the vital concepts of Gate Level Simulation (GLS), understanding blocking and non-blocking assignments in Verilog, and identifying causes of synthesis-simulation mismatches. These topics are essential for ensuring that your simulation results faithfully represent synthesized hardware behavior.

---

## Table of Contents

- [1. GLS Concepts and Flow using Verilog](#1-gls-concepts-and-flow-using-verilog)
- [2. Synthesis-Simulation Mismatch](#2-synthesis-simulation-mismatch)
- [3. Blocking and Non-Blocking Statements in Verilog](#3-blocking-and-non-blocking-statements-in-verilog)
- [4. Caveats with Blocking Statements](#4-caveats-with-blocking-statements)
- [5. Labs on GLS and Synthesis-Simulation Mismatch](#5-labs-on-gls-and-synthesis-simulation-mismatch)
- [6. Labs on Synth-Sim Mismatch for Blocking Statements](#6-labs-on-synth-sim-mismatch-for-blocking-statements)
- [Summary](#summary)

---

## 1. GLS Concepts and Flow using Verilog

**Gate Level Simulation (GLS)** is performed on the synthesized netlist, not the RTL. GLS differs from RTL simulation in the following ways:

- **Realistic Gate-Level Delays:** GLS includes delay elements present in real physical gates.
- **Setup and Hold Time Checks:** GLS verifies timing constraints for flip-flops and other sequential elements.
- **Verification of Synthesized Logic:** Ensures that the synthesized netlist matches the intended RTL design.

GLS is a critical verification step before moving on to physical design, helping catch issues that only show up after synthesis.

---

## 2. Synthesis-Simulation Mismatch

A **synthesis-simulation mismatch** occurs when results from RTL simulation differ from those observed in GLS. Common causes include:

- **Missing signals in sensitivity lists** of combinational always blocks.
- **Use of blocking assignments (`=`) in sequential logic,** which can cause unintended behavior.
- **Incomplete assignments** leading to the inference of unintended latches.

Such mismatches highlight the importance of strict RTL coding practices to ensure that the synthesized hardware behaves as expected.

---

## 3. Blocking and Non-Blocking Statements in Verilog

Verilog supports two types of procedural assignments:

- **Blocking (`=`):**
  - Executes statements sequentially, one after another.
  - Suitable for modeling combinational logic.
- **Non-blocking (`<=`):**
  - All right-hand sides are evaluated before any assignments are made.
  - Updates occur in parallel.
  - Recommended for modeling sequential (clocked) logic.

**Correct usage ensures consistency between simulation and synthesized hardware.**

---

## 4. Caveats with Blocking Statements

Using **blocking assignments (`=`) inside sequential (clocked) always blocks** can lead to simulation results that depend on statement ordering, which does not reflect real hardware behavior.

**Best Practices:**
- Use **non-blocking (`<=`) for sequential logic** (inside `always @(posedge clk)`).
- Use **blocking (`=`) for purely combinational logic** (inside `always @(*)`).

Following these guidelines prevents mismatches and ensures accurate design simulation and synthesis.

---

## 5. Labs on GLS and Synthesis-Simulation Mismatch

### Lab 1: GLS Mismatch Demonstration (Part 1)

Here we check the wave form in GTKWAVE of only rtl and then gls waveform and verify whether we get the same.
the verilog file we use is `ternary_operator_mux.v`
follow previous steps to get the below gtkwave


next observe netlist by invoking yosys, you get the below netlist

now use
`1.iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
 2. ./a.out
 3. gtkwave tb_ternary_operator_mux.vcd
 `
 you should see

 here wire _0_,_1_ imply that this is GLS. You can see that both are same
 
### Lab 2: GLS Mismatch Demonstration (Part 2)

Here we use verilog file bad_mux.v(do the same steps as mentioned above to compare both rtl and gls waveforms)
gtkwave for rtl

yosys netlist

gtkwave for gls

clearly you can observe the difference that in gls output changes according to i0 when sel is low and according to i1 when sel is high.

---

## 6. Labs on Synth-Sim Mismatch for Blocking Statements

### Lab 3: Synth-Sim Mismatch Caused by Blocking Assignments 

Shows how blocking assignments in sequential logic can create mismatches between simulation and synthesis.
we use blocking_caveat.v file here which performs [(a|b)&c]
gtkwave of rtl synthesis shows clear mismatch as due to blocking statements it considers previous values.

yosys netlist:

gtkwave of gls synthesis:

here you can see that the mismatch problem has been solved as it looks only at instantaneous value


---

## Summary

- **GLS** validates the correctness of the synthesized netlist with accurate timing information.
- **Synthesis-simulation mismatches** are mostly due to incomplete or incorrect RTL coding practices.
- **Blocking vs Non-blocking assignments:**
  - Use `=` for combinational logic only.
  - Use `<=` for sequential (clocked) logic.
- Following proper RTL guidelines ensures your designs simulate and synthesize consistently, reducing the risk of costly hardware bugs.

---
