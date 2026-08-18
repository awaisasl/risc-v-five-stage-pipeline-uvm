# risc-v-five-stage-pipeline-uvm
SystemVerilog five-stage RISC-V pipelined processor with hazard detection, forwarding, branch handling, and a Cadence Xcelium/UVM verification environment

<p align="center">
  <b>SystemVerilog RTL Implementation + UVM Functional Verification</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/HDL-SystemVerilog-blue" alt="SystemVerilog">
  <img src="https://img.shields.io/badge/Architecture-RISC--V-orange" alt="RISC-V">
  <img src="https://img.shields.io/badge/Pipeline-5--Stage-green" alt="5 Stage Pipeline">
  <img src="https://img.shields.io/badge/Verification-UVM-purple" alt="UVM">
  <img src="https://img.shields.io/badge/Simulator-Cadence%20Xcelium-red" alt="Cadence Xcelium">
  <img src="https://img.shields.io/badge/Platform-Linux-lightgrey" alt="Linux">
</p>

---

## Overview

This project presents a **32-bit RISC-V five-stage pipelined processor** implemented in **SystemVerilog RTL** and verified using a dedicated **Universal Verification Methodology (UVM)** environment with **Cadence Xcelium**.

The processor is organized around the classical five-stage RISC pipeline:

```text
        ┌────────┐
        │   IF   │  Instruction Fetch
        └───┬────┘
            │
        ┌───▼────┐
        │   ID   │  Instruction Decode
        └───┬────┘
            │
        ┌───▼────┐
        │   EX   │  Execute
        └───┬────┘
            │
        ┌───▼────┐
        │  MEM   │  Memory Access
        └───┬────┘
            │
        ┌───▼────┐
        │   WB   │  Write Back
        └────────┘


Project Highlights
32-bit RISC-V processor architecture
Five-stage instruction pipeline
SystemVerilog RTL implementation
Dedicated pipeline registers
Instruction memory
Data memory
Register file
Immediate generator
Control unit
ALU
Branch comparator
Hazard detection unit
Data forwarding unit
Load-use hazard handling
Pipeline stalls
Branch redirection
Wrong-path instruction flushing
UVM-based verification environment
UVM driver
UVM monitor
UVM sequencer
UVM sequence
UVM scoreboard
UVM agent
UVM environment
UVM test
Reset sequence
Architectural register checking
Data-memory checking
Hazard/forwarding verification
Cadence Xcelium simulation
Linux command-line simulation
Xcelium GUI support
Waveform generation and analysis

Processor Architecture

The processor follows a conventional five-stage pipeline.

1. Instruction Fetch — IF

The IF stage:

Maintains the program counter
Fetches instructions from instruction memory
Calculates the next sequential PC
Handles PC updates
Supports branch redirection

Main modules:

program_counter.sv
inst_mem.sv
2. Instruction Decode — ID

The ID stage:

Decodes the fetched instruction
Reads operands from the register file
Generates immediate values
Generates control signals
Detects applicable pipeline hazards

Main modules:

reg_file.sv
imm_gen.sv
control_unit.sv
hazard_detection.sv
IF_ID_Stage.sv
3. Execute — EX

The EX stage performs:

Arithmetic operations
Logical operations
Comparisons
Address calculations
Branch comparisons
Forwarded operand selection

Main modules:

alu_logic.sv
branch_comp.sv
fwd_logic.sv
ID_EX_Stage.sv
4. Memory Access — MEM

The MEM stage handles:

Load operations
Store operations
Data-memory access
Memory-stage pipeline control

Main modules:

data_mem.sv
EX_MEM_Stage.sv
5. Write Back — WB

The WB stage:

Selects the appropriate result
Writes results back into the register file
Completes the instruction execution path

Main module:

MEM_WB_Stage.sv
🔄 Pipeline Structure

The pipeline registers separate the five execution stages:

        IF
         │
         ▼
   ┌────────────┐
   │  IF / ID   │
   └─────┬──────┘
         │
         ▼
        ID
         │
         ▼
   ┌────────────┐
   │  ID / EX   │
   └─────┬──────┘
         │
         ▼
        EX
         │
         ▼
   ┌────────────┐
   │ EX / MEM   │
   └─────┬──────┘
         │
         ▼
        MEM
         │
         ▼
   ┌────────────┐
   │ MEM / WB   │
   └─────┬──────┘
         │
         ▼
        WB

Pipeline register implementations:

IF_ID_Stage.sv
ID_EX_Stage.sv
EX_MEM_Stage.sv
MEM_WB_Stage.sv
Hazard Handling

Pipeline hazards are handled using dedicated hardware logic.

Data Hazards

The processor implements forwarding paths to reduce unnecessary pipeline stalls.

Forwarding logic handles dependencies involving results available in later pipeline stages.

The forwarding unit is implemented in:

rtl/fwd_logic.sv
Load-Use Hazard

A load instruction may not have its result available soon enough for the immediately following dependent instruction.

The processor therefore includes a dedicated hazard detection mechanism:

rtl/hazard_detection.sv

When required, the pipeline can:

Detect dependency
      ↓
Insert stall
      ↓
Prevent incorrect execution
      ↓
Resume pipeline execution
Branch Handling

Branch instructions require the processor to redirect execution when a branch is taken.

The design contains:

branch_comp.sv

for branch comparison and associated control logic for redirecting the program counter.

Wrong-path instructions can be flushed when a branch changes the control-flow path.

RTL Design

The main processor RTL is located under:

rtl/
RTL Modules
Module	Function
top.sv	Processor top-level integration
program_counter.sv	Program counter
inst_mem.sv	Instruction memory
data_mem.sv	Data memory
reg_file.sv	Register file
imm_gen.sv	Immediate generation
control_unit.sv	Instruction/control decoding
alu_logic.sv	Arithmetic and logical operations
branch_comp.sv	Branch comparison
fwd_logic.sv	Data forwarding
hazard_detection.sv	Pipeline hazard detection
pipeline_register.sv	Generic pipeline register support
IF_ID_Stage.sv	IF/ID pipeline stage
ID_EX_Stage.sv	ID/EX pipeline stage
EX_MEM_Stage.sv	EX/MEM pipeline stage
MEM_WB_Stage.sv	MEM/WB pipeline stage
UVM Verification Environment

The processor is verified using a dedicated SystemVerilog UVM environment.

The UVM environment is located in:

uvm/

The verification architecture follows the standard UVM structure:

                    ┌─────────────────┐
                    │    cpu_test     │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │     cpu_env     │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │    cpu_agent    │
                    └──────┬─────┬────┘
                           │     │
                 ┌─────────┘     └─────────┐
                 ▼                         ▼
          ┌─────────────┐           ┌─────────────┐
          │  Sequencer  │           │   Monitor   │
          └──────┬──────┘           └──────┬──────┘
                 │                         │
          ┌──────▼──────┐                  │
          │   Driver    │                  │
          └──────┬──────┘                  │
                 │                         │
                 └──────────┬──────────────┘
                            ▼
                     ┌──────────────┐
                     │  Scoreboard  │
                     └──────────────┘
UVM Components
Component	Purpose
cpu_uvm_pkg.sv	UVM package
cpu_if.sv	Processor verification interface
cpu_seq_item.sv	Transaction object
cpu_sample.sv	Sample/observation data
cpu_reset_sequence.sv	Processor reset sequence
cpu_sequencer.sv	Transaction sequencing
cpu_driver.sv	Drives testbench transactions
cpu_monitor.sv	Observes DUT behavior
cpu_scoreboard.sv	Checks expected vs actual behavior
cpu_agent.sv	UVM agent
cpu_env.sv	UVM environment
cpu_test.sv	Top-level UVM test
Verification Coverage

The verification environment checks important processor behaviors including:

Arithmetic Operations
ALU operations
Register-to-register execution
Immediate operations
Memory Operations
Load operations
Store operations
Data-memory behavior
Pipeline Hazards
Data dependencies
Load-use hazards
Required pipeline stalls
Forwarding
EX/MEM forwarding
MEM/WB forwarding
Store-data forwarding
Control Hazards
Taken branches
Branch redirection
Wrong-path instruction flushing
Not-taken branch behavior
Special Instructions
LUI
AUIPC
Architectural State

The scoreboard verifies:

Register-file results
Data-memory results
Expected processor behavior
Pipeline control events
Tools & Technologies
Hardware Description Language

SystemVerilog

Used for:

RTL design
Pipeline implementation
Memory models
Testbench
UVM verification components
Processor Architecture

RISC-V

The project implements a 32-bit RISC-V processor datapath and control structure.

Verification Methodology

UVM — Universal Verification Methodology

Used for:

Transaction-based verification
Driver/monitor architecture
Sequencing
Scoreboarding
Environment construction
Automated checking
Simulator

Cadence Xcelium

The project uses Cadence Xcelium's xrun simulation flow.

Operating System

Linux

The project includes shell scripts for command-line execution.

Waveform Debugging

Cadence SimVision / Xcelium waveform database

Waveform support is provided through:

scripts/waves.tcl
Repository Structure
risc-v-five-stage-pipeline-uvm/
│
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   └── PULL_REQUEST_TEMPLATE.md
│
├── rtl/
│   ├── pipeline_register.sv
│   ├── program_counter.sv
│   ├── inst_mem.sv
│   ├── data_mem.sv
│   ├── reg_file.sv
│   ├── imm_gen.sv
│   ├── alu_logic.sv
│   ├── branch_comp.sv
│   ├── control_unit.sv
│   ├── IF_ID_Stage.sv
│   ├── ID_EX_Stage.sv
│   ├── EX_MEM_Stage.sv
│   ├── MEM_WB_Stage.sv
│   ├── fwd_logic.sv
│   ├── hazard_detection.sv
│   └── top.sv
│
├── uvm/
│   ├── cpu_if.sv
│   ├── cpu_uvm_pkg.sv
│   ├── cpu_seq_item.sv
│   ├── cpu_sample.sv
│   ├── cpu_reset_sequence.sv
│   ├── cpu_sequencer.sv
│   ├── cpu_driver.sv
│   ├── cpu_monitor.sv
│   ├── cpu_scoreboard.sv
│   ├── cpu_agent.sv
│   ├── cpu_env.sv
│   └── cpu_test.sv
│
├── tb/
│   └── cpu_uvm_tb.sv
│
├── legacy_tb/
│   └── top_tb.sv
│
├── scripts/
│   └── waves.tcl
│
├── filelist.f
├── filelist_plain.f
├── run_xcelium.sh
├── run_plain_tb.sh
├── clean.sh
├── README.md
├── LICENSE
├── CONTRIBUTING.md
└── .gitignore
Simulation Requirements

Before running the project, make sure the following are available:

Requirement	Purpose
Linux	Simulation environment
Cadence Xcelium	RTL/UVM simulation
xrun	Xcelium command-line simulator
SystemVerilog support	RTL compilation
UVM	Verification framework
SimVision	Optional waveform/debug GUI

Check Xcelium:

xrun -version

Check whether xrun is available:

which xrun
Running the UVM Testbench

Clone the repository:

git clone <YOUR_REPOSITORY_URL>
cd risc-v-five-stage-pipeline-uvm

Make the scripts executable:

chmod +x run_xcelium.sh
chmod +x run_plain_tb.sh
chmod +x clean.sh

Run the UVM environment:

./run_xcelium.sh

The script invokes Cadence Xcelium using the project filelist and starts:

cpu_uvm_tb

with:

+UVM_TESTNAME=cpu_test

The simulation log is generated at:

logs/xrun_uvm.log
Run in Xcelium GUI

To launch the simulation in GUI mode:

./run_xcelium.sh gui

This enables:

-gui
-access +rwc

which allows RTL signal inspection and interactive debugging.

Run With Waveforms

To run the UVM environment with waveform generation:

./run_xcelium.sh waves

The waveform database is generated as:

waves.shm

It can be opened using:

simvision waves.shm &

This allows inspection of:

Clock
Reset
Pipeline registers
Processor state
Internal RTL signals
Hazard signals
Forwarding signals
Branch control
Register/memory activity
Run the Directed Testbench

The repository also contains a non-UVM directed testbench.

Run:

./run_plain_tb.sh

This provides a simpler simulation path for debugging the RTL independently from the UVM environment.

Clean Generated Files

To remove generated simulation artifacts:

./clean.sh
UVM Test Result

A successful UVM run should produce a report with:

UVM_ERROR : 0
UVM_FATAL : 0

The verification environment also reports processor-specific pass/fail information through the UVM report mechanism.

Example successful verification indicators include:

CPU_PASS
CPU_STATS
MEM_CHECK
REG_CHECK
TEST_DONE

The complete simulator output is available in:

logs/xrun_uvm.log
Xcelium UVM Configuration

The default simulation script uses Cadence's UVM integration:

-uvm

The script also supports explicitly specifying the UVM installation through:

UVM_HOME_OPT

For example:

UVM_HOME_OPT=CDNS-1.2 ./run_xcelium.sh

Use the UVM installation appropriate for your Cadence environment.

Verification Flow

The complete verification flow is:

             SystemVerilog RTL
                    │
                    ▼
            ┌───────────────┐
            │   RISC-V CPU  │
            │  Five Stages  │
            └───────┬───────┘
                    │
                    │ DUT Signals
                    ▼
            ┌───────────────┐
            │    cpu_if     │
            └───────┬───────┘
                    │
                    ▼
            ┌───────────────┐
            │    Monitor    │
            └───────┬───────┘
                    │
                    ▼
            ┌───────────────┐
            │   Scoreboard  │
            └───────┬───────┘
                    │
                    ▼
            Expected vs Actual
                    │
                    ▼
             PASS / FAIL
Project Objectives

The project demonstrates the implementation and verification of a pipelined processor at RTL level.

The major objectives are:

Design a five-stage RISC-V processor.
Implement the processor using synthesizable SystemVerilog RTL.
Implement pipeline registers between execution stages.
Handle data hazards using forwarding.
Handle load-use hazards using pipeline stalls.
Handle control hazards using branch redirection and flushing.
Develop a reusable UVM verification environment.
Implement transaction-level stimulus and monitoring.
Implement a scoreboard for architectural checking.
Verify processor behavior using Cadence Xcelium.
Design Methodology

The project follows a modular RTL design methodology.

Each major processor function is separated into an independent SystemVerilog module.

Processor
│
├── Control Path
│   ├── Control Unit
│   ├── Hazard Detection
│   └── Branch Comparator
│
├── Datapath
│   ├── Register File
│   ├── Immediate Generator
│   ├── ALU
│   └── Memories
│
├── Pipeline
│   ├── IF/ID
│   ├── ID/EX
│   ├── EX/MEM
│   └── MEM/WB
│
└── Pipeline Control
    ├── Forwarding
    ├── Stalling
    └── Flushing

This modular structure makes the processor easier to debug, verify, extend, and synthesize.

Educational Scope

This project can be used as a reference for studying:

Computer Architecture
RISC-V ISA concepts
CPU datapath design
Pipeline architecture
RTL design
SystemVerilog
Pipeline hazards
Data forwarding
Pipeline stalls
Branch handling
Functional verification
UVM
Scoreboarding
Simulation-based verification
Cadence Xcelium
Current Scope & Limitations

This repository focuses on the RTL processor and its simulation/UVM verification environment.

The repository does not include:

FPGA-specific Vivado project files
FPGA board constraint files
FPGA bitstreams
Physical FPGA programming files
Commercial Cadence software

Cadence Xcelium and associated verification tools must be installed separately.

The UVM testbench uses the current DUT interface and simulation hierarchy to initialize and observe processor state. This is intended for simulation and verification rather than as an FPGA programming interface.

Future Work

Potential extensions include:

Additional RISC-V instruction support
Expanded ISA coverage
More extensive constrained-random testing
Functional coverage
Assertion-based verification
Coverage-driven verification
Formal verification
Performance counters
Cache implementation
Branch prediction
CSR support
Interrupt/exception handling
AXI or other bus interface
FPGA-specific top-level wrapper
FPGA board deployment
Hardware/software co-verification

This project uses the following technologies and methodologies:

RISC-V architecture
SystemVerilog
Universal Verification Methodology (UVM)
Cadence Xcelium
Cadence SimVision
Linux
