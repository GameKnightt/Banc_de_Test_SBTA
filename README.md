# Banc de Test SBTA - Documentation

![frame_0_delay-0 01s](https://github.com/user-attachments/assets/4d8447a3-5de0-4f55-9d1a-c7518877de53)

![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)
![PlatformIO](https://img.shields.io/badge/plateforme-PlatformIO-orange.svg)
![Arduino](https://img.shields.io/badge/framework-Arduino-00979D.svg)

## Présentation

Le banc de test SBTA (Socket Bit and Tray for Automation) est un système automatisé conçu pour tester les sockets d'un SBTA. Ce système utilise un ensemble de moteurs à axes contrôlés par CAN bus (CANOpen) pour positionner précisément un outil de test sur différents sockets et effectuer des séquences de test automatisées.

## Caractéristiques principales

- Gestion de 3 axes motorisés (X, Y, Z) via CANOpen,
- Test individuel des sockets jusqu'à 4 dans la même séquence,
- Interface utilisateur avec boutons de sélection, indicateurs LED et IHM Node-Red,
- Système de machine à états pour une exécution séquentielle des tests,
- Validation des mouvements grâce à la lecture des trames CANOpen,
- Communication avec Ethernet Shield pour capteurs de chaque socket de SBTA.

## Prérequis matériels

- Arduino Mega 2560,
- Shield CAN Bus (MCP2515, 16Mhz),
- Ethernet Shield,
- 3 moteurs pas à pas IGUS avec drivers IGUS D1 compatibles CANopen,
- Capteurs de référencemnent (procédure de Homing) pour chaque axe,
- Capteur de validation de prise de douille/embout,
- Interface utilisateur avec:
    - 4 boutons + LEDs pour sélection des sockets
    - Boutons START et STOP
    - LEDs de statut (vert/rouge)
    - IHM KEP

## Structure du projet

```
in105-test-bench-sbta/
├── src/                      # Code source principal
│   └── AxisDriver/           # Contrôle des moteurs et de la machine
│       ├── main.cpp          # Point d'entrée du programme
│       ├── AXIS_DRIVER.h/cpp # Contrôle des axes
│       ├── MACHINE.h/cpp     # Gestion de la machine et séquence de tests
│       ├── Constantes.h      # Définitions des pins et constantes
│       ├── CANFrame.h/cpp    # Trames CAN pour communication avec drivers
│       ├── BufferConverter.h/cpp # Utilitaires de conversion de données
│       └── Ethernet_Shield.h # Configuration et accès Ethernet
├── include/                  # Fichiers d'en-tête additionnels
├── lib/                      # Bibliothèques spécifiques au projet
└── platformio.ini            # Configuration PlatformIO
```

*Note : Ce programme utilise la Programmation Orientée Objet (POO).*

## Architecture système

### Schéma d'architecture

Cette section présente le schéma d'architecture global du banc de test SBTA, montrant l'interaction entre les différents composants matériels et logiciels.

![Schéma_Architecture_élec_V3](https://github.com/user-attachments/assets/cd189ab6-6f23-4923-aea2-50d275230598)

### Schéma électrique

Le schéma électrique détaille les connexions entre l'Arduino Mega, les shields, les capteurs et les moteurs.

<img width="2339" height="1653" alt="Banc_Test_SBTA_Schematic-1" src="https://github.com/user-attachments/assets/cfad82e2-caab-4507-bd55-b7c04f4abde8" />

## Protocole CANopen

### Principe de fonctionnement

Le projet utilise le protocole CANopen pour communiquer avec les drivers des moteurs. CANopen est une couche d'application basée sur le bus CAN, particulièrement adaptée pour les systèmes d'automatisation industrielle.

Les principales caractéristiques de CANopen utilisées dans ce projet sont:
- Communication standardisée avec les drivers de moteurs
- Dictionnaire d'objets pour accéder aux paramètres et contrôles des moteurs
- PDO (Process Data Objects) pour l'échange de données en temps réel
- SDO (Service Data Objects) pour la configuration des paramètres
- Gestion des états des nœuds (démarrage, arrêt, etc.)

<img width="347" height="179" alt="Capture_d_écran_2023-10-03_132828" src="https://github.com/user-attachments/assets/5283d0cf-0b72-4cde-910d-7657c9cf070a" />

### Implémentation dans le projet

Dans notre système, le protocole CANopen est utilisé pour:
1. Initialiser les drivers des moteurs
2. Configurer les paramètres de mouvement (vitesse, accélération)
3. Commander les déplacements des axes
4. Lire les positions actuelles et l'état des moteurs
5. Gérer les situations d'erreur

La classe `CANFrame` encapsule la création et l'interprétation des trames CANopen pour simplifier la communication avec les drivers.

*Note : L'identification individuelle de chaque driver moteur est obtenue grâce à un ID indépendant (1,2,3 respectivement X,Y,Z) auquel vient s'ajouter l'identificateur CAN permettant de spécifier la transmission ou la réception de trame. Dans le cas de ce projet l'échange de trame se réalisera toujours par l'identificateur CAN égal à 600. La résultante de cela s'appelle le "COB-ID" qui est donc le CAN identifier + Node ID (respectivement 601,602,603).*

## Screw Shield Arduino

### Conception et utilité

Le projet intègre une carte "Screw Shield" spécialement conçue pour l'Arduino Mega 2560, qui présente plusieurs avantages par rapport aux connexions standard:

- Remplacement des connexions par broches fragiles par des borniers à vis
- Augmentation de la fiabilité des connexions dans un environnement industriel
- Réduction des risques de déconnexion accidentelle
- Possibilité de connecter des fils de section plus importante
- Organisation claire des connexions avec étiquetage
- Protection contre les inversions de polarité et les courts-circuits

### Fabrication et installation

La carte Screw Shield a été conçue sur mesure pour ce projet et fabriquée selon les spécifications suivantes:
- PCB double face avec masque de soudure
- Borniers à vis pour toutes les E/S numériques et analogiques
- Connecteurs de puissance séparés pour l'alimentation externe
- Zones de connexion pour les shields empilables (CAN, Ethernet)

<img width="1416" height="663" alt="Picture7" src="https://github.com/user-attachments/assets/b8922e09-b939-4ab6-8feb-0788d8e9864b" />

<img width="1294" height="628" alt="Picture4" src="https://github.com/user-attachments/assets/b2571a65-dcad-4c77-861d-2c6fddd1df53" />

## Images du projet

Cette section présente des images du banc de test SBTA complet.

### Vue globale du banc de test

<img width="306" height="479" alt="Banc_de_test" src="https://github.com/user-attachments/assets/8070ba95-0845-4407-947d-1faa53126aab" />

![Picture2](https://github.com/user-attachments/assets/81af36e5-1fd5-4ae1-829f-cd01b1184865)

![Picture1](https://github.com/user-attachments/assets/97936290-061e-4026-9a52-bb8457f676fc)

## Installation

1. Installer [PlatformIO](https://platformio.org/) (extension VSCode recommandée)
2. Cloner ce dépôt
3. Ouvrir le projet dans PlatformIO
4. Connecter l'Arduino Mega au PC
5. Compiler et téléverser le programme avec PlatformIO

## Configuration

Le projet utilise les dépendances suivantes (configurées dans platformio.ini):
- Ethernet 2.0.2
- CAN_BUS_Shield 2.3.3
- ArduinoModbus 1.0.9
- ArduinoRS485 1.0.5

## Utilisation

### États du système

Le programme utilise une machine à états avec les phases suivantes:
1. **IDLE**: Attente de sélection des sockets à tester (LED rouge allumée)
2. **READY**: Sockets sélectionnés, en attente du démarrage du test (LED rouge allumée)
3. **TESTING**: Test en cours (LEDs orange: rouge+vert allumées)
4. **COMPLETED**: Tests terminés avec succès (LED verte allumée)

### Opérations de base

1. **Sélection de sockets**:
     - Appuyer sur les boutons correspondants aux sockets à tester (1-4)
     - Les LEDs correspondantes s'allument pour indiquer la sélection

2. **Démarrage du test**:
     - Appuyer sur le bouton START pour lancer les tests
     - Le système testera séquentiellement chaque socket sélectionné

3. **Interruption**:
     - Le bouton STOP interrompt la séquence de test à tout moment
     - Le système revient à l'état IDLE

### Séquence de test typique pour un socket

1. Positionnement au-dessus du socket
2. Descente de l'axe Z
3. Activation de l'outil pour embectage de la douille/embout
4. Saisie de la douille (vérifiée par capteur)
5. Déplacement vers position de dépose
7. Retour à la position initiale
8. Passage au socket suivant si applicable

## Dépannage

### Problèmes courants

- **Erreur d'initialisation CAN**: Vérifier les connexions du shield CAN
- **Erreur de communication Ethernet**: Vérifier les paramètres réseau et connexions
- **Défaut de détection de douille**: Vérifier le capteur et sa position
- **Mouvement non terminé**: Vérifier les fins de course et capteurs de référence

### LED de diagnostic

- **Rouge fixe**: Système en attente
- **Rouge+Vert (orange)**: Test en cours
- **Vert fixe**: Test terminé avec succès
- **Rouge clignotant**: Erreur système (voir messages série)

## Développement

Pour modifier le comportement du système:
- Les séquences de test sont définies dans `MACHINE.cpp` (méthode `TestSocket`)
- Les paramètres des moteurs peuvent être ajustés dans `AXIS_DRIVER.cpp`
- Les assignations de pins se trouvent dans `Constantes.h`

## Licence et crédits

© Desoutter Industrial Tools 2025 - Tous droits réservés

Développé par Alexy LESAULNIER.

![frame_0_delay-0 01s](https://github.com/user-attachments/assets/4d8447a3-5de0-4f55-9d1a-c7518877de53)

---

*Documentation créée le 10/03/2025*
