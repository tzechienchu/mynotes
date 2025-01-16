# FT2232 for Intan through SPI

PYFTDI Github 
[https://github.com/eblot/pyftdi](https://github.com/eblot/pyftdi)

```py
import serial
import serial.tools.list_ports

port_list = list(serial.tools.list_ports.comports())
for i in range(0, len(port_list)):
    print(port_list[i])

port = input("Select Ports (/dev/ttyACM0)")
if (port == ""):
    port = "/dev/ttyACM0"

ser = serial.Serial(port, 2152000, timeout=1)

ser.write(b'cmm=speed,1000\r\n')

```