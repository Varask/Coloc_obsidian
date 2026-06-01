<%*
await tp.file.rename(tp.date.now("YYYY-MM"));
const colos = ["Guillaume 🦊", "Elsa 🛥️", "Azza 🐝", "Axel 🦋"];
const vals  = ["Guillaume",    "Elsa",     "Azza",    "Axel"];
const pick  = async (label) => await tp.system.suggester(colos, vals, false, label);

// Grand ménage
const vitres   = await pick("🪟 Vitres : qui ?");
const frigo    = await pick("❄️ Frigo (vider + nettoyer) : qui ?");
const four     = await pick("🍳 Four : qui ?");
const placards = await pick("🗄️ Placards : qui ?");

// Entretien machines
const laveLinge      = await pick("👕 Lave-linge — entretien complet : qui ?");
const laveVaisselle  = await pick("🍽️ Lave-vaisselle — entretien complet : qui ?");
const filtreHotte    = await pick("💨 Hotte — filtre : qui ?");

const mois = tp.date.now("YYYY-MM");

const row = ([t, r]) => `  <tr><td>${t}</td><td>${r}</td><td style="text-align:center">☐</td><td></td></tr>`;

const lignesMenage = [
  ["🪟 Vitres",                    vitres],
  ["❄️ Frigo (vider + nettoyer)", frigo],
  ["🍳 Four",                      four],
  ["🗄️ Placards",                 placards],
].map(row).join("\n");

const lignesMachines = [
  ["👕 Lave-linge (détartrage + tiroir + joint)", laveLinge],
  ["🍽️ Lave-vaisselle (filtre + bras rotatifs)", laveVaisselle],
  ["💨 Hotte (filtre)",                           filtreHotte],
].map(row).join("\n");

const tableHeader = (cols) => `<table style="width:100%; border-collapse:collapse;">
  <thead>
    <tr>
      ${cols.map(([label, w]) => `<th style="text-align:left; border-bottom:2px solid #000; width:${w}">${label}</th>`).join("\n      ")}
    </tr>
  </thead>`;

const feuille = `# 📋 Feuille mensuelle ${mois}

## 🧹 Grand ménage

${tableHeader([["Tâche","45%"],["Responsable","25%"],["Fait ?","10%"],["Signature","20%"]])}
  <tbody>
${lignesMenage}
  </tbody>
</table>

---

## ⚙️ Entretien des machines

${tableHeader([["Tâche","45%"],["Responsable","25%"],["Fait ?","10%"],["Signature","20%"]])}
  <tbody>
${lignesMachines}
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

## 🧹 Grand ménage mensuel

- [ ] Vitres [par:: <% vitres %>] [pts:: 20]
- [ ] Frigo vider + nettoyer [par:: <% frigo %>] [pts:: 25]
- [ ] Four [par:: <% four %>] [pts:: 25]
- [ ] Placards [par:: <% placards %>] [pts:: 15]

## ⚙️ Entretien des machines

- [ ] Lave-linge — entretien complet [par:: <% laveLinge %>] [pts:: 20]
- [ ] Lave-vaisselle — entretien complet [par:: <% laveVaisselle %>] [pts:: 15]
- [ ] Hotte — filtre [par:: <% filtreHotte %>] [pts:: 10]

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
