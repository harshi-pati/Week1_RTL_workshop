
# Day 5: Optimization in Synthesis

Welcome to Day 5 of the RTL workshop! Today, we will cover optimization in Verilog synthesis, focusing on `if-else` statements, `for` loops, generate blocks, and explore how improper coding can lead to inferred latches. Labs are included for hands-on experience.

---
## Contents

Got it ✅ You want me to generate the **Table of Contents** for the document you pasted.
Here’s the structured **ToC** in Markdown:

---

## 📑 Table of Contents

1. [If-Else Statements in Verilog](#1-if-else-statements-in-verilog)
2. [Inferred Latches in Verilog](#2-inferred-latches-in-verilog)
3. [Labs for If-Else and Case Statements](#3-labs-for-if-else-and-case-statements)

   * [Lab 1: Incomplete If Statement](#lab-1-incomplete-if-statement)
   * [Lab 2: Synthesis Result of Lab 1](#lab-2-synthesis-result-of-lab-1)
   * [Lab 3: Nested If-Else](#lab-3-nested-if-else)
   * [Lab 4: Synthesis Result of Lab 3](#lab-4-synthesis-result-of-lab-3)
   * [Lab 5: Complete Case Statement](#lab-5-complete-case-statement)
   * [Lab 6: Synthesis Result of Lab 5](#lab-6-synthesis-result-of-lab-5)
   * [Lab 7: Incomplete Case Handling](#lab-7-incomplete-case-handling)
4. [For Loops in Verilog](#4-for-loops-in-verilog)
5. [What is an RCA (Ripple Carry Adder)?](#6-what-is-an-rca-ripple-carry-adder)
6. [Labs on Loops and Generate Block](#7-labs-on-loops-and-generate-block)

   * [Lab 8: 8-to-1 Demux Using For Loop](#lab-8-8-to-1-demux-using-for-loop)
   * [Lab 9: 8-bit Ripple Carry Adder with Generate Block](#lab-9-8-bit-ripple-carry-adder-with-generate-block)
7. [Summary](#summary)

---

Do you want me to also **fix the numbering mismatch** (notice section 5 is skipped, it jumps from 4 → 6) so everything is consistent?

---

## 1. If-Else Statements in Verilog

**If-else statements** are used for conditional execution in behavioral modeling, typically within procedural blocks (`always`, `initial`, tasks, or functions).

### Syntax

```verilog
if (condition) begin
    // Code block executed if condition is true
end else begin
    // Code block executed if condition is false
end
```

- **condition**: An expression evaluating to true (non-zero) or false (zero).
- **begin ... end**: Used to group multiple statements. Omit if only one statement is present.
- The `else` part is optional.

#### Nested If-Else

```verilog
if (condition1) begin
    // Code for condition1 true
end else if (condition2) begin
    // Code for condition2 true
end else begin
    // Code if no conditions are true
end
```

---

## 2. Inferred Latches in Verilog

**Inferred latches** occur when a combinational logic block does not assign a value to a variable in all possible execution paths. This causes the synthesis tool to infer a latch, which may not be the designer’s intention.

### Example of Latch Inference

```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
    always @(a, b, sel) begin
        if (sel == 1'b1)
            y = a; // No 'else' - y is not assigned when sel == 0
    end
endmodule
```

**Problem**: When `sel` is 0, `y` is not assigned, so a latch is inferred.

#### Solution: Add Else or Default Case

```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
    always @(a, b, sel) begin
        case(sel)
            1'b1 : y = a;
            default : y = 1'b0; // Default assignment
        endcase
    end
endmodule
```

---

## 3. Labs for If-Else and Case Statements

### Lab 1: Incomplete If Statement

```verilog
module incomp_if (input i0, input i1, input i2, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
end
endmodule
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/91d6cf74-ed4c-4029-b1b3-89da4ab6282c" />


---

### Lab 2: Synthesis Result of Lab 1

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/aee262c8-3769-4e0f-bd55-a9b198f69134" />

---

### Lab 3: Nested If-Else

```verilog
module incomp_if2 (input i0, input i1, input i2, input i3, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
    else if (i2)
        y <= i3;
end
endmodule
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/bf3c3730-9653-4592-a9b0-27cf4a39f61c" />

---

### Lab 4: Synthesis Result of Lab 3


<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/b1ac46e8-4645-4858-bfc5-38375a831f62" />


---

### Lab 5: Complete Case Statement

```verilog
module comp_case (input i0, input i1, input i2, input [1:0] sel, output reg y);
always @(*) begin
    case(sel)
        2'b00 : y = i0;
        2'b01 : y = i1;
        default : y = i2;
    endcase
end
endmodule
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/76fe0fdf-28af-4f0d-936f-f6ecce4ea018" />

---

### Lab 6: Synthesis Result of Lab 5

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/e9ba3307-3792-413d-90cf-c1cb600fecd3" />

---

### Lab 7: Incomplete Case Handling

```verilog
module bad_case (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
always @(*) begin
    case(sel)
        2'b00: y = i0;
        2'b01: y = i1;
        2'b10: y = i2;
        2'b1?: y = i3; // '?' is a wildcard; be careful with incomplete cases!
    endcase
end
endmodule
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/7c6de6eb-ff95-4b70-9c5b-856f3836b9bf" />
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/5b270703-cff4-4c6e-aa1f-5ee2629f3aa7" />

---


---

## 4. For Loops in Verilog

A **for loop** is used within procedural blocks (`initial`, `always`, tasks/functions) to execute statements multiple times based on a loop counter.

### Syntax

```verilog
for (initialization; condition; increment) begin
    // Statements to execute
end
```

- Must be inside procedural blocks.
- Synthesizable only if the number of iterations is fixed at compile time.

#### Example: 4-to-1 MUX Using a For Loop

```verilog
module mux_4to1_for_loop (
    input wire [3:0] data, // 4 input lines
    input wire [1:0] sel,  // 2-bit select
    output reg y           // Output
);
    integer i;
    always @(data, sel) begin
        y = 1'b0; // Default output
        for (i = 0; i < 4; i = i + 1) begin
            if (i == sel)
                y = data[i];
        end
    end
endmodule
```

---


## 5. What is an RCA (Ripple Carry Adder)?

An RCA adds binary numbers using a chain of full adders. To add `n` bits, you need `n` full adders. Each carry-out connects to the carry-in of the next stage.

![image](https://github.com/user-attachments/assets/f1ec27d4-b770-4d7a-a418-6435fc81f538)


## 6. Labs on Loops and Generate Block


### Lab 8: 8-to-1 Demux Using For Loop

```verilog
module demux_generate (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
integer k;
always @(*) begin
    y_int = 8'b0;
    for (k = 0; k < 8; k = k + 1) begin
        if (k == sel)
            y_int[k] = i;
    end
end
endmodule
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/feede22b-14d6-4968-9fb0-e90467c59099" />
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/a000e135-c5ad-407d-a769-097c6d7f3a18" />

---

### Lab 9: 8-bit Ripple Carry Adder with Generate Block

```verilog
module rca (
    input [7:0] num1,
    input [7:0] num2,
    output [8:0] sum
);
wire [7:0] int_sum;
wire [7:0] int_co;

genvar i;
generate
    for (i = 1; i < 8; i = i + 1) begin
        fa u_fa_1 (.a(num1[i]), .b(num2[i]), .c(int_co[i-1]), .co(int_co[i]), .sum(int_sum[i]));
    end
endgenerate

fa u_fa_0 (.a(num1[0]), .b(num2[0]), .c(1'b0), .co(int_co[0]), .sum(int_sum[0]));

assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```
**Full Adder Module:**
```verilog
module fa (input a, input b, input c, output co, output sum);
    assign {co, sum} = a + b + c;
endmodule
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/e6d52123-b337-4aef-8f69-20a7f8b2c06f" />
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/866c3dc3-c870-483c-a683-e74172cf1a84" />

---

## Summary

- Use complete if-else and case statements to avoid unintended latch inference.
- For loops and generate blocks are powerful for writing scalable, synthesizable code.
- Always ensure every signal is assigned in every possible execution path for combinational logic.
- Use labs to reinforce concepts with practical Verilog code and synthesis results.

---
