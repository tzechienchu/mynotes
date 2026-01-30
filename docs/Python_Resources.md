# Python Reource and Tutorial

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

### FTDI SPI DDS AD9833

```py
from pyftdi.spi import SpiController


############user changes these###############
user_freq = 1000

#pinout from H232 for SPI
'''
ad0 SCLK to UNO pin 13
ad1 MOSI to UNO pin     11
ad2 MISO to UNO pin 12 (not used)
ad3 CS0 to UNO pin 10
ad4 cs1 ... ad7 CS4.
'''
#WE WANT TO BE ABLE TO ENTER A FREQ TO SHOW ON SCOPE.
# Instantiate a SPI controller
# We need want to use A*BUS4 for /CS, so at least 2 /CS lines should be
# reserved for SPI, the remaining IO are available as GPIOs.


def get_dec_freq(freq):
    bignum = 2**28
    f = freq
    clock=25000000 #if your clock is different enter that here./
    dec_freq = f*bignum/clock
    return int(dec_freq)


padded_binary = 0
bits_pushed = 0
d = get_dec_freq(user_freq)

print("freq int returned is: " + str(d))

#turn into binary string.
str1 = bin(d)
#print(str1)

#get rid of first 2 chars.
str2 = str1[2:]
#print(str2)

#pad whatever we have so far to 28 bits:
longer = str2.zfill(28)
#print("here is 28 bit version of string")
#print(str(longer))
#print("here is length of that string")
#print(len(str(longer)))

lm1 = "01" + longer[:6]
lm2 = longer[6:14]
rm1 = "01" + longer[14:20]
rm2 = longer[20:]
# print(lm1 + " " + lm2  + " " + rm1 + " " + rm2)


def str_2_int(strx):
    numb = int(strx, 2)
    return numb

lm1x = str_2_int(lm1)
lm2x = str_2_int(lm2)
rm1x = str_2_int(rm1)
rm2x = str_2_int(rm2)
print(str(lm1x) + " " + str(lm2x)  + " " + str(rm1x) + " " + str(rm2x))

##########
#freq0_loadlower16 = [80,199]
#freq0_loadupper16 = [64,0]
#64 0 80 198


spi = SpiController(cs_count=2)
device = 'ftdi://ftdi:232h:0:1/1'
# Configure the first interface (IF/1) of the FTDI device as a SPI master
spi.configure(device)

# Get a port to a SPI slave w/ /CS on A*BUS4 and SPI mode 2 @ 10MHz
slave = spi.get_port(cs=1, freq=8E6, mode=2)


freq0_loadlower16 = [rm1x,rm2x]
freq0_loadupper16 = [lm1x,lm2x]

cntrl_reset = [33,0]

phase0 = [192,0]

cntrl_write = [32,0]

send2_9833 = cntrl_reset + freq0_loadlower16 + freq0_loadupper16 + phase0 + cntrl_write

print(send2_9833)

qq = bytearray(send2_9833)
# Synchronous exchange with the remote SPI slave
#write_buf = qq
#read_buf = slave.exchange(write_buf, duplex=False)
slave.exchange(out=qq, readlen=0, start=True, stop=True, duplex=False, droptail=0)
slave.flush()

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

### Makefile for Python project

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

### Python Import Module from Parent Folder

```py

import os
import sys

current_dir = os.path.dirname(os.path.abspath(__file__))
parent_dir = os.path.join(current_dir, '..')
sys.path.append(os.path.abspath(parent_dir))

```

### Python for PDF

```py
    from pypdf import PdfReader, PdfWriter

    reader = PdfReader("input.pdf")
    writer = PdfWriter()

    for page in reader.pages:
        # Define new crop box coordinates (adjust as needed)
        # Example: crop 10 units from each side
        left = page.mediabox.left + 10
        bottom = page.mediabox.bottom + 10
        right = page.mediabox.right - 10
        top = page.mediabox.top - 10

        page.mediabox.lower_left = (left, bottom)
        page.mediabox.upper_right = (right, top)
        writer.add_page(page)

    with open("output_cropped.pdf", "wb") as fp:
        writer.write(fp)
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
