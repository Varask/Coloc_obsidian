<%*
await tp.file.rename(tp.date.now("YYYY-MM"));
const colos = ["Guillaume 🦊", "Elsa 🛥️", "Azza 🐝", "Axel 🦋"];
const vals  = ["Guillaume",    "Elsa",     "Azza",    "Axel"];
const pick  = async (label) => await tp.system.suggester(colos, vals, false, label);

const vitres   = await pick("🪟 Vitres : qui ?");
const frigo    = await pick("❄️ Frigo (vider + nettoyer) : qui ?");
const four     = await pick("🍳 Four : qui ?");
const machine  = await pick("🫧 Machine à laver (détartrage) : qui ?");
const placards = await pick("🗄️ Placards : qui ?");

const mois = tp.date.now("YYYY-MM");

const lignesTaches = [
  ["🪟 Vitres",                       vitres],
  ["❄️ Frigo (vider + nettoyer)",    frigo],
  ["🍳 Four",                          four],
  ["🫧 Machine à laver — détartrage", machine],
  ["🗄️ Placards",                    placards],
].map(([t, r]) => `  <tr><td>${t}</td><td>${r}</td><td style="text-align:center">☐</td><td></td></tr>`).join("\n");

const feuille = `# 📋 Feuille mensuelle ${mois}

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
`;
await tp.file.create_new(feuille, `${mois} — feuille papier`, false, app.vault.getAbstractFileByPath("Mensuel"));
-%>
---
type: mensuel
mois: <% tp.date.now("YYYY-MM") %>
---

# 🗓️ Mois <% tp.date.now("YYYY-MM") %>

## ✅ Grand ménage mensuel

- [ ] Vitres [par:: <% vitres %>] [pts:: 20]
- [ ] Frigo vider + nettoyer [par:: <% frigo %>] [pts:: 25]
- [ ] Four [par:: <% four %>] [pts:: 25]
- [ ] Machine à laver — détartrage [par:: <% machine %>] [pts:: 20]
- [ ] Placards [par:: <% placards %>] [pts:: 15]

## 📊 Bilan du mois
```dataviewjs
const tasks = dv.current().file.tasks;
const pts = {};
for (const t of tasks) {
  if (t.completed && t.par && t.pts) pts[t.par] = (pts[t.par]||0) + Number(t.pts);
}
const noms = Object.keys(pts);
if (noms.length === 0) { dv.paragraph("_Aucune tâche mensuelle cochée pour l'instant._"); }
else { dv.table(["Colocataire", "Points mois"], noms.sort((a,b)=>pts[b]-pts[a]).map(n => [n, pts[n]])); }
```
