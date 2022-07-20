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
   see ```labforge-bottlenose2.cfg``` and ```tmpv770.cfg```.

## Bottlenose, AMPA, ICTN Jtag setup
 * Build OpenOCD with FTDI support, example
```bash
# Bootstap dependencies
./bootstrap

# Autotools
./configure --prefix=/opt/openocd

### VERY IMPORTANT ###
# Confirm that FTDI, MPSSE targets work, you should see this
# ....
--------------------------------------------------
MPSSE mode of FTDI based devices        yes (auto)
ST-Link Programmer                      yes (auto)
TI ICDI JTAG Programmer                 yes (auto)
Keil ULINK JTAG Programmer              yes (auto)
Altera USB-Blaster II Compatible        yes (auto)
Bitbang mode of FT232R based devices    yes (auto)
Versaloon-Link JTAG Programmer          yes (auto)
TI XDS110 Debug Probe                   yes (auto)
OSBDM (JTAG only) Programmer            yes (auto)
eStick/opendous JTAG Programmer         yes (auto)
Andes JTAG Programmer                   yes (auto)
USBProg JTAG Programmer                 no
Raisonance RLink JTAG Programmer        no
Olimex ARM-JTAG-EW Programmer           no
CMSIS-DAP Compliant Debugger            no
Cypress KitProg Programmer              no
Altera USB-Blaster Compatible           no
ASIX Presto Adapter                     no
OpenJTAG Adapter                        no
SEGGER J-Link Programmer                yes (auto)


# Build
make -j4
sudo make install

# Setup the udev rules from
sudo cp contrib/60-openocd.rules /etc/udev/rules.d
sudo service udev reload
```
 * Recommended probe: FTDI-based, for example [Flyswatter2](https://www.tincantools.com/flyswatter2/)
 * Minimal ```openocd.cfg``` example:
```
# Use Flyswatter 2 probe
source [find interface/ftdi/flyswatter2.cfg]
# Configure JTAG clock and topology
transport select jtag
adapter speed 10000

# Load Bottlenose configuration
source [find board/labforge_bottlenose2.cfg]

# Start SoC
reset run
```
 * Note the Cortex-A53s are latched from the Cortex-R4 island, so their
   examination is delayed.

## Warranty Note

Please note that in-line with [OpenOCDs license](./COPYING) this code and
information is provided with no warranty. Should you modify or run non 
Labforge code on one of our devices, we cannot guarantee the quality of 
outcomes, nor dedicate support resources. Please [contact us](http://labforge.ca/)
for consulting inquires, or bootstrap support for custom platforms based on
the AMPA SoM. 

Since voltage domains are configured at the boot-phase of our 
products, you could cause permanent damage to your device.

----
Original [README](./README)
