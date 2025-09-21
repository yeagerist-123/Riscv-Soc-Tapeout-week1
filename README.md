# Introduction to Verilog RTL Design & Synthesis

Welcome to **Week 1** of the VSD SoC Journey!  
This week focuses on **Verilog RTL design**, open-source simulation using **Icarus Verilog**, and logic synthesis with **Yosys**. Follow this guide to get hands-on experience with **2-to-1 multiplexer simulation and synthesis**.

---

## Table of Contents

1. [Understanding Simulator, Design, and Testbench](#1-understanding-simulator-design-and-testbench)  
2. [Getting Started with Icarus Verilog](#2-getting-started-with-icarus-verilog)  
3. [Lab: Simulating a 2-to-1 Multiplexer](#3-lab-simulating-a-2-to-1-multiplexer)  
4. [Verilog Code Analysis](#4-verilog-code-analysis)  
5. [Introduction to Yosys & Gate Libraries](#5-introduction-to-yosys--gate-libraries)  
6. [Synthesis Lab with Yosys](#6-synthesis-lab-with-yosys)  
7. [Key Takeaways](#7-key-takeaways)

---

## 1. Understanding Simulator, Design, and Testbench

### **Simulator**
A **simulator** is a software tool that verifies the functionality of your digital design by applying test inputs and observing outputs.  

### **Design**
The **design** is your Verilog code that describes the intended logic functionality.  

### **Testbench**
A **testbench** is an environment that applies inputs to your design and checks whether the outputs are correct.

---

## 2. Getting Started with Icarus Verilog

**Icarus Verilog (`iverilog`)** is an open-source simulator for Verilog. It compiles your Verilog design and testbench, then produces output files for waveform visualization using **GTKWave**.

### **Setup & Navigation Commands:**

```bash
cd vlsi
ls
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
ls
```

Here, you should see your Verilog files like `good_mux.v` and `tb_good_mux.v`.  
*(Insert screenshot of directory here)*

---

## 3. Lab: Simulating a 2-to-1 Multiplexer

**Step 1: Compile the Design**

```bash
iverilog good_mux.v tb_good_mux.v
```

**Step 2: Run the Simulation**

```bash
./a.out
```

**Step 3: View the Waveform**

```bash
gtkwave tb_good_mux.vcd
```

*(Insert screenshot of GTKWave waveform here)*

---

## 4. Verilog Code Analysis

You can also open and inspect the files in a text editor:

```bash
gvim tb_good_mux.v -o good_mux.v
```

### **Understanding the Multiplexer**

- **Inputs:** `i0`, `i1` (data), `sel` (select line)
- **Output:** `y` (registered output)
- **Logic:** If `sel` is 1, `y = i1`; if `sel` is 0, `y = i0`

---

## 5. Introduction to Yosys & Gate Libraries

**Yosys** is an open-source logic synthesis tool that converts your Verilog design into a gate-level netlist.

### **Starting Yosys & Loading Library**

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

The `.lib` file contains gate definitions with different speed, power, and area characteristics.

---

## 6. Synthesis Lab with Yosys

**Step 1: Read Your Verilog Design**

```bash
read_verilog good_mux.v
```

**Step 2: Synthesize the Design**

```bash
synth -top good_mux
```

**Step 3: Technology Mapping**

```bash
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

**Step 4: Visualize Gate-Level Netlist**

```bash
show
```

*(Insert screenshot of Yosys gate-level schematic here)*

---

## 7. Key Takeaways

- Learned about simulator, design, and testbench concepts.
- Simulated your first 2-to-1 multiplexer using Icarus Verilog.
- Visualized waveforms in GTKWave.
- Explored Yosys synthesis and gate libraries.
- Generated a gate-level netlist for your multiplexer.

---

*Happy Learning and Synthesis!*
