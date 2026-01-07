# PICO Resource and Tutorial

## PICO Images

Debug Probe
[debug probe download](images/2025/debugprobe.uf2)

Nuke Flash
[Nuke All download](images/2025/flash_nuke.uf2)

## PICO Programming Tutorial

[Digital Systems Design Using Microcontrollers - V. Hunter Adams](https://ece4760.github.io/)

[Raspberry Pi Pico Lectures 2025 YT](https://www.youtube.com/playlist?list=PLDqMkB5cbBA4GisLzRSqw5x5G38M4zlkr)

## RP2040 + FPGA RISC-V AXIS Communication

[YouTube from FPGA Zealot](https://www.youtube.com/watch?v=a7hFD_DbAdk&t=2441s)

## PICO PIO Programming

[PICO PIO Programming Youtube Video](https://www.youtube.com/playlist?list=PLiRALtgGsxmZs_LXGkh09Zr2NUmk_mtEI)

[PICO PIO Programming Youtube Video Github Code](https://github.com/LifeWithDavid/Raspberry-Pi-Pico-PIO)

### Web Reference

[PICO PIO url](https://circuitcellar.com/research-design-hub/basics-of-design/programmable-io-programming/)

[A Practical Look at PIO on the Raspberry Pi Pico URL](https://blues.com/blog/raspberry-pi-pico-pio/)

[Introduction to the PIO (Programmable Input Output) of the RP2040](https://tutoduino.fr/en/pio-rp2040-en/)

[programmable-io-programming @ circuitcellar URL](https://circuitcellar.com/research-design-hub/basics-of-design/programmable-io-programming/)

[playing-with-the-pico](https://gregchadwick.co.uk/blog/playing-with-the-pico-pt1/)

## RP2040 PICO DMA

[RP2040 PICO DMA](https://mcuoneclipse.com/2023/04/02/rp2040-with-pio-and-dma-to-address-ws2812b-leds/)

[RP2040 PICO DAC DMA](https://vanhunteradams.com/Pico/DAC/DMA_DAC.html)

[Playing with the Pico Part 4 - Getting Acquainted with PIO](https://gregchadwick.co.uk/blog/playing-with-the-pico-pt4/)

## RP2040 LVGL

[http://bbs.eeworld.com.cn/thread-1227666-1-1.html](http://bbs.eeworld.com.cn/thread-1227666-1-1.html)

[RT-Thread and LVGL](https://rt-thread.medium.com/get-raspberry-pi-pico-running-on-rt-thread-rtos-with-an-opensource-light-versatile-graphics-library-c1f708882bff)

## RP2040 Arduino How to Make 2 PWM Start at the same time

```c

#define _PWM_LOGLEVEL_        0
#include "RP2040_PWM.h"

//creates pwm instance
RP2040_PWM* PWM_Instance1;
RP2040_PWM* PWM_Instance2;

#define FREQ1  2000000
#define FREQ2  2000000

int updated = 0;

void setup() {
  Serial.begin(115200);

  PWM_Instance1 = new RP2040_PWM(10, FREQ1, 0);
  PWM_Instance1->setPWM(10, FREQ1, 0);
  turn_on_pwm1();
}

void turn_on_pwm1()
{
  rp2040.fifo.push(1);
  PWM_Instance1->setPWM(10, FREQ1, 50);  
}
void turn_on_pwm2()
{
  while(1) {
    if (rp2040.fifo.available()) {
        PWM_Instance2->setPWM(12, FREQ2, 50);
        rp2040.fifo.pop();
        return;
    }
  }
}

void setup1() {
  PWM_Instance2 = new RP2040_PWM(12, FREQ2, 0);
  PWM_Instance2->setPWM(12, FREQ2, 0);
  turn_on_pwm2();
}
void loop() {
  delay(1000);
}
void loop1() {

  delay(1000);
}
```

## SPI Master and Slave on Pico

```py
// Shows how to use SPISlave on a single device.
// Core0 runs as an SPI master and initiates a transmission to the slave
// Core1 runs the SPI Slave mode and provides a unique reply to messages from the master
//
// Released to the public domain 2023 by Earle F. Philhower, III <earlephilhower@yahoo.com>

#include <SPI.h>
#include <SPISlave.h>

// Wiring:
// Master RX  GP0 <-> GP11  Slave TX
// Master CS  GP1 <-> GP9   Slave CS
// Master CK  GP2 <-> GP10  Slave CK
// Master TX  GP3 <-> GP8   Slave RX

SPISettings spisettings(1000000, MSBFIRST, SPI_MODE0);

// Core 0 will be SPI master
void setup() {
  SPI.setRX(0);
  SPI.setCS(1);
  SPI.setSCK(2);
  SPI.setTX(3);
  SPI.begin(true);

  delay(5000);
}

int transmits = 0;
void loop() {
  char msg[42];
  memset(msg, 0, sizeof(msg));
  sprintf(msg, "What's up? This is transmission %d", transmits);
  Serial.printf("\n\nM-SEND: '%s'\n", msg);
  SPI.beginTransaction(spisettings);
  SPI.transfer(msg, sizeof(msg));
  SPI.endTransaction();
  Serial.printf("M-RECV: '%s'\n", msg);
  transmits++;
  delay(5000);
}

// Core 1 will be SPI slave

volatile bool recvBuffReady = false;
char recvBuff[42] = "";
int recvIdx = 0;
void recvCallback(uint8_t *data, size_t len) {
  memcpy(recvBuff + recvIdx, data, len);
  recvIdx += len;
  if (recvIdx == sizeof(recvBuff)) {
    recvBuffReady = true;
    recvIdx = 0;
  }
}

int sendcbs = 0;
// Note that the buffer needs to be long lived, the SPISlave doesn't copy it.  So no local stack variables, only globals or heap(malloc/new) allocations.
char sendBuff[42];
void sentCallback() {
  memset(sendBuff, 0, sizeof(sendBuff));
  sprintf(sendBuff, "Slave to Master Xmission %d", sendcbs++);
  SPISlave1.setData((uint8_t*)sendBuff, sizeof(sendBuff));
}

// Note that we use SPISlave1 here **not** because we're running on
// Core 1, but because SPI0 is being used already.  You can use
// SPISlave or SPISlave1 on any core.
void setup1() {
  SPISlave1.setRX(8);
  SPISlave1.setCS(9);
  SPISlave1.setSCK(10);
  SPISlave1.setTX(11);
  // Ensure we start with something to send...
  sentCallback();
  // Hook our callbacks into the slave
  SPISlave1.onDataRecv(recvCallback);
  SPISlave1.onDataSent(sentCallback);
  SPISlave1.begin(spisettings);
  delay(3000);
  Serial.println("S-INFO: SPISlave started");
}

void loop1() {
  if (recvBuffReady) {
    Serial.printf("S-RECV: '%s'\n", recvBuff);
    recvBuffReady = false;
  }
}

```

---

## PICO_LCD_2 for TFT_eSPI


```C
#define USER_SETUP_INFO "User_Setup"

// Display type -  only define if RPi display
//#define RPI_DISPLAY_TYPE // 20MHz maximum SPI

#define ST7789_2_DRIVER    // Minimal configuration option, define additional parameters below for this display

#define TFT_WIDTH  240 // ST7789 240 x 240 and 240 x 320
#define TFT_HEIGHT 320 // ST7789 240 x 320

// https://www.waveshare.net/shop/Pico-LCD-2.htm
#define TFT_DC    8  // Data Command control pin·
#define TFT_CS    9  // Chip select control pin
#define TFT_SCLK 10
#define TFT_MOSI 11
//#define TFT_MISO 12
#define TFT_RST  12  // Reset pin (could connect to RST pin)
#define TFT_BL   13  // LED back-light

#define LOAD_GLCD   // Font 1. Original Adafruit 8 pixel font needs ~1820 bytes in FLASH
#define LOAD_FONT2  // Font 2. Small 16 pixel high font, needs ~3534 bytes in FLASH, 96 characters
#define LOAD_FONT4  // Font 4. Medium 26 pixel high font, needs ~5848 bytes in FLASH, 96 characters
#define LOAD_FONT6  // Font 6. Large 48 pixel font, needs ~2666 bytes in FLASH, only characters 1234567890:-.apm
#define LOAD_FONT7  // Font 7. 7 segment 48 pixel font, needs ~2438 bytes in FLASH, only characters 1234567890:-.
#define LOAD_FONT8  // Font 8. Large 75 pixel font needs ~3256 bytes in FLASH, only characters 1234567890:-.
//#define LOAD_FONT8N // Font 8. Alternative to Font 8 above, slightly narrower, so 3 digits fit a 160 pixel TFT
#define LOAD_GFXFF  // FreeFonts. Include access to the 48 Adafruit_GFX free fonts FF1 to FF48 and custom fonts

// Comment out the #define below to stop the SPIFFS filing system and smooth font code being loaded
// this will save ~20kbytes of FLASH
#define SMOOTH_FONT

// For the RP2040 processor define the SPI port channel used (default 0 if undefined)
//#define TFT_SPI_PORT 1 // Set to 0 if SPI0 pins are used, or 1 if spi1 pins used
#define RP2040_PIO_SPI 

#define SPI_FREQUENCY       20000000
#define SPI_READ_FREQUENCY  20000000
#define SPI_TOUCH_FREQUENCY  2500000

```
