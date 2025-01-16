# STM32 ADC DMA

```c
/*
Tests the DAC functionality
*/

#include <libmaple/gpio.h>
#include <libmaple/dac.h>
#include <libmaple/dma.h>


#define SINE 1
#define TIANGLE 2

// use only one of the values: TRIANGLE or SINE
#define GEN_WAVE           SINE // use sine_wave lookup table
//#define GEN_WAVE       TRIANGLE // use internal generator.

#if GEN_WAVE==TRIANGLE
  // select wave amplitude:
  // 3 for 3.3Vpp (8192 samples),
  // 2 for 1.65Vpp (4096 samples),
  // 1 for 0.87Vpp (2048 samples)
  #define WAVE_AMP 1
#endif
/*
 sine wave look-up table from: http://www.daycounter.com/Calculators/Sine-Generator-Calculator.phtml
*/
const uint8_t sine_wave[210] = {
0x80,0x83,0x87,0x8b,0x8f,0x93,0x96,0x9a,0x9e,0xa1,0xa5,0xa9,0xac,0xb0,0xb3,0xb7,0xba,0xbe,0xc1,0xc4,0xc7,
0xca,0xcd,0xd0,0xd3,0xd6,0xd9,0xdc,0xde,0xe1,0xe3,0xe6,0xe8,0xea,0xec,0xee,0xf0,0xf2,0xf3,0xf5,0xf6,0xf8,
0xf9,0xfa,0xfb,0xfc,0xfd,0xfd,0xfe,0xfe,0xff,0xff,0xff,0xff,0xff,0xff,0xfe,0xfe,0xfd,0xfd,0xfc,0xfb,0xfa,
0xf9,0xf8,0xf6,0xf5,0xf3,0xf2,0xf0,0xee,0xec,0xea,0xe8,0xe6,0xe3,0xe1,0xde,0xdc,0xd9,0xd6,0xd3,0xd0,0xcd,
0xca,0xc7,0xc4,0xc1,0xbe,0xba,0xb7,0xb3,0xb0,0xac,0xa9,0xa5,0xa1,0x9e,0x9a,0x96,0x93,0x8f,0x8b,0x87,0x83,
0x80,0x7c,0x78,0x74,0x70,0x6c,0x69,0x65,0x61,0x5e,0x5a,0x56,0x53,0x4f,0x4c,0x48,0x45,0x41,0x3e,0x3b,0x38,
0x35,0x32,0x2f,0x2c,0x29,0x26,0x23,0x21,0x1e,0x1c,0x19,0x17,0x15,0x13,0x11,0x0f,0x0d,0x0c,0x0a,0x09,0x07,
0x06,0x05,0x04,0x03,0x02,0x02,0x01,0x01,0x00,0x00,0x00,0x00,0x00,0x00,0x01,0x01,0x02,0x02,0x03,0x04,0x05,
0x06,0x07,0x09,0x0a,0x0c,0x0d,0x0f,0x11,0x13,0x15,0x17,0x19,0x1c,0x1e,0x21,0x23,0x26,0x29,0x2c,0x2f,0x32,
0x35,0x38,0x3b,0x3e,0x41,0x45,0x48,0x4c,0x4f,0x53,0x56,0x5a,0x5e,0x61,0x65,0x69,0x6c,0x70,0x74,0x78,0x7c,
};
//-----------------------------------------------------------------------------

const uint8_t SPEAKER_PIN = PA4; // DAC ch1 output

//-----------------------------------------------------------------------------
const int OUT_FREQ   = 10000; // Output waveform frequency in Hz

const int CNT_FREQ   = 84000000;  // TIM6 counter clock (prescaled APB1*2)

#if GEN_WAVE==SINE
  const int WAVE_RES   = sizeof(sine_wave); // Waveform resolution
#elif GEN_WAVE==TRIANGLE
  #if   WAVE_AMP==3
	const int WAVE_RES   = 8192;
  #elif WAVE_AMP==2
	const int WAVE_RES   = 4096;
  #elif WAVE_AMP==1
	const int WAVE_RES   = 2048;
  #endif
#endif

const int TIM_PERIOD = ((CNT_FREQ)/((WAVE_RES)*(OUT_FREQ))); // Autoreload reg value

//-----------------------------------------------------------------------------
void blink(void)
{
	digitalWrite(LED_BUILTIN, !digitalRead(LED_BUILTIN));
}
//-----------------------------------------------------------------------------
// DMA1 - DMA_CH7 - DMA_STREAM5 (DAC1), DMA_STREAM6 (DAC2) 
// data from the buffer is transferred to DAC_each time TIM6 trigger occurs
//-----------------------------------------------------------------------------
void DMA_setup(void)
{
	// configure DMA
    dma_init(DMA1);
	dma_setup_transfer(	DMA1,
						DMA_STREAM5,
						DMA_CH7,
						DMA_SIZE_8BITS,
						//DMA_SIZE_16BITS,
						&(DAC->regs->DHR8R1),  // peripheral address
						//&(DAC->regs->DHR12R1),   // peripheral address
						sine_wave, // memory bank 0 address
						NULL,	  // memory bank 1 address
						(DMA_FROM_MEM | DMA_MINC_MODE | DMA_CIRC_MODE)
	);
	dma_set_num_transfers(DMA1, DMA_STREAM5, WAVE_RES);
	dma_set_fifo_flags(DMA1, DMA_STREAM5, 0);
	dma_clear_isr_bits(DMA1, DMA_STREAM5);
	dma_enable(DMA1, DMA_STREAM5);
	Serial.print("> DMA: CR = 0x"); Serial.print(DMA1->regs->STREAM[DMA_STREAM5].CR, HEX);
	Serial.print(", NDTR = 0x"); Serial.println(DMA1->regs->STREAM[DMA_STREAM5].NDTR, HEX);
}
//-----------------------------------------------------------------------------
void Timer_setup(void)
{
	timer_pause(TIMER6);
	timer_set_prescaler(TIMER6, 0); // use 84MHz internal clock
	timer_set_reload(TIMER6, (TIM_PERIOD>7) ? (TIM_PERIOD-1) : 7); // minimum allowable value is 7
	timer_set_master_mode(TIMER6, TIMER_MASTER_MODE_UPDATE); // update event will generate TRGO
	timer_generate_update(TIMER6);
	timer_resume(TIMER6); // start timer
	Serial.print("> TIM6 CR1 = 0x"); Serial.println((TIMER6->regs).bas->CR1, HEX);
	Serial.print("> TIM6 PSC = 0x"); Serial.println((TIMER6->regs).bas->PSC, HEX);
	Serial.print("> TIM6 ARR = 0x"); Serial.println((TIMER6->regs).bas->ARR, HEX);
	Serial.print("> TIM6 SR = 0x"); Serial.println((TIMER6->regs).bas->SR, HEX);
}
//-----------------------------------------------------------------------------
void DAC_setup(void)
{
	dac_init();
#if GEN_WAVE==SINE
	dac_enable_dma(DAC_CH1);
	dac_set_wave(DAC_CH1, DAC_WAVE_DISABLED);
#elif GEN_WAVE==TRIANGLE
	dac_set_wave(DAC_CH1, DAC_WAVE_TRIANGLE);
	dac_set_mask_amplitude(DAC_CH1, 
  #if WAVE_AMP==3
	11
  #elif WAVE_AMP==2
	10
  #elif WAVE_AMP==1
	9
  #endif
	);
#else
  #error "No wave selected!"
#endif
	dac_set_trigger(DAC_CH1, DAC_TRG_TIMER6TRGO);
	dac_enable(DAC_CH1);
	Serial.print("> DAC: CR = 0x"); Serial.println(DAC->regs->CR, HEX);
}
//-----------------------------------------------------------------------------
void setup()
{
	pinMode(LED_BUILTIN, OUTPUT);
	digitalWrite(LED_BUILTIN, LOW);

	Serial.begin(115200);
	//while (!Serial); delay(100);
	Serial.println("DAC test example\n");

	DAC_setup();
#if GEN_WAVE==SINE
	DMA_setup();
#endif
	Timer_setup();
	Serial.println("DAC triangle wave generation started.");
}
//-----------------------------------------------------------------------------
uint8_t counter;
uint32_t t;
//-----------------------------------------------------------------------------
void loop()
{
	if ( (millis()-t)>=1000 )
	{
		t = millis();
		blink();
	}
}
```
