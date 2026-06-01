<%*
await tp.file.rename(tp.date.now("gggg-[W]ww"));
const colos = ["Guillaume 🦊", "Elsa 🛥️", "Azza 🐝", "Axel 🦋"];
const vals  = ["Guillaume",    "Elsa",     "Azza",    "Axel"];
const pick  = async (label) => await tp.system.suggester(colos, vals, false, label);

const t_mardi    = await pick("🚽 Toilettes — Mardi : qui ?");
const t_vendredi = await pick("🚽 Toilettes — Vendredi : qui ?");
const sol1       = await pick("🧹 Sol javel #1 : qui ?");
const sol2       = await pick("🧹 Sol javel #2 : qui ?");
const asp1       = await pick("🌀 Aspirateur #1 : qui ?");
const asp2       = await pick("🌀 Aspirateur #2 : qui ?");
const poubelle   = await pick("🗑️ Vider la poubelle : qui ?");
const torchons   = await pick("🧺 Laver les torchons : qui ?");
const vaisselle  = await pick("🍽️ Lave-vaisselle (lancer + vider) : qui ?");
const linge      = await pick("👕 Lave-linge (lancer + étendre) : qui ?");

const sem = tp.date.now("gggg-[W]ww");

const lignesTaches = [
  ["🚽 Toilettes — Mardi",       t_mardi],
  ["🚽 Toilettes — Vendredi",    t_vendredi],
  ["🧹 Sol javel #1",            sol1],
  ["🧹 Sol javel #2",            sol2],
  ["🌀 Aspirateur #1",           asp1],
  ["🌀 Aspirateur #2",           asp2],
  ["🗑️ Poubelle",               poubelle],
  ["🧺 Torchons",                torchons],
  ["🍽️ Lave-vaisselle",         vaisselle],
  ["👕 Lave-linge",              linge],
].map(([t, r]) => `  <tr><td>${t}</td><td>${r}</td><td style="text-align:center">☐</td><td></td></tr>`).join("\n");

const lignesPresence = vals.map((n, i) =>
  `  <tr><td>${colos[i]}</td><td style="text-align:center">☐</td><td style="text-align:center">☐</td></tr>`
).join("\n");

const feuille = `# 📋 Feuille semaine ${sem}

## ✅ Tâches

<table style="width:100%; border-collapse:collapse;">
  <thead>
    <tr>
      <th style="text-align:left; border-bottom:2px solid #000; width:45%">Tâche</th>
      <th style="text-align:left; border-bottom:2px solid #000; width:25%">Responsable</th>
      <th style="text-align:center; border-bottom:2px solid #000; width:10%">Fait ?</th>
      <th style="text-align:left; border-bottom:2px solid #000; width:20%">Signature</th>
    </tr>
  </thead>
  <tbody>
${lignesTaches}
  </tbody>
</table>

---

## 👥 Présence cette semaine

<table style="width:100%; border-collapse:collapse;">
  <thead>
    <tr>
      <th style="text-align:left; border-bottom:2px solid #000; width:60%">Nom</th>
      <th style="text-align:center; border-bottom:2px solid #000; width:20%">Présent(e)</th>
      <th style="text-align:center; border-bottom:2px solid #000; width:20%">Absent(e)</th>
    </tr>
  </thead>
  <tbody>
${lignesPresence}
  </tbody>
</table>
`;
await tp.file.create_new(feuille, `${sem} — feuille papier`, false, app.vault.getAbstractFileByPath("Hebdomadaire"));
-%>
---
type: hebdo
semaine: <% tp.date.now("gggg-[W]ww") %>
debut: <% tp.date.now("YYYY-MM-DD") %>
responsable: 
---

# 📆 Semaine <% tp.date.now("gggg-[W]ww") %>

> Responsable de la semaine : 

## ✅ Tâches de la semaine

### Toilettes (2×)
- [ ] Toilettes — Mardi [par:: <% t_mardi %>] [pts:: 10] 
- [ ] Toilettes — Vendredi [par:: <% t_vendredi %>] [pts:: 10] 

### Sols (2×)
- [ ] Sol javel #1 [par:: <% sol1 %>] [pts:: 15] 
- [ ] Sol javel #2 [par:: <% sol2 %>] [pts:: 15] 

### Aspirateur (2×)
- [ ] Aspirateur #1 [par:: <% asp1 %>] [pts:: 15] 
- [ ] Aspirateur #2 [par:: <% asp2 %>] [pts:: 15] 

### Divers
- [ ] Vider la poubelle (quand pleine) [par:: <% poubelle %>] [pts:: 5] 
- [ ] Laver les torchons [par:: <% torchons %>] [pts:: 5] 
- [ ] Lave-vaisselle (lancer + vider) [par:: <% vaisselle %>] [pts:: 8] 
- [ ] Lave-linge (lancer + étendre) [par:: <% linge %>] [pts:: 8] 

## 📊 Bilan de la semaine
```dataviewjs
const tasks = dv.current().file.tasks;
const pts = {};
for (const t of tasks) {
  if (t.completed && t.par && t.pts) pts[t.par] = (pts[t.par]||0) + Number(t.pts);
}
const noms = Object.keys(pts);
if (noms.length === 0) { dv.paragraph("_Aucune tâche cochée pour l'instant._"); }
else {
  dv.table(["Colocataire", "Points semaine"], noms.sort((a,b)=>pts[b]-pts[a]).map(n => [n, pts[n]]));
}
```
