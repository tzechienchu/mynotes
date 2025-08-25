# What is Harp

## Harp Synchronization Clock Protocol

### Introduction

The `Harp Synchronization Clock` is a dedicated bus that disseminates the current time to/across Harp devices. It is a serial communication protocol that relays the time information. The last byte in each message can be used as a trigger, and allows a `Device`` to align itself with the current `Harp` time.

### Serial configuration

* The Baud rate used is 100kbps;
* The last byte starts *exactly* 672 us before the elapse of the current second (e.g.:)

    !["SynchClockOscilloscope](SynchClockOscilloscope.png)

* The packet is composed of 6 bytes (`header[2]` and `timestamp_s[4]`):
  - `header[2] = {0xAA, 0xAF)`
  - `timestamp_s` is of type U32, little-endian, and contains the current second.


### Example code

Example of a microcontroller C code:

```C

ISR(TCD0_OVF_vect, ISR_NAKED)
    {
        if ((*timestamp_byte0 == 0xAA) && (*timestamp_byte1 == 0xAF)) reti();
        if ((*timestamp_byte1 == 0xAA) && (*timestamp_byte2 == 0xAF)) reti();
        if ((*timestamp_byte2 == 0xAA) && (*timestamp_byte3 == 0xAF)) reti();

        switch (timestamp_tx_counter)
        {
            case 1:
                USARTD1_DATA = 0xAA;
                break;
            case 2:
                USARTD1_DATA = 0xAF;
                break;
            case 4:
                USARTD1_DATA = *timestamp_byte0;
                break;
            case 6:
                USARTD1_DATA = *timestamp_byte1;
                break;
            case 7:
                USARTD1_DATA = *timestamp_byte2;
                break;
            case 1998:
                USARTD1_DATA = *timestamp_byte3;
                break;
        }
    }
```

### Timing

![](./timing.png)

### Timing use PICO Emulation

![](./20250730_121315.jpg)

### Physical Connection Schematics

![](./Screenshot%20from%202025-07-30%2015-02-30.png)

![](./Screenshot%20from%202025-07-30%2015-02-39.png)

### Harp Test

![](../../diagrams/2025/Harp_Pico_Test.png)

![](../../images/2025/20250822_171610.jpg)

### Addon for DAQ

![](./AddonToDaq.png)

### Harp Device need for Test

![](./AISelect_20250729_183401_Chrome.jpg)

[Harp from OE](https://open-ephys.org/harp)

### What are the limitations of increasing the frequency beyond 1Hz for PPS signals

The limitations of increasing the frequency beyond 1 Hz for PPS signals primarily relate to signal shape, timing accuracy, propagation effects, hardware complexity, and pulse distortion:

- **Pulse shape and duty cycle ambiguity:** PPS signals at 1 Hz are usually short pulses with sharply defined edges to precisely mark second boundaries. Increasing frequency means pulse widths and separation shrink, possibly turning the signal into something closer to a square wave or continuous periodic waveform, which can blur the definition of distinct timing edges important for synchronization[^1].
- **Increased pulse distortion and rise time issues:** Higher frequency pulses have faster rise times and shorter durations, making them more susceptible to distortions when transmitted over cables. Dispersion, attenuation, impedance mismatches, and cable length affect the pulse shape and timing delay more severely at higher frequencies, thus degrading timing precision[^4].
- **Measurement and detection uncertainty:** Timing devices rely on clear, stable pulse edges to trigger time measurements. Faster pulses from higher frequency signals increase uncertainties in detection due to limited bandwidth, noise, and trigger thresholds, worsening jitter and reducing synchronization quality[^4].
- **Hardware and distribution challenges:** Generating and distributing high-frequency pulses with ultra-low jitter and minimal distortion over long distances requires expensive, specialized hardware and careful design. 1 Hz PPS signals are simpler and more robust for typical synchronization scenarios[^2][^3].
- **Loss of well-defined absolute timing boundaries:** The main advantage of 1 Hz PPS is that each pulse corresponds to an exact whole second boundary, facilitating phase alignment of clocks. At higher frequencies, while more timing points may be generated, the direct correlation with absolute second boundaries becomes less clear, complicating protocols that rely on these markers.

In essence, increasing frequency beyond 1 Hz for PPS-type signals tends to increase system complexity, degrade pulse integrity and timing accuracy, and reduce the clarity of the timing reference that makes 1 PPS signals highly effective for synchronization applications such as GPS timing, network clocks, and telecom networks[^1][^4].

If needed, systems requiring finer resolution within each second typically use a combination of stable local high-frequency clocks disciplined by the 1 Hz PPS signal rather than replacing PPS with higher frequency pulses directly[^2][^3].

<div style="text-align: center">⁂</div>

[^1]: https://electronics.stackexchange.com/questions/666907/is-a-1-hz-signal-the-same-as-1-pps-one

[^2]: https://www.euramet.org/securedl/sdl-eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE3NDA4ODI2OTQsImV4cCI6MTc3MjUwNTA5NCwidXNlciI6MCwiZ3JvdXBzIjpbMCwtMV0sImZpbGUiOiJNZWRpYS9kb2NzL1B1YmxpY2F0aW9ucy90ZWNoZ3VpZGVzL0ktVEVDLUdVSV8wMDNfVXNlX29mX0dQU19EaXNjaXBsaW5lZF9Pc2NpbGxhdG9yc193ZWIucGRmIiwicGFnZSI6OTk3fQ.cYjpYB1KkVD-o586hmGQu64_oJBsZBumZiWaBZqePOY/I-TEC-GUI_003_Use_of_GPS_Disciplined_Oscillators_web.pdf

[^3]: http://ip-flow.dit.unitn.it/files/msc-gasparini.pdf

[^4]: https://tf.nist.gov/general/pdf/2852.pdf

[^5]: https://portal.u-blox.com/s/question/0D52p0000BfFr1FCQS/does-the-pps-signal-of-a-ublox-device-always-occur-in-whole-seconds-if-i-set-the-frequency-as-1hz

[^6]: http://essay.utwente.nl/94988/1/aanen_MA_EEMCS.pdf

[^7]: https://www.gps.gov/technical/ps/2007-PPS-performance-standard.pdf

[^8]: https://www.ucalgary.ca/engo_webdocs/MEC/04.20199.SMDeshpande.pdf

[^9]: https://www.zhaw.ch/storage/engineering/institute-zentren/ines/forschung-und-entwicklung/time-synchronisation/precision-time-protocol-for-spectroscope-synchronization.pdf

<!-- ### Example Verilog from Breakout Box

```verilog
module harp_counter # (
    parameter CLK_RATE_HZ = 1000000,
    parameter LAST_WORD_US = 672,
    parameter COUNTER_WIDTH = $clog2(CLK_RATE_HZ)
) (
    input reset,
    input clk,
    input run,
    output reg [7:0] uart_data,
    output reg uart_start,
    output uart_blank,
    input uart_end,
    output LED
);

localparam  CYCLES_PER_US = CLK_RATE_HZ / 1000000;
localparam  LAST_WORD_START_US = (1000000 - LAST_WORD_US);
localparam  LAST_WORD_CYCLE = LAST_WORD_START_US*CYCLES_PER_US - 1;
localparam  LAST_CYCLE = CLK_RATE_HZ - 1;
localparam  FIRST_CYCLE = 10;

reg [31:0] timestamp;
assign LED = timestamp[0];

wire [7:0] timestamp_b0;
assign timestamp_b0 = timestamp[7:0];
wire [7:0] timestamp_b1;
assign timestamp_b1 = timestamp[15:8];
wire [7:0] timestamp_b2;
assign timestamp_b2 = timestamp[23:16];
wire [7:0] timestamp_b3;
assign timestamp_b3 = timestamp[31:24];
wire [7:0] start_b0;
assign start_b0 = 8'hAA;
wire [7:0] start_b1;
assign start_b1 = 8'hAF;

reg start_matches;
always @(timestamp_b0, timestamp_b1, timestamp_b2, timestamp_b3)
begin
start_matches <= 1'b0;
if ({timestamp_b0, timestamp_b1} == {start_b0, start_b1}) start_matches <= 1'b1;
if ({timestamp_b1, timestamp_b2} == {start_b0, start_b1}) start_matches <= 1'b1;
if ({timestamp_b2, timestamp_b3} == {start_b0, start_b1}) start_matches <= 1'b1;
end
assign uart_blank = start_matches;

reg [COUNTER_WIDTH - 1 : 0] counter;
reg [2:0] state;
reg [2:0] word;

localparam s_idle = 3'd0,
          s_wait1 = 3'd1,
          s_send = 3'd2,
          s_wait_send = 3'd3,
          s_wait_last = 3'd4,
          s_send_last = 3'd5,
          s_wait_end = 3'd6,
          s_inc_timestamp = 3'd7;

always @(*)
begin
case (word)
    3'd0: uart_data <= start_b0;
    3'd1: uart_data <= start_b1;
    3'd2: uart_data <= timestamp_b0;
    3'd3: uart_data <= timestamp_b1;
    3'd4: uart_data <= timestamp_b2;
    3'd5: uart_data <= timestamp_b3;
    default: uart_data <= 'b0;
endcase
end

always @(state)
begin
if (state == s_send || state == s_send_last) uart_start <= 1'b1;
else uart_start <= 1'b0;
end

always @(posedge clk or posedge reset)
begin
if (reset) begin
    state <= s_idle;
    word <= 'b0;
    counter <= 'b0;
    timestamp <= 'b0;
end else begin
    counter <= counter + 1'b1;
    case (state)
        s_idle: begin
            counter <= 'b0;
            timestamp <= 'b0;
            if (run) state <= s_inc_timestamp;
        end
        s_inc_timestamp: begin
            timestamp <= timestamp + 1'b1;
            state <= s_wait1;
        end
        s_wait1: begin
            word <= 'b0;
            if (counter == FIRST_CYCLE) begin
                state <= s_send;
            end
        end
        s_send: begin
            word <= word + 1'b1;
            state <= s_wait_send;
        end
        s_wait_send: begin
            if (uart_end) begin
                if (word == 3'd5)
                    state <= s_wait_last;
                else
                    state <= s_send;
            end
        end
        s_wait_last: begin
            if (counter == LAST_WORD_CYCLE) begin
                state <= s_send_last;
            end
        end
        s_send_last: begin
            state <= s_wait_end;
        end
        s_wait_end: begin
            if (counter == LAST_CYCLE) begin
                counter <= 'b0;
                state <= s_inc_timestamp;
            end
        end
    endcase

    if (run == 'b0) begin
        state <= s_idle;
    end
end
end

endmodule
``` -->

