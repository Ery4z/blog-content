---
title: 'Programmer un STM32 sous Fedora 41 avec VSCode'
date: '2025-03-03'
description: 'Mise en place de l'environement de développement du STM32F411RE sous Linux (Fedora 41) avec Visual Studio Code
cover: ''
tags: ['STM32','Fedora']
---

## À propos

Dans cet article nous allons mettre en place environment pour pouvoir programmer des STM32 en C via [VSCode](https://code.visualstudio.com/) en intégrant les outils fourni par STMicroelectronics pour générer simplement les bases nécéssaire poru régirer un firmware.

## Prérequis


- L'éditeur de texte [VSCode](https://code.visualstudio.com/)
- Créer un compte sur le site de [STMicroelectronics](https://www.st.com)
- Télécharger [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html)
- Télécharger [STM32CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html)
- Télécharger [ST-MCU-FINDER-PC](https://www.st.com/en/development-tools/st-mcu-finder-pc.html)


##  Installation

> Attention a ne pas aller trop vite lors des instalaltions ! La majorité des soucis lié à l'installation proviennent d'une erreur lié au chemin d'installation des dépendances.

### Outils STM'32

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

Le paramètrage des outils est maintenant effectué ! Nous allons pouvori procéder avec la création de notre premier projet et vérifier que tout est bien installé correctement.

4. 

## Hello World