# Mouse Training - Entraînement Spatial

Jeu d'entraînement au maniement de la souris d'ordinateur, conçu pour les enfants. Thème futuriste / science-fiction avec des sphères ennemies à éliminer en cliquant dessus.

---

## Manuel Utilisateur

### Lancement du jeu

Ouvrir le fichier `index.html` dans un navigateur web (Chrome, Firefox, Edge, Safari). Aucune installation requise.

### Menu principal

Au lancement, deux modes s'affichent :

- **Entrainement** : choisir un niveau et s'entraîner librement
- **Challenge** : enchaîner les 13 niveaux d'affilée

### Contrôles

| Action | Contrôle |
|--------|----------|
| Éliminer une sphère | Clic gauche sur la sphère |
| Perte d'une vie | Clic gauche en dehors de toute sphère |
| Interdire le menu contextuel | Clic droit désactivé pendant le jeu |

### Interface de jeu

- **Haut à gauche** : nom du niveau en cours
- **Haut au centre** : chronomètre (dixièmes de secondes)
- **Haut à droite** : 3 cœurs représentant les vies restantes
- **Centre** : zone de jeu contenant les sphères ennemies

### Compte à rebours

Avant chaque niveau, un compte à rebours géant s'affiche : **3**, **2**, **1**, **GO !**. Le jeu démarre uniquement lorsque le compte à rebours est terminé.

### Sphères ennemies

Les sphères sont des ennemis représentés avec des visages grimacants. Chaque sphère possède :
- Une **couleur** aléatoire parmi 8 dégradés (rose, violet, cyan, vert, orange, magenta, doré, bleu)
- Un **visage** aléatoire parmi 5 expressions (furieux, diabolique, rusé, sourire evil, choqué)
- Un **effet de glow** lumineux et un reflet

### Mode Entrainement - Niveaux 1 à 13

#### Niveaux 1 à 3 : sphères immobiles

5 sphères apparaissent simultanément sur l'écran. Il faut cliquer sur chacune pour les éliminer.

| Niveau | Taille des sphères |
|--------|--------------------|
| 1 | Grande |
| 2 | Moyenne |
| 3 | Petite |

#### Niveaux 4 à 12 : sphères mobiles

Une seule sphère apparaît à la fois. Dès qu'elle est éliminée, une nouvelle sphère apparaît. 5 sphères au total par niveau. Les sphères se déplacent et rebondissent sur les bords de l'écran.

| Niveau | Taille | Vitesse |
|--------|--------|---------|
| 4 | Grande | Lente |
| 5 | Moyenne | Lente |
| 6 | Petite | Lente |
| 7 | Grande | Moyenne |
| 8 | Moyenne | Moyenne |
| 9 | Petite | Moyenne |
| 10 | Grande | Rapide |
| 11 | Moyenne | Rapide |
| 12 | Petite | Rapide |

#### Niveau 13 : aléatoire total

Comme les niveaux 4-12 (1 sphère à la fois, 5 au total), mais la taille et la vitesse sont aléatoires pour chaque sphère.

### Conditions de victoire et défaite

**Victoire** : éliminer les 5 sphères du niveau. Un message de félicitations s'affiche avec le temps réalisé et une animation de confetti.

**Défaite** : perdre les 3 vies (3 clics en dehors des sphères). Un message d'encouragement affiche le nombre de sphères éliminées.

### Mode Challenge

Le mode Challenge enchaîne les 13 niveaux dans l'ordre :

- Un compte à rebours précède chaque niveau
- Un message de félicitations apparaît entre chaque niveau avec le temps réalisé
- En cas de défaite, deux options : **Reprendre au niveau 1** ou **Accueil**
- Après le niveau 13, un **super méga message de félicitations** s'affiche avec :
  - Un récapitulatif de chaque temps par niveau
  - Le temps total cumulé
  - Une grande animation de confetti
  - Les options **Recommencer** ou **Accueil**

---

## Spécifications Techniques

### Architecture du projet

Le projet est constitué d'un **fichier unique** `index.html` contenant l'intégralité du code (HTML, CSS et JavaScript). Cette approche mono-fichier garantit une portabilité maximale : aucun serveur web, aucune dépendance externe, aucune étape d'installation.

```
mouse-training/
├── index.html    ← fichier unique contenant tout le projet
├── prompt.txt    ← description initiale du projet
└── README.md     ← ce fichier
```

### Stack technique

| Technologie | Utilisation |
|-------------|-------------|
| HTML5 | Structure des écrans et conteneurs |
| CSS3 | Mise en page, thèmes visuels, animations, dégradés |
| JavaScript (ES6+) | Logique de jeu, moteur de rendu, gestion d'état |
| SVG | Sphères ennemies avec visages (inline, générés dynamiquement) |
| Canvas 2D | Animation des confetti / paillettes |
| Web Audio API | Sons synthétisés (clics, éliminations, victoire, défaite) |

### Architecture du code

Le code JavaScript est organisé en modules logiques au sein d'un unique bloc `<script>` :

#### 1. Configuration (`LEVELS`, `SIZES`, `SPEEDS`)

Tableau constant définissant les 13 niveaux avec leurs paramètres :

```javascript
LEVELS = [
  {id, spheres, size, speed, movement, label}
]
// sizes : {grande: 90px, moyenne: 62px, petite: 42px}
// speeds : {1: 1.3px/frame, 2: 2.8px/frame, 3: 4.8px/frame}
```

#### 2. Système audio

Fonctions de synthèse sonore via `AudioContext` :
- `playClick()` : son de clic menu
- `playEliminate()` : son d'élimination de sphère (ascending sweep)
- `playLoseLife()` : son de perte de vie (descending sawtooth)
- `playWin()` : mélodie de victoire (4 notes ascendantes C-E-G-C)
- `playLose()` : mélodie de défaite (4 notes descendantes)

#### 3. Gestion d'état (`state`)

Objet central contenant l'intégralité de l'état du jeu :

```javascript
state = {
  mode,              // 'training' | 'challenge'
  currentLevel,      // 1 à 13
  lives,             // 0 à 3
  timerStart,        // timestamp performance.now()
  timerElapsed,      // secondes écoulées
  spheres[],         // tableau des sphères actives
  spheresEliminated, // compteur
  running,           // true quand le jeu est actif
  countdownActive,   // true pendant le compte à rebours
  challengeTimes[],  // temps par niveau en mode challenge
  animFrameId        // ID de la frame d'animation
}
```

#### 4. Génération SVG dynamique

Les sphères sont des éléments SVG générés dynamiquement avec :

- **8 dégradés radiaux** aléatoires pour le corps de la sphère
- **5 types de visages** paramétrés par la taille (fonctions lambda) :
  - Furieux (sourcils froncés, bouche en grinçant)
  - Diabolique (yeux jaunes, bouche avec dents)
  - Rusé (yeux en amande, petit sourire)
  - Sourire evil (yeux violets, grinçant avec dents)
  - Choqué (grands yeux ronds, bouche ouverte)
- **Filtre SVG** `feGaussianBlur` pour l'effet glow
- **Reflet** radial blanc semi-transparent

Chaque sphère reçoit un `radialGradient` unique identifié par un ID aléatoire pour éviter les conflits.

#### 5. Moteur de jeu (`gameLoop`)

Boucle principale basée sur `requestAnimationFrame` :

```
gameLoop()
  ├─ Calcul du delta-time (dt) normalisé à 60fps
  ├─ Mise à jour du chronomètre
  ├─ Déplacement de chaque sphère (position + vélocité)
  ├─ Détection de collision avec les bords (rebond)
  ├─ Mise à jour des positions DOM
  └─ Prochaine frame
```

Le mouvement utilise un vecteur direction `(dx, dy)` multiplié par `dt` pour être indépendant du framerate. Le rebond inverse la composante de vélocité correspondante lorsque la sphère touche un bord.

#### 6. Détection des clics

Deux niveaux de détection :

- **Sur la sphère** : événement `click` sur l'élément `.sphere` → élimination
- **Sur la zone de jeu** : événement `click` sur `document` avec filtrage via `e.target.closest('.sphere')` → perte de vie

L'animation d'élimination utilise une classe CSS `.eliminating` avec `@keyframes` (scale up puis fade out sur 350ms).

#### 7. Gestion des écrans

Navigation entre écrans via `showScreen(id)` qui bascule la classe `.active` :

```
menu-screen → level-screen → game-screen → win-modal / lose-modal
                    ↑                        ↓
                    └────────────────────────┘
```

#### 8. Système de confetti

Animation Canvas 2D avec 150 particules :
- Rectangle coloré (7 couleurs aléatoires)
- Position, vélocité, rotation, rotation angulaire, durée de vie
- Gravité (vy += 0.04)
- Fade out progressif (life -= 0.003)
- Nettoyage automatique des particules hors écran

### Gestion du responsive

- Tailles de police responsive via `clamp()` (ex : `clamp(2rem, 6vw, 4rem)`)
- Largeurs de boutons avec `min()` (ex : `min(340px, 85vw)`)
- Grille de niveaux avec `repeat(auto-fit, minmax(110px, 1fr))`
- Média query `@media(max-width:600px)` pour adapter le HUD et les modales
- Sphères positionnées en `px` absolus avec calcul dynamique des limites

### Manipulation de la souris

| Élément | Curseur | Effet au survol |
|---------|---------|-----------------|
| Sphère | `pointer` | Agrandissement (`scale(1.1)`) |
| Bouton | `pointer` | Glow + légère augmentation |
| Zone de jeu | `default` | Aucun |
| Clic droit | Bloqué | `oncontextmenu="return false"` |

### Performance

- Pas de dépendances externes (framework, bibliothèque)
- Rendu DOM natif (pas de Canvas pour les sphères, interaction directe)
- `requestAnimationFrame` pour la synchronisation visuelle
- Delta-time frame-independent pour des mouvements fluides
- Nettoyage automatique des éléments DOM éliminés (setTimeout 400ms)
- Annulation propre de `requestAnimationFrame` lors des transitions
