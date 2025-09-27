# Day 3: Combinational and Sequential Optimization

Welcome to Day 3! This guide covers key optimization techniques for combinational and sequential circuits in digital design, along with hands-on labs in Verilog.

---

## Table of Contents

- [Constant Propagation](#constant-propagation)
- [State Optimization](#state-optimization)
- [Cloning](#cloning)
- [Retiming](#retiming)
- [Practical Labs](#practical-labs)
  - [Lab 1](#lab-1--constant-propagation)
  - [Lab 2](#lab-2--multiplexer-example)
  - [Lab 3](#lab-3--another-multiplexer-example)
  - [Lab 4](#lab-4--nested-ternary-logic)
  - [Lab 5](#lab-5--d-flip-flop-with-constant-output)
  - [Lab 6](#lab-6--d-flip-flop-always-high)
  - [Lab 7]
  - [Lab 8]
  - [Lab 9
- [Summary](#summary)

---

## Constant Propagation

combinational optimization technique in digital design where variables assigned constant values are replaced directly in the logic. 
This simplification allows synthesis tools to optimize the circuit, reduce logic, and improve performance.

**Example:**  
*See [Lab 1](#lab-1--constant-propagation)*

---

## State Optimization

State optimization is the process of reducing the number of states in a finite state machine (FSM) 
and improving its encoding to minimize logic complexity, power consumption, and area

---

## Cloning

If a logic gate or module is slowing down a path due to too many connections or long wires
, you can make a copy of it and split the load, which improves performance.

**Example:**  
*Refer to visual circuit examples or synthesis reports.*

---

## Retiming

Retiming is a circuit optimization technique used in VLSI design where registers (flip-flops) are repositioned across combinational logic to improve timing performance,
reduce clock period, or optimize power consumption, without changing the functional behavior of the circuit.

---

## Practical Labs

### Lab 1 – Constant Propagation

```verilog
module opt_check (input a , input b , output y);
	assign y = a ? b : 0;
endmodule
```
- If `a` is true → `y = b`
- If `a` is false → `y = 0`
- <img width="1920" height="922" alt="yosys opt_check" src="https://github.com/user-attachments/assets/6b7a0fb8-de1d-4989-a6cc-8ce5bd4a8058" />


**Synthesis**  
Run: `opt_clean -purge` in between synth -top and abc -liberty

---

### Lab 2 – Multiplexer Example

```verilog
module opt_check2 (input a , input b , output y);
	assign y = a ? 1 : b;
endmodule
```
- Acts as a or gate(a+b):
  - `a = 1` → `y = 1`
  - `a = 0` → `y = b`
  - <img width="1920" height="922" alt="yosys opt_check2" src="https://github.com/user-attachments/assets/d6aa552a-bb95-426e-bacb-b0954bf4f58b" />


---

### Lab 3 – Another Multiplexer Example

```verilog
module opt_check3 (input a , input b , output y);
	assign y = a ? 1 : b;
endmodule
```
- Similar to Lab 2; demonstrates simplification of ternary operators.
- <img width="1920" height="922" alt="yosy opt3" src="https://github.com/user-attachments/assets/4eed1ea1-19c5-4a20-8bc3-a723367c4acd" />


---

### Lab 4 – Nested Ternary Logic

```verilog
module opt_check4 (input a , input b , input c , output y);
	assign y = a ? (b ? (a & c) : c) : (!c);
endmodule
```
- Logic simplifies to: `y = a ? c : !c`
- <img width="1920" height="922" alt="yosys op4" src="https://github.com/user-attachments/assets/7b5dff2c-1640-42e3-ac28-a4948cb833e4" />


---

### Lab 5 – D Flip-Flop with Constant Output

```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
endmodule
```
- Asynchronous reset sets `q = 0`
- Otherwise, `q = 1`
- <img width="1920" height="922" alt="dffconst1" src="https://github.com/user-attachments/assets/0a635ac3-2988-4188-84b0-c323522c5d2d" />


---

### Lab 6 – D Flip-Flop Always High

```verilog
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	q <= 1'b1;
end
endmodule
```
- <img width="1920" height="922" alt="dffcont2" src="https://github.com/user-attachments/assets/19701764-2c7b-4b8c-973f-5d5603fd18a5" />


---
### Lab 7 – D Flip-Flop const-3

```
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```
- <img width="1920" height="922" alt="dffconst3" src="https://github.com/user-attachments/assets/d9e952cb-d920-45ff-9409-b1e82514b6e2" />

### Lab 8 module dff_const4
```
module dff_const4(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b1;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```
- 
<img width="1920" height="922" alt="dffconst4" src="https://github.com/user-attachments/assets/cf503bed-a254-41f5-a1d4-8eb1e74897a2" />

### Lab 9 – D Flip-Flop const-5

```verilog

module dff_const5(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b0;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```

- <img width="1920" height="922" alt="dffcont5" src="https://github.com/user-attachments/assets/de924c5d-51f2-4652-96ba-8d5415609e7f" />





## Summary

**Key Takeaways:**
- **Constant Propagation:** Simplifies logic by replacing constants.
- **State Optimization:** Reduces FSM complexity, logic, and power.
- **Cloning:** Improves timing and balances load.
- **Retiming:** Optimizes register placement for performance.
- **Practical Labs:** Reinforce concepts with hands-on Verilog examples.

**Outcome:**  
By applying these techniques, digital circuits can achieve better performance, lower resource usage, and optimized power consumption.
