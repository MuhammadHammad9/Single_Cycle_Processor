# Single Cycle Processor

A complete implementation of a Single Cycle Processor in Verilog/SystemVerilog. This processor executes one instruction per clock cycle, making it simple to understand and ideal for learning computer architecture fundamentals.

## Overview

A Single Cycle Processor is a type of CPU architecture where each instruction is executed in a single clock cycle. While not the most efficient design (as the clock period must accommodate the slowest instruction), it provides an excellent foundation for understanding how processors work.

## Architecture

The Single Cycle Processor consists of the following main components:

### 1. Program Counter (PC)
- Holds the address of the current instruction
- Increments by 4 (for 32-bit word-aligned instructions) after each instruction
- Can be modified by branch and jump instructions

### 2. Instruction Memory
- Read-only memory that stores the program instructions
- Takes the PC value as input and outputs the corresponding instruction

### 3. Register File
- Contains 32 general-purpose registers (for RISC-V/MIPS architectures)
- Supports two simultaneous read operations and one write operation
- Register $0 (or x0 in RISC-V) is hardwired to zero

### 4. ALU (Arithmetic Logic Unit)
- Performs arithmetic operations (ADD, SUB, MUL, DIV)
- Performs logical operations (AND, OR, XOR, NOT)
- Performs comparison operations (SLT - Set Less Than)
- Generates zero flag and other condition flags

### 5. Data Memory
- Read/Write memory for storing and loading data
- Used by load (LW) and store (SW) instructions

### 6. Control Unit
- Decodes the instruction opcode
- Generates control signals for all other components
- Controls data flow through multiplexers

### 7. Sign Extend Unit
- Extends immediate values from 16-bit to 32-bit (sign extension)
- Used for I-type instructions

## Data Path

```
                    +4
                     |
    +----------------+----------------+
    |                |                |
    v                v                |
+-------+      +----------+           |
|  PC   |----->| Inst Mem |           |
+-------+      +----------+           |
    |               |                 |
    |               v                 |
    |         +----------+            |
    |         |  Control |            |
    |         +----------+            |
    |               |                 |
    |               v                 |
    |         +----------+      +-----+-----+
    |         | Reg File |----->|    ALU    |
    |         +----------+      +-----------+
    |               |                 |
    |               v                 v
    |         +----------+      +-----------+
    |         | Sign Ext |      | Data Mem  |
    |         +----------+      +-----------+
    |               |                 |
    +---------------+-----------------+
```

## Supported Instructions

### R-Type Instructions (Register-Register Operations)
| Instruction | Format | Description |
|-------------|--------|-------------|
| ADD | `ADD rd, rs, rt` | rd = rs + rt |
| SUB | `SUB rd, rs, rt` | rd = rs - rt |
| AND | `AND rd, rs, rt` | rd = rs & rt |
| OR | `OR rd, rs, rt` | rd = rs \| rt |
| XOR | `XOR rd, rs, rt` | rd = rs ^ rt |
| SLT | `SLT rd, rs, rt` | rd = (rs < rt) ? 1 : 0 |
| SLL | `SLL rd, rt, shamt` | rd = rt << shamt (Shift Left Logical) |
| SRL | `SRL rd, rt, shamt` | rd = rt >> shamt (Shift Right Logical) |

### I-Type Instructions (Immediate Operations)
| Instruction | Format | Description |
|-------------|--------|-------------|
| ADDI | `ADDI rt, rs, imm` | rt = rs + imm |
| ANDI | `ANDI rt, rs, imm` | rt = rs & imm |
| ORI | `ORI rt, rs, imm` | rt = rs \| imm |
| LW | `LW rt, offset(rs)` | rt = Memory[rs + offset] |
| SW | `SW rt, offset(rs)` | Memory[rs + offset] = rt |
| BEQ | `BEQ rs, rt, offset` | Branch if rs == rt |
| BNE | `BNE rs, rt, offset` | Branch if rs != rt |

### J-Type Instructions (Jump Operations)
| Instruction | Format | Description |
|-------------|--------|-------------|
| J | `J target` | PC = target address |
| JAL | `JAL target` | $ra = PC + 4; PC = target |

## How It Works

### Instruction Fetch (IF)
1. The Program Counter (PC) provides the address of the instruction to fetch
2. The Instruction Memory outputs the 32-bit instruction at that address
3. PC is incremented by 4 to point to the next instruction

### Instruction Decode (ID)
1. The Control Unit decodes the opcode field of the instruction
2. Register addresses are extracted from the instruction
3. The Register File reads the source registers
4. Immediate values are sign-extended if needed

### Execute (EX)
1. The ALU performs the operation specified by the instruction
2. For R-type: operates on two register values
3. For I-type: operates on a register value and an immediate
4. For branches: computes the branch target address

### Memory Access (MEM)
1. Load instructions read data from Data Memory
2. Store instructions write data to Data Memory
3. Other instructions pass through this stage

### Write Back (WB)
1. Results are written back to the Register File
2. For ALU operations: the ALU result is written
3. For load instructions: the memory data is written

## Control Signals

| Signal | Description |
|--------|-------------|
| RegDst | Selects the destination register (rt or rd) |
| RegWrite | Enables writing to the Register File |
| ALUSrc | Selects ALU second operand (register or immediate) |
| ALUOp | Specifies the ALU operation |
| MemRead | Enables reading from Data Memory |
| MemWrite | Enables writing to Data Memory |
| MemToReg | Selects data source for register write (ALU or Memory) |
| Branch | Indicates a branch instruction |
| Jump | Indicates a jump instruction |

## File Structure

```
Single_Cycle_Processor/
├── README.md           # This file
├── src/                # Source files
│   ├── PC.v            # Program Counter
│   ├── InstructionMem.v # Instruction Memory
│   ├── RegisterFile.v  # Register File
│   ├── ALU.v           # Arithmetic Logic Unit
│   ├── DataMemory.v    # Data Memory
│   ├── ControlUnit.v   # Control Unit
│   ├── SignExtend.v    # Sign Extension Unit
│   ├── Mux.v           # Multiplexers
│   └── Processor.v     # Top-level module
├── test/               # Testbench files
│   └── Processor_tb.v  # Processor testbench
└── docs/               # Documentation
    └── diagrams/       # Architecture diagrams
```

## Simulation

To simulate the processor:

```bash
# Using Icarus Verilog
iverilog -o processor_tb src/*.v test/Processor_tb.v
vvp processor_tb

# Using ModelSim
vlog src/*.v test/Processor_tb.v
vsim -c Processor_tb -do "run -all"
```

## Example Program

Here's a simple example program that can run on this processor:

```assembly
# Calculate the sum of numbers 1 to 10
# Result is stored in $t0 and written to memory

    addi $t0, $zero, 0    # $t0 = 0 (sum)
    addi $t1, $zero, 1    # $t1 = 1 (counter)
    addi $t3, $zero, 11   # $t3 = 11 (limit)
    
loop:
    add  $t0, $t0, $t1    # sum += counter
    addi $t1, $t1, 1      # counter++
    bne  $t1, $t3, loop   # if counter != 11, repeat
    
    sw   $t0, 0($zero)    # Store result to memory
```

## Performance Characteristics

- **CPI (Cycles Per Instruction)**: 1 (by definition)
- **Clock Period**: Must be long enough for the slowest instruction
- **Throughput**: 1 instruction per cycle
- **Latency**: 1 cycle per instruction

## Advantages
- Simple design, easy to understand and implement
- Good for educational purposes
- Predictable timing (exactly one cycle per instruction)

## Disadvantages
- Inefficient use of hardware (fast instructions take as long as slow ones)
- Long clock period determined by the slowest instruction (typically LW)
- Not suitable for high-performance applications

## Future Improvements

This design can be extended to:
- **Pipelined Processor**: Execute multiple instructions simultaneously in different stages
- **Multi-Cycle Processor**: Break down instructions into multiple shorter cycles
- **Superscalar Processor**: Execute multiple instructions per cycle

## References

- Patterson, D. A., & Hennessy, J. L. (2017). *Computer Organization and Design: The Hardware/Software Interface*. Morgan Kaufmann.
- MIPS Architecture Documentation
- RISC-V Specification

## License

This project is for educational purposes. Feel free to use and modify for learning.