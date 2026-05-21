# Clone de Flappy Bird sur ESP32

| | |
| --- | --- |
| `Author` | Gheorghiu Maria Isabela |

## Description

Ce projet consiste en la réalisation d'une version matérielle et miniature du célèbre jeu vidéo Flappy Bird. Le jeu est exécuté sur un microcontrôleur ESP32 et affiché en temps réel sur un petit écran OLED de 0.96 pouces. L'interaction avec le joueur se fait de manière très simple et intuitive via un seul bouton poussoir tactile : chaque pression sur le bouton permet à l'oiseau de "sauter" pour éviter les obstacles (les tuyaux) qui défilent de droite à gauche. Le système gère la physique du jeu (gravité, sauts), la détection des collisions, l'affichage graphique ainsi que le score du joueur.

## Motivation

J'ai choisi ce projet car la création d'un jeu vidéo rétro est l'une des manières les plus interactives et amusantes de se familiariser avec la programmation des microcontrôleurs. Le projet de Flappy Bird m'a permis de comprendre comment gérer les entrées utilisateur de manière réactive (interruptions / lecture de boutons) et comment contrôler un affichage graphique externe via le protocole de communication I2C, tout en gérant une logique de jeu fluide.

## Architecture

Le système est très compact et est structuré autour de trois sous-systèmes principaux : l'unité de traitement, le sous-système d'affichage et le sous-système d'entrée utilisateur.

L'unité centrale de contrôle, basée sur la carte de développement ESP32, agit comme le "cerveau" du projet. Elle exécute la boucle principale du jeu, met à jour les positions (l'oiseau et les obstacles) et détecte les conditions de fin de jeu (collisions).

Le sous-système d'affichage utilise un écran OLED de 0.96 pouces. Ce module communique avec l'ESP32 via le bus I2C (utilisant seulement 2 fils de données : SDA et SCK, plus l'alimentation). L'écran est responsable du rendu des graphismes (oiseau, tuyaux, score) en temps réel.

Le sous-système d'entrée est composé d'un bouton poussoir tactile (push button) connecté directement à un pin GPIO de l'ESP32. La lecture de l'état du bouton permet de déclencher le saut de l'oiseau. Les résistances de tirage (pull-up/pull-down) internes de l'ESP32 sont utilisées pour simplifier le circuit matériel.

L'ensemble du circuit est monté sur des plaques de prototypage (breadboards) et relié par des fils jumper mâle-mâle. L'alimentation est fournie directement par le port USB connecté à l'ordinateur.

### Block Diagram 
graph LR
    subgraph Alimentation
        USB[Câble USB / Ordinateur]
    end

    subgraph Entrée
        Bouton[Bouton Poussoir Tactile]
    end

    subgraph Traitement Central
        ESP32[Microcontrôleur ESP32]
    end

    subgraph Sortie
        OLED[Écran OLED 0.96"]
    end

    USB --> |Alimentation 5V / 3.3V| ESP32
    Bouton --> |Signal de saut - GPIO 4| ESP32
    ESP32 --> |Données I2C - SDA & SCK| OLED


### Components

| Device | Usage |
| --- | --- |
| Carte de développement ESP32 | Microcontrôleur principal, gère la logique du jeu |
| Écran OLED 0.96 pouces | Affichage du jeu vidéo (communication I2C) |
| Bouton poussoir tactile | Input du joueur pour faire sauter l'oiseau |
| Breadboards (x2) | Plaques pour le montage du circuit |
| Fils Jumper Mâle-Mâle (x6) | Connexion des modules à l'ESP32 |
| Câble USB (Type-C) | Alimentation et programmation de l'ESP32 |

### Libraries

| Library | Description | Usage |
| --- | --- | --- |
| Adafruit SSD1306 | Bibliothèque officielle Adafruit pour écrans OLED | Utilisée pour contrôler le matériel de l'écran |
| Adafruit GFX | Bibliothèque graphique de base | Utilisée pour dessiner des pixels, des formes géométriques et du texte (le score) |

### Schematic 
<img width="845" height="457" alt="Screenshot 2026-05-22 002913" src="https://github.com/user-attachments/assets/0a02e6a8-6aa1-43e5-95be-52e0e4ab5b31" />


## Log

### Week 6 - 12 May
- [Scrie aici ce ai făcut, ex: Achat des composants et premier test de l'ESP32]

### Week 7 - 19 May
- [Scrie aici ce ai făcut, ex: Connexion de l'écran OLED et test d'affichage]

### Week 20 - 26 May
- [Scrie aici ce ai făcut, ex: Programmation de la logique de Flappy Bird et intégration du bouton]

## Reference links

[Tutoriel ESP32 + OLED](https://randomnerdtutorials.com/esp32-ssd1306-oled-display-arduino-ide/)
