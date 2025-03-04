---
title: Programming an STM32 on Fedora 41 with VSCode
date: 2025-03-04
description: Setting up a development environment for the STM32F411RE on Linux (Fedora 41) with Visual Studio Code
cover: ""
tags:
  - STM32
  - Fedora
draft: false
---

## About

In this article, we will set up an environment to program STM32 microcontrollers in C using [VSCode](https://code.visualstudio.com/). We will integrate STMicroelectronics’ tools to easily generate the base code needed for firmware development.

## Prerequisites

- The [VSCode](https://code.visualstudio.com/) editor
- An account on the [STMicroelectronics](https://www.st.com) website
- [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html)
- [STM32CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html)
- [ST-MCU-FINDER-PC](https://www.st.com/en/development-tools/st-mcu-finder-pc.html)

## Installation

> **Note**: Do not rush through the installation! Most issues arise from incorrect or mismatched installation paths for the dependencies.

### STM32 Tools

First, we need to install ST’s proprietary tools to interact easily with the microcontroller.

- **STM32CubeMX** and **ST-MCU-FINDER-PC** are installed via graphical installers. Carefully note the installation paths (commonly `~/STM32CubeMX` and `~/ST-MCU-FINDER-PC`).
- **STM32CubeCLT** installs via a script that creates the folder `/opt/ST/STM32CubeCLT` by default.

### VSCode

After installing VSCode, configure the tools as follows:

1. Open the **Extensions** panel.
2. Install the **STM32 VS Code Extension**.

![[565cda6a1352ebb74c1f97efc3d0f1c7_MD5.jpeg]]

Once the extension is installed, configure it to indicate the paths to the STM32 tools you just installed:

1. Go back to **Extensions**.
2. Click on the gear icon next to **STM32 VS Code Extension** and open the extension settings.
   ![[IMG_Programmer un STM32 sous Fedora 41 avec VSCode.png]]
3. Provide the paths to the various executables and to the `STM32CubeCLT` folder.

With the tools now configured, we can create our first project to verify that everything is installed correctly.

## Hello World

Our goal is to send a message over UART from the STM32 and to blink the onboard LED.

> *This tutorial is written for the [STM32F411RE](https://www.st.com/en/evaluation-tools/nucleo-f411re.html). Pin numbering or setups may differ depending on your development board.*

### Generating the Base Code Using STM32CubeMX

**STM32CubeMX** is ST’s proprietary software that lets you visually configure a microcontroller and automatically generate the corresponding code (GPIO, SPI, UART, I2C, DMA, etc.).

1. In VSCode, open the **STM32 VS Code Extension** panel.  
   ![[articles/fr/_resources/85d1b2484bc351e1ddb9969595ccfcf6_MD5.jpeg]]

2. Launch **STM32CubeMX** with the dedicated button.  
   ![[articles/fr/_resources/fe988d8c065be00f56923fec8f9de010_MD5.jpeg]]

3. Create a new project using the indicated button.  
   ![[articles/fr/_resources/dad1056dd12d64d3da419120abf90026_MD5.jpeg]]

   - Select the board or the microcontroller you are using.  
     ![[d97e0308efb265549dd2d49b6db77125_MD5.jpeg]]

   - Make sure the LED’s GPIO is properly configured and note its “User Label”; you will need this label in the code.  
     ![[articles/fr/_resources/f33b7af6c74c024efb1924b979d1152b_MD5.jpeg]]

   - Verify that UART is enabled and set to **115200 Baud**.  
     ![[articles/fr/_resources/1b1634ca22f9d64a3e286ec4ebb61a0b_MD5.jpeg]]

4. In the **Project Manager** tab, choose a project name and generate the code.  
   > **Important**: Be sure to select **CMake** as the toolchain!  
   ![[articles/fr/_resources/38e1bcc86ec027345ccf54802b507a1a_MD5.jpeg]]

5. Once code generation is complete, return to VSCode and **import the CMake project**.  
   ![[articles/fr/_resources/6f0effcd4ef4d3a78261265f03e778d3_MD5.jpeg]]

6. If prompted, restart VSCode and select the **Debug** configuration when CMake requests it.

The IDE is now ready! Let’s write our first firmware.

### First Firmware

In this firmware, we will blink the onboard LED and send a message via UART that includes a variable.  
Find the complete code on [this GitHub repository](https://github.com/Ery4z/Blog-STM32HelloWorld).

> **Note**: Make sure you place your code between the comments `USER CODE BEGIN` and `USER CODE END`. Otherwise, any changes made by STM32CubeMX might overwrite your custom code!

There are two main parts to edit for our goal:

#### 1. In `main.c` (Includes section)

```c
/* Private includes ----------------------------------------------------------*/

/* USER CODE BEGIN Includes */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

/* USER CODE END Includes */
```

#### 2. In the `main` function

```c
int main(void)
{
  /* USER CODE BEGIN 1 */
  /* USER CODE END 1 */

  /* MCU Configuration--------------------------------------------------------*/
  /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
  HAL_Init();

  /* USER CODE BEGIN Init */
  /* USER CODE END Init */

  /* Configure the system clock */
  SystemClock_Config();

  /* USER CODE BEGIN SysInit */
  /* USER CODE END SysInit */

  /* Initialize all configured peripherals */
  MX_GPIO_Init();
  MX_USART2_UART_Init();

  /* USER CODE BEGIN 2 */
  uint8_t counter = 0;
  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
    char buffer[64];
    snprintf(buffer, sizeof(buffer), "Hello World! %d\r\n", counter);
    HAL_UART_Transmit(&huart2, (uint8_t*)buffer, strlen(buffer), HAL_MAX_DELAY);

    HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
    HAL_Delay(100);
    HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
    HAL_Delay(200);

    HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
    HAL_Delay(100);
    HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
    HAL_Delay(1000);

    counter++;
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}
```

### Flashing and Debugging

Compile and flash your microcontroller by starting the **debugger** in VSCode:

![[articles/fr/_resources/03ad4d833cd46a328094ed00fc7ad68d_MD5.jpeg]]

### Viewing UART Output

To see the UART output:

1. Install the `screen` utility (or use `minicom` or any other serial terminal).
2. Identify the port by listing `/dev/` (often `ttyACM0`).
3. Run the command:

```bash
screen /dev/ttyACM0 115200
```

You should see your messages appear!

![[articles/fr/_resources/d50c96450533bb1c400e9639886e5d96_MD5.jpeg]]

And that’s it! You have successfully set up your IDE to program STM32 microcontrollers on Fedora 41 using VSCode.
