# KULUNA — La Rue de Kinshasa

Dossier technique du prototype (v4) — version du 13 novembre 2026.
À placer à la racine du projet, à côté de `index.html`.

## Contexte

- Jeu 3D d'action-aventure et d'exploration inspiré de la vie urbaine à Kinshasa (RDC).
- Plateforme finale : **Android**, développé avec **Godot**. Une version PC pourra venir plus tard.
- Premier quartier : **Kingabwa – Mbonoko**, à Kinshasa. Commencer petit et stable, agrandir ensuite.
- `index.html` est un **prototype web (Three.js 0.160.0)** pour tester le gameplay avant de le refaire en GDScript. Il charge Three.js depuis le CDN jsdelivr : connexion Internet nécessaire.

## Règles du projet (à respecter)

- Le nom du jeu reste **KULUNA**. Android est prioritaire.
- Outils gratuits d'abord. **Aucun achat, abonnement ou crédit payant sans l'accord explicite de l'utilisateur.**
- Examiner les fichiers existants avant de modifier. Ne supprimer aucune scène, ressource ou fonctionnalité sans demander confirmation.
- Violence stylisée uniquement : machettes et pierres, K.O. sans gore.
- Ne jamais déclarer une tâche terminée sans fichier, test ou confirmation correspondante.
- Le jeu final n'est pas une page web : ne pas remplacer Godot par le prototype.

## État du prototype v4

### La rue de Kinshasa

- Boutiques, maisons, kiosques, poteaux électriques, nuages, flaques (elles ralentissent le joueur). Décor identique à chaque partie (générateur aléatoire à graine fixe).
- Personnage à la 3e personne, caméra derrière lui, orientable en glissant sur la scène.
- Course, saut, marche animée (jambes et bras), collisions avec bâtiments, kiosques, poteaux et véhicules.
- Circulation : 1 minibus et 2 motos qui roulent, plus des véhicules garés.
- 4 habitants qui marchent, s'arrêtent et se tournent vers le joueur. Bouton PARLER : phrases en lingala (exemples, **à faire vérifier par un locuteur**).
- Mission 1 : saluer les 4 habitants (compteur affiché).

### Véhicules conduisibles

- Bouton ENTRER / SORTIR (touche V) : tous les minibus et motos, garés ou en circulation.
- Accélération, freinage, marche arrière, direction proportionnelle à la vitesse, turbo avec COURIR.
- Collisions avec le décor et les autres véhicules ; ralentissement au contact.
- À la sortie, le joueur réapparaît à côté ; le véhicule reste garé sur place.

### Combat 5 contre 5 — terrain de Kingabwa

- Cercle doré avec panneau « COMBAT 5 vs 5 – Terrain de Kingabwa » au bout de la rue ; y entrer téléporte sur un terrain de terre fermé (murs, caisses, grande flaque).
- Équipe du joueur : lui + 4 alliés (polos jaunes). Adversaires : 5 combattants rouges.
- Armes : machette (corps à corps) et pierres (lancer). Barres de vie au-dessus des combattants.
- À 0 PV, le combattant tombe K.O. (sans gore). Victoire si les 5 adversaires sont K.O., défaite si le joueur l'est. Boutons REJOUER et RETOUR À LA RUE.
- IA simple : va vers l'ennemi le plus proche, frappe à portée, lance parfois des pierres.

## Commandes

| Action | Mobile | PC |
|---|---|---|
| Se déplacer | Joystick gauche | W A S D ou flèches |
| Courir / turbo en véhicule | COURIR | Maj |
| Sauter | SAUTER | Espace |
| Tourner la caméra | Glisser sur la scène | Glisser à la souris |
| Parler à un habitant | PARLER | E |
| Entrer / sortir d'un véhicule | ENTRER / SORTIR | V |
| Frapper à la machette (combat) | FRAPPER | F |
| Lancer une pierre (combat) | PIERRE | G |

## Architecture du fichier HTML

Un seul fichier (HTML + CSS + JavaScript, module ES). Ordre des blocs dans le script :

| Bloc | Contenu |
|---|---|
| Scène et rendu | Scene, caméra, renderer, lumières, ombres (la lumière suit le joueur) |
| Décor | Sol, route, flaques (`puddles`), bâtiments en boucle, kiosques, poteaux, nuages ; utilitaires `box()` et `cyl()` |
| Collisions | Tableau `colliders` (rectangles alignés sur les axes) ; `addCol(x,z,hw,hd)` ; `resolve(position, rayon)` |
| Circulation | Tableau `traffic` ; `vehicle()` et `moto()` ; `updateTraffic()` |
| Personnages | `person(chemise, peau, pantalon, col)` ; `swing()` pour la marche ; le joueur est `me` (remplaçable par un GLB) |
| Habitants | `npcs`, `updateNpcs()`, `talk()`, `updateMission()` |
| Combat | Terrain (`AX`,`AZ`), marqueur d'entrée, `fighters`, `mkFighter()`, `doHit()`, `throwStone()`, `hurt()`, `resetMatch()`, `startMatch()`, `exitMatch()`, `updateCombat()` |
| Contrôles | `camYaw`, `moveX/moveY`, `keys` ; `driveStep()`, `toggleVeh()`, `movePlayer()`, boucle `loop()`, joystick, boutons, clavier |

## Valeurs de réglage

| Réglage | Valeur actuelle |
|---|---|
| Vitesse du joueur | marche 3,8 · course 7,2 (×0,6 dans une flaque) |
| Saut | vitesse initiale 6,2 · gravité 16 |
| Machette du joueur | 25 dégâts · recharge 0,5 s · portée 2,3 |
| Machette des autres combattants | 9 dégâts · recharge 1,2 à 1,7 s · vitesse 3,1 |
| Pierres | 14 dégâts · vitesse 15 · gravité 12 · joueur 8, autres 5 |
| Points de vie | 100 pour tous |
| Terrain de combat | centre (0, -110) · 36 × 36 · entrée en (0, -46), rayon 1,8 |
| Minibus | vitesse max 15 · rayon 1,5 |
| Moto | vitesse max 22 · rayon 0,8 |
| Conduite | accélération 14 · freinage 30 · turbo ×1,4 |

## Limites connues

- Non testé sur un vrai téléphone : performances et ergonomie des boutons à vérifier.
- Collisions en rectangles alignés sur les axes : un véhicule tourné est approximé.
- Personnages en formes simples (pas de modèle Meshy, ni squelette, ni expressions faciales).
- Aucun son. Les phrases en lingala doivent être validées par un locuteur.
- Les véhicules ne renversent pas les habitants ; les habitants n'évitent pas les véhicules.
- Un seul niveau. Pas de sauvegarde, pas de menu principal définitif.

## Prochaines étapes suggérées

1. Tester la v4 sur téléphone et corriger performances et taille des boutons.
2. Ajouter les sons : moteur, klaxon, ambiance de rue, bruits de coups.
3. Ajouter la mission 2 et le football.
4. Récupérer le modèle Meshy (GLB/FBX), le riguer dans Blender, l'importer dans Godot.
5. Recréer caméra, joystick, véhicules et combat en GDScript dans Godot, puis préparer l'APK.

## Prompt prêt à coller dans Cursor

```text
Tu es assistant technique du projet KULUNA - La Rue de Kinshasa.
Le jeu final est un jeu 3D Android developpe avec Godot.
Le fichier index.html est un prototype web
(Three.js) pour tester le gameplay. Lis d'abord ce fichier
KULUNA_CONTEXTE.md et le prototype.

Ne supprime rien sans me demander. Utilise uniquement des
outils gratuits et ne lance aucun achat ni abonnement sans mon
accord. Violence stylisee : machettes et pierres, K.O. sans gore.

Premiere tache : [ecris ici ce que tu veux].
```
