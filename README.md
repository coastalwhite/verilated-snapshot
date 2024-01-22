# Verilated Snapshot

This provides code to make snapshots of a Verilator model state in the process
memory. This allows you to restore to an earlier model state without ever
writing to a file.

Important notice: this cannot be used in conjunction with multithreaded
simulations (i.e. the `--threads <N>` flag, where `N > 1`). This does allow for
multiple simulations to happen in parallel with one thread per simulation.

## Usage

There are two stages to incorporating this. You have to include it in your
testbench and you have to add some flags.

### Testbench

To incorporate this is into your design, you have to include the header file.
Then you can use the `VerilatedSnapshot` to make a snapshot and use the
`VerilatedSnapshotRestore` to restore to an earlier snapshot.

```cpp
#include <verilated_snapshot.hpp>

// Here `dut` is the name of your top-level module.
void simulate_many(Vdut *dut) {
    VerilatedSnapshot snapshot;

    // Call a procedure that runs to a specific point in simulation
    warmup(dut);

    snapshot << *dut;
    VerilatedSnapshotRestore restore(&snapshot);

    for (int i = 0; i < 1000; i++) {
        restore >> *dut;
        simulate_one(dut);
    }
}
```

For more usage information look at the
[`verilated_snapshot.hpp`](./verilated_snapshot.hpp) file.

### Compilation

```bash
# 1. Clone this repository
git clone https://github.com/coastalwhite/verilated-snapshot.git
cd verilated-snapshot

export VERILATED_SNAPSHOT_DIR="$PWD"

cd path/to/your/project

# 2. Add flags to your verilator call
verilator <USUAL FLAGS> --savable -CFLAGS "-I$VERILATED_SNAPSHOT_DIR"
```

# License

This is licensed under an MIT license.