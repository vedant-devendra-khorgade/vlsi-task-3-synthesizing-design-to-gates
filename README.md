# Task 3: Synthesizing a Full Adder to Standard Cells

## Objective

The objective of this task is to synthesize a Verilog RTL design into a gate-level netlist using a standard-cell library.

The 1-bit Full Adder design from Task 1 was synthesized using the open-source **Yosys** synthesis tool and the **SKY130 HD standard-cell library**.

> **Note:** A company-provided ASIC synthesis environment and standard-cell library were not provided for this internship task. Therefore, the open-source SKY130 library and Yosys were used as an educational substitute. This is not the company's proprietary synthesis flow or library.

---

## Design Used

The design is a 1-bit Full Adder with three inputs and two outputs.

### Inputs

- `A`
- `B`
- `Cin`

### Outputs

- `Sum`
- `Cout`

### RTL Logic

```verilog
assign Sum = A ^ B ^ Cin;
assign Cout = (A & B) | ((A ^ B) & Cin);
```

---

## Tools Used

- **Yosys 0.66** – RTL synthesis
- **ABC** – Technology mapping
- **SKY130 HD Standard Cell Library**
- **Windows + MSYS2 UCRT64** – Development environment

---

## Standard-Cell Library

The following SKY130 HD Liberty file was used for technology mapping:

```text
sky130_fd_sc_hd__tt_025C_1v80.lib
```

This Liberty file contains the timing and area information used by the synthesis technology-mapping process.

The library is referenced locally by the synthesis script and is not included in this repository.

---

## Synthesis Flow

The synthesis process used in this task was:

```text
                Verilog RTL
                    |
                    v
                  Yosys
                    |
                    v
             RTL Processing
                    |
                    v
            Logic Optimization
                    |
                    v
              Technology Map
                    |
                    v
          ABC + SKY130 Library
                    |
                    v
          Gate-Level Netlist
```

---

## Synthesis Script

The synthesis was performed using the following Yosys script:

```tcl
read_verilog full_adder.v
hierarchy -top full_adder

proc
opt

techmap
opt

abc -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib

clean

write_verilog -noattr full_adder_synth.v

stat -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

The script is saved as:

```text
synth.ys
```

### Run Synthesis

The synthesis can be executed using:

```bash
yosys synth.ys
```

---

## Synthesis Result

The Full Adder RTL was successfully synthesized and technology-mapped to SKY130 standard cells.

The ABC technology-mapping results were:

```text
ABC RESULTS:
sky130_fd_sc_hd__maj3_1 cells: 1
sky130_fd_sc_hd__xor3_1 cells: 1
internal signals: 3
input signals: 3
output signals: 2
```

---

## Standard Cells Used

| Standard Cell | Count | Function |
|---|---:|---|
| `sky130_fd_sc_hd__xor3_1` | 1 | Generates `Sum` |
| `sky130_fd_sc_hd__maj3_1` | 1 | Generates `Cout` |

### Total Number of Cells

```text
2 cells
```

---

## Area Result

Yosys reported the following chip area:

```text
Chip area for module '\full_adder': 33.782400
```

The individual cell areas reported by Yosys were:

| Standard Cell | Area |
|---|---:|
| `sky130_fd_sc_hd__maj3_1` | 10.01 |
| `sky130_fd_sc_hd__xor3_1` | 23.773 |
| **Total** | **33.782400** |

The area value is reported in the area units defined by the selected standard-cell library.

---

## Gate-Level Netlist

The synthesized gate-level netlist is stored in:

```text
full_adder_synth.v
```

The generated netlist contains actual SKY130 standard-cell instances.

### Synthesized Netlist

```verilog
module full_adder(A, B, Cin, Sum, Cout);

  input A;
  wire A;

  input B;
  wire B;

  input Cin;
  wire Cin;

  output Sum;

  sky130_fd_sc_hd__xor3_1 _0_ (
    .A(Cin),
    .B(A),
    .C(B),
    .X(Sum)
  );

  sky130_fd_sc_hd__maj3_1 _1_ (
    .A(Cin),
    .B(A),
    .C(B),
    .X(Cout)
  );

endmodule
```

This demonstrates the conversion from RTL logic into a technology-specific gate-level implementation.

---

## RTL to Standard-Cell Mapping

The original RTL equations were:

```text
Sum  = A ^ B ^ Cin
Cout = (A & B) | ((A ^ B) & Cin)
```

After synthesis:

```text
Sum  -> sky130_fd_sc_hd__xor3_1
Cout -> sky130_fd_sc_hd__maj3_1
```

Therefore, the complete Full Adder was implemented using only two SKY130 standard cells.

---

## Verification of Synthesis

The Yosys synthesis report confirmed:

```text
=== full_adder ===

5 wires
5 wire bits
5 public wires
5 public wire bits
5 ports
5 port bits

2 cells
1 sky130_fd_sc_hd__maj3_1
1 sky130_fd_sc_hd__xor3_1
```

The final area report was:

```text
Chip area for module '\full_adder': 33.782400
  wire Sum;

  wire Cout;
```

The report also confirmed:

```text

sequential elements: 0.000000 (0.00%)
```

This is expected because the Full Adder is a purely combinational circuit.

---

## Files in This Repository

```text
Task 3
├── README.md
├── full_adder.v
├── full_adder_synth.v
└── synth.ys
```

### File Description

| File | Description |
|---|---|
| `README.md` | Task documentation and synthesis results |
| `full_adder.v` | Original 1-bit Full Adder RTL |
| `full_adder_synth.v` | Technology-mapped gate-level netlist |
| `synth.ys` | Yosys synthesis script |

The SKY130 Liberty library is referenced by the synthesis script but is not included in this repository.

---

## Timing Analysis Note

The task description mentions applying timing constraints such as **1 GHz** and generating a timing report.

The available internship environment did not provide the company's ASIC synthesis/STA tools or company standard-cell timing environment.

Therefore:

- Standard-cell synthesis was successfully completed.
- Technology mapping was successfully completed.
- A gate-level netlist was generated.
- Area statistics were generated.
- A company-specific 1 GHz timing result is **not claimed**.
- No fabricated timing numbers are included in this submission.

The reported results are limited to the synthesis and technology-mapping results obtained using Yosys, ABC, and the SKY130 HD standard-cell library.

---

## Important Note About the Standard-Cell Library

The internship task refers to using a company-provided standard-cell library.

No company-provided synthesis environment or standard-cell library was available for this task.

Therefore, this project uses:

```text
Yosys
+
ABC
+
SKY130 HD Standard-Cell Library
```

as an open-source educational substitute.

The SKY130 library is **not claimed to be the company's proprietary library**.

---

## Conclusion

The 1-bit Full Adder RTL was successfully synthesized using the open-source Yosys synthesis tool.

ABC successfully performed technology mapping using the SKY130 HD standard-cell library.

The final implementation contains:

```text
1 × sky130_fd_sc_hd__xor3_1
1 × sky130_fd_sc_hd__maj3_1
```

with:

```text
Total cells = 2
Reported chip area = 33.782400
Sequential elements = 0
```

The resulting gate-level netlist is available in:

```text
full_adder_synth.v
```

This task demonstrates the complete basic flow from:

```text
RTL Design
    ↓
Logic Synthesis
    ↓
Technology Mapping
    ↓
Standard Cells
    ↓
Gate-Level Netlist
```

using an open-source ASIC synthesis flow.
