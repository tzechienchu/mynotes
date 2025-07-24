# SyncBox Master Specification

## Function Description

    Generate Clock1(1MHz) Clock2(<1KHz) to remote XDaq for Sync
    Suport max 16/32(??) SyncBox Slave.

    Control remote XDaq through UART port to SyncBox Slave.
    Such as , Reset/Start/Stop command.
    And Master can tell slave staus from the UART Port.

## SyncMaster IO Define

| Port         |      Function                      |  IO         |
|--------------|:----------------------------------:|------------:|
| USB          | Connect to PC for upgrade          |  I/O/Power  |
|              | and control as vcom                |             |
| Maste        | Master/Extention                   |  I          |
| Ext CK Sel   | Clock Source Selection             |  I          |
| Start Button | Kick start everything              |  I          |
| Ext CK1      | OSC Input                          |  I          |
| Ext CK2      | OSC Input                          |  I          |
| Power LED    | Indicate Power On                  |  O          |
| Status LED1  | Indicate Slave Status              |  O          |
| Status LED2  | Indicate Slave Status              |  O          |
| RJ45         | 8 Ports                            |  I/O        |
| RJ45 to Slave| VDD/VSS/CKA/CKB/TX2/RX2            |  I/O        |
| Ext IO       | 8Pins/8 VSS/                       |  I/O        |

## SyncSlave IO Define

| Port         |      Function                      |  IO         |
|--------------|:----------------------------------:|------------:|
| USB          | Connect to PC for upgrade          |  I/O        |
|              | and control as vcom                |             |
| HDMI         | Connect to XDaq                    |  I/O/Power  |
| DIP Switch   | Set Slave Address  (5Bits)         |  I          |
| Power LED    | Indicate Power On                  |  O          |
| Status LED1  | Indicate Slave Status              |  O          |
| Status LED2  | Indicate Slave Status              |  O          |
| RJ45         | 1 Ports                            |  I/O        |
| Ext IO       | 8Pins/8 VSS/                       |  I/O        |

## Detail Block

### Power

    Type-C 5V 
    HDMI 12V ?

### Control Path

    Button <-> Pico <-> UART/Clock1/Clock2
    USB <-> Pico <-> UART/Clock1/Clock2

### Data Path

    RJ45 <-> RJ45