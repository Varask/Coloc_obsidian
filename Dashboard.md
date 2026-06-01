---
type: dashboard
cssclasses:
  - dashboard
---

# 🏠 Dashboard Coloc

> [!info] Cette page se met à jour toute seule à partir des tâches cochées dans `Hebdomadaire/` et `Mensuel/`.

## 🔗 Accès rapide
```dataviewjs
const sem = dv.luxon.DateTime.now().toFormat("kkkk-'W'WW");
const mois = dv.luxon.DateTime.now().toFormat("yyyy-LL");
dv.paragraph(`📆 [[${sem}|Semaine en cours]]  ·  🗓️ [[${mois}|Mois en cours]]  ·  🧹 [[Tâches communes]]  ·  🛒 [[Liste de courses]]`);
```

---

## 🏆 Classement général

```dataviewjs
// --- Charge les colocataires (couleur + emoji) ---
const colos = {};
for (const c of dv.pages('"Colocataires"').where(p => p.type == "colocataire")) {
  colos[c.nom] = { emoji: c.emoji ?? "🙂", couleur: c.couleur ?? "#888888" };
}

// --- Agrège les points sur toutes les notes ---
const pages = dv.pages('"Hebdomadaire" or "Mensuel" or "Taches communes"');
const xp = {}, nbTaches = {}, mensuel = {};
const semaineMap = {}; // semaine -> { nom -> pts }
for (const p of pages) {
  const sem = p.semaine ?? p.mois ?? "—";
  for (const t of p.file.tasks) {
    if (!t.completed || !t.par || !t.pts) continue;
    const n = String(t.par).trim();
    if (!n) continue;
    xp[n] = (xp[n] || 0) + Number(t.pts);
    nbTaches[n] = (nbTaches[n] || 0) + 1;
    if (p.type == "mensuel") mensuel[n] = (mensuel[n] || 0) + 1;
    if (p.semaine) {
      semaineMap[sem] = semaineMap[sem] || {};
      semaineMap[sem][n] = (semaineMap[sem][n] || 0) + Number(t.pts);
    }
  }
}

const classement = Object.keys(xp).sort((a, b) => xp[b] - xp[a]);
const medailles = ["🥇", "🥈", "🥉"];

// --- Badges ---
function badges(nom, rang) {
  const b = [];
  if (rang === 0) b.push("👑 Roi du ménage");
  if (xp[nom] >= 100) b.push("💯 +100 XP");
  if (nbTaches[nom] >= 5) b.push("🔥 Assidu");
  if (mensuel[nom] >= 1) b.push("🧴 Grand ménage");
  if (xp[nom] >= 200) b.push("⭐ Légende");
  return b.join(" ") || "—";
}

if (classement.length === 0) {
  dv.paragraph("_Personne n'a encore coché de tâche. Lancez-vous !_");
} else {
  dv.table(
    ["", "Colocataire", "Niveau", "XP", "Tâches", "Badges"],
    classement.map((n, i) => [
      medailles[i] ?? `${i + 1}.`,
      `${colos[n]?.emoji ?? "🙂"} ${n}`,
      "Niv. " + (Math.floor(xp[n] / 100) + 1),
      xp[n],
      nbTaches[n],
      badges(n, i)
    ])
  );
}
```

---

## 📊 XP total par colocataire

```dataviewjs
const colos = {};
for (const c of dv.pages('"Colocataires"').where(p => p.type == "colocataire")) {
  colos[c.nom] = c.couleur ?? "#888888";
}
const xp = {};
for (const p of dv.pages('"Hebdomadaire" or "Mensuel" or "Taches communes"')) {
  for (const t of p.file.tasks) {
    if (t.completed && t.par && t.pts) {
      const n = String(t.par).trim();
      xp[n] = (xp[n] || 0) + Number(t.pts);
    }
  }
}
const noms = Object.keys(xp).sort((a, b) => xp[b] - xp[a]);
if (!window.renderChart) {
  dv.paragraph("⚠️ Installe le plugin **Obsidian Charts** pour afficher les graphiques.");
} else if (noms.length === 0) {
  dv.paragraph("_Pas encore de données._");
} else {
  window.renderChart({
    type: "bar",
    data: {
      labels: noms,
      datasets: [{
        label: "XP total",
        data: noms.map(n => xp[n]),
        backgroundColor: noms.map(n => colos[n] ?? "#888888")
      }]
    },
    options: { plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true } } }
  }, this.container);
}
```

---

## 📈 Progression par semaine

```dataviewjs
const colos = {};
for (const c of dv.pages('"Colocataires"').where(p => p.type == "colocataire")) {
  colos[c.nom] = c.couleur ?? "#888888";
}
const semaineMap = {};
const personnes = new Set();
for (const p of dv.pages('"Hebdomadaire"')) {
  if (!p.semaine) continue;
  for (const t of p.file.tasks) {
    if (t.completed && t.par && t.pts) {
      const n = String(t.par).trim();
      personnes.add(n);
      semaineMap[p.semaine] = semaineMap[p.semaine] || {};
      semaineMap[p.semaine][n] = (semaineMap[p.semaine][n] || 0) + Number(t.pts);
    }
  }
}
const semaines = Object.keys(semaineMap).sort();
const noms = [...personnes];
if (!window.renderChart) {
  dv.paragraph("⚠️ Plugin Obsidian Charts requis.");
} else if (semaines.length === 0) {
  dv.paragraph("_Pas encore d'historique hebdomadaire._");
} else {
  window.renderChart({
    type: "line",
    data: {
      labels: semaines,
      datasets: noms.map(n => ({
        label: n,
        data: semaines.map(s => semaineMap[s][n] ?? 0),
        borderColor: colos[n] ?? "#888888",
        backgroundColor: colos[n] ?? "#888888",
        tension: 0.3,
        fill: false
      }))
    },
    options: { scales: { y: { beginAtZero: true } } }
  }, this.container);
}
```

---

## 🥧 Répartition des tâches faites

```dataviewjs
const colos = {};
for (const c of dv.pages('"Colocataires"').where(p => p.type == "colocataire")) {
  colos[c.nom] = c.couleur ?? "#888888";
}
const nb = {};
for (const p of dv.pages('"Hebdomadaire" or "Mensuel" or "Taches communes"')) {
  for (const t of p.file.tasks) {
    if (t.completed && t.par) {
      const n = String(t.par).trim();
      nb[n] = (nb[n] || 0) + 1;
    }
  }
}
const noms = Object.keys(nb);
if (!window.renderChart) {
  dv.paragraph("⚠️ Plugin Obsidian Charts requis.");
} else if (noms.length === 0) {
  dv.paragraph("_Pas encore de données._");
} else {
  window.renderChart({
    type: "doughnut",
    data: {
      labels: noms,
      datasets: [{ data: noms.map(n => nb[n]), backgroundColor: noms.map(n => colos[n] ?? "#888888") }]
    }
  }, this.container);
}
```

---

## ⏳ Tâches en attente cette semaine

```dataviewjs
const sem = dv.luxon.DateTime.now().toFormat("kkkk-'W'WW");
const page = dv.pages('"Hebdomadaire"').where(p => p.semaine == sem).first();
if (!page) {
  dv.paragraph(`_Pas encore de note pour la semaine ${sem}. Crée-la depuis le modèle._`);
} else {
  const restant = page.file.tasks.filter(t => !t.completed);
  if (restant.length === 0) dv.paragraph("🎉 Tout est fait pour cette semaine !");
  else dv.taskList(restant, false);
}
```

## ⏳ Mensuel restant
```dataview
TASK
FROM "Mensuel"
WHERE !completed
```
