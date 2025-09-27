
# RTL Workshop – Day 2  
**Topics:** Timing Libraries • Synthesis Strategies • Flip-Flop Coding

---

## 📌 Overview
Day 2 of the RTL Workshop introduces three key concepts in front-end digital design:

1. Understanding the `.lib` **timing library** (SKY130 PDK).  
2. Comparing **hierarchical vs. flat synthesis**.  
3. Writing **efficient flip-flop RTL code**.  

By the end, you’ll be able to explore timing libraries, choose the right synthesis approach, and write synthesis-friendly flip-flop modules.

---

## ⏱ Timing Libraries

### SKY130 PDK
The **SkyWater SKY130** is an open-source **130nm CMOS PDK** providing:  
- Standard cell libraries  
- Timing & power models  
- Process-voltage-temperature (PVT) corners  

### Decoding `sky130_fd_sc_hd__tt_025C_1v80.lib`
- `tt` → Typical process corner  
- `025C` → Temperature = 25 °C  
- `1v80` → Supply voltage = 1.8 V  

This `.lib` models delay, power, and functionality of cells at these PVT conditions.

### Opening the Library
```
sudo apt install gedit   # or use vim/nano
gedit sky130_fd_sc_hd__tt_025C_1v80.lib
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/81dbd45b-25ad-459a-9db3-dd3c6e4a34d0" />

---

## 🏗 Hierarchical vs. Flattened Synthesis

### Hierarchical Synthesis

In hierarchical synthesis, sub-modules are preserved as separate blocks in the synthesized netlist.

**Advantages:**

* Faster synthesis for large designs.
* Easier to debug (traces map back to RTL modules).
* Modular design flow (good for reuse and integration).

**Disadvantages:**

* Limited cross-module optimization.
* Reports may require additional setup for full visibility.

**Example (Hierarchical Netlist):**

```verilog
module multiple_modules(a, b, c, y);
  input a, b, c;
  output y;
  wire net1;

  sub_module1 u1 (
    .a(a),
    .b(b),
    .y(net1)
  );

  sub_module2 u2 (
    .a(net1),
    .b(c),
    .y(y)
  );
endmodule

module sub_module1(a, b, y);
  input a, b;
  output y;
  wire _0_, _1_, _2_;

  sky130_fd_sc_hd__and2_0 _3_ (
    .A(_1_),
    .B(_0_),
    .X(_2_)
  );

  assign _1_ = b;
  assign _0_ = a;
  assign y   = _2_;
endmodule

module sub_module2(a, b, y);
  input a, b;
  output y;
  wire _0_, _1_, _2_;

  sky130_fd_sc_hd__or2_0 _3_ (
    .A(_1_),
    .B(_0_),
    .X(_2_)
  );

  assign _1_ = b;
  assign _0_ = a;
  assign y   = _2_;
endmodule
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/4bfa5f3e-a313-45e1-8404-ee76a4bfb853" />

---

### Flattened Synthesis

In flattened synthesis, hierarchy is removed, and everything is merged into a single netlist.

**Advantages:**

* Allows global, cross-module optimization.
* Single netlist sometimes simplifies backend processing.

**Disadvantages:**

* Longer runtime and higher memory usage for large designs.
* Harder to debug since original hierarchy is lost.
* Netlist becomes complex and less modular.

**Example (Flattened Netlist):**

```verilog
module multiple_modules(a, b, c, y);
  input a, b, c;
  output y;
  wire _0_, _1_, _2_, _3_, _4_, _5_;
  wire net1;

  sky130_fd_sc_hd__and2_0 _6_ (
    .A(_1_),
    .B(_0_),
    .X(_2_)
  );

  sky130_fd_sc_hd__or2_0 _7_ (
    .A(_4_),
    .B(_3_),
    .X(_5_)
  );

  assign _4_ = c;
  assign _3_ = net1;
  assign y   = _5_;
  assign _1_ = b;
  assign _0_ = a;
  assign net1 = _2_;
endmodule
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/4fa91a8a-0b23-47c3-bbc4-e21ee1fd963f" />

---

### 🔎 Key Differences

| Feature             | Hierarchical           | Flattened          |
| ------------------- | ---------------------- | ------------------ |
| Hierarchy           | Preserved              | Removed            |
| Optimization        | Local (per module)     | Global             |
| Debugging           | Easier                 | Harder             |
| Runtime (large SoC) | Faster                 | Slower             |
| Use Case            | Modular analysis flows | Final optimization |

---

## 🔁 Flip-Flop Coding Styles

Efficient RTL coding ensures proper mapping to standard cells.

### Asynchronous Reset

```verilog
module dff_asyncres (input clk, input async_reset, input d, output reg q);
  always @(posedge clk or posedge async_reset)
    if (async_reset) q <= 1'b0;
    else             q <= d;
endmodule
```

### Asynchronous Set

```verilog
module dff_async_set (input clk, input async_set, input d, output reg q);
  always @(posedge clk or posedge async_set)
    if (async_set) q <= 1'b1;
    else           q <= d;
endmodule
```

### Synchronous Reset

```verilog
module dff_syncres (input clk, input sync_reset, input d, output reg q);
  always @(posedge clk)
    if (sync_reset) q <= 1'b0;
    else            q <= d;
endmodule
```

---

## ⚙️ Simulation & Synthesis Flow

### Simulation (Icarus Verilog + GTKWave)

```bash
# Compile (example: async reset DFF)
iverilog dff_asyncres.v tb_dff_asyncres.v

# Run
./a.out

# View waveform
gtkwave tb_dff_asyncres.vcd
```

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/8c4c20e2-c77d-4b5c-a9e0-c83771b71866" />


🔁 **Repeat the same steps** for:

* `dff_async_set.v` with its testbench
* `dff_syncres.v` with its testbench

---

### Synthesis (Yosys + SKY130)

```bash
yosys
read_liberty -lib sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_asyncres.v
synth -top dff_asyncres
dfflibmap -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/87b557cb-99ba-4711-bd34-a2cd629e6710" />

🔁 **Repeat the same flow** for:

* `dff_async_set.v`
* `dff_syncres.v`

---

## ✅ Summary

* `.lib` → Defines timing & power of standard cells under PVT conditions.
* **Hierarchical vs. Flat Synthesis** → Tradeoff between modularity & optimization.
* **Hierarchical** → Faster, modular, easier to debug.
* **Flattened** → Global optimization, but harder to debug and slower.
* **RTL flip-flops** → Correct coding ensures smooth mapping to technology cells.
* **Simulation & synthesis** → Same commands apply to all three DFF variants.
* **Tools used:** Icarus Verilog (simulation), GTKWave (waveform), Yosys (synthesis).
