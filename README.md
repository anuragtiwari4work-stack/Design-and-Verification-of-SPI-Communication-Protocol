# SPI Communication System

A complete SystemVerilog implementation of SPI (Serial Peripheral Interface) communication protocol with comprehensive testbench verification environment.

## Overview

This project implements a full SPI communication system consisting of:
- **SPI Master** - Initiates communication and generates clock signals
- **SPI Slave** - Receives data from the master
- **Comprehensive Testbench** - UVM-style verification environment with layered architecture

## Architecture

### Hardware Modules

#### SPI Master (`spi_master`)
- Generates SPI clock (sclk) from system clock
- Implements state machine for SPI protocol
- Transmits 12-bit data LSB first
- Controls chip select (cs) and master out slave in (mosi) signals

**States:**
- `idle` - Waiting for new data
- `send` - Transmitting data bits
- `comp` - Transmission complete

#### SPI Slave (`spi_slave`) 
- Receives data on rising edge of sclk
- Reconstructs 12-bit data from serial input
- Generates done signal when transmission complete
- Implements start detection and data reading states

#### Top Module (`top`)
- Integrates master and slave modules
- Provides system-level interface
- Connects internal SPI signals between master and slave

### Verification Environment

The testbench follows UVM-style layered architecture:

#### Transaction Class
- Defines data packet structure
- Contains 12-bit input/output data and control signals
- Implements copy function for data integrity

#### Generator Class  
- Creates randomized transactions
- Controls test stimulus generation
- Synchronizes with other testbench components

#### Driver Class
- Applies stimulus to DUT interfaces
- Handles reset sequences
- Converts transactions to pin-level activity

#### Monitor Class
- Observes DUT outputs
- Captures response data
- Forwards data to scoreboard for checking

#### Scoreboard Class
- Compares expected vs actual results
- Reports pass/fail status
- Implements data matching logic

#### Environment Class
- Instantiates and connects all verification components
- Manages test execution flow
- Coordinates communication between components

## Signal Descriptions

| Signal | Direction | Width | Description |
|--------|-----------|-------|-------------|
| `clk` | Input | 1 | System clock |
| `rst` | Input | 1 | Active high reset |
| `newd` | Input | 1 | New data flag |
| `din` | Input | 12 | Data to transmit |
| `dout` | Output | 12 | Received data |
| `done` | Output | 1 | Transmission complete flag |
| `sclk` | Internal | 1 | SPI clock (clk/20) |
| `cs` | Internal | 1 | Chip select (active low) |
| `mosi` | Internal | 1 | Master out, slave in |

## Key Features

- **12-bit Data Transfer** - Supports 12-bit parallel to serial conversion
- **Configurable Clock** - Master generates sclk at clk/20 frequency  
- **LSB First Transmission** - Data transmitted least significant bit first
- **Handshaking Protocol** - Uses newd and done signals for flow control
- **State Machine Based** - Robust finite state machine implementation
- **Comprehensive Verification** - Full testbench with stimulus generation and checking

## Usage

### Running the Testbench

```bash
# Compile and run with your SystemVerilog simulator
# For example, with ModelSim/QuestaSim:
vlog *.sv
vsim -c tb -do "run -all"

# Or with other simulators:
xvlog *.sv && xelab tb && xsim tb -R
```

### Customizing Tests

Modify the test parameters in the testbench:

```systemverilog
initial begin
    env = new(vif);
    env.gen.count = 4;  // Number of transactions
    env.run();
end
```

### Adding New Test Scenarios

1. Create new transaction constraints in the generator
2. Modify the scoreboard for custom checking
3. Add new test cases in the environment

## Timing Diagrams

### SPI Transaction Timing
```
clk     __|‾|__|‾|__|‾|__|‾|__|‾|__|‾|__|‾|__|‾|__
newd    _____|‾‾‾‾‾‾‾|___________________
sclk    ________|‾|_|‾|_|‾|_|‾|_|‾|_____
cs      ‾‾‾‾‾‾‾‾|______|‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾
mosi    --------< D0 D1 D2 D3 D4 >------
done    _____________________|‾‾‾‾|____
```

## File Structure

```
├── spi_master.sv       # SPI master module
├── spi_slave.sv        # SPI slave module  
├── top.sv             # Top-level integration
├── testbench.sv       # Complete verification environment
├── spi_if.sv          # SystemVerilog interface
└── README.md          # This file
```

## Design Considerations

- **Clock Domain** - Master operates on system clock, slave on generated sclk
- **Reset Strategy** - Synchronous reset for predictable behavior  
- **Data Width** - Fixed 12-bit width, easily parameterizable
- **Error Handling** - Robust state machines with default cases
- **Verification Coverage** - Randomized stimulus with comprehensive checking

## Known Limitations

- Fixed data width (12 bits)
- Single master, single slave configuration
- No support for SPI modes (CPOL/CPHA variations)
- Fixed clock divider ratio

## Future Enhancements

- [ ] Parameterizable data width
- [ ] Multiple SPI mode support
- [ ] Multi-slave capability with expanded CS logic
- [ ] Configurable clock divider
- [ ] Error injection and recovery testing
- [ ] Performance optimization

## Dependencies

- SystemVerilog simulator (ModelSim, QuestaSim, VCS, etc.)
- Support for SystemVerilog interfaces and classes
- Waveform viewer for debugging (optional)

## License

This project is provided as-is for educational and development purposes.

## Contributing

Feel free to submit issues, fork the repository, and create pull requests for any improvements.
