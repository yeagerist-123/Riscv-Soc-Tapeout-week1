# Day 2 – RTL Workshop: Libraries, Synthesis, and Flip-Flops

This module covers three pillars of RTL design:

- **Timing Libraries (.lib):** Understanding PVT impact.
- **Synthesis Approaches:** Hierarchical vs. flat.
- **Flip-Flop Coding:** Efficient sequential logic design.

Throughout the guide, you’ll find placeholders to add images/screenshots (replace the `![Alt Text](image-link)` with your uploaded links).

---

## 1. Timing Libraries and PVT Corners

The SKY130 PDK provides `.lib` files describing delay, area, and power of standard cells.

**Example:** `sky130_fd_sc_hd__tt_025C_1v80.lib`
- `tt` → Typical process corner
- `025C` → Temperature at 25°C
- `1v80` → Voltage of 1.8 V

👉 These libraries ensure synthesis reflects real-world PVT behavior.

### 📊 Cell Variants and Trade-offs

For cells like AND2, AND2_2, AND2_4:
- **Area ⬆️** with higher drive strength.
- **Delay ⬇️** with higher drive strength.

**Screenshot: .lib snippet**
<img width="1920" height="922" alt="lib im" src="https://github.com/user-attachments/assets/ec130649-5ad8-4f41-977d-a7a910ce83af" />
**and2_0,and2_2 area check**
<img width="1920" height="922" alt="and2,0 vs and2,2" src="https://github.com/user-attachments/assets/91d13385-6f1f-4b3e-b30b-a899a96ffe97" />



---

## 2. Synthesis Approaches

## Hierarchical Synthesis

- **Definition:** Retains the module hierarchy as defined in RTL, synthesizing modules separately.
- **How it Works:** Tools like Yosys process each module independently, using the `hierarchy` command to analyze and set up the design structure.

**Advantages:**
- Faster synthesis time for large designs.
- Improved debugging and analysis due to maintained module boundaries.
- Modular approach, aiding integration with other tools.

**Disadvantages:**
- Cross-module optimizations are limited.
- Reporting can require additional configuration.

**Screenshot: Yosys hierarchy**
<img width="1920" height="922" alt="yosys m_mdles" src="https://github.com/user-attachments/assets/6871fa2c-2b77-4c29-994a-6fc9cf22f97b" />


## Flattened Synthesis

- **Definition:** Merges all modules into a single flat netlist, eliminating hierarchy.
- **How it Works:** The `flatten` command in Yosys collapses the hierarchy, allowing whole-design optimizations.

**Advantages:**
- Enables aggressive, cross-module optimizations.
- Results in a unified netlist, sometimes simplifying downstream processes.

**Disadvantages:**
- Longer runtime for large designs.
- Loss of hierarchy complicates debugging and reporting.
- Can increase memory usage and netlist complexity.

**Screenshot: Yosys flattened netlist**
<img width="1920" height="922" alt="yosys flat" src="https://github.com/user-attachments/assets/78b520d5-a9e3-4913-9bd9-7dc0b89b90a3" />


### ⚖️ Trade-offs Table

| Aspect      | Hierarchical      | Flattened        |
| ----------- | ---------------- | ---------------  |
| Debugging   | Easier           | Harder           |
| Optimization| Local/module-level| Global           |
| Runtime     | Faster (large SoCs)| Slower          |
| Area/Delay  | May be larger     | Optimized        |

#### Practical Notes

- **Total synthesis:** Stacked NMOS perform better than stacked PMOS.
- **Single-module synthesis:** Efficient when one block repeats many times.
- `synth -top <module>`: Defines which module is synthesized.

---

## 3. Flip-Flop Coding Styles

**Asynchronous Reset DFF**
```verilog
always @(posedge clk, posedge reset)
  if (reset) q <= 1'b0;
  else       q <= d;
```

**Asynchronous Set DFF**
```verilog
always @(posedge clk, posedge set)
  if (set)   q <= 1'b1;
  else       q <= d;
```

**Synchronous Reset DFF**
```verilog
always @(posedge clk)
  if (reset) q <= 1'b0;
  else       q <= d;
```


## 4. Simulation & Synthesis Flow

### A. Simulation with Icarus + GTKWave
```shell
iverilog dff_asyncres.v tb_dff_asyncres.v
./a.out
gtkwave tb_dff_asyncres.vcd
```
<img width="1920" height="922" alt="async res" src="https://github.com/user-attachments/assets/bf5f64f5-b186-4882-ac23-5bd76a2e2a62" />

For Async set:
```shell
iverilog dff_async_set.v tb_dff_asyncres.v
./a.out
gtkwave tb_dff_asyn_set.vcd
```
<img width="1920" height="922" alt="d asy set" src="https://github.com/user-attachments/assets/2ed382e8-93af-4cac-aec9-bba5c4ac288f" />




### B. Synthesis with Yosys
```shell
yosys
read_liberty -lib ../sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
**Yosys Synthesis Screenshot**
<img width="1920" height="922" alt="yosys dasyncres" src="https://github.com/user-attachments/assets/ca219887-3c02-49ff-9dd1-6128bce2dbe1" />


---
**Observing multiplication code**
`gvim mult_*.v -o`
you see something like
<img width="1920" height="922" alt="mult" src="https://github.com/user-attachments/assets/a6405a76-52e1-4b56-b385-f0080090892f" />

1.Here you can see that by multiplying with 2 we just add a zero in it's binary ones place.(zero is appended)
now follow the previous steps to see netlist in yosys
<img width="1920" height="922" alt="mlu2 yosys" src="https://github.com/user-attachments/assets/b3b37068-dea6-4cc4-af7e-4a499c833ae4" />

2.when multiplied by nine we get a same number repeated 2 times in it;s binary. You can check the yosys netlist
<img width="1920" height="922" alt="mul8 yosys" src="https://github.com/user-attachments/assets/035f5a04-46e6-4d17-b6ed-27bc8d3a0155" />

## ✅ Summary

- `.lib` files capture PVT conditions and cell performance.
- Larger cells → more area, less delay.
- Hierarchical vs. Flat synthesis = modular debugging vs. global optimization.
- Flip-flops: code carefully depending on reset/set requirements.
- Use `synth -top` to control entry module for synthesis.
