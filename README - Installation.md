---
type: aide
---

# 🛠️ Installation & fonctionnement

> Glisse le dossier **`Coloc/`** dans ton vault Obsidian (ou ouvre-le comme un vault à part). Tout est en Markdown, donc rien n'est perdu si un plugin manque — mais les tableaux, scores et graphiques **n'apparaissent qu'avec les plugins ci-dessous**.

## 1. Plugins à installer (Paramètres → Plugins tiers → Parcourir)

| Plugin | Rôle ici | Indispensable |
|---|---|---|
| **Dataview** | Le moteur de base de données : classements, scores, tâches en attente, tout est calculé tout seul à partir des notes. Active aussi *JavaScript Queries* et *Inline JS Queries* dans ses réglages. | ✅ Oui |
| **Tasks** | Cases à cocher avec dates, récurrence (`🔁 every week`), date de complétion. Gère le « 2× par semaine ». | ✅ Oui |
| **Obsidian Charts** (`obsidian-charts`, par phibr0) | Les graphiques (barres, lignes, radar). Le dashboard l'appelle via `window.renderChart`. | ✅ Oui |
| **Templater** | Génère automatiquement une nouvelle note hebdo/mensuelle pré-remplie. | ⭐ Recommandé |
| **Periodic Notes** | Crée la note de la semaine / du mois en un clic, au bon format (`2026-W23`, `2026-06`). | ⭐ Recommandé |
| **Calendar** | Mini-calendrier dans la barre latérale pour sauter d'une semaine à l'autre. | Optionnel |
| **Homepage** | Ouvre `🏠 Dashboard` au démarrage d'Obsidian. | Optionnel |

> Pourquoi ce choix ? Les plugins « gamification » tout faits (Grind Manager, Gamified Tasks, Gamificate your PKM…) sont pensés pour **une seule personne** et son PKM, pas pour une coloc avec classement multi-joueurs. Le combo **Dataview + Tasks + Charts** permet un système maison, multi-personnes, 100 % adaptable — c'est l'approche éprouvée par la communauté pour gamifier des routines.

## 2. Réglages Periodic Notes (si installé)

- **Weekly Notes** → Format `gggg-[W]ww` → Dossier `Coloc/Hebdomadaire` → Modèle `Coloc/Templates/Modèle Hebdomadaire`
- **Monthly Notes** → Format `YYYY-MM` → Dossier `Coloc/Mensuel` → Modèle `Coloc/Templates/Modèle Mensuel`

## 3. Comment ça marche au quotidien

1. Chaque semaine, on ouvre (ou on crée) la note `Hebdomadaire/2026-Wxx`.
2. Quand quelqu'un fait une tâche, il **coche la case** et met son nom dans le champ `[par:: …]`.
3. Les points (`[pts:: …]`) sont déjà écrits dans la tâche.
4. Le **Dashboard** recalcule tout seul : classement, niveaux, graphiques.

Format d'une tâche :

```
- [ ] Aspirateur RDC [par::  ] [pts:: 15]
```

Une fois faite par Guillaume :

```
- [x] Aspirateur RDC [par:: Guillaume] [pts:: 15] ✅ 2026-06-03
```

> 💡 Le `✅ 2026-06-03` est ajouté **automatiquement** par le plugin Tasks quand tu coches la case (réglage *Set done date on every completed task*).

## 4. Barème des points (modifiable)

| Tâche | Points |
|---|---|
| Sol (javel) | 15 |
| Aspirateur | 15 |
| Toilettes | 10 |
| Lave-linge / lave-vaisselle (lancer + vider) | 8 |
| Vider la poubelle | 5 |
| Torchons | 5 |
| **Mensuel** — Vitres | 20 |
| **Mensuel** — Frigo | 25 |
| **Mensuel** — Four | 25 |
| **Mensuel** — Machine à laver (détartrage) | 20 |
| **Mensuel** — Placards | 15 |

Pour changer un barème : modifie juste le `[pts:: …]` dans les modèles.
