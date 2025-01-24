## File Flow

This diagram is the flow of files from design to bit file. (bit file is the data go to fpga as programe)

We forcus on the Green part, Most of them are Python code.

I know simulation is important,

But now let's just rock the chip, without worried too much about simulation.

## Begin

Let's write gateware use python with Litex.

https://github.com/enjoy-digital/litex

I like FPGA very much, and I use it since XC3000 seriese came out.

In the old time, you need to write verilog or vhdl to program FPGA.

(I know there are block diagram tools, but that is not very easy to scale)

So,

With the help of Litex and Migen, We can use Python to do the job.

Without open the build tool from vendor . (like Vivado ....)

Fire VSCode, write the function you want.

Just like write any python code.

We can forcus on our design and forget the hassle of the vendor tools. (like Vivado..)

It is a good idea to learn more about fpga and vendor tool. If you want more.

## Board 1

To learn FPGA design , you may need FPGA board like the one in the picture.

These kind of board provide many Input Switch, Output LED for you to enter and display data.

But, 

With LiteX , you can use Host PC Python code to provide the data and read out the result.

Or use ILA to capture the wave form then dispay on gtkWave. 

(Vandor provide these tool , too, but if you can consolidate eveything in one language and better language, why not)

So, you don't need these kind of board anymore.

But sometimes you need the IO Port, like PCIE, HSMI?, ethernet, 

So pick the board with the IO you are interesting with.

## Board 2

My criteria to select a learning fpga board is based on 

1.At least one user led.

for heartbeat display. 

2.Direct UART to USB connector

for Host debug.

3.Direct USB Programming , no download cable needed.



Above board is from Microphase A7-Lite. 

I have use it for prototyping and testing tool.

## Host Software

1. PC with 16G Memory

2. OS Ubuntu 20.04 or Windows with VirtualBox + Ubuntu 20.04

3. Hardisk > 120G

4. Vendor Tools, Linux Version 

I use Vivado 2018.3, because the vivado 2018 is smaller.

https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/vivado-design-tools/archive.html

If vivado installation hang.

sudo apt update
sudo apt upgrade
sudo apt install libncurses5
sudo apt install libtinfo5
sudo apt install libncurses5-dev libncursesw5-dev
sudo apt install ncurses-compat-libs
for Ubuntu 20.04

4. iceStorm tool chain for Lattice ICE40 and ECP5 

https://clifford.at/icestorm

5. LiteX (follow the instruction on Github)

https://github.com/enjoy-digital/litex

6. LiteX-Boards from LiteX-Hub

https://github.com/litex-hub/litex-boards

7. OpenFPGALoader

https://github.com/trabucayre/openFPGALoader

8.GTKWave

sudo apt-get install gtkwave

9.VSCode 
https://code.visualstudio.com/

HDL,HLS and Digital Design
We use Python as HDL. It is not HLS.

Which means you still need to know Digital Logic Design

Note:

HDL stand for hardware description language.

verilog and vhdl are HDL

migen, Amaranth are python based HDL

Chisel are Scala based HDL

HLS stand for high level synthesis

HLS let you use normal C program to describe the operation you want hardware to do.

systemC and some vendor specific C belong to that.

 

## Basic Digital Logic Design:

Combination Logic:

    Not, And, Or, Xor, Mux, DeMux, Encoder, Decoder,....

Sequential Logic:

    Dff, Dff with Reset, Counter, Shift_Register, ...

Clock Domain Crossing

Bus Protocol:

    Wishbone Bus, AXI Bus, AXI Stream, ...

Basic IO Protocol:

    UART, I2C, SPI,...

Complex IO:

    PCIE, DDR, Ethernet, USB,....

Terminology:

    Signal, Clock, Reset, Bus, Ready, Valid, .....

Your should familiar with above stuff.

If not, YouTub or Udemy have some lessons for Digital Logic Design.

## Verilog
Why I use migen/Amaranth as HDL , instead of Verilog. 

Because:

Verilog is not good at data encapsulation. 

It is messy when you try to wiring stuff together.

It lack modern language feature like namespace to help you module your code.

Use python you treat wire as data , you can move it around, pass it around.

Group wire together, manipulate them as whole.

It make the code easy to read and debug.

You will see latter.

Verilog is  Assembly code for Synthesizer to generate gateware. 

migen/Amaranth are C code  for People to read.

## Define Board IO Ports (Board-A7Lite)

from migen import *

from litex.gen import *
from litex.build.generic_platform import *

_io = [
    ("clk50", 0, Pins("J19"), IOStandard("LVCMOS33")),
    ("reset",  0, Pins("L18"), IOStandard("LVCMOS33")),
    ("serial", 0,
        Subsignal("tx", Pins("V2")),
        Subsignal("rx", Pins("U2")),
        IOStandard("LVCMOS33"),
    ),
]

a7lite_io.py

Check the board's schematics design, and find these signal.
1. Clock
2. Reset
3. UART

## Kick Starting Minimum Code

#!/usr/bin/env python3

from migen import *

from litex.gen import *
from litex.soc.interconnect.csr import *

from litex.build.generic_platform import *
from litex.build.xilinx import XilinxPlatform

from litex.soc.integration.soc_core import *
from litex.soc.integration.builder import *


from litex.soc.cores.clock import *
from litex.soc.cores.uart import UARTWishboneBridge
from litex.soc.cores import dna

from a7lite_io import _io

class Platform(XilinxPlatform): #-------------> 1
    default_clk_name   = "clk50"
    default_clk_period = 1e9/50e6

    def __init__(self):
        XilinxPlatform.__init__(self, "xc7a35t-fgg484-2", 
                                _io, toolchain="vivado") 
        #---> 2

# Design -------------------------------------------------------------------------------------------
# Create our platform (fpga interface)

# CRG ----------------------------------------------------------------------------------------------

class CRG(LiteXModule): #------------> 3
    def __init__(self, platform, sys_clk_freq):
        self.cd_sys       = ClockDomain()
        self.cd_sys4x     = ClockDomain()
        self.cd_sys4x_dqs = ClockDomain()
        self.cd_idelay    = ClockDomain()

        # Clk/Rst
        clk50 = platform.request("clk50")

        # PLL
        self.pll = pll = S7MMCM(speedgrade=-1)
        self.comb += pll.reset.eq(~platform.request("reset"))
        #self.comb += pll.reset.eq(self.rst)
        pll.register_clkin(clk50, 50e6) #---> Claim input clk is 50MHz
        pll.create_clkout(self.cd_sys,       sys_clk_freq)
        pll.create_clkout(self.cd_sys4x,     4*sys_clk_freq)
        pll.create_clkout(self.cd_sys4x_dqs, 4*sys_clk_freq, phase=90)
        pll.create_clkout(self.cd_idelay,    sys_clk_freq)
        platform.add_false_path_constraints(self.cd_sys.clk, pll.clkin) 
        # Ignore sys_clk to pll.clkin path created by SoC's rst.

        self.idelayctrl = S7IDELAYCTRL(self.cd_idelay)

# Create our soc (fpga description)
class BaseSoC(SoCMini):
    def __init__(self, platform, **kwargs):
        sys_clk_freq = int(100e6)

        # SoCMini (No CPU, we are controlling the SoC over UART)
        SoCMini.__init__(self, platform, sys_clk_freq,  
                         csr_data_width=32,
                         ident="Hello World \r\n", 
                         ident_version=True) #----------------> 4
        
        # Clock Reset Generation
        self.submodules.crg = CRG(platform, sys_clk_freq)

        # No CPU, use Serial to control Wishbone bus
        self.add_uartbone(name="uart_debug", baudrate=115200)  #--------->5

        # FPGA identification
        self.submodules.dna = dna.DNA()
        self.add_csr("dna") #------> 6

# Initialization ----------------------------------------------------------------------------------

platform = Platform()
soc = BaseSoC(platform)

# Build --------------------------------------------------------------------------------------------

builder = Builder(soc, output_dir="build", csr_csv="test/csr.csv") 
#----->7
builder.build(build_name="top1") #----->8
  

a7lite.py 
(Any file name you like, I use the board name
Most people use the function name of the FPGA)

1. What platform it use. (Which FPGA Vendor)
2. Define the FPGA deivce type. "xc7a35t-fgg484-2" 
3. Create System Clocks from clk50 external clock source
4. Create an SOC with No SOC inside but have a Wishbone Bus System
5. Create an UART Wishbone Bus Master , 
for Host access System CSR <--- Explain Latter
6. Add an CSR to access FPGA Identification Code
7. Build the gateware under build folder and 
generate CSR access register file in test folder.
8. gateware bit file name set to top.bit

I use this "minimum code" as my stating point.

## Folder Structure

Before run the code

Create a test folder for LiteX 's csr register address table.

After run >python3 a7lite.py

You will get a build folder with

gateware folder for fpga build process log

and software folder for soc bios code. 

(the bare minimum code don't have soc , so there is no bios code)

## Load bit file to FPGA

After create the bit file.
You need to load it to FPGA.
I wrote a load.py to load the bit file.

load.py

#!/usr/bin/env python3
import os
#os.system("openFPGALoader --scan-usb")
#os.system("openFPGALoader ./build/gateware/top1.bit")
#os.system("openFPGALoader -c digilent_hs3 ./build/gateware/top1.bit")
os.system("openFPGALoader -c digilent_hs2 ./build/gateware/top1.bit")


You need to know the cable type.
Run >openFPGALoader --scan-usb
and then pick the right cable type
Mine is digilent_HS2

Run >openFPGALoader -c digilent_hs2 ./build/gateware/top1.bit

## Start Host Debug Server

start_server.py

#!/usr/bin/env python3
import os

cmd = "litex_server --uart --uart-port=/dev/ttyUSB0"
#cmd = "litex_server --jtag --jtag-config=openocd_xc7_ft232.cfg"
#cmd = "litex_server --jtag --jtag-config=openocd_xc7_ft2232.cfg"

os.system(cmd)

We need to start a host debug server , 
depends on the debug interface.
You can use UART, Jtag, ethernet, SPI....

Reference 
https://github.com/enjoy-digital/litex/wiki/Use-Host-Bridge-to-control-debug-a-SoC


## Test_Identifier.py

#!/usr/bin/env python3

from litex import RemoteClient

wb = RemoteClient()
wb.open()

print("Test Read Identifyer")
# get identifier
fpga_id = ""
for i in range(256):
    c = chr(wb.read(wb.bases.identifier_mem + 4*i) & 0xff)
    fpga_id += c
    if c == "\0":
        break
print("fpga_id: " + fpga_id)

I put all my test in test folder. 
Generated csr.csv is also in the test folder.

By default the RemoteClient() will open csr.csv
So it will know each CSR's address by name.
The we can access these CSR easily.

The CSR we read is the  ident define in the SocMini.

## What is CSR
Reference:
https://github.com/enjoy-digital/litex/wiki/CSR-Bus

CSR is the 2nd best thing in Litex
First is the Build System. (We will talk about it later)

CSR stand for Control Staus Register
If FPGA is a function, CSR is the parameter you pass into the function.


All the CSR can be access through Wishbone Bus system.

And you can access Wishbone Bus from Host 

https://en.wikipedia.org/wiki/Wishbone_(computer_bus)

