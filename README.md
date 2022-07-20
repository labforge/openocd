# Labforge OpenOCD port

This is a modified version of [OpenOCD-0.11](https://openocd.org/) 
that adds support for the Toshiba TMPV770-series (aka. Visconti5), 
that is used in [Labforge Bottlenose 2, ICTN, and AMPA products](http://labforge.ca/).

Due to export issues, we could not procure the suggested 
[debug probe](https://www.dts-insight.co.jp/product/ice-jtag/adviceluna2/index.html) 
for the early tape-out and instead build our interfacing from scratch using
OpenOCD.

Since some processor features vary widely from what other existing
SoCs implementations offer in OpenOCD. We decided to fork OpenOCD for now, 
rather than, providing an upstream changes. We are happy to assist anyone who
would like to merge these upstream and reconcile the different semihosting 
implementations between "our" Cortex-R4 and what was present in OpenOCD @ 0.11.

## Major differences
 * [ARM Cortex-R4 semihosting support](https://developer.arm.com/documentation/dui0471/g/Bgbjjgij)
 * Bootstrap files to support SWJ into Cortex-A53 and Cortex-R4 cores of the SoC,
   see ```bottlenose2.cfg``` and ```tmpv770.cfg```.

## Bottlenose, AMPA, ICTN Jtag setup

 * Recommended probe, FTDI-based, for example [Flyswatter2](https://www.tincantools.com/flyswatter2/)
 * Minimal ```openocd.cfg``` example:
```
# Use Flyswatter 2 probe
source [find interface/ftdi/flyswatter2.cfg]
# Configure JTAG clock
# adapter speed 10000
# Configure JTAG topology
transport select jtag
adapter speed 10000

# Load Bottlenose configuration
source [find board/bottlenose2.cfg]

# Start SoC
reset run
```
 * Note the Cortex-A53s are latched from the Cortex-R4 island, so their
   examination is delayed.

----
Original [README](./README)
