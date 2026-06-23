# Cours Unreal Engine 5.6 — Créer un menu de niveaux et un ennemi IA très simple

> Objectif : apprendre à créer un **menu principal / menu de niveaux** en Blueprint avec UMG, puis un **ennemi IA basique** capable de poursuivre le joueur dans Unreal Engine 5.6.

---

## Sommaire

1. [Pré-requis](#1-pré-requis)
2. [Organisation du projet](#2-organisation-du-projet)
3. [Partie A — Créer un Level Menu](#3-partie-a--créer-un-level-menu)
4. [Partie B — Créer un ennemi IA très simple](#4-partie-b--créer-un-ennemi-ia-très-simple)
5. [Tests et problèmes fréquents](#5-tests-et-problèmes-fréquents)
6. [Améliorations possibles](#6-améliorations-possibles)
7. [Sources officielles utiles](#7-sources-officielles-utiles)

---

## 1. Pré-requis

Ce cours part du principe que tu utilises :

- **Unreal Engine 5.6** ;
- un projet **Third Person** en Blueprint ;
- les Blueprints, sans C++ ;
- un joueur déjà contrôlable dans un niveau de jeu.

Tu dois connaître au minimum :

- créer un Blueprint ;
- ouvrir le Content Browser / Content Drawer ;
- placer un Actor dans une scène ;
- utiliser les nodes de base : `Event BeginPlay`, `Create Widget`, `Add to Viewport`, `Open Level`, `Cast`, `Branch`.

---

## 2. Organisation du projet

Avant de commencer, crée une structure propre dans `Content/` :

```text
Content/
├── AI/
│   ├── BP_Enemy.uasset
│   ├── BP_EnemyAIController.uasset
│   ├── BT_Enemy.uasset
│   └── BB_Enemy.uasset
│
├── UI/
│   ├── WBP_MainMenu.uasset
│   └── WBP_LevelButton.uasset     # optionnel
│
├── Maps/
│   ├── L_MainMenu.umap
│   ├── L_Level01.umap
│   └── L_Level02.umap
│
└── Blueprints/
    └── BP_MenuGameMode.uasset
```

Cette organisation n'est pas obligatoire, mais elle évite de mélanger interface, IA et niveaux.

---

# 3. Partie A — Créer un Level Menu

## 3.1. Principe d'un Level Menu

Un **Level Menu** est une interface permettant de charger une carte du jeu.

Exemple :

```text
Menu principal
├── Jouer Niveau 1
├── Jouer Niveau 2
├── Options
└── Quitter
```

Dans Unreal, on le fait généralement avec :

- un niveau vide ou décoratif : `L_MainMenu` ;
- un Widget Blueprint : `WBP_MainMenu` ;
- un Blueprint qui affiche le widget au lancement du niveau ;
- des boutons qui utilisent `Open Level` pour charger une map.

---

## 3.2. Créer les niveaux

Dans `Content/Maps/`, crée trois niveaux :

```text
L_MainMenu
L_Level01
L_Level02
```

### Étapes

1. Va dans `File > New Level`.
2. Choisis un niveau vide ou Basic.
3. Sauvegarde-le dans `Content/Maps/`.
4. Répète l'opération pour chaque niveau.

Pour éviter les erreurs, utilise des noms simples, sans espaces :

```text
Correct : L_Level01
À éviter : Mon Niveau 1
```

---

## 3.3. Créer le Widget Blueprint du menu

Dans `Content/UI/` :

1. Clique droit dans le Content Browser.
2. Va dans `User Interface > Widget Blueprint`.
3. Choisis `User Widget`.
4. Nomme-le :

```text
WBP_MainMenu
```

Ouvre `WBP_MainMenu`.

---

## 3.4. Construire l'interface du menu

Dans le Designer de `WBP_MainMenu`, ajoute :

- un `Canvas Panel` comme racine ;
- un `Vertical Box` centré ;
- un `TextBlock` pour le titre ;
- plusieurs `Button` ;
- un `TextBlock` dans chaque bouton.

Structure conseillée :

```text
Canvas Panel
└── Vertical Box
    ├── TextBlock_Title       "Mon Jeu"
    ├── Button_Level01
    │   └── TextBlock         "Niveau 1"
    ├── Button_Level02
    │   └── TextBlock         "Niveau 2"
    └── Button_Quit
        └── TextBlock         "Quitter"
```

### Réglages utiles

Sélectionne le `Vertical Box` :

- `Anchors` : centre de l'écran ;
- `Alignment` : `0.5, 0.5` ;
- position : `0, 0` ;
- taille : environ `400 x 300`.

Les **anchors** permettent à l'interface de rester bien placée quand la résolution change.

---

## 3.5. Connecter les boutons aux niveaux

Dans `WBP_MainMenu`, sélectionne `Button_Level01`.

Dans le panneau `Details`, descends jusqu'à `Events`, puis clique sur `+ OnClicked`.

Dans le Graph, crée cette logique :

```text
OnClicked(Button_Level01)
→ Remove from Parent
→ Open Level by Name
    Level Name = L_Level01
```

Pour le niveau 2 :

```text
OnClicked(Button_Level02)
→ Remove from Parent
→ Open Level by Name
    Level Name = L_Level02
```

Pour quitter le jeu :

```text
OnClicked(Button_Quit)
→ Quit Game
```

### Remarque sur `Open Level`

`Open Level` charge une nouvelle map. C'est simple et parfait pour un menu principal. Pour un gros jeu, on peut aussi utiliser du Level Streaming, mais ce n'est pas nécessaire ici.

---

## 3.6. Afficher le menu au lancement

Le widget ne s'affiche pas tout seul. Il faut le créer au début de `L_MainMenu`.

### Méthode simple : Level Blueprint

Ouvre `L_MainMenu`, puis va dans :

```text
Blueprints > Open Level Blueprint
```

Dans le Level Blueprint :

```text
Event BeginPlay
→ Get Player Controller
→ Create Widget
    Class = WBP_MainMenu
    Owning Player = Player Controller
→ Add to Viewport
→ Set Input Mode UI Only
→ Set Show Mouse Cursor = true
```

Schéma :

```text
Event BeginPlay
    ↓
Get Player Controller
    ↓
Create Widget WBP_MainMenu
    ↓
Add to Viewport
    ↓
Set Input Mode UI Only
    ↓
Show Mouse Cursor = true
```

Cela permet au joueur de cliquer dans le menu avec la souris.

---

## 3.7. Revenir au mode jeu après avoir chargé un niveau

Quand tu charges `L_Level01`, tu ne veux plus être en mode UI Only. Dans le Level Blueprint de chaque niveau jouable, ajoute :

```text
Event BeginPlay
→ Get Player Controller
→ Set Input Mode Game Only
→ Set Show Mouse Cursor = false
```

Ainsi :

- dans le menu : souris visible, clics UI ;
- dans le jeu : souris cachée, contrôle du personnage.

---

## 3.8. Définir le niveau de démarrage du projet

Pour que le jeu commence sur le menu :

1. Va dans `Edit > Project Settings`.
2. Cherche `Maps & Modes`.
3. Dans `Default Maps`, mets :

```text
Game Default Map = L_MainMenu
Editor Startup Map = L_MainMenu   # optionnel
```

---

## 3.9. S'assurer que les niveaux sont inclus au packaging

Si tu packages le jeu et qu'un niveau ne se charge pas, vérifie que les maps sont bien incluses.

Va dans :

```text
Edit > Project Settings > Packaging
```

Puis ajoute tes maps dans la liste des maps à cuire si nécessaire :

```text
Content/Maps/L_MainMenu
Content/Maps/L_Level01
Content/Maps/L_Level02
```

---

# 4. Partie B — Créer un ennemi IA très simple

## 4.1. Objectif de l'ennemi

On va créer un ennemi qui :

1. apparaît dans le niveau ;
2. détecte le joueur ;
3. le poursuit ;
4. s'arrête ou continue selon une logique simple.

Il existe plusieurs façons de faire une IA. Ici, on va utiliser une méthode simple en Blueprint :

- un `Character` ennemi ;
- un `AIController` ;
- un `NavMeshBoundsVolume` ;
- un node `AI Move To`.

Ensuite, on verra une version un peu plus propre avec Behavior Tree.

---

## 4.2. Créer le Blueprint de l'ennemi

Dans `Content/AI/` :

1. Clique droit.
2. `Blueprint Class`.
3. Choisis `Character`.
4. Nomme-le :

```text
BP_Enemy
```

Ouvre `BP_Enemy`.

---

## 4.3. Donner une apparence à l'ennemi

Dans `BP_Enemy` :

1. Sélectionne le composant `Mesh`.
2. Assigne un Skeletal Mesh, par exemple le mannequin Unreal.
3. Ajuste la position du mesh si besoin :

```text
Location Z = -90 environ
Rotation Z = -90 ou 0 selon le mesh
```

Réglages conseillés du Character Movement :

```text
Max Walk Speed = 250 à 400
Orient Rotation to Movement = true
Use Controller Desired Rotation = false
```

Dans la classe `BP_Enemy`, désactive éventuellement la rotation directe du controller :

```text
Use Controller Rotation Yaw = false
```

---

## 4.4. Créer un AIController

Dans `Content/AI/` :

1. Clique droit.
2. `Blueprint Class`.
3. Cherche `AIController`.
4. Nomme-le :

```text
BP_EnemyAIController
```

Ensuite, retourne dans `BP_Enemy`.

Dans les détails de la classe :

```text
AI Controller Class = BP_EnemyAIController
Auto Possess AI = Placed in World or Spawned
```

Ces deux réglages sont très importants : sans eux, ton ennemi risque de ne pas bouger.

---

## 4.5. Ajouter un NavMesh dans le niveau

Pour que l'IA puisse se déplacer, Unreal doit savoir où elle a le droit de marcher.

Dans ton niveau de jeu :

1. Va dans `Place Actors`.
2. Cherche :

```text
NavMeshBoundsVolume
```

3. Place-le dans la scène.
4. Agrandis-le pour couvrir toute la zone jouable.
5. Appuie sur `P` pour afficher la navigation.

Si tout va bien, le sol devient vert.

```text
Vert = zone navigable par l'IA
Pas vert = l'IA ne pourra probablement pas y aller
```

---

## 4.6. IA très simple avec Event Tick

Cette première version est volontairement simple. Elle n'est pas la plus optimisée, mais elle permet de comprendre le principe.

Dans `BP_EnemyAIController`, crée cette logique :

```text
Event Tick
→ Get Player Character
→ AI Move To
    Pawn = Get Controlled Pawn
    Target Actor = Player Character
    Acceptance Radius = 100
```

Schéma :

```text
Event Tick
    ↓
Get Player Character
    ↓
Get Controlled Pawn
    ↓
AI Move To Player
```

Résultat : l'ennemi suit constamment le joueur.

### Limite de cette méthode

`Event Tick` s'exécute à chaque frame. Pour une IA unique, ça passe. Pour beaucoup d'ennemis, ce n'est pas idéal.

Une version un peu meilleure consiste à utiliser un Timer.

---

## 4.7. Version améliorée avec Timer

Dans `BP_EnemyAIController` :

```text
Event BeginPlay
→ Set Timer by Event
    Time = 0.3
    Looping = true
→ Custom Event: MoveToPlayer
```

Puis :

```text
MoveToPlayer
→ Get Player Character
→ AI Move To
    Pawn = Get Controlled Pawn
    Target Actor = Player Character
    Acceptance Radius = 100
```

Schéma :

```text
Event BeginPlay
    ↓
Set Timer by Event toutes les 0.3 s
    ↓
MoveToPlayer
    ↓
AI Move To Player
```

Avantage : l'IA ne recalcule pas son mouvement à chaque frame.

---

## 4.8. Ajouter une distance de détection

Actuellement, l'ennemi poursuit le joueur même à l'autre bout de la map. On va ajouter une distance maximale.

Dans `BP_EnemyAIController`, dans `MoveToPlayer` :

```text
Get Controlled Pawn
→ Get Actor Location

Get Player Character
→ Get Actor Location

Distance(Vector)
→ Branch
    Condition = Distance < 1500
```

Si `True` :

```text
AI Move To Player
```

Si `False` :

```text
Stop Movement
```

Schéma :

```text
MoveToPlayer
    ↓
Calculer distance Ennemi ↔ Joueur
    ↓
Distance < 1500 ?
    ├── True  → AI Move To Player
    └── False → Stop Movement
```

---

## 4.9. Ajouter des dégâts au contact

Dans `BP_Enemy`, ajoute une collision simple.

### Méthode avec Capsule Component

Dans l'Event Graph de `BP_Enemy` :

```text
CapsuleComponent → OnComponentBeginOverlap
→ Other Actor
→ Cast To BP_ThirdPersonCharacter
→ Apply Damage
    Damaged Actor = Other Actor
    Base Damage = 10
```

Il faut aussi que le joueur puisse recevoir les dégâts.

Dans le Blueprint du joueur :

```text
Event AnyDamage
→ Health = Health - Damage
→ Branch Health <= 0
    True → mort du joueur
```

Crée une variable dans le joueur :

```text
Health : Float = 100
```

---

## 4.10. Version propre avec Behavior Tree

La version avec `AI Move To` est parfaite pour débuter. Mais Unreal propose aussi les **Behavior Trees**, très utilisés pour organiser l'IA.

Un Behavior Tree permet de dire :

```text
Si je vois le joueur → je le poursuis
Sinon → je patrouille
```

Pour une IA simple, on peut faire :

```text
Behavior Tree
└── Selector
    ├── Sequence Chase Player
    │   ├── Blackboard Decorator: TargetActor Is Set
    │   └── Move To TargetActor
    └── Sequence Idle
        └── Wait 1 second
```

---

## 4.11. Créer un Blackboard

Dans `Content/AI/` :

1. Clique droit.
2. `Artificial Intelligence > Blackboard`.
3. Nomme-le :

```text
BB_Enemy
```

Ouvre-le et ajoute une clé :

```text
TargetActor
Type: Object
Base Class: Actor
```

Cette clé servira à stocker le joueur.

---

## 4.12. Créer un Behavior Tree

Dans `Content/AI/` :

1. Clique droit.
2. `Artificial Intelligence > Behavior Tree`.
3. Nomme-le :

```text
BT_Enemy
```

Ouvre-le et assigne :

```text
Blackboard Asset = BB_Enemy
```

Dans le graphe :

```text
Root
└── Selector
    ├── Sequence_Chase
    │   ├── Blackboard Decorator: TargetActor Is Set
    │   └── Move To
    │       Blackboard Key = TargetActor
    │       Acceptable Radius = 100
    └── Wait
        Time = 0.5
```

---

## 4.13. Lancer le Behavior Tree depuis l'AIController

Dans `BP_EnemyAIController` :

```text
Event On Possess
→ Run Behavior Tree
    BTAsset = BT_Enemy
```

Schéma :

```text
Event On Possess
    ↓
Run Behavior Tree BT_Enemy
```

---

## 4.14. Envoyer le joueur dans le Blackboard

Dans `BP_EnemyAIController`, tu peux créer une version simple :

```text
Event BeginPlay
→ Set Timer by Event
    Time = 0.3
    Looping = true
→ Custom Event UpdateTarget
```

Dans `UpdateTarget` :

```text
Get Player Character
→ Get Controlled Pawn
→ calculer distance
→ Branch Distance < 1500
```

Si `True` :

```text
Set Blackboard Value as Object
    Key Name = TargetActor
    Object Value = Player Character
```

Si `False` :

```text
Clear Value
    Key Name = TargetActor
```

Ainsi, le Behavior Tree poursuit le joueur seulement quand `TargetActor` est défini.

---

## 4.15. Résumé de la logique IA

```text
BP_Enemy
├── Character visible dans le monde
├── AI Controller Class = BP_EnemyAIController
└── Auto Possess AI = Placed in World or Spawned

BP_EnemyAIController
├── Lance BT_Enemy
└── Met à jour TargetActor dans BB_Enemy

BB_Enemy
└── TargetActor : Actor

BT_Enemy
└── Si TargetActor existe → Move To TargetActor
```

---

# 5. Tests et problèmes fréquents

## 5.1. Le menu ne s'affiche pas

Vérifie :

- `Event BeginPlay` est bien dans le Level Blueprint de `L_MainMenu` ;
- `Create Widget` utilise bien `WBP_MainMenu` ;
- `Add to Viewport` est bien connecté ;
- `Game Default Map` est bien `L_MainMenu`.

---

## 5.2. Je ne peux pas cliquer sur les boutons

Vérifie que tu as :

```text
Set Input Mode UI Only
Set Show Mouse Cursor = true
```

Le `Player Controller` doit être la cible de ces nodes.

---

## 5.3. Le niveau ne se charge pas

Vérifie :

- le nom exact du niveau ;
- l'orthographe dans `Open Level` ;
- que la map est sauvegardée ;
- que la map est incluse dans le packaging.

Exemple :

```text
Nom réel : L_Level01
Dans Open Level : L_Level01
```

Pas :

```text
L_Level1
Level01
L level 01
```

---

## 5.4. L'ennemi ne bouge pas

Vérifie :

- il y a un `NavMeshBoundsVolume` dans le niveau ;
- la zone est verte quand tu appuies sur `P` ;
- `BP_Enemy` utilise bien `BP_EnemyAIController` ;
- `Auto Possess AI` est sur `Placed in World or Spawned` ;
- le sol a une collision ;
- l'ennemi est posé sur le NavMesh ;
- `AI Move To` reçoit bien un `Pawn` et une cible.

---

## 5.5. L'ennemi tourne mal

Dans `BP_Enemy` :

```text
Use Controller Rotation Yaw = false
```

Dans `Character Movement` :

```text
Orient Rotation to Movement = true
```

---

## 5.6. Le Behavior Tree ne fait rien

Vérifie :

- `Run Behavior Tree` est bien appelé dans `Event On Possess` ;
- `BT_Enemy` utilise bien `BB_Enemy` ;
- la clé s'appelle exactement `TargetActor` ;
- le Decorator vérifie bien que `TargetActor` est défini ;
- `Set Blackboard Value as Object` utilise le même nom de clé.

---

# 6. Améliorations possibles

## 6.1. Améliorer le menu

Tu peux ajouter :

- un bouton `Options` ;
- un réglage du volume ;
- un écran de pause ;
- une animation d'apparition ;
- un fond vidéo ou image ;
- une sauvegarde de progression ;
- un menu de sélection de niveau avec niveaux verrouillés.

---

## 6.2. Améliorer l'IA

Tu peux ajouter :

- une patrouille avec plusieurs points ;
- une vision avec `AI Perception` ;
- un son quand l'ennemi voit le joueur ;
- une attaque avec animation ;
- un délai entre deux attaques ;
- une barre de vie ;
- une mort de l'ennemi ;
- un comportement de recherche quand le joueur disparaît.

---

## 6.3. Exemple de comportement plus complet

```text
Selector
├── Sequence Attack
│   ├── TargetActor Is Set
│   ├── Distance To Target < 150
│   └── Attack Task
│
├── Sequence Chase
│   ├── TargetActor Is Set
│   └── Move To TargetActor
│
└── Sequence Patrol
    ├── Find Random Location
    ├── Move To Location
    └── Wait
```

---

# 7. Sources officielles utiles

- Documentation Epic — Blueprints Visual Scripting : https://dev.epicgames.com/documentation/en-us/unreal-engine/blueprints-visual-scripting-in-unreal-engine
- Documentation Epic — UMG UI Designer Quick Start Guide : https://dev.epicgames.com/documentation/unreal-engine/umg-ui-designer-quick-start-guide-in-unreal-engine
- Documentation Epic — UserWidget Python API, incluant `add_to_viewport` : https://dev.epicgames.com/documentation/en-us/unreal-engine/python-api/class/UserWidget?application_version=5.6
- Documentation Epic — Behavior Tree Quick Start Guide : https://dev.epicgames.com/documentation/unreal-engine/behavior-tree-in-unreal-engine---quick-start-guide

---

# Mini checklist finale

## Menu

- [ ] `L_MainMenu` existe.
- [ ] `WBP_MainMenu` existe.
- [ ] Le widget est créé au `BeginPlay`.
- [ ] Les boutons appellent `Open Level`.
- [ ] Le curseur souris est visible dans le menu.
- [ ] Le mode de contrôle revient à `Game Only` dans le niveau jouable.

## IA

- [ ] `BP_Enemy` hérite de `Character`.
- [ ] `BP_EnemyAIController` existe.
- [ ] `BP_Enemy` utilise ce contrôleur IA.
- [ ] `Auto Possess AI` est configuré.
- [ ] Un `NavMeshBoundsVolume` couvre la zone de jeu.
- [ ] Le sol devient vert avec la touche `P`.
- [ ] L'IA utilise `AI Move To` ou un Behavior Tree.

---

## Conclusion

Tu as maintenant deux systèmes de base très utiles dans Unreal Engine 5.6 :

1. un **menu de niveaux** avec UMG ;
2. un **ennemi IA simple** capable de poursuivre le joueur.

Ces deux bases peuvent servir dans presque tous les prototypes : jeu d'horreur, platformer 3D, shooter, RPG simple ou jeu d'exploration.
