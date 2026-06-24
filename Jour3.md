# Unreal Engine 5.6 — Attaquer avec son Player

## Objectif

Créer une attaque simple pour un personnage joueur :
- appuyer sur une touche
- jouer une animation d’attaque
- activer une hitbox
- détecter un ennemi touché
- lui infliger des dégâts

## 1. Préparer l’Input d’attaque

Créer `IA_Attack` puis l’ajouter à votre Input Mapping Context.

## 2. Brancher l’Input

```text
IA_Attack Started
→ Attack
```

## 3. Variables

- IsAttacking (Bool)
- AttackDamage (Float = 25)
- AttackRange (Float = 150)
- AttackRadius (Float = 50)

## 4. Fonction Attack

```text
Attack
→ Branch(IsAttacking == false)
→ Set IsAttacking = true
→ Play Anim Montage
→ DoAttackTrace
→ Set IsAttacking = false
```

## 5. Trace d’attaque

Utiliser un Sphere Trace puis Apply Damage sur l'acteur touché.

## 6. Ennemi

```text
Event Any Damage
→ Health -= Damage
→ Si Health <= 0
    → Die()
```

## 7. Amélioration

Utiliser des Anim Notifies pour synchroniser précisément la fenêtre de dégâts.
