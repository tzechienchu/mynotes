# Need to Solve

## FPGA

### Litex 2 Independent CPU

#### Where is SOC Module Initialized

LiteXModule -> SOC -> LiteXSoc -> SocCore -> SocMINI

#### SDK Generation become an issue ?

---

### Litex DSP 

[litedsp](https://github.com/enjoy-digital/litedsp)

---

### <del>Litex 2 Bus</del> 

```py
        # Wishbone Master
        serwb_slave_core = SERWBCore(self.serwb_slave_phy, self.clk_freq, mode="master")
        self.submodules += serwb_slave_core

        # Wishbone SRAM
        self.serwb_sram = wishbone.SRAM(8192)
        # self.comb += serwb_slave_core.bus.connect(self.serwb_sram.bus)

        # You cannot do it.
        self.submodules.wbreg = wbreg = WBSlave_Reg()
        # self.comb += serwb_slave_core.bus.connect(wbreg.bus)

        self.slave_bus = SoCBusHandler(
            name             = "SoCSlaveBusHandler",
            standard         = "wishbone",
            data_width       = 32,
            address_width    = 32,
        )
        self.slave_bus.add_master("s_master", serwb_slave_core.bus)
        self.slave_bus.add_slave("ssram", self.serwb_sram.bus, region=SoCRegion(origin=0x3000_0000, size=0x0000_2000))
        self.slave_bus.add_slave("sreg", wbreg.bus, region=SoCRegion(origin=0x3000_3000, size=0x0000_1000))
```

---

### Harp TX/RX and Sync

---

## Python

### DearPyGUI for Data flow

---

## Embedded

### CH32H417 Serdes to FPGA

### CH32H417 UHSIF to FPGA

### Pi 5 Mipi no I2C

---

## Instrumentation

### CH347 for GPIO Access