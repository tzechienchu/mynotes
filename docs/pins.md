
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