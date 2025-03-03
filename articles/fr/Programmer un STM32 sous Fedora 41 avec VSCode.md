---
title: Programmer un STM32 sous Fedora 41 avec VSCode
date: 2025-04-01
description: Mise en place de l'environement de développement du STM32F411RE sous Linux (Fedora 41) avec Visual Studio Code
cover: ""
tags:
  - STM32
  - Fedora
draft: true
---

## À propos

Dans cet article nous allons mettre en place environment pour pouvoir programmer des STM32 en C via [VSCode](https://code.visualstudio.com/) en intégrant les outils fourni par STMicroelectronics pour générer simplement les bases nécéssaire pour régirer un firmware.

## Prérequis


- L'éditeur de texte [VSCode](https://code.visualstudio.com/)
- Créer un compte sur le site de [STMicroelectronics](https://www.st.com)
- Télécharger [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html)
- Télécharger [STM32CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html)
- Télécharger [ST-MCU-FINDER-PC](https://www.st.com/en/development-tools/st-mcu-finder-pc.html)


##  Installation

> Attention a ne pas aller trop vite lors des instalaltions ! La majorité des soucis lié à l'installation proviennent d'une erreur lié au chemin d'installation des dépendances.

### Outils STM32

Dans un premier temps nosu allosn installer les outils propriétaire de STM afin de pouvoir intérargir simplement avec le microcontrolleur.

L'installation de STM32CubeMX ainsi que ST-MCU-FINDER-PC est simple, les logiciels d'installation graphiques permetent de savoir les endroits d'installations, il est important pour la suite de les noter (géneralement `/home/<utilisateur>/STM32CubeMX` et `/home/<utilisateur>/ST-MCU-FINDER-PC`)

Le script d'installation de STM32CubeCLT créé un dossier  `/opt/ST/STM32CubeCLT`

### VSCode

Après l'instalaltion de VSCode nous allons pouvoir configurer les outils.

1. Ouvrir la partie **\[Extensions\]**
2. Installer l'extension **\[STM32 VS Code Extension\]**

>Set here image of the extension

Maintenant que l'extension est intallée nous allons pouvoir la configurer en fournissant le chemin d'accès des logiciels STM32 précedemment installés.

1. Ouvrir la partie **\[Extensions\]**
2. Cliquer sur la roue dentée à coté de l'extension **\[STM32 VS Code Extension\]** et ouvrir les paramètres de l'extension
> Image de la roue dentée
3. Ajouter le chemin d'accès des différents exécutable ainsi que du dossier STM32CubeCLT

Le paramètrage des outils est maintenant effectué ! Nous allons pouvoir procéder avec la création de notre premier projet et vérifier que tout est bien installé correctement.

## Hello World

L'objectif va être d'envoyer un simple message par UART depluis notre STM32 ainsi que de faire clignoter la LED intérgré. 

>Cette partie du tutoriel est écrit pour le [STM32F411RE](https://www.st.com/en/evaluation-tools/nucleo-f411re.html) il se peut qu'en fonction de votre devboard précise le numéro des pins varie.

### Génération du code de base et utilisation de STM32CubeMX

STM32CubeMX est le logiciel propriétaire de STM permettant de configurer graphiquement notre microcontrolleur ainsi que de générer automatiquement le code nécéssaire à cette configuration. Dans le cas de projet plsu avancé comme seront présenté dans les article suivant, il est possible de configurer automatiquement des fonctionnalitées comme le SPI, UART, I2C, DMA, ... 

1. Ouvrir dans VSCode l'onglet **\[STM32 VS Code Extension\]** 
> Mettre image

2. Ouvrir **STM32CubeMX** grâce au bouton dédié
> Image

