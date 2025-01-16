# ICEStorm Install

[Verilog Example](https://github.com/johnwinans/Verilog-Examples)

```sh
ubuntu 22
Icestorm

sudo apt install iverilog -y
sudo apt install gtkwave -y
sudo apt install fpga-icestorm -y
sudo apt install yosys -y
sudo apt install nextpnr-ice40 -y
sudo apt install flashrom -y

Litex
sudo apt install git -y
sudo apt install python3-pip -y

wget https://raw.githubusercontent.com/enjoy-digital/litex/master/litex_setup.py
chmod +x litex_setup.py
./litex_setup.py --init --install --user (--user to install to user directory)

pip3 install meson ninja
sudo ./litex_setup.py --gcc=riscv
sudo apt install libevent-dev libjson-c-dev
```