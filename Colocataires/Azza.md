---
type: colocataire
nom: Azza
emoji: 🐝
couleur: "#f1c40f"
chambre: 3
actif: true
---

# 🐝 Azza

## 🎮 Statistiques

```dataviewjs
const nom = dv.current().nom;
const pages = dv.pages('"Hebdomadaire" or "Mensuel"');
let total = 0, faites = 0;
for (const p of pages) {
  for (const t of p.file.tasks) {
    if (t.completed && t.par == nom && t.pts) { total += Number(t.pts); faites++; }
  }
}
const niveau = Math.floor(total / 100) + 1;
const reste = total % 100;
dv.paragraph(`**Niveau ${niveau}**  —  ${total} XP  —  ${faites} tâches accomplies`);
dv.paragraph(`Progression vers niveau ${niveau + 1} : ${reste}/100 XP`);
const blocs = Math.round(reste / 10);
dv.paragraph("`" + "█".repeat(blocs) + "░".repeat(10 - blocs) + "`");
```

## 📋 Mes tâches en cours
```dataview
TASK
FROM "Hebdomadaire" OR "Mensuel"
WHERE !completed AND par = this.nom
```

## ✅ Mes 10 dernières tâches faites
```dataview
TASK
FROM "Hebdomadaire" OR "Mensuel"
WHERE completed AND par = this.nom
SORT completion DESC
LIMIT 10
```
