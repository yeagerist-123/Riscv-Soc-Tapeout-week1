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
![Lib File Example](your-image-link)

---

## 2. Synthesis Approaches

### A. Hierarchical Synthesis

- Modules preserved.
- Useful for repeated submodules (divide & conquer).
- Easier debugging and modular flow.

**Screenshot: Yosys hierarchy**
![Hierarchical Example](your-image-link)

### B. Flattened Synthesis

- All modules collapsed into one netlist.
- Enables global optimizations.
- Harder to debug, higher runtime.

**Screenshot: Yosys flattened netlist**
![Flattened Example](your-image-link)

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
**Waveform Screenshot**
![Async Reset DFF](your-image-link)

**Asynchronous Set DFF**
```verilog
always @(posedge clk, posedge set)
  if (set)   q <= 1'b1;
  else       q <= d;
```
**Waveform Screenshot**
![Async Set DFF](your-image-link)

**Synchronous Reset DFF**
```verilog
always @(posedge clk)
  if (reset) q <= 1'b0;
  else       q <= d;
```
**Waveform Screenshot**
![Sync Reset DFF](your-image-link)

---

## 4. Simulation & Synthesis Flow

### A. Simulation with Icarus + GTKWave
```shell
iverilog dff_asyncres.v tb_dff_asyncres.v
./a.out
gtkwave tb_dff_asyncres.vcd
```
**GTKWave Output Screenshot**
![GTKWave Simulation](your-image-link)

### B. Synthesis with Yosys
```shell
yosys
read_liberty -lib ./sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty ./sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ./sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
**Yosys Synthesis Screenshot**
![Yosys Netlist](your-image-link)

---

## ✅ Summary

- `.lib` files capture PVT conditions and cell performance.
- Larger cells → more area, less delay.
- Hierarchical vs. Flat synthesis = modular debugging vs. global optimization.
- Flip-flops: code carefully depending on reset/set requirements.
- Use `synth -top` to control entry module for synthesis.
