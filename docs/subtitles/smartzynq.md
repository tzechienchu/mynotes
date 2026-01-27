# Hello FPGA Smart Zynq

## Resource Page

🌐[Smart Zynq SP](http://www.hellofpga.com/index.php/2023/04/27/smart-zynq-sp/)

---

## PS Project Page

🌐[PS-AXI_GPIO](http://www.hellofpga.com/index.php/2023/04/28/smart_zynq_axi_gpio-2-2/)

🌐[PS-UART](http://www.hellofpga.com/index.php/2023/04/28/ps_uart_test/)

---

## PL Project Page

---

## FPGA Type

Device Type: xc7z020clg484-1
DDR : MT41K256M16RE-125 16Bits

---

## Constrain

### UART

set_property PACKAGE_PIN M19 [get_ports clk]
set_property IOSTANDARD LVCMOS33 [get_ports clk]

set_property IOSTANDARD LVCMOS33 [get_ports rx]
set_property IOSTANDARD LVCMOS33 [get_ports tx]
set_property IOSTANDARD LVCMOS33 [get_ports rst_n]

set_property PACKAGE_PIN M17 [get_ports rx]
set_property PACKAGE_PIN L17 [get_ports tx]
set_property PACKAGE_PIN K21 [get_ports rst_n]

### LED Constrain

set_property IOSTANDARD LVCMOS33 [get_ports GPIO_0_0_tri_io[0]]
set_property IOSTANDARD LVCMOS33 [get_ports GPIO_0_0_tri_io[1]]

set_property PACKAGE_PIN P20 [get_ports GPIO_0_0_tri_io[0]]
set_property PACKAGE_PIN P21 [get_ports GPIO_0_0_tri_io[1]]

## Design Flow

### PS Part

- Setup PS DDR
- Setup PS IO

### PL Part

### Generate Bits

### Export Hardware

### Open Vits

### Create Platform

### Create Application