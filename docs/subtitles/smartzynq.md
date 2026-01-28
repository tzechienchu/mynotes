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

## Petalinux Page

🌐[PS Setup and PL](http://www.hellofpga.com/index.php/2025/02/23/smart_zynq_petalinux_01/)

🌐[Petalinux of TF](http://www.hellofpga.com/index.php/2025/02/23/smart_zynq_building_petalinux/)

🌐[Image to TF Card](http://www.hellofpga.com/index.php/2025/02/23/zynq_creating_sd_card/)

🌐[Boot](http://www.hellofpga.com/index.php/2025/02/25/petalinux_boot_test/)

---

## FPGA Type

Device Type: xc7z020clg484-1
DDR : MT41K256M16RE-125 16Bits
Clock : 33.33M
CPU: 6:2:1

## PetaLinux

MIO Bank 0 : LVCOMS 3.3V
MIO Bank 1 : LVCMOS 3.3V

MIO QSPI : Single SS 4-Bit MIO1-6 Feeback Clk MIO 8

MIO SD 0 : MIO 40-45

MIO ENET 0 : MDIO : EMIO

MIO USB 0 : MIO 28-39

MIO GPIO :

    GPIO MIO : MIO
    EMIO GPIO : 4
    USB Reset : Share Reset Pin/ MIO46

MIO UART0 : EMIO

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

### Petalinux

set_property -dict {PACKAGE_PIN J20 IOSTANDARD LVCMOS33} [get_ports GPIO_EMIO[3]]
set_property -dict {PACKAGE_PIN K21 IOSTANDARD LVCMOS33} [get_ports GPIO_EMIO[2]]
set_property -dict {PACKAGE_PIN P21 IOSTANDARD LVCMOS33} [get_ports GPIO_EMIO[1]]
set_property -dict {PACKAGE_PIN P20 IOSTANDARD LVCMOS33} [get_ports GPIO_EMIO[0]]

set_property -dict {PACKAGE_PIN M17 IOSTANDARD LVCMOS33} [get_ports UART_rxd]
set_property -dict {PACKAGE_PIN L17 IOSTANDARD LVCMOS33} [get_ports UART_txd]

set_property -dict {PACKAGE_PIN G21 IOSTANDARD LVCMOS33} [get_ports MDIO_PHY_mdc]
set_property -dict {PACKAGE_PIN H22 IOSTANDARD LVCMOS33} [get_ports MDIO_PHY_mdio_io]
set_property -dict {PACKAGE_PIN A22 IOSTANDARD LVCMOS33} [get_ports {RGMII_rd[0]}]
set_property -dict {PACKAGE_PIN A18 IOSTANDARD LVCMOS33} [get_ports {RGMII_rd[1]}]
set_property -dict {PACKAGE_PIN A19 IOSTANDARD LVCMOS33} [get_ports {RGMII_rd[2]}]
set_property -dict {PACKAGE_PIN B20 IOSTANDARD LVCMOS33} [get_ports {RGMII_rd[3]}]
set_property -dict {PACKAGE_PIN A21 IOSTANDARD LVCMOS33} [get_ports RGMII_rx_ctl]
set_property -dict {PACKAGE_PIN B19 IOSTANDARD LVCMOS33} [get_ports RGMII_rxc]
set_property -dict {PACKAGE_PIN E21 IOSTANDARD LVCMOS33} [get_ports {RGMII_td[0]}]
set_property -dict {PACKAGE_PIN F21 IOSTANDARD LVCMOS33} [get_ports {RGMII_td[1]}]
set_property -dict {PACKAGE_PIN F22 IOSTANDARD LVCMOS33} [get_ports {RGMII_td[2]}]
set_property -dict {PACKAGE_PIN G20 IOSTANDARD LVCMOS33} [get_ports {RGMII_td[3]}]
set_property -dict {PACKAGE_PIN G22 IOSTANDARD LVCMOS33} [get_ports RGMII_tx_ctl]
set_property -dict {PACKAGE_PIN D21 IOSTANDARD LVCMOS33} [get_ports RGMII_txc]
set_property SLEW FAST [get_ports {RGMII_td[0]}]
set_property SLEW FAST [get_ports {RGMII_td[1]}]
set_property SLEW FAST [get_ports {RGMII_td[2]}]
set_property SLEW FAST [get_ports {RGMII_td[3]}]
set_property SLEW FAST [get_ports RGMII_tx_ctl]
set_property SLEW FAST [get_ports RGMII_txc]

create_clock -period 8 -name RGMII_rxc [get_ports RGMII_rxc]

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