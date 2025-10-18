---
rapport:
  player1: 10
  player2: 10
  player3: 10
  player4: 10
---

```dataviewjs
const RAPPORT_KEY = "rapport";
const MAX_VALUE = 20;
const MIN_VALUE = 0;

const current = dv.current();
const currentFile = app.vault.getAbstractFileByPath(current.file.path);
const rapport = current[RAPPORT_KEY] ?? {};

async function updateRapport(player, delta, displayEl) {
  let newValue = 0;

  await app.fileManager.processFrontMatter(currentFile, (fm) => {
    if (!(RAPPORT_KEY in fm)) fm[RAPPORT_KEY] = {};
    if (!(player in fm[RAPPORT_KEY])) fm[RAPPORT_KEY][player] = 0;

    newValue = fm[RAPPORT_KEY][player] + delta;
    if (newValue > MAX_VALUE) newValue = MAX_VALUE;
    if (newValue < MIN_VALUE) newValue = MIN_VALUE;

    fm[RAPPORT_KEY][player] = newValue;
  });

  displayEl.textContent = `${player}: ${newValue}`;
}

function renderPlayer(player) {
  const currentValue = rapport[player] ?? 0;

  const row = dv.el("div", "", {
    cls: "rapport-row",
    style: "margin-bottom: 4px; display: flex; align-items: center; gap: 6px;"
  });

  const valueSpan = dv.el("span", `${player}: ${currentValue}`, { parent: row });

  const plusBtn = dv.el("button", "+", { parent: row });
  plusBtn.onclick = async () => updateRapport(player, 1, valueSpan);

  const minusBtn = dv.el("button", "-", { parent: row });
  minusBtn.onclick = async () => updateRapport(player, -1, valueSpan);
}

Object.keys(rapport).forEach(renderPlayer);

```


Aephas Gallinaas is an Elven merchant and local leader of the [[Planes/Aryn (Material)/Continents/Eug/States/Syl'Vitara/Factions/The Coinwrights|The Coinwrights]] in Enathera. 