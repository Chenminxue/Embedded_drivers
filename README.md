# Embedded_drivers
## Write the global driver in stm32f407xx.h

	1. Write the BASE ADDRESS for flash memory, SRAM.
	2. Find the BASE ADDRESS for APBx and AHBx buses.
	3. Base on the bus address write the BASE ADDRESS for peripherals such as GPIOx, I2Cx, SPIx, RCC, EXTI, etc...
	4. Write the register struct for peripherals such as 
'''
	type struct{...} GPIO_RegDef_t; 
	type struct {...} RCC_RegDef_t;
	etc
'''
	5. Use the register struct to write the specific group of register for specific peripherals such as #define GPIOA	((GPIO_RegDef_t*) 		GPIOA_BASEADDR), #define RCC	((RCC_RegDef_t*) RCC_BASEADDR)
	6. Write clock enable for each peripheral by using the enable register in RCC such as #define GPIOA_PCLK_EN()	(RCC->AHB1ENR |= (1 		<< 0)), #define I2C1_PLCK_EN()	(RCC->APB1ENR |= (1 << 21))
	7. Write clock disable such as GPIOA_PLCK_DI()	(RCC->AHB1ENR &= ~(1 << 0))

## Write the specific driver for GPIO, I2C, SPI, etc...
	1. Create specific .c and .h file for the driver such as stm32f407_gpio_driver.h and .c
	2. Create handler srtuct for peripherals such as GPIOx, I2Cx, etc... the struct contains the address of the peripheral u want to use and the configuration of the peripheral for 			initialization. type struct {GPIO_RegDef_t * pGPIOx; GPIO_PinConfig_t GPIO_PinConfig;;} GPIO_Handle_t;
	3. Create configuration struct which stores the configuration for the real peripheral based on the requirements. For example, typedef 		struct {uint8_t GPIO_PinNumber; uint8_t 		GPIO_PinMode; uint8_t GPIO_PinSpeed; uint8_t GPIO_PinPuPdControl; uint8_t GPIO_PinOPType; uint8_t GPIO_PinAltFunMode;}GPIO_PinConfig_t;
	4. Add APIs such as GPIO_Init(), GPIO_DeInit(), GPIO_PeriClockControl(), GPIO_IRQConfig(), GPIO_IRQHandling(), etc...
