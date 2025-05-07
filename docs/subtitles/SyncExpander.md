# Sync Expander  and Sync Hub Specification

## Block 1

![](./SyncExpander/syncexpander.png)

## Design Consideration

### DAQ Distance Consideration

    If DAQ Distance between each other > 100M , Time Base Sync is better (PTP)
    Otherwise, Signal Base Sync is OK.

### Disconnect Possibility

    If Signal between DAQ will lost, DPLL will be needed.
    Otherwise, use the Signal as Clock is OK.

## Sync Hub Specification 

### MCU + FPGA or Not

    USB Power and Data
    CK_IN for External Clock Source.
    (if none use Internal clock source)
---
    Signal Based Sync:
    CLK_OUT1 : User Define Clock Source 1
    CLK_OUT2 : User Define Clock Source 2

    Time Based Sync:
    CLK : Data Clock
    Data : Time Data 
---
    TX : Send Data to Sync Expander
    RX : Receive Data from Sync Expander

### LVDS Bus

    1.Output LVDS , 1 to 4 (DS91M124)
    2.Inout LVDS  , 4 to 1 (SN65MLVD203B for TX/RX)

## Sync Expander

    Signal Based Sync:
    CLK_IN1: Time Base Clock 1
    CLK_IN2: Time Base Clock 2
---
    Time Based Sync:
    CLK : Data Clock
    Data : Time Data
---
    RX : Date from Hub
    TX : Data to Hub
    TX_EN : Enable Send Data
    Address : Sync Expnader's ID
    Locked: Lock to CLK_IN1 or CLK_IN2 (For DPLL)
    CLK_Fail: No CLK Input
    ARM : Output to DAQ
    Start : Output to DAQ
    Stop : Output to DAQ
    Ready : DAQ to Sync Expander

## Test Bus Board

![](./SyncExpander/bus.png)

