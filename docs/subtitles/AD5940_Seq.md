# AD5940 Sequencer 

## Macro

```c

/** 
 * Write register command. Command Code: 'b10 or 'b11 
 * @warning Address range is 0x2000 to 0x21FF. Data is limited to 24bit width.
 * */
#define SEQ_WR(addr,data)           (0x80000000|(((((uint32_t)(addr))>>2)&0x7f)<<24)  \
                                        |(((uint32_t)(data))&0xffffff))
```

## Sequencer Data Strucature

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

## Manual Insert

```c

/**
 * @brief Manually input a command to sequencer generator.
 * @param CmdWord: The 32-bit width sequencer command word. @ref Sequencer_Helper can be used to generate commands.
 * @return None;
*/
void AD5940_SEQGenInsert(uint32_t CmdWord)

/**
 * @brief Get the register default value by SPI read. This function requires AD5940 is in active state, otherwise we cannot get the default register value.
 * @param RegAddr: The register address.
 * @param pRegData: Pointer to a variable to store register default value.
 * @return Return AD5940ERR_OK.
*/
static AD5940Err AD5940_SEQGenGetRegDefault(uint32_t RegAddr, uint32_t *pRegData)

/**
 * @brief Record the current register info to data-base. Update LastError if there is error.
 * @param RegAddr: The register address.
 * @param RegData: The register data
 * @return Return None.
*/
static void AD5940_SEQRegInfoInsert(uint16_t RegAddr, uint32_t RegData)

/**
 * @brief Get current register value. If we have record in data-base, read it. Otherwise, return the register default value.
 * @param RegAddr: The register address.
 * @return Return register value.
*/
static uint32_t AD5940_SEQReadReg(uint16_t RegAddr)

/**
 * @brief Generate a sequencer command to write register. If the register address is out of range, it won't generate a command.
 *        This function will also update the register-info in data-base to record current register value.
 * @param RegAddr: The register address.
 * @param RegData: The register value.
 * @return Return None.
*/
static void AD5940_SEQWriteReg(uint16_t RegAddr, uint32_t RegData)

```

## Sequencer Generator

```c

/**
 * @brief Initialize sequencer generator with specified buffer.
 *        The buffer is used to store sequencer generated and record register value changes.
 *        The command is stored from start address of buffer while register value is stored from end of buffer.
 *    Buffer[0] : First sequencer command;
 *    Buffer[1] : Second Sequencer command;
 *    ...
 *    Buffer[Last-1]: The second register value record.
 *    Buffer[Last]: The first register value record.
 * @param pBuffer: Pointer to the buffer.
 * @param BufferSize: The buffer length.
 * @return Return None.
*/
void AD5940_SEQGenInit(uint32_t *pBuffer, uint32_t BufferSize)

/**
 * @brief Get sequencer command generated.
 * @param ppSeqCmd: Pointer to a variable(pointer) used to store the pointer to generated sequencer command.
 * @param pSeqLen: Pointer to a variable that used to store how many commands available in buffer.
 * @return Return lasterror.
*/
AD5940Err AD5940_SEQGenFetchSeq(const uint32_t **ppSeqCmd, uint32_t *pSeqLen)

/**
 * @brief Start or stop the sequencer generator. Once started, the register write will be recorded to sequencer generator.
 *        Once it's disabled, the register write is written to AD5940 directly by SPI bus.
 * @param bFlag: Enable or disable sequencer generator.
 * @return Return None.
*/
void AD5940_SEQGenCtrl(BoolFlag bFlag)

/**
 * @brief Calculate the number of cycles in the sequence
 * @return Return Number of ACLK Cycles that a generated sequence will take.
*/
uint32_t AD5940_SEQCycleTime(void)

```

## Sequencer Control

```c

/* Sequencer */
/**
 * @brief Initialize Sequencer
 * @param pSeqCfg: Pointer to configuration structure
   @return return none.
*/
void AD5940_SEQCfg(SEQCfg_Type *pSeqCfg)

/**
 * @brief Read back current sequencer configuration and store it to pSeqCfg
 * @param pSeqCfg: Pointer to structure
 * @return return AD5940ERR_OK if succeed.
*/
AD5940Err AD5940_SEQGetCfg(SEQCfg_Type *pSeqCfg)

/**
 * @brief Enable or Disable sequencer. 
 * @note Only after valid trigger signal, sequencer can run.
 * @return return none.
*/
void AD5940_SEQCtrlS(BoolFlag SeqEn)

/**
 * @brief Halt sequencer immediately. Use this to debug. In normal application, there is no situation that can use this function.
 * @return return none.
*/
void AD5940_SEQHaltS(void)

/**
 * @brief Trigger sequencer by register write.
 * @return return none.
**/
void AD5940_SEQMmrTrig(uint32_t SeqId)

/**
 * @brief Write sequencer commands to AD5940 SRAM.
 * @return return none.
**/
void AD5940_SEQCmdWrite(uint32_t StartAddr, const uint32_t *pCommand, uint32_t CmdCnt)

/**
   @brief Initialize Sequence INFO. 
   @details There are four set of registers that record sequence information. 
          The info contains command start address in SRAM and sequence length. 
          Hardware can automatically manage these four sequences. If the application 
          requires more than 4 sequences, user should manually record the sequence 
          Info(address and length) in MCU.
   @param pSeq: Pointer to configuration structure. Specify sequence start address in SRAM and sequence length.
   @return return none.
*/
void AD5940_SEQInfoCfg(SEQInfo_Type *pSeq)

/**
 * @brief Get sequence info: start address and sequence length.
 * @param SeqId: Select from {SEQID_0, SEQID_1, SEQID_2, SEQID_3}
          - Select which sequence we want to get the information. 
   @param pSeqInfo: Pointer to sequence info structure. 
   @return return AD5940ERR_OK when succeed.
*/
AD5940Err AD5940_SEQInfoGet(uint32_t SeqId, SEQInfo_Type *pSeqInfo)

/**
   @brief Control GPIO with register SYNCEXTDEVICE. Because sequencer have no ability to access register GPIOOUT,
         so we use this register for sequencer.
   @param Gpio : Select from {AGPIO_Pin0|AGPIO_Pin1|AGPIO_Pin2|AGPIO_Pin3|AGPIO_Pin4|AGPIO_Pin5|AGPIO_Pin6|AGPIO_Pin7}
          - The combination of GPIO pins. The selected pins will be set to High. Others will be pulled low.
   @return return None.

**/
void AD5940_SEQGpioCtrlS(uint32_t Gpio)

/**
 * @brief Read back current count down timer value for Sequencer Timer Out command.
 * @return return register value of Sequencer Timer out value.
**/
uint32_t AD5940_SEQTimeOutRd(void)

/**
 * @brief Configure GPIO to allow it to trigger corresponding sequence(SEQ0/1/2/3).
 * @details There are four sequences. We can use GPIO to trigger each sequence. For example,
 *          GP0 or GP4 can be used to trigger sequence0 and GP3 or GP7 to trigger sequence3.
 *          There are five mode available to detect pin action: Rising edge, falling edge, both rising and falling
 *          edge, low level or high level.
 *          Be careful to use level detection. The trigger signal is always available if the pin level is matched.
 *          Once the sequence is done, it will immediately run again if the pin level is still matched.
 * @return return AD5940ERR_OK if succeed.
**/
AD5940Err AD5940_SEQGpioTrigCfg(SeqGpioTrig_Cfg *pSeqGpioTrigCfg)


```
