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

## Amaranth

[Amaranth HDL Document](https://amaranth-lang.org/docs/amaranth/latest/)

## Verilog

[https://verilogguide.readthedocs.io/en/latest/](https://verilogguide.readthedocs.io/en/latest/)

[https://www.chipverify.com/](https://www.chipverify.com/)

## The Art of FPGA Design - element14 Community

[The Art of FPGA Design - element14 Community](https://community.element14.com/technologies/fpga-group/b/blog/posts/the-art-of-fpga-design)

## Digital Signal Processing, from Algorithm to FPGA Bitstream - element14 Community

[Digital Signal Processing, from Algorithm to FPGA Bitstream](https://community.element14.com/technologies/fpga-group/b/blog/posts/the-art-of-fpga-design-season-2---digital-signal-processing-from-algorithm-to-fpga-bitstream)

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

## PICO

### PICO PIO Programming

[PICO PIO Programming Youtube Video](https://www.youtube.com/playlist?list=PLiRALtgGsxmZs_LXGkh09Zr2NUmk_mtEI)

[PICO PIO Programming Youtube Video Github Code](https://github.com/LifeWithDavid/Raspberry-Pi-Pico-PIO)

Web Reference :

[PICO PIO url](https://circuitcellar.com/research-design-hub/basics-of-design/programmable-io-programming/)

[A Practical Look at PIO on the Raspberry Pi Pico URL](https://blues.com/blog/raspberry-pi-pico-pio/)

[Introduction to the PIO (Programmable Input Output) of the RP2040](https://tutoduino.fr/en/pio-rp2040-en/)

[programmable-io-programming @ circuitcellar URL](https://circuitcellar.com/research-design-hub/basics-of-design/programmable-io-programming/)

[playing-with-the-pico](https://gregchadwick.co.uk/blog/playing-with-the-pico-pt1/)

### RP2040 PICO DMA

[RP2040 PICO DMA](https://mcuoneclipse.com/2023/04/02/rp2040-with-pio-and-dma-to-address-ws2812b-leds/)

[RP2040 PICO DAC DMA](https://vanhunteradams.com/Pico/DAC/DMA_DAC.html)

### RP2040 LVGL

[http://bbs.eeworld.com.cn/thread-1227666-1-1.html](http://bbs.eeworld.com.cn/thread-1227666-1-1.html)

[RT-Thread and LVGL](https://rt-thread.medium.com/get-raspberry-pi-pico-running-on-rt-thread-rtos-with-an-opensource-light-versatile-graphics-library-c1f708882bff)

### RP2040 Arduino How to Make 2 PWM Start at the same time

```c

#define _PWM_LOGLEVEL_        0
#include "RP2040_PWM.h"

//creates pwm instance
RP2040_PWM* PWM_Instance1;
RP2040_PWM* PWM_Instance2;

#define FREQ1  2000000
#define FREQ2  2000000

int updated = 0;

void setup() {
  Serial.begin(115200);

  PWM_Instance1 = new RP2040_PWM(10, FREQ1, 0);
  PWM_Instance1->setPWM(10, FREQ1, 0);
  turn_on_pwm1();
}

void turn_on_pwm1()
{
  rp2040.fifo.push(1);
  PWM_Instance1->setPWM(10, FREQ1, 50);  
}
void turn_on_pwm2()
{
  while(1) {
    if (rp2040.fifo.available()) {
        PWM_Instance2->setPWM(12, FREQ2, 50);
        rp2040.fifo.pop();
        return;
    }
  }
}

void setup1() {
  PWM_Instance2 = new RP2040_PWM(12, FREQ2, 0);
  PWM_Instance2->setPWM(12, FREQ2, 0);
  turn_on_pwm2();
}
void loop() {
  delay(1000);
}
void loop1() {

  delay(1000);
}
```

## Python

### 使用 uv 管理 Python 環境

[使用 uv 管理 Python 環境](https://docs.astral.sh/uv/)

[UV Commands](https://docs.astral.sh/uv/getting-started/features/)

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

### Makefile for Python

# Makefile for Python project

```
# 預設目標，當直接執行 make 時會執行的目標
.PHONY: all
all: install test build

# 安裝相依性
.PHONY: install
install:
	pip install -r requirements.txt

# 執行測試
.PHONY: test
test:
	python -m unittest discover tests

# 打包程式碼 (使用 setuptools)
.PHONY: build
build:
	python setup.py sdist bdist_wheel

# 清理
.PHONY: clean
clean:
	rm -rf build dist *.egg-info
```

## Micropython

### Pyboard Sleep and Wakeup

[lowpower.py](https://gist.github.com/dpgeorge/bf477eb883b6d189eae9)

```py
import pyb, stm
from pyb import Pin

    # wakeup callback
    wakeup = False
    def cb(exti):
        nonlocal wakeup
        wakeup = True

    # configure switch to generate interrupt on press
    sw = pyb.Switch()
    sw.callback(lambda:cb(0))

    # function to flash an LED
    def flash(led):
        led.on()
        pyb.delay(100)
        led.off()

    while True:
        # standby (need to exit by pressing RST, or wait 15s)
        if stm.mem32[stm.RTC + stm.RTC_BKP1R] == 0:
            flash(led1)
            stm.mem32[stm.RTC + stm.RTC_BKP1R] = 1
            rtc.wakeup(15000, cb)
            pyb.standby()
        else:
            stm.mem32[stm.RTC + stm.RTC_BKP1R] = 0

        # stop
        flash(led2)
        led_off()
        pyb.stop()
        led_on()

        # idle
        flash(led3)
        wakeup = False
        while not wakeup:
            pyb.wfi()

        # run
        flash(led4)
        wakeup = False
        while not wakeup:
            pass
```

### Micropython pyBoard DAC use DMA

```py
import math
from array import array
from pyb import DAC

# create a buffer containing a sine-wave, using half-word samples
buf = array('H', 2048 + int(2047 * math.sin(2 * math.pi * i / 128)) for i in range(128))

# output the sine-wave at 400Hz
dac = DAC(1, bits=12)
dac.write_timed(buf, 400 * len(buf), mode=DAC.CIRCULAR)
```

### Micropython Debounce

```py
import pyb

def wait_pin_change(pin):
    # wait for pin to change value
    # it needs to be stable for a continuous 20ms
    cur_value = pin.value()
    active = 0
    while active < 20:
        if pin.value() != cur_value:
            active += 1
        else:
            active = 0
        pyb.delay(1)

pin_x1 = pyb.Pin('X1', pyb.Pin.IN, pyb.Pin.PULL_DOWN)
while True:
    wait_pin_change(pin_x1)
    pyb.LED(4).toggle()
    
```

### Micropython json

```py
import ujson
parsed = ujson.loads("""{"name":"John"}""")
print(parsed)
```

### Micropython USB UART Passthrough

```py
import pyb
import select

def pass_through(usb, uart):
    usb.setinterrupt(-1)
    while True:
        select.select([usb, uart], [], [])
        if usb.any():
            uart.write(usb.read(256))
        if uart.any():
            usb.write(uart.read(256))

pass_through(pyb.USB_VCP(), pyb.UART(1, 9600, timeout=0))
```

### Micropython GPIO IRQ

```py
# Rui Santos & Sara Santos - Random Nerd Tutorials
# Complete project details at https://RandomNerdTutorials.com/raspberry-pi-pico-interrupts-micropython/

from machine import Pin

button = Pin(21, Pin.IN, Pin.PULL_DOWN)

def button_pressed(pin):
    print("Button Pressed!")

# Attach the interrupt to the button's rising edge
button.irq(trigger=Pin.IRQ_RISING, handler=button_pressed)
```

### Micropython Timer IRQ

```py
# Rui Santos & Sara Santos - Random Nerd Tutorials
# Complete project details at https://RandomNerdTutorials.com/raspberry-pi-pico-interrupts-micropython/

from machine import Pin, Timer
from time import sleep

# LED pin
led_pin = 20
led = Pin(led_pin, Pin.OUT)

# Callback function for the timer
def toggle_led(timer):
    led.value(not led.value())  # Toggle the LED state (ON/OFF)

# Create a periodic timer
blink_timer = Timer()
blink_timer.init(mode=Timer.PERIODIC, period=500, callback=toggle_led)  # Timer repeats every half second

# Main loop (optional)
while True:
    print('Main Loop is running')
    sleep(2)
```

### Micrpython IRQ

[raspberry-pi-pico-interrupts-micropython](https://randomnerdtutorials.com/raspberry-pi-pico-interrupts-micropython/)

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
