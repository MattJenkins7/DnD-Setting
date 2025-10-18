---
rapport:
  BobTheBarkeep:
    player1: 10
    player2: 2
    player3: 10
    player4: 10
  AriaTheWizard:
    player1: 10
    player2: 10
    player3: 10
    player4: 10
  CaptainThorn:
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

async function updateRapport(npc, player, delta, displayEl) {
  let newValue = 0;

  await app.fileManager.processFrontMatter(currentFile, (fm) => {
    if (!(RAPPORT_KEY in fm)) fm[RAPPORT_KEY] = {};
    if (!(npc in fm[RAPPORT_KEY])) fm[RAPPORT_KEY][npc] = {};
    if (!(player in fm[RAPPORT_KEY][npc])) fm[RAPPORT_KEY][npc][player] = 0;

    newValue = fm[RAPPORT_KEY][npc][player] + delta;
    if (newValue > MAX_VALUE) newValue = MAX_VALUE;
    if (newValue < MIN_VALUE) newValue = MIN_VALUE;

    fm[RAPPORT_KEY][npc][player] = newValue;
  });

  displayEl.textContent = `${player}: ${newValue}`;
}

function renderNpc(npcName, npcData) {
  const container = dv.el("div", "", {
    cls: "npc-rapport",
    style: "margin-bottom: 12px; padding: 6px; border: 1px solid var(--background-modifier-border); border-radius: 6px;"
  });

  dv.el("h3", npcName, { parent: container });

  Object.keys(npcData).forEach(player => {
    const row = dv.el("div", "", {
      cls: "rapport-row",
      parent: container,
      style: "margin-bottom: 4px; display: flex; align-items: center; gap: 10px;"
    });

    const valueSpan = dv.el("span", `${player}: ${npcData[player]}`, { parent: row });

    const btnContainer = dv.el("div", "", { parent: row, style: "display: flex; gap: 6px;" });

	const minusBtn = dv.el("button", "-", { parent: btnContainer });
    minusBtn.onclick = async () => updateRapport(npcName, player, -1, valueSpan);

    const plusBtn = dv.el("button", "+", { parent: btnContainer });
    plusBtn.onclick = async () => updateRapport(npcName, player, 1, valueSpan);
  });
}

Object.entries(rapport).forEach(([npcName, npcData]) => renderNpc(npcName, npcData));

```
