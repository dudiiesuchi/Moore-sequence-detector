# Moore Sequence Detector (1010) - Non-Overlapping

A Verilog implementation of a Moore finite state machine (FSM) that detects the sequence "1010" in a non-overlapping manner.

## Overview

This project implements a Moore sequence detector that identifies the pattern "1010" in an input bit stream. In a Moore machine, the output depends only on the current state and is independent of the current input, which distinguishes it from a Mealy machine.

### Key Features

- **Non-overlapping detection**: After detecting "1010", the machine resets to the initial state
- **Moore machine architecture**: Output is determined solely by the current state
- **Synchronous design**: Uses positive edge-triggered clock with active-low reset
- **5-state FSM**: Includes an additional state compared to Mealy implementation

## State Diagram

The FSM has 5 states:

- **State A (Initial)**: Waiting for first '1' or staying on '0'
- **State B**: Received '1', waiting for '0'
- **State C**: Received "10", waiting for '1'
- **State D**: Received "101", waiting for '0'
- **State E**: Received "1010" - **OUTPUT = 1** (sequence detected)

## State Transitions

```
State A: 0/A, 1/B
State B: 0/C, 1/B
State C: 0/A, 1/D
State D: 0/E, 1/B
State E: 0/A, 1/B
```

## Files

- `seq_detector_1010.v` - Main module implementation
- `testbench.v` - Testbench for simulation
- `dump.vcd` - Generated waveform file (after simulation)

## Module Interface

```verilog
module seq_detector_1010(
    input bit clk,    // Clock signal
    input bit rst_n,  // Active-low reset
    input bit x,      // Input bit stream
    output reg z      // Output (1 when sequence detected)
);
```

## Parameters

```verilog
parameter A = 4'h1;  // Initial state
parameter B = 4'h2;  // Received '1'
parameter C = 4'h3;  // Received "10"
parameter D = 4'h4;  // Received "101"
parameter E = 4'h5;  // Received "1010" (detection state)
```

## Simulation

### Test Sequence

The testbench applies the following input sequence:
```
Input:  1 1 0 1 0 1 0 1 1 1 0 1 0 1 0
Output: 0 0 0 0 1 0 0 1 0 0 0 0 1 0 0
```

### Running the Simulation

1. Compile the Verilog files:
   ```bash
   iverilog -o sim seq_detector_1010.v testbench.v
   ```

2. Run the simulation:
   ```bash
   ./sim
   ```

3. View waveforms:
   ```bash
   gtkwave dump.vcd
   ```

## Non-Overlapping vs Overlapping

This implementation is **non-overlapping**, meaning:
- After detecting "1010", the FSM returns to the initial state
- The ending "0" of one sequence cannot be the starting "1" of the next sequence
- State E transitions to State A on '0' and State B on '1'

For an overlapping detector, State E would transition to State C on '0' (allowing "10101" to detect two sequences).

## Moore vs Mealy Comparison

| Aspect | Moore Machine | Mealy Machine |
|--------|---------------|---------------|
| Output depends on | Current state only | Current state + input |
| Number of states | 5 states | 4 states |
| Output timing | Synchronized with clock | Can change with input |
| Implementation | More states, simpler output logic | Fewer states, complex output logic |

## Usage Example

```verilog
// Instantiate the detector
seq_detector_1010 detector(
    .clk(clock),
    .rst_n(reset_n),
    .x(input_stream),
    .z(sequence_detected)
);
```

## Timing Specifications

- **Clock Period**: 4 time units (positive edge every 4 units)
- **Reset**: Active-low, applied at simulation start
- **Input Changes**: Every 4 time units after reset deassertion

## Applications

- Pattern recognition in digital communication
- Protocol detection in networking
- Sequence analysis in data processing
- Educational purposes for FSM design
