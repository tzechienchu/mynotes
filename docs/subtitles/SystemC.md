# System C

## Serdes HeadStage

![Serdes HeadStage](../diagrams/2025/Serdes_HS.png)

| FPGA        | Power        | EEPROM       |
|:------------|:-------------|:-------------|
| Intel Max10 | 3.3V         | Embedded     |
| efinix T8   | 1.1V Core <br>  3.3V IO | External   |

## Pin Requirements

| Block       | Pin Number   | Description       |
|:------------|:-------------|:------------------|
| RHD         | 7 CMOS <br> 14 LVDS | CK <br> MOSI <br> MISO x 4 <br> CS |
| I2C Master  | 2            | SCL <br> SDA |
| I2C Slave   | 2            | SCL <br> SDA |
| DMCI        | 17           | Data x 16 <br> PixelCK |
