
# Tutorial

## Litex

[fjullien migen_litex_tutorials](https://github.com/fjullien/migen_litex_tutorials) <-- Best Litex tutorial

[officail litx wiki](https://github.com/enjoy-digital/litex/wiki).

[Litex MIPI CSI](https://github.com/gatecat/litex-nexus-mipi)

ICEStorm Install [icestorm_install.md](subtitles/icestorm_install.md)

[getting-started-with-litex](https://sourcesup.renater.fr/www/mic-sec-2022/labs/getting-started-with-litex.html)

## Chisel FPGA開発日記

[https://msyksphinz.hatenablog.com/](https://msyksphinz.hatenablog.com/)

[https://www.hatena.ne.jp/](https://www.hatena.ne.jp/)

[https://hatenablog.com/](https://hatenablog.com/)

[Agile Hardware Design Video 2024](https://www.youtube.com/playlist?list=PLfrN7RIcMe6g2LBRJLTHTdhyj5s8ag0Rg)

![Chip Alliance](../images/2025/Screenshot%20from%202025-02-04%2016-58-19.png)

[Chip Alliance](https://www.chipsalliance.org/)

## Amaranth

[Amaranth HDL Document](https://amaranth-lang.org/docs/amaranth/v0.5.4/)

## Verilog

[https://verilogguide.readthedocs.io/en/latest/](https://verilogguide.readthedocs.io/en/latest/)

[https://www.chipverify.com/](https://www.chipverify.com/)

## Programming

### Linux Thread

Linuxとpthreadsによる マルチスレッドプログラミング入門｜サポート｜秀和システム
[Book Link](https://www.shuwasystem.co.jp/support/7980html/5372.html)

### Linux Kernel Module Programming Guide

[https://sysprog21.github.io/lkmpg/](https://sysprog21.github.io/lkmpg/)

Tutorial 1
[https://linux-kernel-labs.github.io/refs/heads/master/#](https://linux-kernel-labs.github.io/refs/heads/master/#)

## PICO

### PICO PIO Programming

[PICO PIO Programming Youtube Video](https://www.youtube.com/playlist?list=PLiRALtgGsxmZs_LXGkh09Zr2NUmk_mtEI)

[PICO PIO Programming Youtube Video Github Code](https://github.com/LifeWithDavid/Raspberry-Pi-Pico-PIO)

Web Reference :

[PICO PIO url](https://circuitcellar.com/research-design-hub/basics-of-design/programmable-io-programming/)

[A Practical Look at PIO on the Raspberry Pi Pico URL](https://blues.com/blog/raspberry-pi-pico-pio/)

[Introduction to the PIO (Programmable Input Output) of the RP2040](https://tutoduino.fr/en/pio-rp2040-en/)

[programmable-io-programming @ circuitcellar URL](https://circuitcellar.com/research-design-hub/basics-of-design/programmable-io-programming/)

### RP2040 PICO DMA

[RP2040 PICO DMA](https://mcuoneclipse.com/2023/04/02/rp2040-with-pio-and-dma-to-address-ws2812b-leds/)

[RP2040 PICO DAC DMA](https://vanhunteradams.com/Pico/DAC/DMA_DAC.html)

### RP2040 LVGL

[http://bbs.eeworld.com.cn/thread-1227666-1-1.html](http://bbs.eeworld.com.cn/thread-1227666-1-1.html)

[RT-Thread and LVGL](https://rt-thread.medium.com/get-raspberry-pi-pico-running-on-rt-thread-rtos-with-an-opensource-light-versatile-graphics-library-c1f708882bff)

## Python

### PySerial

PySerial Example Code [pyserial_sample.md](subtitles/pyserial_sample.md)

### Python FTDI for SPI

[Python FTDI for SPI](https://www.alexallmont.com/spi-refresher/)

``` py
from pyftdi.ftdi import Ftdi
Ftdi.show_devices()
from pyftdi.spi import SpiController

spi.configure('ftdi://ftdi:2232h:1:7b/1')
slave = spi.get_port(cs=1, freq=10E6, mode=2)
write_buf = b'\x01\x02\x03'
read_buf = slave.exchange(write_buf, duplex=True)
```

### Virtual Enviroment

``` py
python3 -m venv virtkv
source ./virtkv/bin/activate
```

[Pyenv](https://sdwh.dev/posts/2021/08/Python-Pyenv/)

Other Solution
[https://python-poetry.org/](https://python-poetry.org/)

## Chromebook

Install Desktop GUI in chromebook [chromebookDesktop.md](subtitles/chromebookDesktop.md)

## DSP

### Wavelet

[Wavelet_101](./subtitles/wavelet_101.md)

## Neuro Science

[Neuroscience exploration Video](https://www.youtube.com/playlist?list=PLgtmMKe4spCMzkiVa4-eSHVk-N4SC8r9K)

[Topological Data Analysis (TDA)](https://www.youtube.com/playlist?list=PLz-ep5RbHosVi8Qoyqvz1MEiYrz35Zb7F)
