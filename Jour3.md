# Unreal Engine 5.6 — Système d’attaque complet pour le Player

## Niveau

Débutant à intermédiaire.

## Objectif du cours

Dans ce cours, tu vas créer un système d’attaque complet pour ton personnage joueur dans Unreal Engine 5.6.

À la fin, ton player pourra :

- attaquer avec une touche ou un clic souris ;
- jouer une animation d’attaque ;
- utiliser une hitbox d’arme ;
- toucher un ennemi ;
- infliger des dégâts ;
- empêcher le spam d’attaque ;
- faire un combo de plusieurs coups ;
- tuer un ennemi ;
- ajouter du son, des effets visuels et du knockback.

---

# 1. Préparation du projet

## 1.1 Créer ou ouvrir un projet

Tu peux utiliser :

- Third Person Template ;
- un projet Blueprint ;
- un projet C++ avec Blueprint.

Pour ce cours, on part sur un projet **Blueprint Third Person**.

---

# 2. Préparer le Player

## 2.1 Blueprint du joueur

Ouvre ou crée ton Blueprint de personnage :

```text
BP_PlayerCharacter
```

Ce Blueprint doit hériter de :

```text
Character
```

Il doit contenir au minimum :

- Capsule Component ;
- Mesh ;
- Character Movement ;
- Camera Boom ;
- Follow Camera.

---

## 2.2 Variables du Player

Dans `BP_PlayerCharacter`, crée les variables suivantes :

| Nom | Type | Valeur par défaut |
|---|---|---|
| `Health` | Float | 100 |
| `MaxHealth` | Float | 100 |
| `AttackDamage` | Float | 25 |
| `IsAttacking` | Boolean | false |
| `CanCombo` | Boolean | false |
| `ComboIndex` | Integer | 0 |
| `MaxCombo` | Integer | 3 |
| `AlreadyHitActors` | Actor Array | vide |

---

# 3. Créer l’Input d’attaque

## 3.1 Créer l’Input Action

Dans le Content Browser :

```text
Right Click → Input → Input Action
```

Nom :

```text
IA_Attack
```

Dans l’Input Action :

```text
Value Type = Digital Bool
```

---

## 3.2 Ajouter l’Input dans le Mapping Context

Ouvre ton Input Mapping Context, par exemple :

```text
IMC_Default
```

Ajoute :

```text
IA_Attack
```

Assigne une touche :

```text
Left Mouse Button
```

ou :

```text
E
```

ou :

```text
Gamepad Face Button Right
```

---

# 4. Ajouter l’Input dans le Player

Dans `BP_PlayerCharacter`, ajoute l’événement :

```text
IA_Attack Started
```

Puis connecte-le à :

```text
Attack
```

Exemple :

```text
IA_Attack Started
→ Attack
```

---

# 5. Créer l’animation d’attaque

## 5.1 Importer ou utiliser une animation

Tu dois avoir au moins une animation d’attaque, par exemple :

```text
Attack_01
```

Pour un combo, utilise :

```text
Attack_01
Attack_02
Attack_03
```

---

## 5.2 Créer un Montage

Clique droit sur l’animation :

```text
Create → Anim Montage
```

Nom :

```text
AM_Player_Attack
```

---

## 5.3 Créer les sections du montage

Dans le montage, crée trois sections :

```text
Attack1
Attack2
Attack3
```

Chaque section correspond à un coup du combo.

---

# 6. Fonction Attack

## 6.1 Créer le Custom Event

Dans `BP_PlayerCharacter`, crée un Custom Event :

```text
Attack
```

---

## 6.2 Logique de base

Pseudo Blueprint :

```text
Attack
→ Branch IsAttacking
```

Si `IsAttacking` est `false` :

```text
Set IsAttacking = true
Set ComboIndex = 1
Clear AlreadyHitActors
Play Anim Montage AM_Player_Attack
Jump To Montage Section Attack1
```

Si `IsAttacking` est `true` :

```text
Branch CanCombo
```

Si `CanCombo` est `true` :

```text
ComboIndex = ComboIndex + 1
Clamp ComboIndex entre 1 et MaxCombo
CanCombo = false
```

---

# 7. Ajouter une hitbox d’arme

## 7.1 Ajouter une Box Collision

Dans `BP_PlayerCharacter`, ajoute :

```text
Box Collision
```

Nom :

```text
WeaponHitbox
```

Attache-la à la main du personnage ou à l’arme.

Exemple de socket :

```text
hand_r
```

---

## 7.2 Réglages de collision

Par défaut :

```text
Collision Enabled = No Collision
```

Pendant l’attaque :

```text
Collision Enabled = Query Only
```

À la fin de l’attaque :

```text
Collision Enabled = No Collision
```

---

# 8. Anim Notify pour activer la hitbox

## 8.1 Pourquoi utiliser des Anim Notify ?

Les `Delay` fonctionnent, mais ils ne sont pas précis.

Les `Anim Notify` permettent d’activer la hitbox exactement au bon moment dans l’animation.

---

## 8.2 Créer les notifies

Dans le montage d’attaque, ajoute :

```text
AttackHitboxStart
AttackHitboxEnd
ComboWindowOpen
ComboWindowClose
AttackEnd
```

---

## 8.3 AttackHitboxStart

Dans l’Animation Blueprint ou le Character :

```text
AnimNotify_AttackHitboxStart
→ Clear AlreadyHitActors
→ WeaponHitbox Set Collision Enabled Query Only
```

---

## 8.4 AttackHitboxEnd

```text
AnimNotify_AttackHitboxEnd
→ WeaponHitbox Set Collision Enabled No Collision
```

---

## 8.5 ComboWindowOpen

```text
AnimNotify_ComboWindowOpen
→ Set CanCombo = true
```

---

## 8.6 ComboWindowClose

```text
AnimNotify_ComboWindowClose
→ Set CanCombo = false
```

---

## 8.7 AttackEnd

```text
AnimNotify_AttackEnd
→ Set IsAttacking = false
→ Set CanCombo = false
→ Set ComboIndex = 0
```

---

# 9. Détecter les ennemis touchés

## 9.1 Event Begin Overlap

Sélectionne `WeaponHitbox`, puis ajoute :

```text
On Component Begin Overlap
```

---

## 9.2 Vérifier l’acteur touché

Blueprint logique :

```text
Other Actor
→ Is Valid
→ Other Actor != Self
```

Ensuite vérifie que l’acteur est un ennemi.

Méthode simple :

```text
Actor Has Tag Enemy
```

Méthode plus propre :

```text
Does Implement Interface BPI_Damageable
```

---

# 10. Empêcher plusieurs dégâts sur le même ennemi

Avant d’appliquer les dégâts :

```text
AlreadyHitActors Contains Other Actor
```

Si `false` :

```text
Add Other Actor to AlreadyHitActors
Apply Damage
```

Cela évite qu’un ennemi reçoive 10 fois les dégâts pendant une seule attaque.

---

# 11. Appliquer les dégâts

Dans l’overlap de la hitbox :

```text
Apply Damage
```

Paramètres :

```text
Damaged Actor = Other Actor
Base Damage = AttackDamage
Event Instigator = Get Controller
Damage Causer = Self
```

---

# 12. Créer l’ennemi

## 12.1 Blueprint ennemi

Crée :

```text
BP_Enemy
```

Héritage :

```text
Character
```

Ajoute le tag :

```text
Enemy
```

---

## 12.2 Variables de l’ennemi

| Nom | Type | Valeur |
|---|---|---|
| `Health` | Float | 100 |
| `MaxHealth` | Float | 100 |
| `IsDead` | Boolean | false |

---

# 13. Recevoir les dégâts côté ennemi

Dans `BP_Enemy`, ajoute :

```text
Event Any Damage
```

Logique :

```text
Branch IsDead == false
→ Health = Health - Damage
→ Branch Health <= 0
```

Si `Health <= 0` :

```text
Die
```

---

# 14. Fonction Die

Crée une fonction :

```text
Die
```

Logique :

```text
Set IsDead = true
Disable Movement
Set Collision Enabled No Collision
Play Death Animation
Delay 2.0
Destroy Actor
```

---

# 15. Ajouter un knockback

Après `Apply Damage`, tu peux repousser l’ennemi.

Dans l’ennemi, crée un Custom Event :

```text
ReceiveKnockback
```

Paramètre :

```text
Direction Vector
Force Float
```

Logique :

```text
Launch Character
```

Exemple :

```text
Direction * Force
```

Valeur conseillée :

```text
Force = 600
```

---

# 16. Ajouter un effet visuel d’impact

## 16.1 Créer un Niagara System

Crée ou importe :

```text
NS_HitImpact
```

---

## 16.2 Spawn à l’impact

Dans l’overlap de la hitbox :

```text
Spawn System At Location
```

Location :

```text
Other Actor Location
```

---

# 17. Ajouter un son d’impact

Importe un son :

```text
SFX_Hit.wav
```

Dans l’overlap :

```text
Play Sound At Location
```

Location :

```text
Other Actor Location
```

---

# 18. Ajouter une attaque lourde

## 18.1 Nouvel Input

Créer :

```text
IA_HeavyAttack
```

Touche :

```text
Right Mouse Button
```

---

## 18.2 Variables

Ajoute :

```text
HeavyAttackDamage = 50
```

---

## 18.3 Logique

```text
IA_HeavyAttack Started
→ HeavyAttack
```

Dans `HeavyAttack` :

```text
Branch IsAttacking == false
→ Set IsAttacking = true
→ Set AttackDamage = HeavyAttackDamage
→ Play Montage HeavyAttack
```

---

# 19. Ajouter de la stamina

## 19.1 Variables

| Nom | Type | Valeur |
|---|---|---|
| `Stamina` | Float | 100 |
| `MaxStamina` | Float | 100 |
| `LightAttackCost` | Float | 15 |
| `HeavyAttackCost` | Float | 35 |

---

## 19.2 Vérifier avant attaque

Avant l’attaque :

```text
Branch Stamina >= LightAttackCost
```

Si oui :

```text
Stamina = Stamina - LightAttackCost
Attack
```

---

# 20. Ajouter une barre de vie au joueur

Créer un Widget :

```text
WBP_PlayerHUD
```

Ajouter une Progress Bar.

Pourcentage :

```text
Health / MaxHealth
```

Dans `BeginPlay` du Player :

```text
Create Widget WBP_PlayerHUD
Add To Viewport
```

---

# 21. Ajouter une barre de vie à l’ennemi

Créer :

```text
WBP_EnemyHealth
```

Dans `BP_Enemy`, ajoute :

```text
Widget Component
```

Assigne :

```text
WBP_EnemyHealth
```

La barre utilise :

```text
Health / MaxHealth
```

---

# 22. Debug

Pour tester :

```text
Print String
```

Exemples :

```text
"Attack"
"Hit Enemy"
"Damage Applied"
"Enemy Dead"
```

Tu peux aussi afficher la hitbox :

```text
Show Collision
```

Commande console :

```text
show collision
```

---

# 23. Erreurs fréquentes

## 23.1 L’attaque ne se lance pas

Vérifie :

- `IA_Attack` existe ;
- `IMC_Default` est ajouté au joueur ;
- la touche est bien assignée ;
- l’event `IA_Attack Started` est branché.

---

## 23.2 L’animation ne joue pas

Vérifie :

- le montage est compatible avec le Skeleton ;
- le Mesh utilise le bon Animation Blueprint ;
- le Slot du montage est bien présent dans l’Anim Graph.

---

## 23.3 L’ennemi ne prend pas de dégâts

Vérifie :

- l’ennemi a le tag `Enemy` ;
- la hitbox passe en `Query Only` ;
- `Generate Overlap Events` est activé ;
- les collisions sont compatibles ;
- `Apply Damage` est bien appelé.

---

## 23.4 L’ennemi prend trop de dégâts

Utilise :

```text
AlreadyHitActors
```

et vide le tableau seulement au début d’une nouvelle attaque.

---

# 24. Organisation recommandée

Structure propre :

```text
Blueprints/
├── Player/
│   ├── BP_PlayerCharacter
│   ├── ABP_Player
│   └── AM_Player_Attack
│
├── Enemy/
│   ├── BP_Enemy
│   └── WBP_EnemyHealth
│
├── Combat/
│   ├── BPI_Damageable
│   ├── BP_Weapon
│   └── DT_Attacks
│
├── UI/
│   └── WBP_PlayerHUD
│
└── FX/
    ├── NS_HitImpact
    └── SFX_Hit
```

---

# 25. Version avancée : Combat Component

Pour un projet plus propre, crée un composant :

```text
BP_CombatComponent
```

Il peut contenir :

- Attack ;
- HeavyAttack ;
- Combo ;
- Stamina Cost ;
- Damage ;
- Hitbox ;
- FX ;
- SFX.

Avantage :

```text
Tu peux réutiliser le système sur plusieurs personnages.
```

---

# 26. Résultat final

Ton système permet maintenant :

- attaque légère ;
- attaque lourde ;
- combo 3 coups ;
- hitbox activée par Anim Notify ;
- dégâts ;
- mort de l’ennemi ;
- knockback ;
- effets visuels ;
- sons ;
- barre de vie ;
- stamina ;
- architecture propre.

---

# 27. Exercices

## Exercice 1

Ajoute une attaque sautée.

## Exercice 2

Ajoute une esquive.

## Exercice 3

Ajoute une parade.

## Exercice 4

Ajoute un lock-on target.

## Exercice 5

Crée un boss avec trois phases.

---

# 28. Conclusion

Tu as maintenant une base solide pour créer un système de combat melee dans Unreal Engine 5.6.

Cette base peut évoluer vers un système plus avancé inspiré de :

- Dark Souls ;
- Elden Ring ;
- God of War ;
- Ghost of Tsushima ;
- Devil May Cry.
- 
