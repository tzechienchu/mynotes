# Resources

## Tools to Solve Complex Engineering Problem

1. Clean Code / Gaphics
2. Mob Programming
3. Git / Github
4. Modulization / Standard Connector

## Software Engineering 🌐 🎬 💾 📚 📑

### Mob Programming

![mob](images/2025/Screenshot%20from%202024-09-12%2012-18-24.png)

Mob Programming
[woodyzuill.com](https://woodyzuill.com/).

Mob Programming Example
[Youtube Video](https://www.youtube.com/watch?v=p_pvslS4gEI&t=4s).

### Agile Firmware and Hardware Design

[Youtube Video](https://www.youtube.com/watch?v=rG4rC5oLx7Y&t=1s)

## Embedded System

### Design circuit boards with code

    Design circuit boards with code! 
    ✨ Get software-like design reuse 🚀, 
    validation, version control and 
    collaboration in hardware; 
    starting with electronics ⚡️

[Design circuit boards with code!](https://github.com/atopile/atopile)

### No RTOS

[ANTIRTOS](https://github.com/WeSpeakEnglish/ANTIRTOS)

### Yes RTOS

1. FreeRTOS
2. Zephyr
3. RT-Thread

### Raspberry Pi Documentation

[Raspberry Pi Documentation](https://www.raspberrypi.com/documentation/)

### Bead Usage

[errite-beads-common-applications-and-considerations](https://greatpcb.com/zh-TW/ferrite-beads-common-applications-and-considerations-in-circuit-design/)

### Fast Serial IO Serdes-LVDS

[Fast Serial IO](papers/2025/serialio.pdf)

### High Speed PCB Design

[High Speed PCB Design](papers/2025/High-Speed%20PCB%20Design%20Guide.pdf)

### Some C Stuff

#### .h file

pin.h : store pin name define and hardware related constant

constant.h : store constant related to software

data.h : store software related general data structure

error.h : error code and error message

global.h : place to define global access variable

log.h : Macros about log

---

#### c naming convention

Variable start with Lowercase

Function, Enum, Class, Class start with Uppercases

Parameters start with underscore

Macro start with all uppercase with underscore between words

#### c others stuff

comment closing curly braces

variable suppose to have min and max

#### c function error handling

1st parameter is alwasy *error

```c
int my_function(Error *err, int a, int b)
{
    if a < 0 {
        *err = ERROR::LESSTHENZERO;
        return 0;
    }
}

```

---

#### setjmp longjmp 用法

```c
#include <stdio.h>
#include <setjmp.h>

jmp_buf buf;

void nested_function() {
    printf("在深層函數中，發生錯誤...\n");
    longjmp(buf, 1); // 跳回到 setjmp 處，並傳回 1
    printf("這行不會被執行\n");
}

int main() {
    // 1. 設定跳轉點
    if (setjmp(buf) == 0) {
        printf("準備呼叫深層函數\n");
        nested_function();
    } else {
        // 2. 當從 longjmp 跳回時
        printf("已從錯誤中恢復\n");
    }

    return 0;
}
```

---

#### C Header File Example

```c
// 1. Include Guards (Essential to prevent multiple inclusions)
#ifndef MY_HEADER_H
#define MY_HEADER_H

// 2. Includes (Only necessary ones, like standard types)
#include <stdint.h>

// 3. Macros and Constants
#define MAX_BUFFER 1024

// 4. Data Type Definitions (Structures, Enums, Typedefs)
typedef struct {
    int id;
    char name[20];
} User;

// 5. Function Prototypes (Public interface)
void ProcessUser(User* u);
int GetStatus(void);

// 6. External Global Variables (If shared)
extern int globalConfig;

#endif // MY_HEADER_H
```

---

## FPGA 🌐 🎬 💾 📚 📑

### FPGA DFX

📚[Dynamic Function eXchange Licensing](https://docs.amd.com/r/en-US/ug909-vivado-partial-reconfiguration/Dynamic-Function-eXchange-Licensing)

### FPGA Tandem

📚[UltraScale+ Devices Integrated Block for PCI Express Product Guide (PG213)](https://docs.amd.com/r/en-US/pg213-pcie4-ultrascale-plus/Tandem-PROM/PCIe-Resource-Restrictions)

### Generates Makefiles for FPGA EDA

[Generates Makefiles for FPGA EDA](https://github.com/cambridgehackers/fpgamake)

### Riffa

[RIFFA_A_Reusable_Integration_Framework_for_FPGA_Accelerators](https://www.researchgate.net/publication/261396774_RIFFA_A_Reusable_Integration_Framework_for_FPGA_Accelerators)

[https://kastner.ucsd.edu/wp-content/uploads/2014/04/admin/fpl-riffa2.pdf](https://kastner.ucsd.edu/wp-content/uploads/2014/04/admin/fpl-riffa2.pdf)

[https://github.com/KastnerRG/riffa](https://github.com/KastnerRG/riffa)

[Riffa AXI in OpenCore](https://opencores.org/websvn/listing?repname=qaz_libs&path=%2Fqaz_libs%2Ftrunk%2FPCIe%2Fsrc%2FRIFFA%2F&rev=43)

[Some Riffa User](https://gitlab.in2p3.fr/csantos/apc/WA105/ml605-parisroc-wa105-firmware/-/tree/master/src)

[Riffa as Vivado IP](https://github.com/briansune/Artix-7-PCIE-Riffa)

### XDMA

[Xilinx XDMA](https://ebics.net/xilinx-xdma/)

### SYZYGY Interface

[SYZYGY Interface md](./subtitles/SYZYGY_Interface.md)

### HLS

[https://www.acri.c.titech.ac.jp/wordpress/](https://www.acri.c.titech.ac.jp/wordpress/)

[https://github.com/acri-room/hls-challenge-labs](https://github.com/acri-room/hls-challenge-labs)

[https://acri-vhls-challenge.web.app/](https://acri-vhls-challenge.web.app/)

### Manta: A Configurable and Approachable Tool for FPGA Debugging and Rapid Prototyping

[https://fischermoseley.github.io/manta/](https://fischermoseley.github.io/manta/)

### DDR3 Memory

![](./images/2025/Screenshot%20from%202025-01-24%2011-47-27.png)

### 7 Series Compare

[7 Series Compare](./subtitles/Artix7vsKintex.md)

### Valid Ready Handshake use Verilog

[Valid-Ready Handshake](https://blog.csdn.net/maowang1234588/article/details/100065072)

### Verilog Signal Naming Guide

|Type of Signal     |Suffix	Prefix (optional)  |  Example           |
|-------------------|:------------------------:|-------------------:|
|Clock signals      | clk or _ck               | sys_clk, clk       |
|Reset signals      | _rst or _reset           | cpu_reset, rst     |
|Active-low signals | _n or _x                 | reset_n, enable_x  |
|Enable signals     | _en                      | write_en           |
|Input ports        | _i or _in i_ or in_      | data_in, i_valid   |
|Output ports       | _o or _out o_ or out_    | data_out, o_ready  |
|Registered signals | _reg o _r                | count_reg, state_r |
|Next state signals | n_                       | n_state            | 

### USB3 DAQ

#### USB3 FIFO Interface for DAQ use FT60X and Cypress FX3

[USB3 FIFO Interface for DAQ](./papers/2025/Mroczek_an_universal_MAM_12_2016.pdf)

#### Korean Blog about FT601

[Korean Blog about FT601](https://blog.naver.com/acidc/223321211341)

#### USB3.2 Interface for DAQ Cypress FX20

![](../images/2025/Screenshot_20251207_110013_Chrome.jpg)

![](../images/2025/Screenshot_20251207_110222_Chrome.jpg)

## ONIX

[Open Ephys: Onix](./subtitles/onix.md)

## Onix Breakout Board Github

[Github Source Code](./subtitles/onix-breakout-main/README.md)

## DAQ

### Intan

[RHX Impedence Measure](https://github.com/MatsumotoJ/Tetroplater)

### Use hdmi video capture as data input

[HDMI Output as Data to PC](https://github.com/steve-m/hsdaoh)

### A 2 GHz oscilloscope for everyone

[https://www.crowdsupply.com/andy-haas/haasoscope-pro](https://www.crowdsupply.com/andy-haas/haasoscope-pro)

[https://github.com/drandyhaas/HaasoscopePro](https://github.com/drandyhaas/HaasoscopePro)

### DAQ Sync

[data-acquisition-synchronisation](https://dewesoft.com/blog/data-acquisition-synchronisation)

### Sync Expander and Sync Hub

[Sync Expander and Sync Hub](./subtitles/SyncExpander.md)

### Harp on Pico

[Harp Core Pico Github Source](./subtitles/harp.core.pico-main/README.md)

### Wireless Headstages

[Wireless Headstage](./subtitles/wireless_hs.md)

### AD5940 Collections

[AD5940.md](./subtitles/AD5940.md)

### RJ45 has No Ground without Transformer Coupling

## Design Review

### PCB Review

    supply voltage
    logic level
    gpio fn
    pull up , pull down
    protection ckt
    power budget
    battery low condition
    race condition
    connectors 
    unused pin check
    termination
    tx rx check
    reset 

### Mistakes People Make When Designing Prototype PCBs

3. Designing for Production:
• Design the first PCB expecting it to fail, focusing on functionality testing.
• Size and shape considerations can come later; prioritize testing various features.

4. No Test Points:
• Lack of test points hinders debugging and fixing mistakes.
• Test pads for common functionalities reduce the risk of blocking progress.

5. No Power or Diagnostic LEDs:
• Diagnostic lights for voltage levels and operations save time in identifying simple mistakes.

6. Overcrowding Components:
• Avoid packing components tightly during prototyping; leave space for adjustments.
• Keep passives relatively large for easier removal during testing.

7. Underutilizing Silk Screen:
• Clearly label components on the silk screen for easy assembly and orientation.
• Ensure markings are readable on the smallest boards.

8. Not Using Isolation Jumpers:
• Incorporate zero-ohm resistors or cutable jumpers for easy isolation during testing.
• Facilitates methodical bring-ups and simplifies troubleshooting.

9. Not Breaking Out Unused GPIOs:
• Break out additional GPIOs for testing and fixing mistakes without ordering a new PCB.
• Adds flexibility for rewiring components or integrating external modules.

10. UART Mixups:
• Ensure correct pairing of transmit and receive pins in UART components.
• Use jumpers or specific designs to easily correct mistakes.

11. Locking Into I2C Addresses:
• Provide options to change I2C addresses using resistors for flexibility.
• Prevents the need for a new PCB revision due to address conflicts.

12. Separate Power PCB:
• Consider splitting the design into multiple boards, especially separating power.
• Enables testing power solutions independently without scrapping the entire PCB.

13. Choosing Labeled Surface Mount Resistors:
• Opt for labeled surface mount resistors for easier visual inspection and testing.

14. Verify Footprints:
• Check dimensions on the data sheet against PCB footprints in your design software.
• Prevents ordering the wrong footprint for components.

15. Check Parts Availability:
• Consider part availability before designing the circuit.
• Speculatively order critical parts before PCB production to mitigate shortages.

![](../images/2025/Screenshot%20from%202025-12-17%2016-53-54.png)

### Digilent Analog Discovery 2 Schematics

[Digilent Analog Discovery 2 Schematics](https://digilent.com/reference/test-and-measurement/analog-discovery-2/hardware-design-guide?srsltid=AfmBOopvdmBdjPj54B-BKPZd-laQ4OiJSQnNHrf0MrcLWDgZMCemvK6Q)

---

## Math

### Probabilistic numerics

[Probabilistic numerics](https://en.wikipedia.org/wiki/Probabilistic_numerics)

[https://www.probabilistic-numerics.org/](https://www.probabilistic-numerics.org/)

## JP Books Site

[https://gihyo.jp/dp](https://gihyo.jp/dp)

[https://honto.jp/](https://honto.jp/)

[https://cc.cqpub.co.jp/lib/](https://cc.cqpub.co.jp/lib/)
