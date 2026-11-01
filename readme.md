# Pilotage de diapositives par gestes de la main

Un script Python qui suit ta main avec la webcam et la transforme en télécommande : un mouvement de poignet fait défiler les diapositives, et un second mode permet de déplacer le curseur de la souris avec l'index.

<!-- Ajouter ici un GIF de démonstration : ![démo](demo.gif) -->

## Fonctionnement

La détection repose sur le **Hand Landmarker de MediaPipe** (21 points par main) appliqué au flux de la webcam par défaut, via OpenCV. Les touches et le curseur sont pilotés avec PyAutoGUI. Une seule main est suivie.

Deux modes, qu'on bascule en **fermant le poing** (4 images consécutives, au plus un changement toutes les 0,8 s) :

| Mode | Geste | Action |
|---|---|---|
| **Swipe** (par défaut) | Mouvement horizontal rapide du poignet | Envoie la touche flèche gauche ou droite |
| **Pointeur** | Index levé, autres doigts repliés | Déplace le curseur de la souris |

- **Swipe** : le script mémorise les 8 dernières positions du poignet. Le geste est validé si le déplacement dépasse 8 % de la largeur de l'image en moins de 0,4 s. Un délai de 1,2 s est imposé entre deux swipes de même sens.
- **Pointeur** : la position du bout de l'index est reprise en miroir sur l'écran, avec un lissage (plus fort en cas de grand déplacement) et une petite zone morte pour éviter les tremblements. Ce mode ne clique pas.

## Installation

Prérequis : Python 3.10 ou 3.11 (TensorFlow 2.15 ne gère pas les versions plus récentes) et une webcam.

```bash
pip install mediapipe pyautogui tensorflow==2.15.0 opencv-python
```

Le modèle `hand_landmarker.task` doit se trouver à côté de `main.py` (il est fourni dans ce dépôt).

## Utilisation

```bash
python main.py
```

Ouvre ta présentation, mets-la au premier plan, puis fais tes gestes devant la caméra. Appuie sur **q** dans la fenêtre vidéo pour quitter.

## Réglages

Les seuils sont des constantes en haut de `main.py` :

| Constante | Valeur | Rôle |
|---|---|---|
| `SWIPE_DISTANCE` | 0,08 | Déplacement horizontal minimal du poignet |
| `MAX_TIME` | 0,4 s | Durée maximale d'un swipe |
| `COOLDOWN` | 1,2 s | Délai entre deux swipes de même sens |
| `NB_FRAMES_POING_MODE` | 4 | Images de poing fermé pour changer de mode |
| `LISSAGE_POINTEUR` | 0,35 | Lissage du curseur (0,6 si déplacement rapide) |
| `ZONE_MORTE_POINTEUR_PX` | 2 | Déplacement minimal pris en compte |

## À améliorer

- Les libellés affichés à l'écran pour le sens du swipe ne correspondent pas toujours à la touche envoyée.
- Le mode pointeur ne gère pas le clic.
- Les paramètres ne sont pas modifiables sans éditer le code.
- Pas de tests automatisés.
