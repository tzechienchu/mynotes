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

## OpenCV

### Python Precision Delay

```py
start_time_ns = time.perf_counter_ns()
time_elapsed_ns = time.perf_counter_ns() - start_time_ns
while(time_elapsed_ns < gap_us*1000):
    time_elapsed_ns = time.perf_counter_ns() - start_time_ns
```

### UVC camera exposure timing in OpenCV

```py

ExpoTime_ms = 5

fourcc = cv2.VideoWriter_fourcc('M','J','P','G')
#camera.set(cv2.CAP_PROP_AUTO_EXPOSURE, 0.25) On
camera.set(cv2.CAP_PROP_AUTO_EXPOSURE, 0.75)

camera.set(cv2.CAP_PROP_FOURCC, fourcc)
camera.set(cv2.CAP_PROP_FRAME_WIDTH, 800)
camera.set(cv2.CAP_PROP_FRAME_HEIGHT,600)
camera.set(cv2.CAP_PROP_FPS, 120) # Must after CAP_PROP_FOURCC
camera.set(cv2.CAP_PROP_EXPOSURE, ExpoTime_ms*10)

```

### OpenCV Camera Caputer and Display

```py
ret, frame = camera.read()
    
ret = camera.grab()
ret, frame = camera.retrieve()

cv2.imshow("image1", frame)

if cv2.waitKey(1) & 0xff == ord('q'):
    print("exit")
    break
```

---

## Python TK

### Python UART TK GUI Program

```py
import tkinter as tk
from tkinter import ttk, scrolledtext
import serial
import serial.tools.list_ports
import threading

class SerialGui:
    def __init__(self, root):
        self.root = root
        self.root.title("USB Serial JSON Controller")
        self.serial_port = None
        self.running = False

        # --- 1. 連接設定區域 ---
        setup_frame = tk.Frame(root)
        setup_frame.pack(pady=10, padx=10, fill='x')

        tk.Label(setup_frame, text="Port:").pack(side='left')
        self.port_var = tk.StringVar()
        self.port_combo = ttk.Combobox(setup_frame, textvariable=self.port_var, width=15)
        self.port_combo.pack(side='left', padx=5)
        self.refresh_ports()

        tk.Label(setup_frame, text="Baud:").pack(side='left', padx=5)
        self.baud_var = tk.StringVar(value="115200")
        self.baud_combo = ttk.Combobox(setup_frame, textvariable=self.baud_var, values=["9600", "115200"], width=8)
        self.baud_combo.pack(side='left', padx=5)

        self.btn_connect = tk.Button(setup_frame, text="Connect", command=self.toggle_connection)
        self.btn_connect.pack(side='left', padx=10)

        # --- 2. 快速 JSON 指令按鈕區 (每列 5 個) ---
        btn_frame = tk.LabelFrame(root, text="Quick JSON Commands")
        btn_frame.pack(pady=5, padx=10, fill='x')

        json_commands = [
            ("Status", '{"status":1}'),
            ("USB Data On", '{"send_to_usb":1}'),
            ("USB Data Off", '{"send_to_usb":0}'),
            ("Speed 100/100", '{"speed":2}'),
            ("TWIST1", '{"TWIST_MODE":1}'),
            ("TWIST2", '{"TWIST_MODE2":1}'),
            ("PC Control", '{"CMD_MODE":1}'),
        ]

        MAX_COLUMNS = 5
        for index, (label, cmd) in enumerate(json_commands):
            r = index // MAX_COLUMNS
            c = index % MAX_COLUMNS
            btn = tk.Button(btn_frame, text=label, width=10, 
                            command=lambda c=cmd: self.send_json_cmd(c))
            btn.grid(row=r, column=c, padx=5, pady=5, sticky='we')

        # --- 3. 接收資料顯示區 ---
        self.txt_output = scrolledtext.ScrolledText(root, height=15, width=65)
        self.txt_output.pack(pady=10, padx=10)

        # --- 4. 自定義發送區 ---
        input_frame = tk.Frame(root)
        input_frame.pack(pady=10, padx=10, fill='x')

        self.ent_input = tk.Entry(input_frame)
        self.ent_input.pack(side='left', fill='x', expand=True, padx=5)
        
        self.btn_send = tk.Button(input_frame, text="Send Raw", command=self.send_data)
        self.btn_send.pack(side='right', padx=5)

    def refresh_ports(self):
        ports = [p.device for p in serial.tools.list_ports.comports()]
        self.port_combo['values'] = ports
        if ports: self.port_combo.current(0)

    def toggle_connection(self):
        if not self.serial_port or not self.serial_port.is_open:
            try:
                self.serial_port = serial.Serial(self.port_var.get(), int(self.baud_var.get()), timeout=0.1)
                self.running = True
                self.btn_connect.config(text="Disconnect", bg="#ff9999")
                threading.Thread(target=self.receive_data, daemon=True).start()
            except Exception as e:
                self.log(f"Error: {e}\n")
        else:
            self.running = False
            if self.serial_port: self.serial_port.close()
            self.btn_connect.config(text="Connect", bg="SystemButtonFace")

    def send_data(self):
        if self.serial_port and self.serial_port.is_open:
            data = self.ent_input.get() + "\n"
            self.serial_port.write(data.encode('utf-8'))
            self.log(f">> Sent: {data}")
            self.ent_input.delete(0, tk.END)

    def send_json_cmd(self, json_cmd):
        if self.serial_port and self.serial_port.is_open:
            self.serial_port.write((json_cmd + "\n").encode('utf-8'))
            self.log(f">> Sent JSON: {json_cmd}\n")
        else:
            self.log("Error: Please connect to a port first!\n")

    def receive_data(self):
        while self.running:
            if self.serial_port and self.serial_port.in_waiting > 0:
                try:
                    data = self.serial_port.read(self.serial_port.in_waiting).decode('utf-8', errors='replace')
                    self.log(data)
                except:
                    pass

    def log(self, msg):
        self.txt_output.insert(tk.END, msg)
        self.txt_output.see(tk.END)

if __name__ == "__main__":
    root = tk.Tk()
    app = SerialGui(root)
    root.mainloop()
    
```

---

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
