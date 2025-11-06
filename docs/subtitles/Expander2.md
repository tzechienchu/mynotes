# Expander 2

## System Block

![](./Expander2/BlockDiag01.png)

## FPGA Internal

![](./Expander2/BlockDiag02.png)

## FT601 and USB Downlaoder

![](../diagrams/2025/FT601andFPGA.png)

## Efinix FPGA with RAM

![](./Expander2/Screenshot%20from%202025-11-03%2017-54-00.png)

| FPGA        | Power        | EEPROM       |
|:------------|:-------------|:-------------|
| Intel Max10 | 3.3V         | Embedded     |
| efinix T20  | 1.2V Core <br> 3.3V IO  | Embedded     |
| efinix Ti60 | 0.95V Core <br> 1.8V AUX <br> 3.3V IO  | Embedded RAM and EEPROM   |

### Power Management for FPGAs

[Power Management for FPGAs](https://www.analog.com/en/resources/analog-dialogue/articles/power-management-for-fpgas.html)

![](../images/2025/Screenshot%20from%202025-11-06%2014-35-09.png)

![](../images/2025/Screenshot%20from%202025-11-06%2014-36-43.png)

![](../images/2025/Screenshot%20from%202025-11-06%2014-36-11.png)

---

TI's Solution

![](../images/2025/Screenshot%20from%202025-11-06%2014-32-42.png)

## JOB Breakdown

| JOB             | Duration(W)| Description       |
|:----------------|:-----------|:------------------|
| Schematics      | 2-4W       | Use FPGA Module   |
| PCB             | 2-4W       |                   |
| FPGA Design     |            |                   |
| ---FT601        | 2-4W       | USB3 to PC        |
| ---ADC SM       | 2-4W       | ADC Driver        |
| ---DAC SM       | 2-4W       | DAC Driver        |
| ---Pattern Gen  | 2-4W       | DAC/Digital Pattern |
| ---FIFO To PC   | 2-4W       | FIFO to PC        |
| ---PC to FIFO   | 2-4W       | PC to FIFO        |
| ---Serdes       | 4-8W       | FPGA 2 FPGA       |
| FPGA Module Sch | 2-4W       | Efinix or Other FPGA module |
| FPGA Module PCB | 2-4W       |                   |
| Analog Front End| 1-2W       | Input Voltage Range Selection ?|

## AD7616 ADC

![](./Expander2/Screenshot%20from%202025-10-22%2017-54-07.png)

## AD5766 DAC

![](./Expander2/Screenshot%20from%202025-10-27%2014-33-06.png)

## FT60X

![](./Expander2/Screenshot%20from%202025-10-27%2014-40-53.png)

![](./Expander2/Screenshot%20from%202025-10-27%2014-40-41.png)

## ONIX Breakout Board

![](./Expander2/Screenshot%20from%202025-10-29%2018-05-29.png)

![](./Expander2/Screenshot%20from%202025-10-29%2018-08-43.png)

![](./Expander2/Screenshot%20from%202025-10-29%2018-08-59.png)

![](./Expander2/Screenshot%20from%202025-10-29%2018-09-19.png)

![](./Expander2/Screenshot%20from%202025-10-29%2018-11-00.png)

## Resource Sharing between SoCs through a Dedicated SerDes-channel

[Resource Sharing between SoCs through a Dedicated SerDes-channel](./Expander2/Master_Thesis_Casper_Kesheng_final.pdf)

[Lund University LUP Student Papers](https://lup.lub.lu.se/student-papers/search/publication/9164576)

![](./Expander2/IMG_0323.jpg)

![](./Expander2/IMG_0324.jpg)

![](./Expander2/IMG_0329.PNG)