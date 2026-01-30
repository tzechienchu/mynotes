# Tutorial

## Litex

[fjullien migen_litex_tutorials](https://github.com/fjullien/migen_litex_tutorials) <-- Best Litex tutorial

[Local migen_litex_tutorials](./subtitles/migen_litex_tutorials.md)

[officail litx wiki](https://github.com/enjoy-digital/litex/wiki).

[Litex MIPI CSI](https://github.com/gatecat/litex-nexus-mipi)

ICEStorm Install [icestorm_install.md](subtitles/icestorm_install.md)

[getting-started-with-litex](https://sourcesup.renater.fr/www/mic-sec-2022/labs/getting-started-with-litex.html)

## Migen Simulation

### Counter

``` py
from migen import *

class DPLL(Module):
    def __init__(self):
        self.count = Signal(4)

        self.sync += self.count.eq(self.count + 1)

def dpll_test(dut):
    for i in range(20):
         print((yield dut.count))
         yield

if __name__ == "__main__":
    dut = DPLL()
    run_simulation(dut, dpll_test(dut), vcd_name="dpll.vcd")

```

### Migen Selection of Signal from Signal as Index

```py
        shiftout = Array({} for i in range(REG_Number))

        for i in range(16):
            shiftout[0][i] = [self.spi_miso1.eq(Regsiters[0][i] & ~self.spi_cs)]
            shiftout[1][i] = [self.spi_miso2.eq(Regsiters[1][i] & ~self.spi_cs)]

        for i in range(REG_Number):
            self.comb += [
                Case(shift_count, shiftout[i])
            ]
```

### Migen Case

```py
        self.comb += Case(word_cound,{
            0: self.uart_tx.eq(start_b0),
            1: self.uart_tx.eq(start_b1),
            2: self.uart_tx.eq(timestamp_b0),
            3: self.uart_tx.eq(timestamp_b1),
            4: self.uart_tx.eq(timestamp_b2),
            5: self.uart_tx.eq(timestamp_b3),
            "default": self.uart_tx.eq(0),
        })

```

### Migen Module

```py
        encoder = Encoder8b10b()
        decoder = Decoder8b10b()

        self.submodules += [
            encoder, decoder
        ]
```

### Migen Reset FSM

```python
        fsm   = FSM(reset_state="WAIT")
        fsm   = ClockDomainsRenamer("icap")(fsm)
        fsm   = ResetInserter()(fsm)
        self.submodules += fsm
        self.comb += fsm.reset.eq(~(self.write | self.read))
```

## IceStorm toolset for ICE40 FPGA

```makefile
# Project setup
PROJ      = blinky
BUILD     = ./build
DEVICE    = 8k
FOOTPRINT = ct256

# Files
FILES = top.v

.PHONY: all clean burn timing

all $(BUILD)/$(PROJ).asc $(BUILD)/$(PROJ).bin:
	# if build folder doesn't exist, create it
	mkdir -p $(BUILD)
	# synthesize using Yosys
	yosys -p "synth_ice40 -top top -blif $(BUILD)/$(PROJ).blif -json $(BUILD)/$(PROJ).json" $(FILES)
	# Place and route using arachne
	#arachne-pnr -d $(DEVICE) -P $(FOOTPRINT) -o $(BUILD)/$(PROJ).asc -p pinmap.pcf $(BUILD)/$(PROJ).blif
	nextpnr-ice40 --hx$(DEVICE) --json build/$(PROJ).json --pcf pinmap.pcf --asc build/$(PROJ).asc
	# Convert to bitstream using IcePack
	icepack $(BUILD)/$(PROJ).asc $(BUILD)/$(PROJ).bin

burn: $(BUILD)/$(PROJ).bin
	iceprog $(BUILD)/$(PROJ).bin

timing: $(BUILD)/$(PROJ).asc
	icetime -tmd hx$(DEVICE) $(BUILD)/$(PROJ).asc

clean:
	rm build/*
```

## Chisel FPGA開発日記

[https://msyksphinz.hatenablog.com/](https://msyksphinz.hatenablog.com/)

[https://www.hatena.ne.jp/](https://www.hatena.ne.jp/)

[https://hatenablog.com/](https://hatenablog.com/)

[Agile Hardware Design Video 2024](https://www.youtube.com/playlist?list=PLfrN7RIcMe6g2LBRJLTHTdhyj5s8ag0Rg)

![Chip Alliance](images/2025/Screenshot%20from%202025-02-04%2016-58-19.png)

[Chip Alliance](https://www.chipsalliance.org/)

---

## Amaranth

[Amaranth HDL Document](https://amaranth-lang.org/docs/amaranth/latest/)

---

## Verilog

[https://verilogguide.readthedocs.io/en/latest/](https://verilogguide.readthedocs.io/en/latest/)

[https://www.chipverify.com/](https://www.chipverify.com/)

[Verilog Tutorial](https://www.asic-world.com/verilog/index.html)

---

## The Art of FPGA Design - element14 Community

[The Art of FPGA Design - element14 Community](https://community.element14.com/technologies/fpga-group/b/blog/posts/the-art-of-fpga-design)

## Digital Signal Processing, from Algorithm to FPGA Bitstream - element14 Community

[Digital Signal Processing, from Algorithm to FPGA Bitstream](https://community.element14.com/technologies/fpga-group/b/blog/posts/the-art-of-fpga-design-season-2---digital-signal-processing-from-algorithm-to-fpga-bitstream)

---

## Xilinx Petalinux

### 使用Buildroot编译AMD/Xilinx Zynq ZC702 单板 Linux （内核和文件系统）

🌐[使用Buildroot编译AMD/Xilinx Zynq ZC702 单板 Linux （内核和文件系统）](https://www.cnblogs.com/hankfu/p/19048169)

---

### ZCU104_MPSoC Development - Petalinux 2024.2 Basic Tutorial

🌐[ZCU104_MPSoC Development - Petalinux 2024.2 Basic Tutorial](https://www.hackster.io/engrinam0077/zcu104-mpsoc-development-petalinux-2024-2-basic-tutorial-c82b8d)

---

### Xillinux

🌐[Xillinux: A Linux distribution for Z-Turn Lite, Zedboard, ZyBo and MicroZed](https://xillybus.com/xillinux)

The Xillinux distribution is a software + FPGA code kit for running a full-blown graphical desktop on the Z-Turn Lite, Zedboard and (non-Z7) ZyBo, attaching a monitor, keyboard and mouse to the board itself. Xillinux also supports MicroZed without the graphics.

---

### UltraZed-EG PCIe Carrier Card 開發紀錄

🌐[UltraZed-EG PCIe Carrier Card 開發紀錄 使用 PetaLinux 建立系統](https://coldnew.github.io/b394a9ce/)

---

### Petalinux Demo

🎬[Xilinx Zynq & PetaLinux Project Demo](https://www.youtube.com/watch?v=U2QBNz2XzYs)

🎬[PetaLinux SPI Device Control LCD Panel](https://www.youtube.com/watch?v=yMJrtXS_5iU)

🎬[Creating Multi-Boot Bitstream In Xilinx FPGA](https://www.youtube.com/watch?v=nacLtYDEbRk)

🎬[Xilinx HLS Project Demo - SHA256 Calculation](https://www.youtube.com/watch?v=pir2bskQwBA)

---

### Perfecting PetaLinux Workshop

💾[Perfecting PetaLinux Workshop](https://github.com/ATaylorCEngFIET/perfecting_petalinux)

🎬[Perfecting PetaLinux Workshop](https://www.youtube.com/watch?v=Tloz2tJsJow)

📚[PetaLinux Tools Documentation: Reference Guide (UG1144)](https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide)

---

### Vivado Vitis Petalinux 2024 on Ubuntu 2024

🌐[Vivado Vitis Petalinux 2024.2](https://www.hackster.io/whitney-knitter/vivado-vitis-petalinux-2024-2-install-on-ubuntu-59f3c3)

🌐[Hardware acceleration in FPGA with Vivado and Vitis](https://www.hackster.io/juan-abelaira/hardware-acceleration-in-fpga-with-vivado-and-vitis-1d0043)

🌐[Vivado 2024 on Ubuntu 2024](https://dspdev.io/en/posts/vivado-2024-ubuntu/)

![](../images/2026/Screenshot%20from%202026-01-26%2013-23-23.png)

---

### Zynq PetaLinux and Vitis

🌐[Zynq PetaLinux 2024-1](https://www.hackster.io/whitney-knitter/fixed-platform-design-on-zynq-7000-in-petalinux-2024-1-ae3f6d)

🌐[Fixed Platform Design on Zynq-7000 in Vitis 2024.1](https://www.hackster.io/whitney-knitter/fixed-platform-design-on-zynq-7000-in-vitis-2024-1-5ac3a1)

---

## Linux and Programming

### Linux Thread

Linuxとpthreadsによる マルチスレッドプログラミング入門｜サポート｜秀和システム
[Book Link](https://www.shuwasystem.co.jp/support/7980html/5372.html)

### Linux Kernel Module Programming Guide

[https://sysprog21.github.io/lkmpg/](https://sysprog21.github.io/lkmpg/)

Tutorial 1
[https://linux-kernel-labs.github.io/refs/heads/master/#](https://linux-kernel-labs.github.io/refs/heads/master/#)

### SSH Turnnel

    ssh -f -N -L 127.0.0.1:8888:127.0.0.1:8888 -i pemKey  user@ipaddress
    For Jupyter notebook

---

## Chromebook

Install Desktop GUI in chromebook [chromebookDesktop.md](subtitles/chromebookDesktop.md)

## DSP

### Wavelet

[Wavelet_101](./subtitles/wavelet_101.md)

## Neuro Science

[Neuroscience exploration Video](https://www.youtube.com/playlist?list=PLgtmMKe4spCMzkiVa4-eSHVk-N4SC8r9K)

[Topological Data Analysis (TDA)](https://www.youtube.com/playlist?list=PLz-ep5RbHosVi8Qoyqvz1MEiYrz35Zb7F)

## VSCode

[VSCode and Docker](https://mcuoneclipse.com/2025/02/08/optimizing-embedded-development-with-vs-code-and-devcontainer/)

## LTSpice Tutorial

[LTspice Tutorial](https://www.youtube.com/playlist?list=PLT84nve2j1g_wgGcm0Bv3K4RSl2Jdjsey)

[LTSpice Tutorial](https://www.youtube.com/playlist?list=PLGtyXSn57qnKRiIqfpVK3ZtzOD8eb_2ro)
