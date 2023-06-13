### Hall module: control LED_GPIO input/output

#### Hardware wiring

![1](..\Infrared_module\1.jpg)

| Hall module | STM32F103RCT6 |
| :---------: | :-----------: |
|     VCC     |    5V/3.3V    |
|     K1      |      PA1      |
|     NC      |               |
|     GND     |      GND      |

#### Brief principle

##### Circuit schematic

![LED原理图](..\人体红外模块：控制LED_GPIO输入_输出\LED原理图.png)

The LED is connected to the PB4 pin, you need to pay attention to the pin configuration of PB4 (see the code for the specific configuration):

PB4 output high level, LED on;

The PB4 output is low power

#### Main code

##### main.c

```
#include "stm32f10x.h"
#include "Hall_module.h"
#include "LED.h"


int main(void)
{
    LED_Init();//LED初始化(PB4)
		Hall_module_Init();//霍尔模块初始化(PA1)
    
    while(1)
    {
			if(GPIO_ReadInputDataBit(GPIOA, GPIO_Pin_1) == Bit_RESET)//判断霍尔模块是否检测到磁铁
        {
            
            GPIO_WriteBit(GPIOB, GPIO_Pin_4, Bit_SET);//LED 亮
        }
        else
        {
            GPIO_WriteBit(GPIOB, GPIO_Pin_4, Bit_RESET);//LED 灭
        }
    }
}
```

##### LED.c

```
#include "LED.h"

void LED_Init(void)//LED初始化(PB4)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    
    /* Enable GPIOB and AFIO clocks */
    /* 使能GPIOB和功能复用IO时钟 */
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB | RCC_APB2Periph_AFIO, ENABLE); 
    
    /* JTAG-DP Disabled and SW-DP Enabled */
    /* 禁用JTAG 启用SWD */
    GPIO_PinRemapConfig(GPIO_Remap_SWJ_JTAGDisable, ENABLE);
    
    /* Configure PB4 in output pushpull mode */
    /* 配置PB4 推挽输出模式 */
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_4;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;   
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; 
    GPIO_Init(GPIOB, &GPIO_InitStructure);
    
    /* Set the GPIOB port pin 4 */
    /* 设置PB4端口数据位 */
    GPIO_WriteBit(GPIOB, GPIO_Pin_4, Bit_RESET);
}
```

##### LED.h

```
#ifndef __LED_H__
#define __LED_H__

#include "stm32f10x.h"

void LED_Init(void);//LED初始化(PB4)

#endif
```

##### Hall_module.c

```
#include "Hall_module.h"

void Hall_module_Init(void)//霍尔模块初始化(PA1)
{
    GPIO_InitTypeDef GPIO_InitStructure;
    
    /* Enable GPIOA */
    /* 使能GPIOA时钟 */
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE); 
    
    /* Configure PA1 in Floating input mode */
    /* 配置PA1 浮空输入模式 */
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_1;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;   
    GPIO_Init(GPIOA, &GPIO_InitStructure);  
}
```

##### Hall_module.h

```
#ifndef __HALL_MOUDLE_H__
#define __HALL_MOUDLE_H__

#include "stm32f10x.h"

void Hall_module_Init(void);//霍尔模块初始化(PA1)

#endif
```

#### **Phenomenon**

After downloading the program, press the Reset key once, and the downloaded program will run.

When the Hall module detects a magnet:

OUT outputs a low level and the LED lights up.

When the Hall module does not detect magnets:

OUT outputs a high level and the LED goes off.