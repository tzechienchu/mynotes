# AD5940 Software Data Structure

## Top

```c
/** 
 * @} MISC_Block_Const
 * @} MISC_Block
 * */
/**
 * @defgroup TypeDefinitions
 * @{
*/

typedef int32_t AD5940Err;    /**< error number defination */

/**
 * bool definition for ad5940lib.
*/
typedef enum 
{
  bFALSE = 0, bTRUE = !bFALSE,   /**< True and False definition*/
}BoolFlag;

```

## ADC/DAC/TIA reference and buffer

```c

typedef struct
{
  /* ADC/DAC/TIA reference and buffer */
  BoolFlag HpBandgapEn;     /**< Enable High power band-gap. Clear bit AFECON.HPREFDIS will enable Bandgap, while set this bit will disable bandgap */
  BoolFlag Hp1V8BuffEn;     /**< High power 1.8V reference buffer enable */
  BoolFlag Hp1V1BuffEn;     /**< High power 1.1V reference buffer enable */
  BoolFlag Lp1V8BuffEn;     /**< Low power 1.8V reference buffer enable */
  BoolFlag Lp1V1BuffEn;     /**< Low power 1.1V reference buffer enable */
  /* Low bandwidth loop reference and buffer */
  BoolFlag LpBandgapEn;     /**< Enable Low power band-gap. */
  BoolFlag LpRefBufEn;      /**< Enable the 2.5V low power reference buffer */
  BoolFlag LpRefBoostEn;    /**< Boost buffer current */
  /* DAC Reference Buffer */
  BoolFlag HSDACRefEn;      /**< Enable DAC reference buffer from HP Bandgap */
  /* Misc. control  */
  BoolFlag Hp1V8ThemBuff;   /**< Thermal Buffer for internal 1.8V reference to AIN3 pin  */              
  BoolFlag Hp1V8Ilimit;     /**< Current limit for High power 1.8V reference buffer */
  BoolFlag Disc1V8Cap;      /**< Discharge 1.8V capacitor. Short external 1.8V decouple capacitor to ground. Be careful when use this bit  */
  BoolFlag Disc1V1Cap;      /**< Discharge 1.1V capacitor. Short external 1.1V decouple capacitor to ground. Be careful when use this bit  */
}AFERefCfg_Type;
```

## ADC Basic settings and Filter setting

```c
/** 
 * @defgroup ADC_BlockType
 * @{
*/

/**
 * Structure for ADC Basic settings include MUX and PGA.
*/
typedef struct
{
  uint32_t ADCMuxP;         /**< ADC Positive input channel selection. select from @ref ADCMUXP */
  uint32_t ADCMuxN;         /**< ADC negative input channel selection. select from @ref ADCMUXN */
  uint32_t ADCPga;          /**< ADC PGA settings, select from @ref ADCPGA */
}ADCBaseCfg_Type;

/**
 * Structure for ADC filter settings.
*/
typedef struct
{
  uint32_t ADCSinc3Osr;
  uint32_t ADCSinc2Osr;
  uint32_t ADCAvgNum;           /**< Average filter is enabled when DFT source is @ref DFTSRC_AVG in function @ref AD5940_DFTCfgS. This average filter is only used by DFT engine. */
  uint32_t ADCRate;             /**< ADC Core sample rate */
  BoolFlag BpNotch;             /**< Bypass Notch filter in SINC2+Notch block, so only SINC2 is used. ADCFILTERCON.BIT4 */
  BoolFlag BpSinc3;             /**< Bypass SINC3 Module */
  BoolFlag Sinc3ClkEnable;      /**< Enable SINC3 clock */
  BoolFlag Sinc2NotchClkEnable; /**< Enable SINC2+Notch clock */
  BoolFlag Sinc2NotchEnable;    /**< Enable SINC2+Notch block */
  BoolFlag DFTClkEnable;        /**< Enable DFT clock */
  BoolFlag WGClkEnable;         /**< Enable Waveform Generator clock */
}ADCFilterCfg_Type;
/** @} */

```

## DFT

```c

/**
 * DFT Configuration structure.
*/
typedef struct
{
  uint32_t DftNum;      /**< DFT number */
  uint32_t DftSrc;      /**< DFT Source */
  BoolFlag HanWinEn;    /**< Enable Hanning window */
}DFTCfg_Type;

```

## ADC digital comparator

```c

/**
 * ADC digital comparator
*/
typedef struct
{
  uint16_t ADCMin;      /**< The ADC code minimum limit value */
  uint16_t ADCMinHys; 
  uint16_t ADCMax;      /**< The ADC code maximum limit value */
  uint16_t ADCMaxHys;   
}ADCDigComp_Type;

```

## Statistic function

```c

/**
 * Statistic function
*/
typedef struct
{
  uint32_t StatDev;     /**< Statistic standard deviation configure */
  uint32_t StatSample;  /**< Sample size */
  BoolFlag StatEnable;  /**< Set true to enable statistic block */
}StatCfg_Type;

```

## Switch matrix configure

```c

/**
 * Switch matrix configure */
typedef struct
{
  uint32_t Dswitch;  /**< D switch settings. Select from @ref SWD_Const*/
  uint32_t Pswitch;  /**< P switch settings. Select from @ref SWP_Const */
  uint32_t Nswitch;  /**< N switch settings. Select from @ref SWN_Const */
  uint32_t Tswitch;  /**< T switch settings. Select from @ref SWT_Const */
}SWMatrixCfg_Type;

```

## HSTIA Configure

```c

/** HSTIA Configure */
typedef struct
{
  uint32_t HstiaBias;         /**< When select Vzero as bias, the related switch(VZERO2HSTIA) at LPDAC should be closed */
  uint32_t HstiaRtiaSel;      /**< RTIA selection @ref HSTIARTIA_Const */
  uint32_t ExtRtia;      /**< Value of external RTIA*/
  uint32_t HstiaCtia;         /**< Set internal CTIA value from 1 to 32 pF */
  BoolFlag DiodeClose;        /**< Close the switch for internal back to back diode */
  uint32_t HstiaDeRtia;       /**< DE0 node RTIA selection @ref HSTIADERTIA_Const */
  uint32_t HstiaDeRload;      /**< DE0 node Rload selection @ref HSTIADERLOAD_Const */
  uint32_t HstiaDe1Rtia;      /**< (ADuCM355 only, ignored on AD594x)DE1 node RTIA selection @ref HSTIADERTIA_Const */
  uint32_t HstiaDe1Rload;     /**< (ADuCM355 only)DE1 node Rload selection @ref HSTIADERLOAD_Const */
}HSTIACfg_Type;

```

## HSDAC Configure

```c

/** HSDAC Configure */
typedef struct
{
  uint32_t ExcitBufGain;      /**< Select from  EXCITBUFGAIN_2, EXCITBUFGAIN_0P25 */     
  uint32_t HsDacGain;         /**< Select from  HSDACGAIN_1, HSDACGAIN_0P2 */
  uint32_t HsDacUpdateRate;   /**< Divider for DAC update. Available range is 7~255. */
}HSDACCfg_Type;

```

## LPDAC Configure

```c

/** LPDAC Configure 
 * @note The LPDAC structure:
 * @code
 * Switch to select DAC output to Vzero and Vbias nodes. Vzero and Vbias can select from DAC6BIT and DAC12BIT output freely. 
 * LPDAC  >DAC6BIT ---- Vzero   LPDACVZERO_12BIT
 *                 \--- Vbias   LPDACVBIAS_6BIT
 *        >DAC12BIT---- Vzero   LPDACVZERO_6BIT
 *                 \--- Vbias   LPDACVBIAS_12BIT
 * Vzero/Vbias switch, controlled by @ref LPDACCfg_Type LpDacSW
 * Vzero ------PIN
 *       \-----LPTIA  LPDACSW_VZERO2LPTIA. LPTIA positive input
 *        \----HSTIA  LPDACSW_VZERO2LPAMP. HSTIA positive input. Note, there is a MUX on HSTIA positive input pin to select the bias voltage between Vzero and 1.1V fixed internal reference.
 * Vbias ------PIN    LPDACSW_VBIAS2PIN
 *       \-----LPAMP  LPDACSW_VBIAS2LPAMP positive input. The potential state amplifier input, or called LPAMP or PA(potential amplifier).
 * @endcode
*/
typedef struct
{
  uint32_t LpdacSel;        /**< Selectr from LPDAC0 or LPDAC1. LPDAC1 is only available on ADuCM355. */
  uint32_t LpDacSrc;        /**< LPDACSRC_MMR or LPDACSRC_WG. Note: HSDAC is always connects to WG. Disable HSDAC if there is need. */
  uint32_t LpDacVzeroMux;   /**< Select which DAC output connects to Vzero. 6Bit or 12Bit DAC */
  uint32_t LpDacVbiasMux;   /**< Select which DAC output connects to Vbias */
  uint32_t LpDacSW;         /**< LPDAC switch set. Only available from Si2 */
  uint32_t LpDacRef;        /**< Reference selection. Either internal 2.5V LPRef or AVDD. select from @ref LPDACREF_Const*/
  BoolFlag DataRst;         /**< Keep Reset register REG_AFE_LPDACDAT0DATA */
  BoolFlag PowerEn;         /**< Power up REG_AFE_LPDACDAT0 */
  uint16_t DacData12Bit;    /**< Data for 12bit DAC */
  uint16_t DacData6Bit;     /**< Data for 6bit DAC */
}LPDACCfg_Type;

/**
 * Low power amplifiers(PA and TIA)
*/
typedef struct
{
  uint32_t LpAmpSel;        /**< Select from LPAMP0 and LPAMP1. LPAMP1 is only available on ADuCM355. */
  uint32_t LpTiaRf;         /**< The one order RC filter resistor selection. Select from @ref LPTIARF_Const */
  uint32_t LpTiaRload;      /**< The Rload resistor right in front of LPTIA negative input terminal. Select from @ref LPTIARLOAD_Const*/
  uint32_t LpTiaRtia;       /**< LPTIA RTIA resistor selection. Set it to open(@ref LPTIARTIA_Const) when use external resistor. */
  uint32_t LpAmpPwrMod;     /**< Power mode for LP PA and LPTIA */
  uint32_t LpTiaSW;         /**< Set of switches, using macro LPTIASW() to close switch */
  BoolFlag LpPaPwrEn;       /**< Enable(bTRUE) or disable(bFALSE) power of PA(potential amplifier) */
  BoolFlag LpTiaPwrEn;      /**< Enable(bTRUE) or Disable(bFALSE) power of LPTIA amplifier */
}LPAmpCfg_Type;

```

## Trapezoid Generator parameters

```c

/**
 * @brief Trapezoid Generator parameters
 * The definition of the Trapezoid waveform is shown below. Note the Delay and Slope are all in clock unit.
 * @code
 * 
 * DCLevel2         _________
 *                 /         \
 *                /           \
 * DCLevel1 _____/             \______
 *         |     |  |       |  |
 *         Delay1|S1|Delay2 |S2| Delay1 repeat...
 * Where S1 is slope1 and S2 is slop2
 * @endcode
 * The DAC update rate from Trapezoid generator is SystemClock/50. The default SystemClock
 * is internal HFOSC 16MHz. So the update rate is 320kHz.
 * The time parameter specifies in clock number.
 * For example, if Delay1 is set to 10, S1 is set 20, the time for Delay1 period is 10/320kHz = 31.25us,
 * and time for S1 period is 20/320kHz = 62.5us.
*/
typedef struct
{
  uint32_t WGTrapzDCLevel1;   /**< Trapezoid generator DC level1, this value is written directly to corresponding register */
  uint32_t WGTrapzDCLevel2;   /**< DC level2, similar to DCLevel1 */
  uint32_t WGTrapzDelay1;     /**< Trapezoid generator delay 1 */
  uint32_t WGTrapzDelay2;     /**< Trapezoid generator delay 2 */
  uint32_t WGTrapzSlope1;     /**< Trapezoid generator Slope 1 */
  uint32_t WGTrapzSlope2;     /**< Trapezoid generator Slope 2 */
}WGTrapzCfg_Type;

```

## Sin wave generator parameters

```c

/**
 * Sin wave generator parameters
*/
typedef struct
{
  uint32_t SinFreqWord;       /**< Frequency word */
  uint32_t SinAmplitudeWord;  /**< Amplitude word, range is 0 to 2047. Amplitude range is 0 to 800mV */
  uint32_t SinOffsetWord;     /**< Offset word, range is -2048 to 2047. Offset voltage range is -800 to +800mV */
  uint32_t SinPhaseWord;      /**< the start phase of sine wave. Use to tune start phase of signal. */
}WGSinCfg_Type;

```

## Waveform generator configuration

```c

/**
 * Waveform generator configuration
*/
typedef struct
{
  uint32_t WgType;            /**< Select from WGTYPE_MMR, WGTYPE_SIN, WGTYPE_TRAPZ. HSDAC is always connected to WG. */
  BoolFlag GainCalEn;         /**< Enable Gain calibration */
  BoolFlag OffsetCalEn;       /**< Enable offset calibration */
  WGTrapzCfg_Type TrapzCfg;   /**< Configure Trapezoid generator */
  WGSinCfg_Type SinCfg;       /**< Configure Sine wave generator */
  uint32_t WgCode;            /**< The 12bit data WG will move to DAC data register. */
}WGCfg_Type;

```

## High speed loop configuration 

```c

/**
 * High speed loop configuration 
 * */
typedef struct
{
  SWMatrixCfg_Type SWMatCfg;  /**< switch matrix configuration. */
  HSDACCfg_Type HsDacCfg;     /**< HSDAC configuration. */
  WGCfg_Type WgCfg;           /**< Waveform generator configuration. */
  HSTIACfg_Type HsTiaCfg;     /**< HSTIA configuration. */
}HSLoopCfg_Type;

```

## Low power loop Configure

```c

/**
 * Low power loop Configure 
 * */
typedef struct
{
  LPDACCfg_Type LpDacCfg;     /**< LPDAC configuration. @note Must select LPDAC0 or LPDAC1 in structure. */
  LPAmpCfg_Type LpAmpCfg;     /**< LPAMP(LPTIA and PA) configuration. @note Must select LPAMP0 or LPAMP1 in structure. */
}LPLoopCfg_Type;

```

## DSP Configure 

```c

/**
 * DSP Configure 
 * */
typedef struct
{
  ADCBaseCfg_Type ADCBaseCfg;       /**< ADC base configuration */
  ADCFilterCfg_Type ADCFilterCfg;   /**< ADC filter configuration include SINC3/SINC2/Notch/Average(for DFT only) */
  ADCDigComp_Type ADCDigCompCfg;    /**< ADC digital comparator */
  DFTCfg_Type DftCfg;               /**< DFT configuration include data source, DFT number and Hanning Window */
  StatCfg_Type StatCfg;             /**< Statistic block */
}DSPCfg_Type;

```

## GPIO Configure 

```c

/**
 * GPIO Configure 
 * */
typedef struct
{
  uint32_t FuncSet;         /**< AGP0 to AGP7 function sets */
  uint32_t OutputEnSet;     /**< AGPIO_Pin0|AGPIO_Pin1|...|AGPIO_Pin7, Enable output of selected pins, disable other pins */
  uint32_t InputEnSet;      /**< Enable input of selected pins, disable other pins */
  uint32_t PullEnSet;       /**< Enable pull up or down on selected pin. disable other pins */
  uint32_t OutVal;          /**< Value for GPIOOUT register */
}AGPIOCfg_Type;

```

## FIFO Configure 

```c

/**
 * FIFO configure
*/
typedef struct
{
  BoolFlag FIFOEn;          /**< Enable DATAFIFO. Disable FIFO will reset FIFO */
  uint32_t FIFOMode;        /**< Stream mode or standard FIFO mode */
  uint32_t FIFOSize;        /**< How to allocate the internal 6kB SRAM. Data FIFO and sequencer share all 6kB SRAM */
  uint32_t FIFOSrc;         /**< Select which data source will be stored to FIFO */
  uint32_t FIFOThresh;      /**< FIFO threshold value, 0 to 1023. Threshold can be used to generate interrupt so MCU can read back data before FIFO is full */
}FIFOCfg_Type;

```

## Sequencer Configure 

```c

/**
 * Sequencer configure
*/
typedef struct
{
  uint32_t SeqMemSize;      /**< Sequencer memory size. SRAM is used by both FIFO and Sequencer. Make sure the total SRAM used is less than 6kB. */
  BoolFlag SeqEnable;       /**< Enable sequencer. Only with valid trigger, sequencer can run */
  BoolFlag SeqBreakEn;      /**< Do not use it */
  BoolFlag SeqIgnoreEn;     /**< Do not use it */
  BoolFlag SeqCntCRCClr;    /**< Clear sequencer count and CRC */
  uint32_t SeqWrTimer;      /**< Set wait how much clocks after every commands executed */
}SEQCfg_Type;

/**
 * Sequence info structure
*/
typedef struct
{
  uint32_t SeqId;           /**< The Sequence ID @ref SEQID_Const */
  uint32_t SeqRamAddr;      /**< The start address that in AF5940 SRAM */
  uint32_t SeqLen;          /**< Sequence length */
  BoolFlag WriteSRAM;       /**< Write command to SRAM or not. */
  const uint32_t *pSeqCmd;  /**< Pointer to the sequencer commands that stored in MCU */
}SEQInfo_Type;

typedef struct
{
  uint32_t PinSel;          /**< Select which pin are going to be configured. @ref AGPIOPIN_Const */
  uint32_t SeqPinTrigMode;  /**< The pin detect mode. Select from @ref SEQPINTRIGMODE_Const */
  BoolFlag bEnable;         /**< Allow detected pin action to trigger corresponding sequence. */
}SeqGpioTrig_Cfg;

```

## Wakeup Timer Configure

```c

/**
 * Wakeup Timer Configure
 * */
typedef struct
{
  uint32_t WuptEndSeq;       /**<  end sequence selection @ref WUPTENDSEQ_Const. Wupt will go back to slot A after this one is executed. */
  uint32_t WuptOrder[8];     /**<  The 8 slots for WakeupTimer. Place @ref SEQID_Const to this array. */
  uint32_t SeqxSleepTime[4];  /**< Time before put AFE to sleep. 0 to 0x000f_ffff. We normally don't use this feature and it's disabled in @ref AD5940_Initialize */
  uint32_t SeqxWakeupTime[4]; /**< Time before Wakeup AFE.  */
  BoolFlag WuptEn;            /**< Timer enable. Once enabled, it starts to run. */
}WUPTCfg_Type;

```

## Clock configure

```c


/**
 * Clock configure
*/
typedef struct
{
  uint32_t SysClkSrc;       /**< System clock source @ref SYSCLKSRC_Const */
  uint32_t ADCCLkSrc;       /**< ADC clock source @ref ADCCLKSRC_Const */
  uint32_t SysClkDiv;       /**< System clock divider. Use this to ensure System clock < 16MHz. */
  uint32_t ADCClkDiv;       /**< ADC control clock divider. ADC core clock is @ADCCLkSrc, but control clock should be <16MHz.  */
  BoolFlag HFOSCEn;         /**< Enable internal 16MHz/32MHz HFOSC */
  BoolFlag HfOSC32MHzMode;  /**< Enable internal HFOSC to output 32MHz */
  BoolFlag LFOSCEn;         /**< Enable internal 32kHZ OSC */
  BoolFlag HFXTALEn;        /**< Enable XTAL driver */
}CLKCfg_Type;

```

## HSTIA internal RTIA calibration structure

```c

/**
 * HSTIA internal RTIA calibration structure
 * @note ADC filter settings and DFT should be configured properly based on signal frequency.
*/
typedef struct
{
  float fFreq;                /**< Calibration frequency */
  float fRcal;                /**< Rcal resistor value in Ohm*/
  float SysClkFreq;           /**< The real frequency of system clock */  
  float AdcClkFreq;           /**< The real frequency of ADC clock */   

  HSTIACfg_Type HsTiaCfg;     /**< HSTIA configuration */
  uint32_t ADCSinc3Osr;       /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */
  uint32_t ADCSinc2Osr;       /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */ 
  DFTCfg_Type DftCfg;         /**< DFT configuration. */
  uint32_t bPolarResult;      /**< bTRUE-Polar coordinate:Return results in Magnitude and Phase. bFALSE-Cartesian coordinate: Return results in Real part and Imaginary Part */
}HSRTIACal_Type;

```

## LPTIA internal RTIA calibration structure

```c

/**
 * LPTIA internal RTIA calibration structure
*/
typedef struct
{
  float fFreq;                /**< Calibration frequency. Set it to 0.0 for DC calibration */
  float fRcal;                /**< Rcal resistor value in Ohm*/
  float SysClkFreq;           /**< The real frequency of system clock */  
  float AdcClkFreq;           /**< The real frequency of ADC clock */   

  uint32_t LpAmpSel;          /**< Select from LPAMP0 and LPAMP1. LPAMP1 is only available on ADuCM355. */
  BoolFlag bWithCtia;         /**< Connect external CTIA or not. */
  uint32_t LpTiaRtia;         /**< LPTIA RTIA selection. */
  uint32_t LpAmpPwrMod;       /**< Amplifiers power mode setting */
  uint32_t ADCSinc3Osr;       /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */
  uint32_t ADCSinc2Osr;       /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */ 
  DFTCfg_Type DftCfg;         /**< DFT configuration */
  uint32_t bPolarResult;      /**< bTRUE-Polar coordinate:Return results in Magnitude and Phase. bFALSE-Cartesian coordinate: Return results in Real part and Imaginary Part */
}LPRTIACal_Type;

```

## HSDAC calibration structure.

```c

/**
 * HSDAC calibration structure.
*/
typedef struct
{
  float fRcal;                /**< Rcal resistor value in Ohm*/
  float SysClkFreq;           /**< The real frequency of system clock */  
  float AdcClkFreq;           /**< The real frequency of ADC clock */ 

  uint32_t AfePwrMode;        /**< Calibrate DAC in High power mode */
  uint32_t ExcitBufGain;      /**< Select from  EXCITBUFGAIN_2, EXCITBUFGAIN_0P25 */     
  uint32_t HsDacGain;         /**< Select from  HSDACGAIN_1, HSDACGAIN_0P2 */

  uint32_t ADCSinc3Osr;       /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */
  uint32_t ADCSinc2Osr;       /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */ 
}HSDACCal_Type;

```

## LPDAC calibration structure.

```c

/**
 * LPDAC calibration structure.
*/
typedef struct
{
  uint32_t  LpdacSel;           /**< Select from LPDAC0 and LPDAC1. LPDAC1 is ADuCM355 only. */
  float     SysClkFreq;         /**< The real frequency of system clock */  
  float     AdcClkFreq;         /**< The real frequency of ADC clock */
  float     ADCRefVolt;         /**< ADC reference voltage. Default is 1.82V*/
  uint32_t  ADCSinc3Osr;        /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */
  uint32_t  ADCSinc2Osr;        /**< SINC2 OSR settings. */ 
  int32_t   SettleTime10us;     /**< Wait how much time after TIA is enabled? */   
  int32_t   TimeOut10us;        /**< ADC converts signal need time. Specify the maximum time allowed. Timeout in 10us. negative number means wait no time. */
}LPDACCal_Type;

/**
 * LPDAC parameters: LPDAC code to voltage transfer function.
 * Voltage(mV) = kC2V_DACxB * Code + bC2V_DACxB; 
 *  where x is 12 or 6 represent 12Bit DAC and 6Bit DAC. C2V means code to voltage.
 *  Code is the data register value for LPDAC. The equation gives real output voltage of LPDAC.    
 * Similarly, Code(LSB) = kV2C_DACxB * Voltage(mV) + bV2C_DACxB;
 * 
 * Apparently, kV2C_DACxB = 1/kC2V_DACxB;
 *             bV2C_DACxB = -bC2V_DACxB/kC2V_DACxB;
*/
typedef struct
{
  /* Code to voltage equation parameters */
  float kC2V_DAC12B;        /**< the k factor of code to voltage(in mV) transfer function */
  float bC2V_DAC12B;        /**< the offset of code to voltage transfer function. It's the voltage in mV when code is zero. */
  float kC2V_DAC6B;         /**< the k factor for LPDAC 6 bit output. */
  float bC2V_DAC6B;         /**< the offset for LPDAC 6 bit output. */
 /* Code to voltage equation parameters */
  float kV2C_DAC12B;        /**< the k factor for converting voltage to code for LPDAC 12bit output. */
  float bV2C_DAC12B;        /**< the offset for converting voltage to code for LPDAC 12bit output. */
  float kV2C_DAC6B;         /**< the k factor for converting voltage to code for LPDAC 6bit output. */
  float bV2C_DAC6B;         /**< the offset for converting voltage to code for LPDAC 6bit output. */
}LPDACPara_Type;

```

## LFOSC frequency measure structure

```c

/**
 * LFOSC frequency measure structure
*/
typedef struct
{
  uint32_t CalSeqAddr;        /**< Sequence start address */
  float CalDuration;          /**< Time can be used for calibration in unit of ms. Recommend to use tens of millisecond like 10ms */
  float SystemClkFreq;        /**< System clock frequency.  */
}LFOSCMeasure_Type;

```

## ADC PGA calibration type

```c

/**
 * ADC PGA calibration type
*/
typedef struct
{
  float SysClkFreq;           /**< The real frequency of system clock */  
  float AdcClkFreq;           /**< The real frequency of ADC clock */  
  float VRef1p82;             /**< The real voltage of 1.82 reference. Unit is volt. */
  float VRef1p11;             /**< The real voltage of 1.1 reference. Unit is volt. */
  uint32_t ADCSinc3Osr;       /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */
  uint32_t ADCSinc2Osr;       /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */ 
  uint32_t ADCPga;            /**< Which PGA gain we are going to calibrate? */
  uint32_t PGACalType;        /**< Calibrate gain of offset or gain+offset? */
  int32_t TimeOut10us;        /**< Timeout in 10us. -1 means no time-out*/
}ADCPGACal_Type;

```

## LPTIA Offset calibration type

```c

/**
 * LPTIA Offset calibration type
*/
typedef struct
{
  uint32_t  LpAmpSel;           /**< Select from LPAMP0 and LPAMP1. LPAMP1 is only available on ADuCM355. */
  float     SysClkFreq;         /**< The real frequency of system clock */  
  float     AdcClkFreq;         /**< The real frequency of ADC clock */  
  uint32_t  ADCSinc3Osr;        /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */
  uint32_t  ADCSinc2Osr;        /**< SINC3OSR_5, SINC3OSR_4 or SINC3OSR_2 */ 
  uint32_t  ADCPga;             /**< PGA Gain selection */
  uint32_t  DacData12Bit;       /**< 12Bit DAC data */
  uint32_t  DacData6Bit;        /**< 6Bit DAC data */
  uint32_t  LpDacVzeroMux;      /**< Vzero is used as LPTIA bias voltage, select 12Bit/6Bit DAC */
  uint32_t  LpAmpPwrMod;        /**< LP amplifiers power mode, select from LPAMPPWR_NORM, LPAMPPWR_BOOSTn*/ 
  uint32_t  LpTiaSW;            /**< Switch configuration for LPTIA. Normally for SW(5) and SW(9).*/
  uint32_t  LpTiaRtia;          /**< LPTIA RTIA resistor selection. */
  int32_t   SettleTime10us;     /**< Wait how much time after TIA is enabled? */   
  int32_t   TimeOut10us;        /**< ADC converts signal need time. Specify the maximum time allowed. Timeout in 10us. negative number means wait no time. */
}LPTIAOffsetCal_Type;

```

## Structure for calculating how much system clocks needed for specified number of data

```c

/**
 * Structure for calculating how much system clocks needed for specified number of data
*/
typedef struct
{
  uint32_t DataType;          /**< The final data output selection. @ref DATATYPE_Const */
  uint32_t DataCount;         /**< How many data you want. */
  uint32_t ADCSinc3Osr;       /**< ADC SINC3 filter OSR setting */
  uint32_t ADCSinc2Osr;       /**< ADC SINC2 filter OSR setting */
  uint32_t ADCAvgNum;         /**< Average number for DFT engine. Only used when data type is DATATYPE_DFT and DftSrc is DFTSRC_AVG */
  uint32_t DftSrc;            /**< The DFT source. Only used when data type is DATATYPE_DFT */
  uint8_t  ADCRate;           /**< ADCRate @ref ADCRATE_Const. Only used when data type is DATATYPE_NOTCH */
  BoolFlag BpNotch;           /**< Bypass notch filter or not. Only used when data type is DATATYPE_DFT and DftSrc is DFTSRC_SINC2NOTCH */
  float RatioSys2AdcClk;      /**< Ratio of system clock to ADC clock frequency */
}ClksCalInfo_Type;

```

## Software controlled Sweep Function 

```c

/** 
 * Software controlled Sweep Function 
 * */
typedef struct
{
  BoolFlag SweepEn;         /**< Software can automatically sweep frequency from following parameters. Set value to 1 to enable it. */
  float SweepStart;         /**< Sweep start frequency. Software will go back to the start frequency when it reaches SWEEP_STOP */
  float SweepStop;          /**< Sweep end frequency. */
  uint32_t SweepPoints;     /**< How many points from START to STOP frequency */
  BoolFlag SweepLog;        /**< The step is linear or logarithmic. 0: Linear, 1: Logarithmic*/
  uint32_t SweepIndex;      /**< Current position of sweep */
}SoftSweepCfg_Type;

```

## Impedance result in Polar coordinate 

```c

/**
 * Impedance result in Polar coordinate 
*/
typedef struct
{
  float Magnitude;         /**< The magnitude in polar coordinate */
  float Phase;             /**< The phase in polar coordinate */
}fImpPol_Type; //Polar

```

## Impedance result in Cartesian coordinate 

```c

/**
 * Impedance result in Cartesian coordinate 
*/
typedef struct
{
  float Real;              /**< The real part in Cartesian coordinate */
  float Image;             /**< The imaginary in Cartesian coordinate */
}fImpCar_Type; //Cartesian

```

## int32_t type Impedance result in Cartesian coordinate 

```c

/**
 * int32_t type Impedance result in Cartesian coordinate 
*/
typedef struct
{
  int32_t Real;         /**< The real part in Cartesian coordinate */
  int32_t Image;        /**< The real imaginary in Cartesian coordinate */
}iImpCar_Type;

```

## FreqParams_Type - Structure to store optimum filter settings 

```c

/**
 *  FreqParams_Type - Structure to store optimum filter settings 
*/
typedef struct
{
	BoolFlag HighPwrMode;
	uint32_t DftNum;
	uint32_t DftSrc;
	uint32_t ADCSinc3Osr;
	uint32_t ADCSinc2Osr;
	uint32_t NumClks;
}FreqParams_Type;

```

## Sequencer Generator

```c
typedef struct
{
  uint32_t RegAddr  :8;   /**< 8bit address is enough for sequencer */
  uint32_t RegValue :24;  /**< Reg data is limited to 24bit by sequencer  */
}SEQGenRegInfo_Type;

/**
 * Sequencer generator data base.
*/
struct
{
  BoolFlag EngineStart;         /**< Flag to mark start of the generator */
  uint32_t BufferSize;          /**< Total buffer size */

  uint32_t *pSeqBuff;           /**< The buffer for sequence generator(both sequences and RegInfo) */
  uint32_t SeqLen;              /**< Generated sequence length till now */
  SEQGenRegInfo_Type *pRegInfo; /**< Pointer to buffer where stores register info */
  uint32_t RegCount;            /**< The count of register info available in buffer *pRegInfo. */
  AD5940Err LastError;          /**< The last error message. */
}SeqGenDB;  /* Data base of Seq Generator */

```
