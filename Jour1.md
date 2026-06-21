Formation Unreal Engine 5 Blueprint

Jour 1 - Découverte du moteur et initiation au gameplay

Durée : 6 heures

---

Objectifs pédagogiques

À la fin de cette journée, vous serez capables de :

- Comprendre l'organisation d'un projet Unreal Engine
- Naviguer dans l'éditeur Unreal Engine
- Comprendre la logique des Blueprints
- Créer des interactions simples
- Détecter des collisions
- Créer une mécanique de collecte
- Concevoir le jeu qui sera développé pendant les deux jours suivants

---

Programme de la journée

Horaire| Sujet
09h00 - 10h30| Découverte d'Unreal Engine
10h45 - 12h00| Introduction aux Blueprints
13h00 - 14h30| Atelier Gameplay
14h45 - 16h00| Brainstorming et conception du projet

---

1. Présentation d'Unreal Engine

Qu'est-ce qu'un moteur de jeu ?

Un moteur de jeu est un ensemble d'outils permettant de créer des expériences interactives.

Il fournit :

- Le rendu graphique
- La physique
- La gestion des collisions
- Le son
- L'animation
- Les systèmes de scripting

---

Pourquoi Unreal Engine ?

Avantages

- Gratuit jusqu'à un certain seuil de revenus
- Qualité graphique élevée
- Très utilisé dans l'industrie
- Documentation abondante
- Support du C++ et des Blueprints

Domaines d'utilisation

- Jeux vidéo
- Réalité virtuelle
- Réalité augmentée
- Simulation industrielle
- Architecture
- Cinéma

---

2. Structure d'un projet Unreal

Organisation recommandée

Content
│
├── Blueprints
├── Maps
├── Characters
├── UI
├── Audio
├── Materials
├── Meshes
└── VFX

---

Les Assets

Un Asset est une ressource du projet.

Exemples :

- Texture
- Modèle 3D
- Son
- Animation
- Blueprint

---

Les Levels

Un Level représente une scène jouable.

Exemples :

- Menu principal
- Niveau 1
- Niveau 2

---

Les Actors

Un Actor est tout objet placé dans un niveau.

Exemples :

- Joueur
- Ennemi
- Porte
- Pièce
- Lumière

---

3. Actor et Components

Actor

Objet principal.

Exemple :

BP_Door

---

Components

Éléments qui composent un Actor.

Exemple :

BP_Door
│
├── StaticMesh
├── BoxCollision
└── AudioComponent

---

Règle importante

«Un Actor est composé de plusieurs Components.»

---

4. Découverte de l'éditeur Unreal

Viewport

Zone de visualisation 3D.

Permet :

- Déplacement
- Rotation
- Mise à l'échelle

---

World Outliner

Liste de tous les objets présents dans la scène.

---

Details Panel

Affiche les propriétés de l'objet sélectionné.

---

Content Browser

Bibliothèque contenant toutes les ressources du projet.

---

Exercice 1 : Découverte du projet

Consignes

Dans le template Third Person :

1. Trouver le personnage.
2. Trouver le sol.
3. Trouver les lumières.
4. Ajouter un Cube.
5. Modifier sa taille.

---

Correction

Ajouter un Cube

Place Actors
→ Cube
→ Glisser dans la scène

Modifier sa taille

Details
→ Transform
→ Scale

Modifier :

X = 2
Y = 2
Z = 2

---

5. Introduction aux Blueprints

Définition

Les Blueprints permettent de programmer visuellement.

Ils utilisent des nœuds reliés entre eux.

---

Principe général

Événement
    ↓
Logique
    ↓
Action

Exemple :

Collision
    ↓
Ajouter score
    ↓
Détruire objet

---

Pourquoi utiliser les Blueprints ?

- Rapides à développer
- Visuels
- Faciles à déboguer
- Très utilisés pour le prototypage

---

Structure d'un Blueprint

Un Blueprint contient généralement :

- Components
- Variables
- Fonctions
- Event Graph

---

6. Les événements

Begin Play

Exécuté au lancement du jeu.

BeginPlay
    ↓
Afficher Message

---

Tick

Exécuté à chaque image.

60 FPS
=
60 exécutions par seconde

Attention :

Le Tick peut devenir coûteux en performances.

---

7. Les variables

Pourquoi utiliser une variable ?

Stocker une information.

Exemples :

- Score
- Temps
- Nombre de vies
- Nom du joueur

---

Types de variables

Type| Exemple
Integer| 10
Float| 5.5
Boolean| True / False
String| "Bonjour"
Vector| Position
Rotator| Rotation

---

Exercice 2 : Premier Blueprint

Objectif

Créer un Blueprint nommé :

BP_Test

Afficher :

Bonjour Unreal !

au lancement du jeu.

---

Correction

BeginPlay
    ↓
Print String

Texte :

Bonjour Unreal !

---

8. Les conditions

Branch

Le nœud Branch correspond à un :

if

---

Exemple :

Si Score >= 5
    → Victoire
Sinon
    → Continuer

---

Exercice 3 : Variable et condition

Créer :

NombreVies = 3

Si :

NombreVies > 0

Afficher :

Je suis vivant

Sinon :

Game Over

---

Correction

BeginPlay
    ↓
Get NombreVies
    ↓
> 0
    ↓
Branch

TRUE :

Print String
"Je suis vivant"

FALSE :

Print String
"Game Over"

---

9. Les collisions

Définition

Une collision permet de détecter une interaction entre deux objets.

---

Modes principaux

Block

Empêche le passage.

Overlap

Détecte le passage.

---

Les Triggers

Zone invisible permettant de détecter l'entrée d'un acteur.

Exemple :

Joueur entre
    ↓
Porte s'ouvre

---

10. Création d'un Collectible

Logique

Joueur touche la pièce
        ↓
Ajouter 1 point
        ↓
Détruire la pièce

---

Exercice 4 : Pièce à collecter

Créer :

BP_Coin

Quand le joueur touche la pièce :

- Afficher un message
- Détruire la pièce

---

Correction

Components :

Static Mesh
Sphere Collision

Event Graph :

OnComponentBeginOverlap
    ↓
Print String
"Pièce récupérée"
    ↓
Destroy Actor

---

11. Communication entre Blueprints

Problème

Comment une pièce modifie-t-elle le score du joueur ?

---

Solution

Utiliser un Cast.

Overlap
      ↓
Cast To ThirdPersonCharacter
      ↓
Modifier Score

---

Exercice 5 : Système de score

Créer dans le personnage :

Score = 0

Lorsqu'une pièce est collectée :

Score = Score + 1

---

Correction

Overlap
   ↓
Cast To ThirdPersonCharacter
   ↓
Get Score
   ↓
+1
   ↓
Set Score

---

12. Porte interactive

Objectif

Créer une porte qui s'ouvre lorsque le joueur entre dans une zone.

---

Composants

Door Mesh
Box Collision

---

Logique

BeginOverlap
      ↓
Set Relative Rotation

Rotation :

Yaw = 90°

---

Exercice 6 : Porte automatique

Créer :

BP_Door

Lorsqu'un joueur entre dans la zone :

- La porte pivote de 90 degrés.

---

Correction

BeginOverlap
      ↓
Set Relative Rotation

Valeur :

Yaw = 90

---

13. Débogage

Print String

Votre meilleur outil de débogage.

Exemple :

Print String
"Collision détectée"

Utilisations :

- Vérifier les événements
- Vérifier les variables
- Comprendre les bugs

---

14. Bonnes pratiques Blueprint

Nommage

Bon :

BP_Door
BP_Coin
BP_Player

Mauvais :

Test
Blueprint1
ObjetFinalFinalV2

---

Variables

Bon :

Score
Health
IsDoorOpen

Mauvais :

a
x
test123

---

15. Boucle de Gameplay

Tous les jeux reposent sur :

Action
↓
Récompense
↓
Progression
↓
Nouvelle action

---

Exemple :

Explorer
↓
Collecter
↓
Augmenter le score
↓
Victoire

---

16. Brainstorming du projet

Questions à se poser

Que fait le joueur ?

Exemple :

- Explorer
- Courir
- Collecter

---

Quel est l'objectif ?

Exemple :

- Trouver des objets
- Sortir d'un niveau
- Atteindre une zone

---

Comment gagne-t-il ?

Exemple :

- Atteindre un score
- Trouver une clé
- Survivre un certain temps

---

Contraintes du projet

Le jeu doit être réalisable en :

12 heures de développement

Éviter :

- Multijoueur
- Inventaire complexe
- IA avancée
- Sauvegarde

---

Livrables de fin de journée

Chaque groupe doit disposer de :

- Un concept de jeu
- Un pitch
- Une liste de mécaniques
- Une roadmap Jour 2
- Une roadmap Jour 3

Et avoir réalisé :

- Une pièce collectible
- Une porte interactive
- Un système de score
- Une condition de victoire simple

---

Ce que vous savez faire maintenant

✅ Naviguer dans Unreal Engine

✅ Créer un Blueprint

✅ Utiliser des variables

✅ Utiliser des conditions

✅ Détecter des collisions

✅ Communiquer entre Blueprints

✅ Créer une mécanique de collecte

✅ Concevoir un mini-jeu

---

Préparation du Jour 2

Objectif :

Transformer les prototypes réalisés aujourd'hui en un véritable jeu jouable du début à la fin.
