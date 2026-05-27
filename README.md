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
#include <Wire.h>
#include "SSD1306Wire.h" //librarie instalata pt ecran
#include "images.h" 
#include "fontovi.h"

SSD1306Wire display(0x3c, 21, 22); //aici se initializeaza ecranul cu porturile in care am pus firele 

float zidx[4];
int prazan[4];
int space = 32;
int throughlength = 30;

int score = 0;
int stis = 0;
float fx = 30.00;
float fy = 22.00;
int direction = 0;
unsigned long timp = 0;

int joc = 0;
int frame = 0;
int start = 0;

void setup() {
  Serial.begin(115200);
  pinMode(2, OUTPUT);
  pinMode(4, INPUT_PULLUP);

  display.init();
  display.flipScreenVertically();
  display.setFont(ArialMT_Plain_10);

  for (int i = 0; i < 4; i++) {
    zidx[i] = 128 + ((i + 1) * space);
    prazan[i] = random(8, 32);
  }
}

void loop() {
  display.clear();

  if (joc == 0) {
    display.drawXbm(0, 0, 128, 64, pozadina);
    display.drawXbm(20, 32, 14, 9, ptica);
    display.setFont(ArialMT_Plain_10);
    display.drawString(0, 44, "Press to start");
    if (digitalRead(4) == 0)
      joc = 1;
  }

  if (joc == 1) {
    display.setFont(ArialMT_Plain_10);
    display.drawString(3, 0, String(score));

    if (digitalRead(4) == 0) {
      if (stis == 0) {
        timp = millis();
        direction = 1;
        start = 1;
        stis = 1;
      }
    } else {
      stis = 0;
    }

    // Draw walls
    for (int j = 0; j < 4; j++) {
      display.setColor(WHITE);
      display.fillRect(zidx[j], 0, 6, 64);
      display.setColor(BLACK);
      display.fillRect(zidx[j], prazan[j], 6, throughlength);
    }

    // Draw bird
    display.setColor(WHITE);
    display.drawXbm(fx, fy, 14, 9, ptica);

    // Move walls
    for (int j = 0; j < 4; j++) {
      zidx[j] = zidx[j] - 0.01;
      if (zidx[j] < -7) {
        score = score + 1;
        prazan[j] = random(8, 32);
        zidx[j] = 128;
      }
    }

    // Gravity
    if ((timp + 185) < millis())
      direction = 0;

    if ((start + 40) < millis())
      start = 0;

    if (direction == 0)
      fy = fy + 0.01;
    else
      fy = fy - 0.03;

    // Out of bounds — game over
    if (fy > 63 || fy < 0) {
      resetGame();
    }

    // Collision detection
    for (int m = 0; m < 4; m++) {
      if (zidx[m] <= fx + 7 && fx + 7 <= zidx[m] + 6) {
        if (fy < prazan[m] || fy + 8 > prazan[m] + throughlength) {
          resetGame();
        }
      }
    }

    display.drawRect(0, 0, 128, 64);
  }

  display.display();
}

void resetGame() {
  joc = 0;
  fy = 22;
  score = 0;
  delay(500);
  for (int i = 0; i < 4; i++) {
    zidx[i] = 128 + ((i + 1) * space);
    prazan[i] = random(8, 32);
  }
}

## Reference links

[Tutoriel ESP32 + OLED](https://randomnerdtutorials.com/esp32-ssd1306-oled-display-arduino-ide/)
