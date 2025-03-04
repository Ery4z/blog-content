---
title: Programmer un STM32 sous Fedora 41 avec VSCode
date: 2025-03-04
description: Mise en place de l’environnement de développement pour le STM32F411RE sous Linux (Fedora 41) avec Visual Studio Code
cover: ""
tags:
  - STM32
  - Fedora
draft: true
---

## À propos

Dans cet article, nous allons mettre en place un environnement pour programmer des STM32 en C via [VSCode](https://code.visualstudio.com/), en intégrant les outils fournis par STMicroelectronics afin de générer facilement les bases nécessaires pour développer un firmware.

## Prérequis

- L’éditeur [VSCode](https://code.visualstudio.com/)
- Un compte sur le site de [STMicroelectronics](https://www.st.com)
- [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html)
- [STM32CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html)
- [ST-MCU-FINDER-PC](https://www.st.com/en/development-tools/st-mcu-finder-pc.html)

## Installation

> **Attention** : Ne vous précipitez pas lors de l’installation ! La plupart des problèmes proviennent d’erreurs liées aux chemins d’installation des différentes dépendances.

### Outils STM32

Dans un premier temps, nous allons installer les outils propriétaires de ST afin de pouvoir interagir simplement avec le microcontrôleur.

- **STM32CubeMX** et **ST-MCU-FINDER-PC** s’installent via des installateurs graphiques. Notez bien le chemin d’installation (généralement `~/STM32CubeMX` et `~/ST-MCU-FINDER-PC`).
- **STM32CubeCLT** s’installe via un script qui va créer par défaut le dossier `/opt/ST/STM32CubeCLT`.

### VSCode

Après l’installation de VSCode, configurons les outils :

1. Ouvrez l’onglet **Extensions**.
2. Installez l’extension **STM32 VS Code Extension**.

![[565cda6a1352ebb74c1f97efc3d0f1c7_MD5.jpeg]]

Une fois l’extension installée, il faut la configurer en indiquant les chemins d’accès des différents outils STM32 précédemment installés :

1. Ouvrez de nouveau **Extensions**.
2. Cliquez sur la roue dentée à côté de **STM32 VS Code Extension** puis sélectionnez **Paramètres de l’extension** (Settings).
   ![[IMG_Programmer un STM32 sous Fedora 41 avec VSCode.png]]
3. Renseignez les chemins d’accès aux différents exécutables ainsi qu’au dossier `STM32CubeCLT`.

Le paramétrage des outils étant terminé, nous allons créer un premier projet pour vérifier que tout est correctement installé.

## Hello World

L’objectif est d’envoyer un message par UART depuis notre STM32 et de faire clignoter la LED intégrée.

> *Cette partie du tutoriel utilise le [STM32F411RE](https://www.st.com/en/evaluation-tools/nucleo-f411re.html). Selon votre carte de développement, les numéros de pins peuvent varier.*

### Génération du code de base et utilisation de STM32CubeMX

**STM32CubeMX** est le logiciel propriétaire de ST permettant de configurer graphiquement un microcontrôleur puis de générer automatiquement le code correspondant à cette configuration (GPIO, SPI, UART, I2C, DMA, etc.).

1. Ouvrez dans VSCode l’onglet **STM32 VS Code Extension**.  
   ![[articles/fr/_resources/85d1b2484bc351e1ddb9969595ccfcf6_MD5.jpeg]]

2. Lancez **STM32CubeMX** grâce au bouton dédié.  
   ![[articles/fr/_resources/fe988d8c065be00f56923fec8f9de010_MD5.jpeg]]

3. Créez un nouveau projet via le bouton indiqué.  
   ![[articles/fr/_resources/dad1056dd12d64d3da419120abf90026_MD5.jpeg]]

   - Sélectionnez la carte ou le microcontrôleur que vous utilisez.  
     ![[d97e0308efb265549dd2d49b6db77125_MD5.jpeg]]

   - Vérifiez que la GPIO pour la LED est bien définie et notez son “User Label”, ce sera utile dans le code.  
     ![[articles/fr/_resources/f33b7af6c74c024efb1924b979d1152b_MD5.jpeg]]

   - Vérifiez que l’UART est bien activé et configuré en **115200 Baud**.  
     ![[articles/fr/_resources/1b1634ca22f9d64a3e286ec4ebb61a0b_MD5.jpeg]]

4. Dans l’onglet **Project Manager**, choisissez le nom du projet et générez le code.  
   > **Attention** à bien sélectionner **CMake** comme toolchain !  
   ![[articles/fr/_resources/38e1bcc86ec027345ccf54802b507a1a_MD5.jpeg]]

5. Une fois le code généré, retournez dans VSCode pour **Importer le projet CMake**.  
   ![[articles/fr/_resources/6f0effcd4ef4d3a78261265f03e778d3_MD5.jpeg]]

6. Redémarrez VSCode si nécessaire et sélectionnez la configuration **Debug** lorsque CMake vous le propose.

L’IDE est prêt ! Passons maintenant à l’écriture de notre premier firmware.

### Premier firmware

Pour ce premier firmware, nous ferons clignoter la LED intégrée à la carte de développement et enverrons un message par UART contenant une variable.  
Vous trouverez le code complet sur [ce repository GitHub](https://github.com/Ery4z/Blog-STM32HelloWorld).

> **Attention** : Pensez à bien écrire votre code entre les balises de commentaires `USER CODE BEGIN` et `USER CODE END`. Sinon, lors d’une régénération via STM32CubeMX, votre code personnalisé risque d’être écrasé !

Seules deux parties doivent être modifiées pour atteindre notre objectif :

#### 1. Dans `main.c` (section **Includes**)

```c
/* Private includes ----------------------------------------------------------*/

/* USER CODE BEGIN Includes */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

/* USER CODE END Includes */
```

#### 2. Dans la fonction `main`

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

### Flash et debug

Compilez et flashez votre microcontrôleur en lançant le **debugger** dans VSCode :

![[articles/fr/_resources/03ad4d833cd46a328094ed00fc7ad68d_MD5.jpeg]]

### Visualiser l’UART

Pour observer les messages envoyés par l’UART :

1. Installez l’utilitaire `screen` (ou utilisez `minicom` ou tout autre terminal série).
2. Repérez le nom du port en listant le contenu de `/dev/` (souvent `ttyACM0`).
3. Lancez la commande :

```bash
screen /dev/ttyACM0 115200
```
Vous devriez voir défiler vos messages !

![[articles/fr/_resources/d50c96450533bb1c400e9639886e5d96_MD5.jpeg]]

Voilà ! Vous avez maintenant configuré votre environnement pour programmer des STM32 sous Fedora 41 avec VSCode.
