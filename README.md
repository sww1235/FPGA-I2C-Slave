This is a simple I2C slave written in VHDL.

The slave supports single and repeated reads/writes, as well as quick
read/writes (master can switch between read and write commands while also
addressing different slaves in quick succession without having to write STOP
command on the bus).

Clock stretching is not implemented, so make sure that data to master is ready
upon request.

## Usage

The module can be used through the following interface:

``` vhdl
  read_req         : out std_logic;
  data_to_master   : in  std_logic_vector(7 downto 0);
  data_valid       : out std_logic;
  data_from_master : out std_logic_vector(7 downto 0));
```

When `read_req` is high, the slave fetches the data `data_to_master` in the same clock cycle, so make sure to set the correct data in advance.
When `data_valid` becomes high, `data_from_master` provides valid data in the same clock cycle.

The slave ignores all master commands directed at addresses that do not match `SLAVE_ADDR` generic.
The 7 bits of the `SLAVE_ADDR` are usually given in the most significant bit (MSB) first format on the master side.
For example, a 7 bit address `0000011` corresponds to decimal 3 on the slave, is actually a 6 on the master because the LSB is never sent.

## Tests

A testbench is provided in the design.
To run the testbench, run `make`, for which you need to have [ghdl](https://github.com/ghdl/ghdl).
If you want no error at the end of a successful simulation in ghdl, in the ghdl library you need to substitute `Error ("simulation stopped by --stop-delta");` by `Info ("simulation stopped by --stop-delta");` in `src/grt/grt-processes.adb`.
The testbench should also work in ModelSim.

Here is an example of a single write: ![](./pics/single-write.png)

And here, an example of 3 quick writes followed by a quick read. Quick means
that the slave address is given only once in the beginning of the first "quick"
operation. ![](./pics/consequtive-3xwrite-and-read.png)
