
# PINS

## STM32 JLink

    1.VDD_Target
    2.SW_CLK
    3.GND
    4.SW_DIO
    5.NRST
    6.SW_O

## Pico

![Pico Pinout](./images/2025/Screenshot%20from%202025-06-10%2016-09-10.png)

### Pico SWD IO Port

![](./images/2025/Screenshot%20from%202025-08-01%2018-03-49.png)

![](./images/2025/Screenshot%20from%202025-08-01%2018-04-18.png)

[Debug Probe](https://www.raspberrypi.com/documentation/microcontrollers/debug-probe.html)

## STM32 

### PyBoard STM32F405

![PyBoard](./images/2025/pybv11-pinout.jpg)

### STM32F405 I2C

    I2C Pin Mapping Overview
    Each I2C port can be routed to multiple alternative GPIO pin packs to suit your board layout: [1]
    I2C1: SCL on PB6 (or PB8), SDA on PB7 (or PB9)
    I2C2: SCL on PB10, SDA on PB11
    I2C3: SCL on PA8 (or PH7), SDA on PC9 (or PH8) [1, 2]

## FT2232

### FT245 Fifo

| Pin Name  |   Functions   |  I/O  |
|-----------|:-------------:|------:|
| AD0 ~ AD7 |      D0 ~ D7  | IO    |
| AC0 | RXF#     |   output  |
| AC1 | TXE#     |   output  |
| AC2 | RD#      |   input   |
| AC3 | WR#      |   input   |
| AC4 | SIWR#    |   input   |
| AC5 | ClockOut |   output  |
| AC6 | OE#      |   input   |

### FT2232 / FT2232H JTAG Pin Out Mapping Table

When configuring the FT2232 for JTAG debugging (typically using the **MPSSE** engine), **Channel A (ADBUS)** is used as the standard interface.

| JTAG Signal | FT2232 Channel A Pin | I/O Direction (FT2232 Side) | Description |
| :--- | :--- | :--- | :--- |
| **TCK** | `ADBUS0` | Output | Test Clock |
| **TDI** | `ADBUS1` | Output | Test Data In |
| **TDO** | `ADBUS2` | Input | Test Data Out |
| **TMS** | `ADBUS3` | Output | Test Mode Select |
| **nTRST** | `ADBUS4` or `ACBUS0` | Output | Optional: Test Reset (Depends on layout configuration) |
| **nSRST** | `ADBUS5` or `ACBUS1` | Output / Open-Drain | Optional: System Reset (Depends on layout configuration) |
| **GND** | `GND` | - | Ground Reference |

### PyFTID Doc

[PyFTDI Doc](https://eblot.github.io/pyftdi/index.html)

### SPI and Other Pins

![FT2232 IO](pins/Screenshot%20from%202025-01-14%2017-07-20.png){: style="height:600px"}

### FT2232 Code

[Python FTDI for SPI](https://www.alexallmont.com/spi-refresher/).

``` py
from pyftdi.ftdi import Ftdi
Ftdi.show_devices()
from pyftdi.spi import SpiController

spi.configure('ftdi://ftdi:2232h:1:7b/1')
slave = spi.get_port(cs=1, freq=10E6, mode=2)
write_buf = b'\x01\x02\x03'
read_buf = slave.exchange(write_buf, duplex=True)
```

## Raspberry Pi

### Pi 4 IO

![RPI IO](pins/RPI4PinOut.png)

[The Raspberry Pi GPIO pinout guide](https://pinout.xyz/)

## FPGA

### Nexy A7 PMod

![PMod](pins/NexyA7PMod.png)

### Efinix T20 

![Efinix:T20](./images/2025/Screenshot%20from%202025-02-13%2012-16-33.png)

### SYZYGY

[SYZYGY FPGA IO](https://syzygyfpga.io/)

[SYZYGY Specification](https://syzygyfpga.io/wp-content/uploads/2019/09/Syzygy-Specification-V1p1.pdf)

---

### SYZYGY Standard Pod/Port 40 Pin

以下為符合 SYZYGY 規格 的 Standard Pod/Port 標準連接埠 40 針腳位定義與訊號說明。 [1, 2] 
## 腳位配置表 (Pinout Table)
SYZYGY 標準連接埠採用 40 針腳的雙排微間距連接器（母端為 Samtec QSE，公端為 Samtec QTE），中央帶有接地金屬片（GND Spine）。 [2, 3] 

| 腳位 (Pin) | 訊號名稱 (Signal Name) | 說明 (Description) | 腳位 (Pin) | 訊號名稱 (Signal Name) | 說明 (Description) |
|---|---|---|---|---|---|
| 1 | SCL | I2C 時脈（用於 SYZYGY DNA 識別） | 2 | +5V | 5V 固定電源供應（最大 2A） |
| 3 | SDA | I2C 資料（用於 SYZYGY DNA 識別） | 4 | R_GA | 地理編碼地址電阻腳位 |
| 5 | S0_D0P | 單端 S0 / 差動對 0 正極 | 6 | S1_D1P | 單端 S1 / 差動對 1 正極 |
| 7 | S2_D0N | 單端 S2 / 差動對 0 負極 | 8 | S3_D1N | 單端 S3 / 差動對 1 負極 |
| 9 | S4_D2P | 單端 S4 / 差動對 2 正極 | 10 | S5_D3P | 單端 S5 / 差動對 3 正極 |
| 11 | S6_D2N | 單端 S6 / 差動對 2 負極 | 12 | S7_D3N | 單端 S7 / 差動對 3 負極 |
| 13 | S8_D4P | 單端 S8 / 差動對 4 正極 | 14 | S9_D5P | 單端 S9 / 差動對 5 正極 |
| 15 | S10_D4N | 單端 S10 / 差動對 4 負極 | 16 | S11_D5N | 單端 S11 / 差動對 5 負極 |
| 17 | S12_D6P | 單端 S12 / 差動對 6 正極 | 18 | S13_D7P | 單端 S13 / 差動對 7 正極 |
| 19 | S14_D6N | 單端 S14 / 差動對 6 負極 | 20 | S15_D7N | 單端 S15 / 差動對 7 負極 |
| 21 | S16 | 單端通用 I/O | 22 | S17 | 單端通用 I/O |
| 23 | S18 | 單端通用 I/O | 24 | S19 | 單端通用 I/O |
| 25 | S20 | 單端通用 I/O | 26 | S21 | 單端通用 I/O |
| 27 | S22 | 單端通用 I/O | 28 | S23 | 單端通用 I/O |
| 29 | S24 | 單端通用 I/O | 30 | S25 | 單端通用 I/O |
| 31 | S26 | 單端通用 I/O | 32 | S27 | 單端通用 I/O |
| 33 | P2C_CLKP | 週邊至載板差動時脈 正極（Pod to Carrier） | 34 | C2P_CLKP | 載板至週邊差動時脈 正極（Carrier to Pod） |
| 35 | P2C_CLKN | 週邊至載板差動時脈 負極（Pod to Carrier） | 36 | C2P_CLKN | 載板至週邊差動時脈 負極（Carrier to Pod） |
| 37 | RSVD | 保留腳位（請勿連接） | 38 | RSVD | 保留腳位（請勿連接） |
| 39 | VIO | 可程式化 SmartVIO 供電軌（1.2V - 3.3V） | 40 | +3.3V | 3.3V 固定電源供應 |

------------------------------
## 訊號類別詳細說明## 1. 電源與 SmartVIO 控制

* 
* +5V & +3.3V：固定電壓供電軌。載板（Carrier）通常先啟動 3.3V，再進行後續辨識。
* VIO：可變 I/O 電壓軌。由載板的 SmartVIO 系統 控制，在讀取 Pod 的 DNA 資訊後，才會輸出該 Pod 指定的正確電壓（如 1.8V、2.5V 等），以保護 FPGA 暫存器不受電壓過高損害。 [4, 5, 6, 7] 
* 

## 2. 系統管理與辨識 (SYZYGY DNA)

* 
* SCL & SDA：I2C 匯流排，連接至 Pod 上的微控制器（pMCU）。載板透過此介面讀取儲存在週邊上的 [SYZYGY DNA 資料](https://github.com/SYZYGYfpga/avr-dna-fw)（包含電壓需求、製造商資訊、腳位限制等）。
* R_GA：地理編碼地址腳位。載板端此腳位接下拉電阻（各連接埠阻值唯一），Pod 端接上拉電阻，pMCU 藉由偵測分壓後的 ADC 數值來判斷自身位於載板的哪一個插槽（I2C 設備位址）。 [8, 9, 10] 
* 

## 3. 高速資料與時脈訊號 (Data & Clocks)

* 
* S0 到 S15 (Pin 5-20)：具備差動傳輸能力。依照規格，若週邊需要使用差動訊號（例如 LVDS），必須優先佈線於這些對應的 P/N 腳位上，且正極（P）必須位於編號較低的 I/O 腳位（如 S0 配 S2、S1 配 S3）。若無差動需求，亦可直接作為單端獨立資料線使用。 [11] 
* S16 到 S27 (Pin 21-32)：僅支援單端（Single-Ended）通用的數位 I/O 線。 [11, 12] 
* P2C_CLK{P|N} & C2P_CLK{P|N}：專用的硬體差動時脈線。P2C（週邊至載板）主要用於將 Pod 端的採樣時脈或同步時脈送入 FPGA 的專用時脈腳位（Clock-Capable Pins）；C2P（載板至週邊）則是由 FPGA 主控端提供參考時脈給 Pod 設備。 [9, 13] 
* 

如需參考電路設計範本或查看硬體實作細節，可至 [SYZYGY 官方硬體模板庫](https://github.com/SYZYGYfpga/pcb-templates) 獲取 Altium 或 KiCad 的開源文件。 [3, 14] 
請問您目前正在進行 Carrier（載板） 還是 Pod（週邊子板） 的硬體開發？如果需要，我可以為您提供 Transceiver (TXR) 規格 的腳位定義，或是 SmartVIO 的電源啟動順序說明。

[1] [https://syzygyfpga.io](http://syzygyfpga.io/wp-content/uploads/2019/09/Syzygy-Specification-V1p1.pdf)
[2] [https://syzygyfpga.io](http://syzygyfpga.io/wp-content/uploads/2019/09/Syzygy-Specification-V1p1.pdf)
[3] [https://syzygyfpga.io](https://syzygyfpga.io/specification/)
[4] [https://docs.opalkelly.com](https://docs.opalkelly.com/syzygy-peripherals/szg-pmod4/)
[5] [https://syzygyfpga.io](http://syzygyfpga.io/wp-content/uploads/2019/09/Syzygy-Specification-V1p1.pdf)
[6] [https://docs.opalkelly.com](https://docs.opalkelly.com/xem8320/syzygy-ports/)
[7] [https://syzygyfpga.io](http://syzygyfpga.io/wp-content/uploads/2019/09/Syzygy-Specification-V1p1.pdf)
[8] [https://docs.opalkelly.com](https://docs.opalkelly.com/syzygy-peripherals/szg-tst-std/)
[9] [https://syzygyfpga.io](https://syzygyfpga.io/wp-content/uploads/2023/09/Syzygy-Specification-V1p1p1.pdf)
[10] [https://docs.opalkelly.com](https://docs.opalkelly.com/resources/syzygy-design-guide/design-checklist/)
[11] [https://docs.opalkelly.com](https://docs.opalkelly.com/resources/syzygy-design-guide/design-checklist/)
[12] [https://syzygyfpga.io](http://syzygyfpga.io/wp-content/uploads/2019/09/Syzygy-Specification-V1p1.pdf)
[13] [https://www.studocu.com](https://www.studocu.com/in/document/birla-institute-of-technology-and-science-pilani/microelectronic-circuits/xboard-zu1-hw-user-guide-v0-2/69370821)
[14] [https://github.com](https://github.com/SYZYGYfpga/pcb-templates)

---