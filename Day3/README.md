
# Day 3: Combinational and Sequential Optimization

Welcome to **Day 3** of the RTL Workshop!  
Today we focus on **optimization techniques** for both **combinational** and **sequential circuits**, learning how synthesis tools simplify logic, reduce area, improve timing, and save power.

---

## 📑 Table of Contents

- [1. Constant Propagation](#1-constant-propagation)
- [2. State Optimization](#2-state-optimization)
- [3. Cloning](#3-cloning)
- [4. Retiming](#4-retiming)
- [5. Labs on Optimization](#5-labs-on-optimization)

---

## 1. Constant Propagation

Constant propagation is an optimization technique where **variables with constant values are replaced directly in the logic**.  

**Benefits:**
- ✅ Reduced logic complexity  
- ✅ Smaller circuit area  
- ✅ Improved performance due to fewer gates  
- ✅ Less power consumption  

---

## 2. State Optimization

State optimization targets **Finite State Machines (FSMs)**.  
It reduces the number of states or optimizes their encoding to save hardware.

**Steps:**
1. **State Reduction:** Merge equivalent states.  
2. **State Encoding:** Choose optimal binary/Gray/one-hot encoding.  
3. **Logic Minimization:** Use Boolean simplification or CAD tools.  
4. **Power Optimization:** Apply techniques like clock gating.  

---

## 3. Cloning

**Cloning** is the duplication of logic cells to **balance fan-out load** and **improve timing**.  

**Why clone?**
- High fan-out nets can cause long delays.  
- Duplicating the driver cell splits the load.  

---

## 4. Retiming

Retiming is the process of **moving registers across combinational logic** to improve performance.  

**Steps:**
1. Represent circuit as a graph.  
2. Move registers (flip-flops) across nodes.  
3. Ensure functionality remains identical.  
4. Optimize clock period and power.  

👉 Used heavily in **pipelined designs** (e.g., CPUs, DSPs).  

---

## 5. Labs on Optimization

**Lab 1**

```verilog
module opt_check (input a , input b , output y);
  assign y = a ? b : 0;
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/fa5412cb-d072-47c5-9979-a2a8a2e632c1" />

---

**Lab 2**

```verilog
module opt_check2 (input a , input b , output y);
  assign y = a ? 1 : b;
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/cd34853a-739d-436e-a2a3-5bf257ec6f96" />


---

**Lab 3**

```verilog
module opt_check3 (input a , input b , output y);
  assign y = a ? 1 : b;
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/b4179b77-c15a-4f0e-948c-61a838a35dcf" />

---

**Lab 4**

```verilog
module opt_check4 (input a , input b , input c , output y);
  assign y = a ? (b ? (a & c) : c) : (!c);
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/757a9849-94c1-4e85-a33d-4e6ef37ebe23" />

---

**Lab 5**

```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset) begin
  if (reset)
    q <= 1'b0;
  else
    q <= 1'b1;
end
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/35ff965c-6d82-47e1-960a-2cdb30a94bc5" />

---

**Lab 6**

```verilog
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset) begin
  if (reset)
    q <= 1'b1;
  else
    q <= 1'b1;
end
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/51c06b31-c4cb-4e79-b070-bf1e25fdcc37" />

---

**Lab 7**

```verilog
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset) begin
  if (reset) begin
    q  <= 1'b1;
    q1 <= 1'b0;
  end
  else begin
    q1 <= 1'b1;
    q  <= q1;
  end
end
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/279ab1cd-33af-45ae-b011-622451c4762c" />

---

**Lab 8**

```verilog
module dff_const4(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset) begin
  if (reset) begin
    q  <= 1'b1;
    q1 <= 1'b1;
  end
  else begin
    q1 <= 1'b1;
    q  <= q1;
  end
end
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/883ecc83-bc68-4244-9809-8c54efc78c82" />


---

**Lab 9**

```verilog
module dff_const5(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset) begin
  if (reset) begin
    q  <= 1'b0;
    q1 <= 1'b0;
  end
  else begin
    q1 <= 1'b1;
    q  <= q1;
  end
end
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/4aed2dfe-af66-435a-840a-2e29c7143dd4" />

---

**Lab 10**

```verilog
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset) begin
  if (reset)
    count <= 3'b000;
  else
    count <= count + 1;
end
endmodule
```
<img width="1210" height="773" alt="Image" src="https://github.com/user-attachments/assets/b9e7c6a8-1bc6-4381-af4a-bd6b2993cecc" />

---

## ✅ Summary

* **Constant Propagation:** Simplifies design by replacing signals with constants.
* **State Optimization:** Reduces redundant states and minimizes FSM logic.
* **Cloning:** Duplicates cells to balance load and improve timing.
* **Retiming:** Relocates registers for better clock performance.

**Labs Recap:**

* Labs 1–4 → Combinational optimizations (ternary operators simplified).
* Labs 5–9 → Sequential optimizations (flip-flops reduced to constants).
* Lab 10 → Counter optimization.
