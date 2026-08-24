# Python Reource and Tutorial

### 使用 uv 管理 Python 環境

[使用 uv 管理 Python 環境](https://docs.astral.sh/uv/)

[UV Commands](https://docs.astral.sh/uv/getting-started/features/)

### UV

####  UV Commands

    Project setup
    uv init myproject           # Create new project
    uv add requests             # Add dependency
    uv remove requests             # Remove dependency
    uv sync                     # Install from lockfile

    Running code
    uv run script.py    # Run in project environment
    uv run pytest              # Run tests

    Python management
    uv python install 3.12     # Install Python version
    uv python pin 3.12         # Set project Python

    Tool usage
    uvx black .                # Run tool temporarily
    uv tool install ruff       # Install tool globally

    Package management (pip-compatible)
    uv pip install requests  # Direct pip replacement
    uv pip install -r requirements.txt

#### UV Kickstart

- uv init project_folder_name
- uv add package_name
- uv add package_name --dev

#### after UV -> Git

- git init
- git add .
- git commit -m "Initial commit"

#### Create new repo with GitHub CLI

- gh repo create my-project --private --source=. --remote=origin --push

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

### Connect PM400 to Python at Ubuntu

To connect a Thorlabs PM400 power meter on Ubuntu 22.04 LTS, the most reliable approach is using the Standard Commands for Programmable Instruments (SCPI) via Python's pyvisa library. Thorlabs power meters natively present themselves as USBTMC (USB Test and Measurement Class) devices on Linux systems, eliminating the need for proprietary Windows .dll drivers

- pip3 install pyvisa pyvisa-py
- sudo nano /etc/udev/rules.d/99-thorlabs.rules
                                                
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1313", ATTRS{idProduct}=="8075", MODE="0666", GROUP="plugdev"

KERNEL=="usbtmc*", ATTRS{idVendor}=="1313", ATTRS{idProduct}=="8075", MODE="0666", GROUP="plugdev"

- sudo udevadm control --reload-rules
- sudo udevadm trigger

```py
import pyvisa
import time

def connect_thorlabs_pm400():
    # Initialize the Visa Resource Manager using the pure-Python backend (@py)
    rm = pyvisa.ResourceManager('@py')
    
    # List all discovered resources
    resources = rm.list_resources()
    print("Discovered VISA Resources:", resources)
    
    pm400_resource = None
    for res in resources:
        # Check for Thorlabs USBTMC identifier strings (Vendor ID 0x1313)
        if "0x1313" in res or "USB" in res:
            pm400_resource = res
            break
            
    if not pm400_resource:
        print("Error: Thorlabs PM400 device not found. Check physical USB connection and udev rules.")
        return

    print(f"Connecting to: {pm400_resource}")
    
    try:
        # Open connection with defined termination character required by Thorlabs SCPI
        instrument = rm.open_resource(pm400_resource, read_termination='\n', write_termination='\n')
        
        # Query device identification details
        idn = instrument.query('*IDN?')
        print(f"Connected to Device Identification: {idn.strip()}")
        
        # Configure PM400 to measure Power in Watts
        instrument.write('CONF:POW') 
        
        # Read the current wavelength configuration
        current_wavelength = instrument.query('SENS:CORR:WAV?')
        print(f"Current Wavelength Setting: {current_wavelength.strip()} nm")
        
        # Example: Set to a specific wavelength if needed (uncomment line below)
        # instrument.write('SENS:CORR:WAV 532')
        
        print("\nStarting live power logging (Press Ctrl+C to stop)...")
        while True:
            # Request a new measurement sample from the sensor head
            power_str = instrument.query('READ?')
            power_val = float(power_str)
            
            # Format the output cleanly into scientific notation
            print(f"Measured Power: {power_val:.6e} W", end='\r')
            time.sleep(0.5)
            
    except KeyboardInterrupt:
        print("\nMeasurement loop stopped by user.")
    except Exception as e:
        print(f"\nAn error occurred: {e}")
    finally:
        if 'instrument' in locals():
            instrument.close()
            print("Instrument connection securely closed.")

if __name__ == "__main__":
    connect_thorlabs_pm400()

```
---

### An example of setting webcam settings via v4l2-ctl in a python script.

```py
import cv2
import subprocess

### for reference, the output of v4l2-ctl -d /dev/video1 -l (helpful for min/max/defaults)
#                     brightness (int)    : min=0 max=255 step=1 default=128 value=128
#                       contrast (int)    : min=0 max=255 step=1 default=128 value=128
#                     saturation (int)    : min=0 max=255 step=1 default=128 value=128
# white_balance_temperature_auto (bool)   : default=1 value=1
#                           gain (int)    : min=0 max=255 step=1 default=0 value=0
#           power_line_frequency (menu)   : min=0 max=2 default=2 value=2
#      white_balance_temperature (int)    : min=2000 max=6500 step=1 default=4000 value=2594 flags=inactive
#                      sharpness (int)    : min=0 max=255 step=1 default=128 value=128
#         backlight_compensation (int)    : min=0 max=1 step=1 default=0 value=0
#                  exposure_auto (menu)   : min=0 max=3 default=3 value=1
#              exposure_absolute (int)    : min=3 max=2047 step=1 default=250 value=333
#         exposure_auto_priority (bool)   : default=0 value=1
#                   pan_absolute (int)    : min=-36000 max=36000 step=3600 default=0 value=0
#                  tilt_absolute (int)    : min=-36000 max=36000 step=3600 default=0 value=0
#                 focus_absolute (int)    : min=0 max=250 step=5 default=0 value=125
#                     focus_auto (bool)   : default=1 value=0
#                  zoom_absolute (int)    : min=100 max=500 step=1 default=100 value=100

### I created a dict of the settings of interest
### note that if you have any auto settings on, e.g. focus_auto=1,
### it will complain when it goes to set focus_absolute, but I didn't have
### any issues other than the warning
cam_props = {'brightness': 128, 'contrast': 128, 'saturation': 180,
             'gain': 0, 'sharpness': 128, 'exposure_auto': 1,
             'exposure_absolute': 150, 'exposure_auto_priority': 0,
             'focus_auto': 0, 'focus_absolute': 30, 'zoom_absolute': 250,
             'white_balance_temperature_auto': 0, 'white_balance_temperature': 3300}

### go through and set each property; remember to change your video device if necessary~
### on my RPi, video0 is the usb webcam, but for my laptop the built-in one is 0 and the
### external usb cam is 1
for key in cam_props:
    subprocess.call(['v4l2-ctl -d /dev/video1 -c {}={}'.format(key, str(cam_props[key]))],
                    shell=True)

### uncomment to print out/verify the above settings took
# subprocess.call(['v4l2-ctl -d /dev/video1 -l'], shell=True)
  
### showing that I *think* one should only create the opencv capture object after these are set
### also remember to change the device number if necessary
cam = cv2.VideoCapture(1)
```

💾[Gist Source](https://gist.github.com/dortmans/7b27b76ad911ca4d9ebefe6c8b7e6f64)

---

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

### Python Functional Pointer 

```py
PAYMENT_HANDLERS = {}

def register(payment_type):
    def decorator(func):
        PAYMENT_HANDLERS[payment_type] = func
        return func
    return decorator

@register("credit_card")
def charge_credit_card(amount):
    return f"Charged ${amount} to credit card"

@register("paypal")
def charge_paypal(amount):
    return f"Charged ${amount} via PayPal"

@register("crypto")
def charge_crypto(amount):
    return f"Charged ${amount} in crypto"

def process_payment(payment_type, amount):
    handler = PAYMENT_HANDLERS.get(payment_type)
    if handler is None:
        raise ValueError(f"Unknown payment type: {payment_type!r}")
    return handler(amount)
```

```py
class Registry:
    """A reusable name-to-object registry."""

    def __init__(self, name):
        self.name = name
        self._registry = {}

    def register(self, key):
        def decorator(obj):
            if key in self._registry:
                raise KeyError(
                    f"{key!r} already registered in {self.name!r}"
                )
            self._registry[key] = obj
            return obj
        return decorator

    def get(self, key):
        if key not in self._registry:
            raise KeyError(
                f"{key!r} not found in {self.name!r}. "
                f"Available: {list(self._registry)}"
            )
        return self._registry[key]

    def __contains__(self, key):
        return key in self._registry

    def keys(self):
        return self._registry.keys()
```

```py

transforms = Registry("transforms")

@transforms.register("lowercase")
def to_lower(text):
    return text.lower()

@transforms.register("strip")
def strip_whitespace(text):
    return text.strip()

@transforms.register("remove_digits")
def remove_digits(text):
    return "".join(c for c in text if not c.isdigit())

# The pipeline is now just data. It could come from a YAML file,
# a CLI argument, or a database row.
pipeline = ["strip", "lowercase", "remove_digits"]
text = "  Order #4521 CONFIRMED  "

for step in pipeline:
    text = transforms.get(step)(text)

print(repr(text))

Output:
'order # confirmed'
```

---

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
from tkinter import ttk, messagebox, scrolledtext
import serial
import serial.tools.list_ports
import json
import threading
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
import csv
import queue
from datetime import datetime

# ==========================================================
# CUSTOMIZE YOUR SETTINGS HERE
# ==========================================================

# Updated Button Configuration as requested
BUTTON_CONFIG = [
    {"text": "Status", "cmd": {"status": 1}},
    {"text": "Speed 100",  "cmd": {"speed": 2}},
    {"text": "Speed 150",  "cmd": {"speed": 3}},
    {"text": "Speed 200",  "cmd": {"speed": 4}},
    {"text": "Speed 250",  "cmd": {"speed": 5}},
    {"text": "TWIST1", "cmd": {"TWIST_MODE": 1}},
    {"text": "TWIST2",  "cmd": {"TWIST_MODE2": 1}},
    {"text": "PC Control", "cmd": {"CMD_MODE": 1}},
    {"text": "Disable to USB", "cmd": {"send_to_usb": 0}},
    {"text": "Enable to USB",  "cmd": {"send_to_usb": 1}},
]

# Define Groups of keys to display
DATA_GROUPS = {
    "Group 1 (Quaternion)": ["qw", "qx", "qy", "qz"],
    "Group 2 (Accel)": ["ax", "ay", "az"],
    "Group 3 (Gyro)": ["gx", "gy", "gz"]
}

# Unique keys for CSV logging
ALL_CSV_KEYS = sorted(list(set([key for group in DATA_GROUPS.values() for key in group])))

# ==========================================================

class UARTApp:
    def __init__(self, root):
        self.root = root
        self.root.title("UART JSON Controller - Pro Edition")
        
        self.max_pts = 100
        self.group_names = list(DATA_GROUPS.keys())
        self.current_group_name = self.group_names[0]
        self.current_keys = DATA_GROUPS[self.current_group_name]
        
        self.init_data_buffer()
        
        self.ser = None
        self.is_running = False
        self._after_id = None
        
        self.csv_queue = queue.Queue()
        self.save_enabled = tk.BooleanVar(value=False)
        self.log_filename = ""

        self.setup_ui()
        self.update_plot_loop()

    def init_data_buffer(self):
        num_keys = len(self.current_keys)
        self.data = np.zeros((num_keys, self.max_pts), dtype=np.float64)
        self.x_axis = np.arange(self.max_pts, dtype=np.int32)

    def setup_ui(self):
        # --- Top: Connection & Global Controls ---
        top_frame = ttk.Frame(self.root)
        top_frame.pack(side="top", fill="x", padx=10, pady=5)
        
        ttk.Label(top_frame, text="Port:").pack(side="left")
        self.port_cb = ttk.Combobox(top_frame, width=15)
        self.refresh_ports()
        self.port_cb.pack(side="left", padx=5)
        ttk.Button(top_frame, text="Refresh", command=self.refresh_ports, width=8).pack(side="left")
        
        self.btn_conn = ttk.Button(top_frame, text="Connect", command=self.toggle_serial)
        self.btn_conn.pack(side="left", padx=10)

        self.chk_save = ttk.Checkbutton(top_frame, text="Enable Saving (CSV)", variable=self.save_enabled)
        self.chk_save.pack(side="left", padx=5)
        
        self.log_status_lbl = ttk.Label(top_frame, text="Status: Idle", foreground="gray")
        self.log_status_lbl.pack(side="left", padx=10)
        
        ttk.Button(top_frame, text="Quit Program", command=self.quit_program).pack(side="right")

        # --- Main Layout ---
        main_paned = ttk.Panedwindow(self.root, orient="horizontal")
        main_paned.pack(fill="both", expand=True, padx=10, pady=5)

        # --- Left Panel: Controls ---
        left_panel = ttk.Frame(main_paned)
        main_paned.add(left_panel, weight=3)

        # 1. Custom Command Buttons (Updated Layout for 10 buttons)
        btn_grid = ttk.LabelFrame(left_panel, text="Command Buttons")
        btn_grid.pack(fill="x", pady=5)
        for i, config in enumerate(BUTTON_CONFIG):
            cmd_str = json.dumps(config['cmd'])
            btn = ttk.Button(btn_grid, text=config['text'], command=lambda s=cmd_str: self.send_data(s))
            # Grid: 2 rows x 5 columns
            btn.grid(row=i//5, column=i%5, padx=2, pady=4, sticky="nsew")
        btn_grid.columnconfigure((0,1,2,3,4), weight=1)

        # 2. Value Slider
        slider_frame = ttk.LabelFrame(left_panel, text="Value Selection (-1.0 to 1.0)")
        slider_frame.pack(fill="x", pady=5)
        self.val_var = tk.DoubleVar(value=0.0)
        self.slider = tk.Scale(slider_frame, from_=-1.0, to=1.0, resolution=0.05, 
                              orient="horizontal", variable=self.val_var)
        self.slider.pack(side="left", padx=10, expand=True, fill="x")
        ttk.Button(slider_frame, text="Send Value", 
                   command=lambda: self.send_data(json.dumps({"type": "set_val", "value": round(self.val_var.get(), 2)}))
                   ).pack(side="right", padx=10)

        # 3. Group Picker
        picker_frame = ttk.LabelFrame(left_panel, text="Display Data Group")
        picker_frame.pack(fill="x", pady=5)
        self.group_cb = ttk.Combobox(picker_frame, values=self.group_names, state="readonly")
        self.group_cb.current(0)
        self.group_cb.bind("<<ComboboxSelected>>", self.on_group_change)
        self.group_cb.pack(side="left", fill="x", expand=True, padx=10, pady=10)

        # 4. Plot Area
        self.fig, self.ax = plt.subplots(figsize=(5, 3), dpi=100)
        self.setup_plot_lines()
        self.canvas = FigureCanvasTkAgg(self.fig, master=left_panel)
        self.canvas.get_tk_widget().pack(fill="both", expand=True, pady=5)

        # --- Right Panel: Terminal ---
        right_panel = ttk.LabelFrame(main_paned, text="Terminal Viewer")
        main_paned.add(right_panel, weight=2)
        self.log_area = scrolledtext.ScrolledText(right_panel, state='disabled', font=("Consolas", 9))
        self.log_area.pack(fill="both", expand=True, padx=5, pady=5)

        send_frame = ttk.Frame(right_panel)
        send_frame.pack(fill="x", padx=5, pady=5)
        self.raw_input = ttk.Entry(send_frame)
        self.raw_input.pack(side="left", fill="x", expand=True)
        self.raw_input.bind("<Return>", lambda e: self.send_raw())
        ttk.Button(send_frame, text="Send Raw", command=self.send_raw).pack(side="right", padx=2)

    def setup_plot_lines(self):
        self.ax.clear()
        self.lines = []
        for key in self.current_keys:
            line, = self.ax.plot(self.x_axis, np.zeros(self.max_pts), label=key)
            self.lines.append(line)
        self.ax.legend(loc="upper right", ncol=len(self.current_keys), fontsize='x-small')
        self.ax.set_title(f"Group: {self.current_group_name}")
        self.ax.grid(True, linestyle=':', alpha=0.6)

    def on_group_change(self, event):
        self.current_group_name = self.group_cb.get()
        self.current_keys = DATA_GROUPS[self.current_group_name]
        self.init_data_buffer()
        self.setup_plot_lines()
        self.canvas.draw()

    def refresh_ports(self):
        ports = [p.device for p in serial.tools.list_ports.comports()]
        self.port_cb['values'] = ports
        if ports: self.port_cb.current(0)

    def toggle_serial(self):
        if not self.ser or not self.ser.is_open:
            try:
                self.ser = serial.Serial(self.port_cb.get(), 115200, timeout=0.1)
                self.is_running = True
                self.btn_conn.config(text="Disconnect")
                threading.Thread(target=self.read_serial, daemon=True).start()
                if self.save_enabled.get():
                    self.start_logging()
            except Exception as e:
                messagebox.showerror("Error", str(e))
        else:
            self.is_running = False
            if self.ser: self.ser.close()
            self.btn_conn.config(text="Connect")
            self.log_status_lbl.config(text="Status: Idle", foreground="gray")

    def start_logging(self):
        self.log_filename = f"log_{datetime.now().strftime('%Y%m%d_%H%M%S')}.csv"
        self.log_status_lbl.config(text=f"Logging: {self.log_filename}", foreground="green")
        threading.Thread(target=self.csv_logging_thread, daemon=True).start()

    def send_data(self, text):
        if self.ser and self.ser.is_open:
            self.ser.write((text + "\n").encode('utf-8'))
            self.log_to_ui(f"TX -> {text}\n")

    def send_raw(self):
        raw_text = self.raw_input.get()
        if raw_text:
            self.send_data(raw_text)
            self.raw_input.delete(0, tk.END)

    def log_to_ui(self, msg):
        self.log_area.config(state='normal')
        self.log_area.insert(tk.END, msg)
        self.log_area.see(tk.END)
        self.log_area.config(state='disabled')

    def read_serial(self):
        while self.is_running:
            if self.ser and self.ser.in_waiting:
                try:
                    line = self.ser.readline().decode('utf-8').strip()
                    if not line: continue
                    self.root.after(0, self.log_to_ui, f"RX <- {line}\n")
                    payload = json.loads(line)
                    self.data = np.roll(self.data, -1, axis=1)
                    for i, key in enumerate(self.current_keys):
                        self.data[i, -1] = payload.get(key, 0.0)
                    if self.save_enabled.get():
                        if not self.log_filename:
                            self.root.after(0, self.start_logging)
                        self.csv_queue.put(payload)
                except:
                    continue

    def csv_logging_thread(self):
        try:
            with open(self.log_filename, mode='a', newline='') as f:
                writer = csv.DictWriter(f, fieldnames=["timestamp"] + ALL_CSV_KEYS)
                writer.writeheader()
                while self.is_running and self.save_enabled.get():
                    try:
                        data = self.csv_queue.get(timeout=0.5)
                        row = {"timestamp": datetime.now().strftime('%H:%M:%S.%f')[:-3]}
                        for key in ALL_CSV_KEYS: row[key] = data.get(key, "")
                        writer.writerow(row)
                        f.flush()
                    except queue.Empty:
                        continue
        finally:
            self.log_filename = ""

    def update_plot_loop(self):
        if self.is_running:
            for i in range(len(self.current_keys)):
                self.lines[i].set_ydata(self.data[i])
            d_min, d_max = np.min(self.data), np.max(self.data)
            margin = max((d_max - d_min) * 0.1, 1.0)
            self.ax.set_ylim(d_min - margin, d_max + margin)
            self.canvas.draw_idle()
        self._after_id = self.root.after(50, self.update_plot_loop)

    def quit_program(self):
        self.is_running = False
        if self._after_id: self.root.after_cancel(self._after_id)
        if self.ser and self.ser.is_open: self.ser.close()
        self.root.quit()
        self.root.destroy()

if __name__ == "__main__":
    root = tk.Tk()
    root.geometry("1300x850")
    app = UARTApp(root)
    root.protocol("WM_DELETE_WINDOW", app.quit_program)
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

### Host Python Control PyBoard Through USB UART

```py
#
# This file is part of the MicroPython project, http://micropython.org/
#
# The MIT License (MIT)
#
# Copyright (c) 2014-2016 Damien P. George
# Copyright (c) 2017 Paul Sokolovsky
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.

"""
pyboard interface
This module provides the Pyboard class, used to communicate with and
control the pyboard over a serial USB connection.
Example usage:
    import pyboard
    pyb = pyboard.Pyboard('/dev/ttyACM0')
    pyb.enter_raw_repl()
    pyb.exec('pyb.LED(1).on()')
    pyb.exit_raw_repl()
To run a script from the local machine on the board and print out the results:
    import pyboard
    pyboard.execfile('test.py', device='/dev/ttyACM0')
This script can also be run directly.  To execute a local script, use:
    ./pyboard.py test.py
Or:
    python pyboard.py test.py
"""

import sys
import time
import serial

def stdout_write_bytes(b):
    sys.stdout.buffer.write(b)
    sys.stdout.buffer.flush()

class PyboardError(BaseException):
    pass

class Pyboard:
    def __init__(self, serial_device, baudrate=115200):
        self.serial = serial.Serial(serial_device, baudrate=baudrate, interCharTimeout=1)

    def close(self):
        self.serial.close()

    def read_until(self, min_num_bytes, ending, timeout=10, data_consumer=None):
        data = self.serial.read(min_num_bytes)
        if data_consumer:
            data_consumer(data)
        timeout_count = 0
        while True:
            if data.endswith(ending):
                break
            elif self.serial.inWaiting() > 0:
                new_data = self.serial.read(1)
                data = data + new_data
                if data_consumer:
                    data_consumer(new_data)
                #time.sleep(0.01)
                timeout_count = 0
            else:
                timeout_count += 1
                if timeout is not None and timeout_count >= 10 * timeout:
                    break
                time.sleep(0.1)
        return data

    def enter_raw_repl(self):
        self.serial.write(b'\r\x03\x03') # ctrl-C twice: interrupt any running program
        # flush input (without relying on serial.flushInput())
        n = self.serial.inWaiting()
        while n > 0:
            self.serial.read(n)
            n = self.serial.inWaiting()
        self.serial.write(b'\r\x01') # ctrl-A: enter raw REPL
        data = self.read_until(1, b'to exit\r\n>')
        if not data.endswith(b'raw REPL; CTRL-B to exit\r\n>'):
            print(data)
            raise PyboardError('could not enter raw repl')
        self.serial.write(b'\x04') # ctrl-D: soft reset
        data = self.read_until(1, b'to exit\r\n>')
        if not data.endswith(b'raw REPL; CTRL-B to exit\r\n>'):
            print(data)
            raise PyboardError('could not enter raw repl')

    def exit_raw_repl(self):
        self.serial.write(b'\r\x02') # ctrl-B: enter friendly REPL

    def follow(self, timeout, data_consumer=None):
        # wait for normal output
        data = self.read_until(1, b'\x04', timeout=timeout, data_consumer=data_consumer)
        if not data.endswith(b'\x04'):
            raise PyboardError('timeout waiting for first EOF reception')
        data = data[:-1]

        # wait for error output
        data_err = self.read_until(2, b'\x04>', timeout=timeout)
        if not data_err.endswith(b'\x04>'):
            raise PyboardError('timeout waiting for second EOF reception')
        data_err = data_err[:-2]

        # return normal and error output
        return data, data_err

    def exec_raw_no_follow(self, command):
        if isinstance(command, bytes):
            command_bytes = command
        else:
            command_bytes = bytes(command, encoding='utf8')

        # write command
        for i in range(0, len(command_bytes), 256):
            self.serial.write(command_bytes[i:min(i + 256, len(command_bytes))])
            time.sleep(0.01)
        self.serial.write(b'\x04')

        # check if we could exec command
        data = self.serial.read(2)
        if data != b'OK':
            raise PyboardError('could not exec command')

    def exec_raw(self, command, timeout=10, data_consumer=None):
        self.exec_raw_no_follow(command);
        return self.follow(timeout, data_consumer)

    def eval(self, expression):
        ret = self.exec('print({})'.format(expression))
        ret = ret.strip()
        return ret

    def exec(self, command):
        ret, ret_err = self.exec_raw(command)
        if ret_err:
            raise PyboardError('exception', ret, ret_err)
        return ret

    def execfile(self, filename):
        with open(filename, 'rb') as f:
            pyfile = f.read()
        return self.exec(pyfile)

    def get_time(self):
        t = str(self.eval('pyb.RTC().datetime()'), encoding='utf8')[1:-1].split(', ')
        return int(t[4]) * 3600 + int(t[5]) * 60 + int(t[6])

def execfile(filename, device='/dev/ttyACM0'):
    pyb = Pyboard(device)
    pyb.enter_raw_repl()
    output = pyb.execfile(filename)
    stdout_write_bytes(output)
    pyb.exit_raw_repl()
    pyb.close()
    
```

### ICEZero programming use Micropython

```py
##############################################################################
# ice_zero_prog.py
#             Copyright (c) Kevin M. Hubbard 2017 BlackMesaLabs
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#
# Description:
#   This uses GPIO pins on a RaspPi to bit-bang SPI protocol to a FPGA PROM.
#
# Revision History:
# Ver   When       Who       What
# ----  --------   --------  ---------------------------------------------------
# 0.01  2017.01.22 khubbard  Creation. Reads PROM ID.
# 0.02  2017.01.28 khubbard  Writes Lattice bitfil to PROM now.
# 0.02  2017.01.29 khubbard  Bulk Erase Added.
# NOTE: Sector Erase not working yet.
##############################################################################
import sys;
import RPi.GPIO as GPIO;
import time;
from time import sleep;

class App:
  def __init__(self):
    return;

  def main(self):
    self.main_init();
#   self.main_loop();

  def main_init( self ):
    args = sys.argv + [None]*5;# args[0] is this scripts name
    self.arg0     = args[1];
    self.arg1     = args[2];
    self.spi_link = spi_link( platform="ice_zero_proto" );
    self.prom     = micron_prom( self.spi_link );

    file_name = self.arg0;
    if ( self.arg1 == None ):
      addr = 0x000000;
    else:
      addr = int( self.arg1, 16 );

    (mfr_id,dev_id,dev_cap) = self.prom.read_id();
    print("Found "+mfr_id+" "+dev_id+" "+str(dev_cap)+" MBytes" );

    print("Bulk Erasing...");
    self.prom.erase();
    print("Complete.");

    print("Erasing and loading " + file_name + " to %06x" % addr );
    self.prom.write_file_to_mem( file_name, addr );

    # Read out 1st 8 bytes as visual check
    miso_bytes = self.prom.read_mem( addr, 8 );
    for each in miso_bytes:
      print("%02x" % ( each ) );

    self.spi_link.close();
    return;

  def main_loop( self ):
    while( True ):
      pass;# Not used for this design
    return;


###############################################################################
# Class for bit banging to Micron SPI PROM connected to Lattice ICE40 FPGA
# The memory is organized as 256 (64KB) main sectors that are further divided
# into 16 subsectors each (4096 subsectors in total). The memory can be erased 
# one 4KB subsector at a time, 64KB sectors at a time, or as a whole. 
class micron_prom:
  def __init__ ( self, spi_link ):
    self.id           = 0x9f;# Read ID   
    self.wr           = 0x02;# Page Program
    self.rd           = 0x03;# Read Data
    self.rd_status    = 0x05;# Read Data
    self.wr_en        = 0x06;# Write Enable 
    self.wr_dis       = 0x04;# Write Disable
    self.relpd        = 0xab;# Release Deep PowerDown
    self.subsec_erase = 0x20;# Erase Sector
    self.sec_erase    = 0xd8;# Erase Sector
    self.bulk_erase   = 0xc7;# Bulk Erase Device
    self.spi_link = spi_link;
    return;

  def read_id ( self ):
    miso_bytes = self.spi_link.xfer( [0x9F], 17 );# Micron READ_ID
    ( mfr_id, dev_id, dev_capacity ) = miso_bytes[0:3];
    if ( mfr_id == 0x20 ):
      mfr_id = "Micron";
    else:
      mfr_id = "%02" % mfr_id;
    if ( dev_id == 0xBA ): 
      dev_id = "N25Q128A";
    else:
      dev_id = "%02" % dev_id;
    dev_capacity = (2**dev_capacity) / (1024 * 1024 );
    return ( mfr_id, dev_id, dev_capacity );# 0x20, 0xBA, 0x18 == 128Mb

  def erase( self ):
    miso_bytes = self.spi_link.xfer( [ self.wr_en ], 0 );
    mosi_bytes = [ self.bulk_erase ];
    miso_bytes = self.spi_link.xfer( mosi_bytes, 0 );
    status = 0x01;# Loop until Status says erase is done
    while ( status & 0x01 != 0x00 ):
      status = self.spi_link.xfer( [ self.rd_status ], 1 )[0];
    miso_bytes = self.spi_link.xfer( [ self.wr_dis ], 0 );
    return;

  def read_mem ( self, addr, num_bytes ):
    mosi_bytes = [ self.rd, 
                   ( addr & 0xFF0000 ) >> 16,
                   ( addr & 0x00FF00 ) >>  8,
                   ( addr & 0x0000FF ) >>  0 ];
    miso_bytes = self.spi_link.xfer( mosi_bytes, num_bytes );
    return miso_bytes;

  def write_file_to_mem( self, file_name, addr ):
    # Great example of reading a binary file
    import array, struct;
    bytes = array.array('B');
    file_in = open ( file_name, 'r' );
    file_bytes = file_in.read();
    total_bytes = len( file_bytes );
    miso_bytes = self.spi_link.xfer( [ self.wr_en ], 0 );
    mosi_bytes = [ self.sec_erase, 
                   ( addr & 0xFF0000 ) >> 16,
                   ( addr & 0x00FF00 ) >>  8,
                   ( addr & 0x0000FF ) >>  0 ];
    miso_bytes = self.spi_link.xfer( mosi_bytes, 0 );# Erase the sector
    miso_bytes = self.spi_link.xfer( [ self.wr_dis ], 0 );
    
    status = 0x01;# Loop until Status says erase is done
    while ( status & 0x01 != 0x00 ):
      status = self.spi_link.xfer( [ self.rd_status ], 1 )[0];
    k = 0;
    perc = 0; xferd = 0;
    while( len( file_bytes ) > 0 ):
      if ( ( 100.0*float(xferd) / float(total_bytes) ) > perc ):
        print("%d%%" % (perc) );
        perc += 10;
      # Grab 256 bytes at a time
      if ( len( file_bytes ) > 256 ):
        xfer_bytes = file_bytes[0:256];
        file_bytes = file_bytes[256:];
      else:
        xfer_bytes = file_bytes[0:];
        file_bytes = [];
      mosi_bytes = [ self.wr, 
                     ( addr & 0xFF0000 ) >> 16,
                     ( addr & 0x00FF00 ) >>  8,
                     ( addr & 0x0000FF ) >>  0 ];
      for byte in xfer_bytes:
        mosi_bytes += [ ord( byte )];
      miso_bytes = self.spi_link.xfer( [ self.wr_en ], 0 );
      miso_bytes = self.spi_link.xfer( mosi_bytes, 0 );# Write 256 bytes  
      miso_bytes = self.spi_link.xfer( [ self.wr_dis ], 0 );
      status = 0x01;# Loop until Status says write is done
      while ( status & 0x01 != 0x00 ):
        status = self.spi_link.xfer( [ self.rd_status ], 1 )[0];
      addr += 256; xferd += 256;
    return;

  def write_mem ( self, addr, num_bytes ):
    mosi_bytes = [ self.rd, 
                   ( addr & 0xFF0000 ) >> 16,
                   ( addr & 0x00FF00 ) >>  8,
                   ( addr & 0x0000FF ) >>  0 ];
    miso_bytes = self.spi_link.xfer( mosi_bytes, num_bytes );
    return miso_bytes;

  def close( self ):
    return;


###############################################################################
# Class for bit banging to Micron SPI PROM connected to Lattice ICE40 FPGA
class spi_link:
  def __init__ ( self, platform ):
    try:
      import RPi.GPIO as GPIO;
    except:
      raise RuntimeError("ERROR: Unable to import RaspPi RPi.GPIO module");

    if ( platform == "ice_zero_proto" ):
      GPIO.setmode(GPIO.BOARD);
      self.pin_rst_l = 37;
      self.pin_clk   = 36;
      self.pin_cs_l  = 32;
      self.pin_miso  = 31;
      self.pin_mosi  = 33;
      self.pin_done  = 39;
    else:
      raise RuntimeError("ERROR: Unknown platform " + platform );

    GPIO.setup( self.pin_rst_l, GPIO.OUT, initial = GPIO.LOW );
    GPIO.setup( self.pin_cs_l , GPIO.OUT, initial = GPIO.HIGH );
    GPIO.setup( self.pin_clk  , GPIO.OUT, initial = GPIO.LOW  );
    GPIO.setup( self.pin_mosi , GPIO.OUT, initial = GPIO.LOW  );
    GPIO.setup( self.pin_miso , GPIO.IN                       );
    return;

  def close( self ):
    GPIO.setup( self.pin_cs_l , GPIO.IN );
    GPIO.setup( self.pin_clk  , GPIO.IN );
    GPIO.setup( self.pin_mosi , GPIO.IN );
    GPIO.setup( self.pin_rst_l, GPIO.IN );
    return;

  def xfer( self, mosi_bytes, miso_bytes_len ):
    GPIO.output( self.pin_cs_l , GPIO.LOW);# Assert Chip Select
    miso_bytes = [];
    for each_byte in mosi_bytes:
      shift_reg = each_byte;
      for i in range(0,8,1):
        bit = 0x80 & shift_reg;
        if ( bit == 0x00 ):
          GPIO.output( self.pin_mosi , GPIO.LOW);
        else:
          GPIO.output( self.pin_mosi , GPIO.HIGH);
        GPIO.output( self.pin_clk , GPIO.HIGH);
        GPIO.output( self.pin_clk , GPIO.LOW );
        shift_reg = ( shift_reg << 1 );
    for i in range(0, miso_bytes_len):
      shift_reg = 0x00;
      for i in range(0,8,1):
        bit = GPIO.input( self.pin_miso );
        GPIO.output( self.pin_clk , GPIO.HIGH);
        GPIO.output( self.pin_clk , GPIO.LOW );
        shift_reg = (bit     ) + (shift_reg << 1);
      miso_bytes += [ shift_reg ];   
    GPIO.output( self.pin_cs_l , GPIO.HIGH);# Assert Chip Select
    return miso_bytes;


###############################################################################
app = App();
app.main();
```
