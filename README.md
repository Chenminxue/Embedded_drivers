# Embedded_drivers
## Write the global driver in stm32f407xx.h

1. Write the BASE ADDRESS for flash memory, SRAM.

```
#define FLASH_BASEADDR		0x08000000U
#define SRAM1_BASEADDR		0x20000000U 	
#define SRAM2_BASEADDR		0x2001C000U	
#define ROM_BASEADDR		0x1FFF0000U
#define SRAM 			SRAM1_BASEADDR
```

2. Find the BASE ADDRESS for APBx and AHBx buses.

```
#define PERIPH_BASEADDR 	0x40000000U
#define APB1PERIPH_BASEADDR	PERIPH_BASEADDR
#define APB2PERIPH_BASEADDR	0x40010000U
#define AHB1PERIPH_BASEADDR	0x40020000U
#define AHB2PERIPH_BASEADDR	0x50000000U
```

3. Base on the bus address write the BASE ADDRESS for peripherals such as GPIOx, I2Cx, SPIx, RCC, EXTI, etc...

```
#define GPIOA_BASEADDR          (AHB1PERIPH_BASEADDR + 0x0000)
#define GPIOB_BASEADDR          (AHB1PERIPH_BASEADDR + 0x0400)
	.
	.
	.
#define RCC_BASEADDR            (AHB1PERIPH_BASEADDR + 0x3800)

#define I2C1_BASEADDR		(APB1PERIPH_BASEADDR + 0x5400)
	.
	.
	.
#define SPI2_BASEADDR		(APB1PERIPH_BASEADDR + 0x3800)
	.
	.
	.
#define EXTI_BASEADDR		(APB2PERIPH_BASEADDR + 0x3C00)
	.
	.
	.
```

4. Write the register struct for peripherals such as GPIO and RCC
	
```
typedef struct
{
	__vo uint32_t MODER;
	__vo uint32_t OTYPER;                       
	__vo uint32_t OSPEEDR;
	__vo uint32_t PUPDR;
	__vo uint32_t IDR;
	__vo uint32_t ODR;
	__vo uint32_t BSRR;
	__vo uint32_t LCKR;
	__vo uint32_t AFR[2];
}GPIO_RegDef_t;

typedef struct
{
 	 __vo uint32_t CR;
 	 __vo uint32_t PLLCFGR;
 	 __vo uint32_t CFGR;
 	 __vo uint32_t CIR;
	.
	.
	.
 	 __vo uint32_t CKGATENR;
	 __vo uint32_t DCKCFGR2;

} RCC_RegDef_t;
	
	.
	.
	.
```
	
5. Use the register struct to write the specific group of register for specific peripherals such as 

```
#define GPIOA	((GPIO_RegDef_t*) GPIOA_BASEADDR)

#define RCC	((RCC_RegDef_t*) RCC_BASEADDR)

#define EXTI	((EXTI_RegDef_t*)EXTI_BASEADDR)

#define I2C1  	((I2C_RegDef_t*)I2C1_BASEADDR)
	.
	.
	.
```

6. Write clock enable for each peripheral by using the enable register in RCC such as 

```
#define GPIOA_PCLK_EN()    	(RCC->AHB1ENR |= (1 << 0))
#define GPIOB_PCLK_EN()		(RCC->AHB1ENR |= (1 << 1))
#define I2C1_PCLK_EN() 		(RCC->APB1ENR |= (1 << 21))
#define SPI1_PCLK_EN() 		(RCC->APB2ENR |= (1 << 12))
	.
	.
	.
```

7. Write clock disable such as 

```
GPIOA_PLCK_DI()	(RCC->AHB1ENR &= ~(1 << 0))
	.
	.
	.
```

## Write the specific driver for GPIO, I2C, SPI, etc...(Define GPIO first)
1. Create specific .c and .h file for the driver such as stm32f407_gpio_driver.h and .c
2. Create handler srtuct for peripherals such as GPIOx, I2Cx, etc... the struct contains the address of the peripheral u want to use and the configuration of the peripheral for initialization. 

```
typedef struct
{
	GPIO_RegDef_t *pGPIOx;
	GPIO_PinConfig_t GPIO_PinConfig;

}GPIO_Handle_t;
```

3. Create configuration struct which stores the configuration for the real peripheral based on the requirements.

```
typedef struct
{
	uint8_t GPIO_PinNumber;
	uint8_t GPIO_PinMode;
	uint8_t GPIO_PinSpeed;
	uint8_t GPIO_PinPuPdControl;
	uint8_t GPIO_PinOPType;
	uint8_t GPIO_PinAltFunMode;
}GPIO_PinConfig_t;
```

4. Add APIs such as GPIO_Init(), GPIO_DeInit(), GPIO_PeriClockControl(), GPIO_IRQConfig(), GPIO_IRQHandling(), etc...
