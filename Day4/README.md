
# RTL Workshop - Day 4

## Gate-Level Simulation (GLS), Blocking vs. Non-Blocking in Verilog, and Synthesis-Simulation Mismatch

Welcome to **Day 4** of the RTL Workshop! Today’s session focuses on three essential topics in digital design:

* **Gate-Level Simulation (GLS)**
* **Blocking vs. Non-Blocking Assignments in Verilog**
* **Synthesis-Simulation Mismatch**

You’ll learn both the theory and practical implications, complete with hands-on labs and illustrations.

---

## 📑 Table of Contents

* [1. Gate-Level Simulation (GLS)](#1-gate-level-simulation-gls)
* [2. Synthesis-Simulation Mismatch](#2-synthesis-simulation-mismatch)
* [3. Blocking vs. Non-Blocking Assignments in Verilog](#3-blocking-vs-non-blocking-assignments-in-verilog)

  * [3.1 Blocking Statements](#31-blocking-statements)
  * [3.2 Non-Blocking Statements](#32-non-blocking-statements)
  * [3.3 Comparison Table](#33-comparison-table)
* [4. Labs](#4-labs)
* [5. Summary](#5-summary)

---

## 1. Gate-Level Simulation (GLS)

**GLS** stands for **Gate-Level Simulation**. It is a critical verification step in the VLSI design flow where the synthesized gate-level netlist of a digital circuit is simulated to validate:

* Functional correctness
* Timing behavior
* Power estimates
* Test structures (e.g., scan chains for DFT)
  
![Image](https://github.com/user-attachments/assets/7fdc0635-1730-462c-9868-f18ca4de00bc)


### 🔹 Why Perform GLS?

* **Synthesis Validation**: Ensures that synthesis tools faithfully translate RTL into gates.
* **Timing Verification**: Simulates with realistic delays (from SDF files), allowing you to check for timing violations (e.g., setup/hold errors).
* **Testability**: Confirms that scan chains and other test features work post-synthesis.

### 🔹 When is GLS Performed?

* **After synthesis**: Once the RTL is converted into a gate-level netlist.
* **Before physical design**: To catch issues early, before layout.

### 🔹 Types of GLS

* **Functional GLS**: Logic-only simulation, often with zero or unit delays.
* **Timing GLS**: Uses annotated timing data to check real-world timing behavior.



---

## 2. Synthesis-Simulation Mismatch

A **synthesis-simulation mismatch** occurs when the simulation results of RTL (pre-synthesis) do not match simulation results of the gate-level netlist (post-synthesis) or hardware.

### ⚠️ Common Causes

* **Non-synthesizable constructs**: Use of delays, initial blocks, or other unsupported code.
* **Incomplete or ambiguous coding**: E.g., missing `else` clauses, improper sensitivity lists.
* **Tool interpretation differences**: Simulation and synthesis tools may interpret ambiguous RTL differently.

👉 **Tip:** Always write synthesizable, unambiguous RTL and follow good coding practices to minimize mismatches.

---

## 3. Blocking vs. Non-Blocking Assignments in Verilog

Verilog offers two types of procedural assignments:

### 3.1 Blocking Statements (`=`)

* **Syntax:** `=`
* **Execution:** Sequential, executes immediately.
* **Use Case:** Combinational logic (e.g., `always @(*)`).

```verilog
always @(*) y = a & b;
```

### 3.2 Non-Blocking Statements (`<=`)

* **Syntax:** `<=`
* **Execution:** Scheduled, executes concurrently at the end of the time step.
* **Use Case:** Sequential logic (e.g., `always @(posedge clk)`).

```verilog
always @(posedge clk) q <= d;
```

### 3.3 Comparison Table

| **Blocking (`=`)**                      | **Non-Blocking (`<=`)**                    |
| --------------------------------------- | ------------------------------------------ |
| Uses `=` operator                       | Uses `<=` operator                         |
| Sequential, immediate execution         | Concurrent, scheduled at end of timestep   |
| Updates happen instantly in code order  | Updates applied after time step            |
| For combinational logic, temp variables | For sequential logic, registers/flip-flops |
| Infers combinational logic (gates)      | Infers sequential logic (flip-flops)       |



---

## 4. Labs

### Lab 1: Ternary Operator MUX

```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/0f1fe6b8-c360-4e53-bcbc-968cc126192e" />

---

### Lab 2: Synthesis Using Yosys

Synthesize the MUX using Yosys (standard flow).

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/40491a7d-ce08-430f-8bd8-2a470cb7cb9b" />

---

### Lab 3: Gate-Level Simulation (GLS) of MUX

```sh
iverilog /path/to/primitives.v /path/to/sky130_fd_sc_hd.v ternary_operator_mux.v testbench.v
```

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/9e3d1b85-5be1-43e9-84aa-4d0ec60c7e27" />

---

### Lab 4: Bad MUX Example (Common Pitfalls)

```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
  always @ (sel) begin
    if (sel)
      y <= i1;
    else 
      y <= i0;
  end
endmodule
```

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/f58d41aa-1629-415a-97cf-452ccba798af" />

⚠️ **Issues:**

* Incomplete sensitivity list.
* Wrong use of non-blocking assignments in combinational logic.

✅ Corrected version:

```verilog
always @ (*) begin
  if (sel)
    y = i1;
  else
    y = i0;
end
```

---

### Lab 5: Synthesis Using Yosys

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/7635ab61-af00-42fc-a0a8-6e50dddd48f0" />

### Lab 6: GLS of Bad MUX

Perform GLS on the `bad_mux`. Expect mismatches or warnings.

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/fcea6575-16f6-4d1b-bfed-518f9ab792c6" />

```verilog
module blocking_caveat (input a, input b, input c, output reg d);
  reg x;
  always @ (*) begin
    d = x & c;
    x = a | b;
  end
endmodule
```

⚠️ Issue: `d` uses the old value of `x`.

✅ Corrected version:

```verilog
always @ (*) begin
  x = a | b;
  d = x & c;
end
```

---

### Lab 7: Synthesis of the Blocking Caveat Module

```verilog
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/d0d86787-776e-415c-80d2-723fe670f25f" />

---


### Lab 8: Synthesis Using Yosys

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/bc157fba-cb34-4c56-b3f4-00bc2638efd9" />

---

### Lab 9: Gate-Level Simulation (GLS) of Blocking Caveat Module

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/57751582-7155-45b1-ba01-3aafaeccb13a" />

---

## 5. Summary

* **GLS** validates netlist functionality, timing, and testability after synthesis.
* **Synthesis-Simulation Mismatch** arises from bad coding practices or tool interpretation differences.
* **Blocking vs. Non-Blocking:**

  * Blocking (`=`) → Combinational
  * Non-blocking (`<=`) → Sequential
* **Labs** reinforced key concepts with real-world examples.
