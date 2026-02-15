Ambient Light Monitoring using ADC Interrupt – STM32F446RE
-----------------------------------------------------------
This project reads analog voltage from a KY-018 photoresistor module using ADC in Interrupt mode on the STM32F446RE Nucleo board.Instead of polling the ADC continuously, the conversion completion generates an interrupt. The ADC value is processed inside the interrupt callback function.

Hardware Used
--------------
STM32F446RE Nucleo
KY-018 LDR Module
USB Cable
Onboard LED (PA5)

Pin Connections
-----------------
KY-018              	STM32
S	                   PA0 (ADC1_IN0)
+	                     3.3V
-	                      GND
⚠️ 3.3V only (STM32 ADC max input is 3.3V)

Working Principle
------------------
1.KY-018 outputs analog voltage depending on light intensity.
2.ADC converts analog voltage into 12-bit digital value (0–4095).
3.When conversion completes:
    > ADC interrupt is triggered.
    > HAL_ADC_ConvCpltCallback() is executed.
4.LED is controlled based on light level.

STM32CubeMX Configuration
--------------------------
1)ADC1 Settings:
   Channel: IN0 (PA0)
   Resolution: 12-bit
   Continuous Conversion: ENABLE
   Interrupt: ENABLE
2)NVIC:
   Enable ADC global interrupt


   Main Code
--------------
1)Start ADC in Interrupt Mode

uint32_t adc_value;

int main(void)
{
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();
  MX_ADC1_Init();

  HAL_ADC_Start_IT(&hadc1);

  while (1)
  {
    // Main loop remains free
  }
}


2)ADC Conversion Complete Callback

void HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef* hadc)
{
    if(hadc->Instance == ADC1)
    {
        adc_value = HAL_ADC_GetValue(hadc);

        if(adc_value < 2000)
            HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);
        else
            HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET);
    }
}

ADC Value Range (Approximate)
------------------------------
Light Condition	ADC Value
              Dark	0 – 1500
              Normal	1500 – 3000
              Bright	3000 – 4095
