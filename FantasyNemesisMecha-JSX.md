# FantasyNemesisMecha-JSX

<!-- TINS Specification v1.0 -->
<!-- ZS:COMPLEXITY:HIGH -->
<!-- ZS:PRIORITY:HIGH -->
<!-- ZS:PLATFORM:WEB -->
<!-- ZS:LANGUAGE:JAVASCRIPT -->

## Description

FantasyNemesisMecha is a single-file interactive demo that visualises the **Nemesis System** concept — inspired by *Shadow of Mordor* — reframed as a **Zeon Fantasy Football Manager** set in the Universal Century Gundam universe. The player manages a Zeon ace-pilot roster and engages in simulated sorties (matches) against a Federation squad. When the player loses a sortie, the Federation pilot who defeated them is **promoted**, awarded higher-grade equipment or experimental mobile suits, and returns as a persistent antagonist across subsequent sorties with escalating stats and menace dialogue.

The demo has **no AI or LLM components**. All logic is deterministic rule-based simulation. The UI is styled as a retro military-green CRT football-manager terminal — think FM97 crossed with a Principality of Zeon mission briefing screen. The purpose is to serve as a grounding / pitch document for a full game implementation by showing the emotional and mechanical loop of the Nemesis System in a compact, self-contained JSX component rendered inside a single HTML5 file.

The target audience is game designers and developers evaluating whether the Nemesis concept translates outside of action games into strategy / management contexts.

---

## Functionality

### Core Concept Loop

```
Player selects Zeon pilots → Sortie (simulated match) → Player wins or loses
         ↑                                                        ↓
  Roster upgrades ←── Zeon pilot XP gain           Federation nemesis promoted
                                                   + new mech tier awarded
                                                   + revenge dialogue unlocked
                                                   + returns in next sortie
```

### Screen Layout (ASCII)

```
╔══════════════════════════════════════════════════════════════════════╗
║  ⚡ ZEON NEMESIS SYSTEM  ·  OPERATION BRITISH  ·  UC 0079           ║
╠══════════════════════════════════════════════════════════════════════╣
║  [ROSTER]          [SORTIE LOG]         [NEMESIS BOARD]             ║
╠═══════════════════════════╦═════════════════════════════════════════╣
║  ZEON PILOTS (your squad) ║  NEMESIS TRACKER                       ║
║  ┌──────────────────────┐ ║  ┌───────────────────────────────────┐ ║
║  │ ★ Char Aznable       │ ║  │ [!] LT. GENE COLBY  [RANK: ACE]   │ ║
║  │   RK: ACE  ATK:88    │ ║  │   Defeated you: 2x               │ ║
║  │   Mech: ZAKU II S    │ ║  │   Mech: GM COMMAND (upgraded)    │ ║
║  │   [SELECT]           │ ║  │   "I remember you, Zeon dog."    │ ║
║  └──────────────────────┘ ║  └───────────────────────────────────┘ ║
║  ┌──────────────────────┐ ║                                        ║
║  │  Gaia               │ ║  SORTIE HISTORY                        ║
║  │   RK: VETERAN ATK:72│ ║  > Sortie 3: DEFEAT  (Colby won)      ║
║  │   Mech: DOM         │ ║  > Sortie 2: VICTORY                  ║
║  │   [SELECT]           │ ║  > Sortie 1: DEFEAT  (Colby won)     ║
║  └──────────────────────┘ ║                                        ║
╠═══════════════════════════╩═════════════════════════════════════════╣
║  [▶ LAUNCH SORTIE]   [⟳ RESET CAMPAIGN]   SCORE: 1W / 2L          ║
╚══════════════════════════════════════════════════════════════════════╝
```

### Panels

**ROSTER Panel (left)**
- Displays 4 selectable Zeon pilots, each card showing: name, rank, ATK stat, DEF stat, current mobile suit name and tier (I–IV), XP bar.
- Player clicks "SELECT" to toggle a pilot into the active sortie lineup (max 3 active).
- Selected pilots glow with a green border.

**NEMESIS BOARD Panel (right-top)**
- Lists all Federation pilots who have ever defeated the player (up to 4 tracked).
- Each nemesis card shows: name, current Federation rank, number of victories over the player, current mech (upgrades each time they win), and a revenge dialogue quote.
- If a nemesis has not yet appeared, the slot shows "— NO DATA —".
- Nemesis cards pulse with a red border if they are present in the upcoming sortie.

**SORTIE LOG (right-bottom)**
- Scrollable list of past sortie results: outcome (VICTORY / DEFEAT), which nemesis was involved, date (turn number).

**Action Bar (bottom)**
- "LAUNCH SORTIE" button: triggers one simulated sortie.
- "RESET CAMPAIGN" button: resets all state to initial.
- Win/loss record display.

### Sortie Simulation (no AI — rule-based)

1. The system selects a Federation squad for this sortie: 3 Federation pilots drawn from a pool of 6. If any tracked nemesis pilots exist, at least one nemesis is guaranteed to appear in the Federation squad.
2. Each pilot has ATK, DEF, and SPD stats. Sortie outcome is determined by comparing the sum of active Zeon pilot ATK minus the sum of Federation pilot DEF, modified by a small seeded random jitter (±10%).
3. If Zeon total > Federation total → **VICTORY**. If Federation total ≥ Zeon total → **DEFEAT**.
4. On DEFEAT: the Federation pilot with the highest individual ATK in the winning squad is identified as the "defeater". That pilot becomes a nemesis (if not already tracked). Their nemesis record is updated: win count +1, rank promoted one tier, mech upgraded one tier.
5. On VICTORY: active Zeon pilots each gain XP. If XP threshold met, pilot rank upgrades.
6. All events are logged to the Sortie Log.

### Nemesis Progression

Each time a Federation pilot defeats the player, they are promoted through the following ranks:
`ROOKIE → VETERAN → ACE → ELITE ACE → NEMESIS`

Their mobile suit upgrades through tiers:
`Tier I: GM Sniper → Tier II: GM Command → Tier III: RX-78-2 Gundam → Tier IV: Full Armour Gundam`

Each tier change unlocks a new revenge dialogue quote specific to that tier (see Data Models).

### Player Rank Progression (Zeon Pilots)

XP thresholds per rank:
`CADET (0 XP) → SOLDIER (100 XP) → VETERAN (250 XP) → ACE (500 XP) → RED COMET (900 XP)`

When a pilot's rank increases, their ATK and DEF stats increase by 5 each.

### Win/Loss & Visual Feedback

- On VICTORY: a flash of green overlays the screen briefly, victory message appears in the log.
- On DEFEAT: a flash of red overlays the screen, the nemesis card animates (shakes/glows), the new nemesis rank and equipment appears with a dramatic "PROMOTED" badge.
- If the player defeats a nemesis in a VICTORY sortie, the nemesis card displays "ELIMINATED" and is removed from active tracking (a new Federation pilot may take their place in future sorties).

---

## Technical Implementation

### Technology

- Single HTML5 file containing one `<script type="text/babel">` block.
- React 18 loaded from CDN (unpkg).
- Babel standalone loaded from CDN for in-browser JSX transpilation.
- Tailwind CSS loaded from CDN for utility styling.
- No external data, no backend, no localStorage — all state lives in React `useState`.

### File Structure

```
FantasyNemesisMecha.html
└── <head>
    ├── CDN: React 18 (react.development.js + react-dom.development.js)
    ├── CDN: Babel standalone
    └── CDN: Tailwind CSS
└── <body>
    └── <div id="root">
    └── <script type="text/babel">
        ├── DATA CONSTANTS  (pilots, mechs, dialogues)
        ├── UTILITY FUNCTIONS (sortie engine, stat calc)
        ├── COMPONENTS
        │   ├── PilotCard
        │   ├── NemesisCard
        │   ├── SortieLog
        │   └── App (root)
        └── ReactDOM.createRoot(...).render(<App />)
```

### CDN URLs (exact)

```html
<script src="https://unpkg.com/react@18/umd/react.development.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
<script src="https://cdn.tailwindcss.com"></script>
```

### Data Models

#### ZeonPilot

```javascript
{
  id: string,               // e.g. "char", "gaia", "ortega", "mash"
  name: string,             // Display name e.g. "Char Aznable"
  rank: "CADET" | "SOLDIER" | "VETERAN" | "ACE" | "RED COMET",
  xp: number,               // 0–999
  atk: number,              // 50–100
  def: number,              // 40–90
  spd: number,              // 40–90
  mechName: string,         // e.g. "ZAKU II S TYPE"
  mechTier: 1 | 2 | 3 | 4, // affects visual badge colour
  selected: boolean         // is this pilot in the active sortie lineup
}
```

#### FederationPilot (pool, never displayed directly in roster — only in Nemesis Board if nemesis)

```javascript
{
  id: string,               // e.g. "colby", "burns", "reed", "white", "bright", "amuro"
  name: string,
  baseAtk: number,
  baseDef: number,
  baseSpd: number,
  mechPool: string[]        // array of 4 mech names, index = tier-1
}
```

#### NemesisRecord (tracked per Federation pilot who has beaten player)

```javascript
{
  fedPilotId: string,       // references FederationPilot.id
  name: string,
  wins: number,             // how many times they have beaten the player
  rank: "ROOKIE" | "VETERAN" | "ACE" | "ELITE ACE" | "NEMESIS",
  mechTier: 1 | 2 | 3 | 4,
  mechName: string,         // derived from mechPool[mechTier-1]
  atk: number,              // baseAtk + (wins * 5)
  def: number,              // baseDef + (wins * 5)
  dialogueIndex: number,    // 0–3, indexes into dialoguePool
  eliminated: boolean       // true if player beat them while they were active nemesis
}
```

#### SortieLogEntry

```javascript
{
  turn: number,
  outcome: "VICTORY" | "DEFEAT",
  details: string           // e.g. "Defeated by LT. GENE COLBY · Promoted to ACE"
}
```

### Initial State Constants

#### Zeon Pilot Pool (4 pilots)

```javascript
const ZEON_PILOTS_INITIAL = [
  { id: "char",   name: "Char Aznable",  rank: "ACE",     xp: 500, atk: 88, def: 75, spd: 95, mechName: "ZAKU II S TYPE",  mechTier: 3, selected: false },
  { id: "gaia",   name: "Gaia",          rank: "VETERAN", xp: 250, atk: 72, def: 68, spd: 70, mechName: "DOM",             mechTier: 2, selected: false },
  { id: "ortega", name: "Ortega",        rank: "VETERAN", xp: 260, atk: 75, def: 80, spd: 65, mechName: "DOM",             mechTier: 2, selected: false },
  { id: "mash",   name: "Mash",          rank: "SOLDIER", xp: 120, atk: 65, def: 62, spd: 68, mechName: "ZAKU II",         mechTier: 1, selected: false }
]
```

#### Federation Pilot Pool (6 pilots)

```javascript
const FED_PILOTS = [
  { id:"colby",  name:"LT. GENE COLBY",   baseAtk:60, baseDef:55, baseSpd:60,
    mechPool:["GM","GM COMMAND","RX-78-2 GUNDAM","FULL ARMOUR GUNDAM"] },
  { id:"burns",  name:"CPT. ROY BURNS",   baseAtk:65, baseDef:58, baseSpd:62,
    mechPool:["GM SNIPER","GM CANNON","G-FIGHTER","CORE BOOSTER"] },
  { id:"reed",   name:"ENS. SARA REED",   baseAtk:55, baseDef:60, baseSpd:70,
    mechPool:["GM","GM LIGHT ARMOUR","RX-78-2 GUNDAM","G-ARMOUR"] },
  { id:"white",  name:"MAJ. ALAN WHITE",  baseAtk:70, baseDef:65, baseSpd:55,
    mechPool:["GM COMMAND","BALL (CUSTOM)","GM CANNON II","FULL ARMOUR GUNDAM"] },
  { id:"bright", name:"CDR. BRIGHT NOA",  baseAtk:58, baseDef:72, baseSpd:58,
    mechPool:["GM","GM SNIPER II","RX-78-2 GUNDAM","G-ARMOUR II"] },
  { id:"amuro",  name:"ENS. AMURO RAY",   baseAtk:85, baseDef:70, baseSpd:92,
    mechPool:["RX-78-2 GUNDAM","RX-78-2 GUNDAM (A)","FULL ARMOUR GUNDAM","RX-93 ν GUNDAM"] }
]
```

#### Nemesis Rank Progression Array

```javascript
const NEMESIS_RANKS = ["ROOKIE","VETERAN","ACE","ELITE ACE","NEMESIS"]
```

#### Nemesis Dialogue Pool (per tier 0–3)

Each Federation pilot shares this pool, indexed by `dialogueIndex` = `min(mechTier - 1, 3)`:

```javascript
const NEMESIS_DIALOGUES = [
  "\"You got lucky last time, Zeon scum.\"",
  "\"I remember you. I've been waiting for this.\"",
  "\"Your tricks won't save you now. Not with this machine.\"",
  "\"This ends here. For the Earth Federation. For everyone you killed.\""
]
```

### Sortie Engine Algorithm

```javascript
function runSortie(zeonPilots, fedPilots, nemesisRecords, turn) {
  // 1. Get selected Zeon pilots (max 3)
  const activeZeon = zeonPilots.filter(p => p.selected).slice(0, 3)
  if (activeZeon.length === 0) return { error: "SELECT AT LEAST ONE PILOT" }

  // 2. Build Federation squad: guarantee nemesis presence
  const activeNemeses = nemesisRecords.filter(n => !n.eliminated)
  let fedSquad = []
  if (activeNemeses.length > 0) {
    // Pick the highest-wins nemesis
    const topNemesis = activeNemeses.sort((a,b) => b.wins - a.wins)[0]
    fedSquad.push({ ...FED_PILOTS.find(f => f.id === topNemesis.fedPilotId), nemesisBoost: topNemesis })
  }
  // Fill remaining Federation squad slots from non-nemesis pool
  const remainingFed = fedPilots.filter(f => !fedSquad.find(s => s.id === f.id))
  shuffle(remainingFed)
  while (fedSquad.length < 3) fedSquad.push(remainingFed.pop())

  // 3. Calculate totals with jitter
  const jitter = () => 0.9 + Math.random() * 0.2   // 0.9–1.1
  const zeonTotal = activeZeon.reduce((sum, p) => sum + p.atk, 0) * jitter()
  const fedTotal = fedSquad.reduce((sum, f) => {
    const boost = f.nemesisBoost ? f.nemesisBoost.atk : f.baseAtk
    return sum + boost
  }, 0) * jitter()

  // 4. Determine outcome
  const victory = zeonTotal > fedTotal

  let newNemesisRecords = [...nemesisRecords]
  let updatedZeonPilots = [...zeonPilots]
  let logEntry = { turn, outcome: victory ? "VICTORY" : "DEFEAT", details: "" }
  let promotedNemesis = null

  if (!victory) {
    // Find defeater: highest ATK Federation pilot in squad
    const defeater = fedSquad.reduce((top, f) => {
      const atk = f.nemesisBoost ? f.nemesisBoost.atk : f.baseAtk
      const topAtk = top.nemesisBoost ? top.nemesisBoost.atk : top.baseAtk
      return atk > topAtk ? f : top
    })

    // Update or create nemesis record
    const existingIdx = newNemesisRecords.findIndex(n => n.fedPilotId === defeater.id)
    if (existingIdx >= 0) {
      const rec = { ...newNemesisRecords[existingIdx] }
      rec.wins += 1
      rec.mechTier = Math.min(4, rec.mechTier + 1)
      rec.mechName = defeater.mechPool[rec.mechTier - 1]
      rec.atk = defeater.baseAtk + (rec.wins * 5)
      rec.def = defeater.baseDef + (rec.wins * 5)
      const rankIdx = Math.min(rec.wins, NEMESIS_RANKS.length - 1)
      rec.rank = NEMESIS_RANKS[rankIdx]
      rec.dialogueIndex = Math.min(rec.mechTier - 1, 3)
      newNemesisRecords[existingIdx] = rec
      promotedNemesis = rec
    } else if (newNemesisRecords.filter(n => !n.eliminated).length < 4) {
      const newRec = {
        fedPilotId: defeater.id,
        name: defeater.name,
        wins: 1,
        rank: "ROOKIE",
        mechTier: 1,
        mechName: defeater.mechPool[0],
        atk: defeater.baseAtk + 5,
        def: defeater.baseDef + 5,
        dialogueIndex: 0,
        eliminated: false
      }
      newNemesisRecords.push(newRec)
      promotedNemesis = newRec
    }
    logEntry.details = `Defeated by ${defeater.name} · ${promotedNemesis?.rank ?? "ROOKIE"} · Now piloting ${promotedNemesis?.mechName}`
  } else {
    // Victory: grant XP to active Zeon pilots; check if any nemesis was in the fed squad and eliminate
    updatedZeonPilots = zeonPilots.map(p => {
      if (!p.selected) return p
      const newXp = p.xp + 80
      let newRank = p.rank
      let newAtk = p.atk
      let newDef = p.def
      const rankThresholds = [0,100,250,500,900]
      const rankNames = ["CADET","SOLDIER","VETERAN","ACE","RED COMET"]
      const newRankIdx = rankThresholds.filter(t => newXp >= t).length - 1
      const newRankName = rankNames[Math.min(newRankIdx, rankNames.length-1)]
      if (newRankName !== p.rank) { newAtk = p.atk + 5; newDef = p.def + 5 }
      return { ...p, xp: newXp, rank: newRankName, atk: newAtk, def: newDef }
    })
    // Eliminate nemeses present in this sortie
    newNemesisRecords = newNemesisRecords.map(n => {
      if (fedSquad.find(f => f.id === n.fedPilotId)) return { ...n, eliminated: true }
      return n
    })
    const eliminated = newNemesisRecords.filter(n => n.eliminated && fedSquad.find(f => f.id === n.fedPilotId))
    logEntry.details = eliminated.length > 0
      ? `Victory! Eliminated: ${eliminated.map(n=>n.name).join(", ")}`
      : "Victory! All Federation pilots repelled."
  }

  return { victory, updatedZeonPilots, newNemesisRecords, logEntry, promotedNemesis }
}
```

### Component Tree

```
<App>
  ├── state: pilots (ZeonPilot[]), nemesisRecords (NemesisRecord[]),
  │          sortieLog (SortieLogEntry[]), turn (number),
  │          wins (number), losses (number), flashOutcome (null|"VICTORY"|"DEFEAT"),
  │          promotedNemesis (NemesisRecord|null)
  │
  ├── <header> — title bar with Zeon logo text + operation name
  ├── <TabBar> — [ROSTER] [SORTIE LOG] [NEMESIS BOARD] tab switcher (mobile)
  │                (on desktop all three panels visible simultaneously in grid)
  ├── <PilotRoster>
  │   └── <PilotCard> × 4  (clickable, toggles selected state)
  ├── <NemesisBoard>
  │   └── <NemesisCard> × 4 slots  (shows nemesis or "NO DATA")
  ├── <SortieLogPanel>
  │   └── scrollable list of SortieLogEntry
  ├── <ActionBar>
  │   ├── <LaunchButton>   (disabled if 0 pilots selected)
  │   ├── <ResetButton>
  │   └── <ScoreDisplay>
  └── <FlashOverlay>  (full-screen colour flash on outcome, fades out in 1s)
```

### Visual Style Specification

All styling via Tailwind utility classes. Colour palette:

| Token | Tailwind class | Hex |
|---|---|---|
| Background | `bg-black` | #000000 |
| Panel BG | `bg-zinc-900` | #18181b |
| Border | `border-green-700` | #15803d |
| Text primary | `text-green-400` | #4ade80 |
| Text dim | `text-green-700` | #15803d |
| Accent victory | `text-green-300` | #86efac |
| Accent defeat | `text-red-500` | #ef4444 |
| Nemesis glow | `ring-2 ring-red-600 animate-pulse` | #dc2626 |
| Selected pilot | `ring-2 ring-green-400` | #4ade80 |
| Button | `bg-green-800 hover:bg-green-600 text-green-100` | — |

Font: monospace (`font-mono`) throughout. All text uppercase except dialogue quotes.

Panel borders: `border border-green-700 rounded-none` (sharp corners, military terminal aesthetic).

### XP Bar Component

Render as a series of block characters `█` and `░` proportional to XP progress toward next rank threshold. Width: 10 characters total. Example: 60% = `██████░░░░`.

### "PROMOTED" Badge Animation

When a nemesis is promoted after a DEFEAT, the NemesisCard for that nemesis should briefly display a flashing `[!! PROMOTED !!]` badge in `text-yellow-400 animate-bounce` for 2 seconds, then revert to normal display.

### Accessibility

- All interactive elements have `role="button"` or are native `<button>` elements.
- Colour is not the sole indicator of state (icons and text labels accompany colour changes).
- `aria-label` on all icon-only buttons.

---

## Complete Implementation

Below is the full, ready-to-run HTML5 file. Save as `FantasyNemesisMecha.html` and open in any modern browser.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>⚡ ZEON NEMESIS SYSTEM</title>
  <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { background: #000; margin: 0; padding: 0; }
    @keyframes crtFlicker { 0%,100%{opacity:1} 50%{opacity:0.97} }
    .crt { animation: crtFlicker 4s infinite; }
    ::-webkit-scrollbar { width: 6px; }
    ::-webkit-scrollbar-thumb { background: #15803d; }
    ::-webkit-scrollbar-track { background: #000; }
  </style>
</head>
<body>
  <div id="root"></div>
  <script type="text/babel">
    const { useState, useEffect, useCallback } = React;

    // ─────────────────────────────────────────────
    // CONSTANTS
    // ─────────────────────────────────────────────

    const NEMESIS_RANKS = ["ROOKIE","VETERAN","ACE","ELITE ACE","NEMESIS"];
    const NEMESIS_DIALOGUES = [
      '"You got lucky last time, Zeon scum."',
      '"I remember you. I\'ve been waiting for this."',
      '"Your tricks won\'t save you now. Not with this machine."',
      '"This ends here. For the Earth Federation. For everyone you killed."'
    ];
    const RANK_XP = [0,100,250,500,900];
    const RANK_NAMES = ["CADET","SOLDIER","VETERAN","ACE","RED COMET"];

    const FED_PILOTS = [
      { id:"colby",  name:"LT. GENE COLBY",  baseAtk:60, baseDef:55, baseSpd:60, mechPool:["GM","GM COMMAND","RX-78-2 GUNDAM","FULL ARMOUR GUNDAM"] },
      { id:"burns",  name:"CPT. ROY BURNS",  baseAtk:65, baseDef:58, baseSpd:62, mechPool:["GM SNIPER","GM CANNON","G-FIGHTER","CORE BOOSTER"] },
      { id:"reed",   name:"ENS. SARA REED",  baseAtk:55, baseDef:60, baseSpd:70, mechPool:["GM","GM LIGHT ARMOUR","RX-78-2 GUNDAM","G-ARMOUR"] },
      { id:"white",  name:"MAJ. ALAN WHITE", baseAtk:70, baseDef:65, baseSpd:55, mechPool:["GM COMMAND","BALL CUSTOM","GM CANNON II","FULL ARMOUR GUNDAM"] },
      { id:"bright", name:"CDR. BRIGHT NOA", baseAtk:58, baseDef:72, baseSpd:58, mechPool:["GM","GM SNIPER II","RX-78-2 GUNDAM","G-ARMOUR II"] },
      { id:"amuro",  name:"ENS. AMURO RAY",  baseAtk:85, baseDef:70, baseSpd:92, mechPool:["RX-78-2 GUNDAM","RX-78-2 GUNDAM-A","FULL ARMOUR GUNDAM","RX-93 ν GUNDAM"] }
    ];

    const INITIAL_PILOTS = [
      { id:"char",   name:"CHAR AZNABLE",  rank:"ACE",     xp:500, atk:88, def:75, spd:95, mechName:"ZAKU II S TYPE", mechTier:3, selected:false },
      { id:"gaia",   name:"GAIA",          rank:"VETERAN", xp:250, atk:72, def:68, spd:70, mechName:"DOM",            mechTier:2, selected:false },
      { id:"ortega", name:"ORTEGA",        rank:"VETERAN", xp:260, atk:75, def:80, spd:65, mechName:"DOM",            mechTier:2, selected:false },
      { id:"mash",   name:"MASH",          rank:"SOLDIER", xp:120, atk:65, def:62, spd:68, mechName:"ZAKU II",        mechTier:1, selected:false }
    ];

    // ─────────────────────────────────────────────
    // UTILITIES
    // ─────────────────────────────────────────────

    function shuffle(arr) {
      for (let i = arr.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
      return arr;
    }

    function xpBar(xp, rank) {
      const rankIdx = RANK_NAMES.indexOf(rank);
      const lo = RANK_XP[rankIdx] ?? 900;
      const hi = RANK_XP[rankIdx + 1] ?? 999;
      if (rankIdx >= RANK_NAMES.length - 1) return "██████████";
      const pct = Math.min((xp - lo) / (hi - lo), 1);
      const filled = Math.round(pct * 10);
      return "█".repeat(filled) + "░".repeat(10 - filled);
    }

    function runSortie(pilots, nemesisRecords, turn) {
      const active = pilots.filter(p => p.selected).slice(0, 3);
      if (active.length === 0) return { error: "SELECT AT LEAST ONE PILOT" };

      const activeNemeses = nemesisRecords.filter(n => !n.eliminated);
      let fedSquad = [];

      if (activeNemeses.length > 0) {
        const top = [...activeNemeses].sort((a, b) => b.wins - a.wins)[0];
        const baseP = FED_PILOTS.find(f => f.id === top.fedPilotId);
        fedSquad.push({ ...baseP, _nemesis: top });
      }

      const pool = shuffle(FED_PILOTS.filter(f => !fedSquad.find(s => s.id === f.id)));
      while (fedSquad.length < 3 && pool.length > 0) fedSquad.push(pool.pop());

      const jit = () => 0.9 + Math.random() * 0.2;
      const zeonTotal = active.reduce((s, p) => s + p.atk, 0) * jit();
      const fedTotal = fedSquad.reduce((s, f) => {
        const atk = f._nemesis ? f._nemesis.atk : f.baseAtk;
        return s + atk;
      }, 0) * jit();

      const victory = zeonTotal > fedTotal;
      let newNem = nemesisRecords.map(n => ({ ...n }));
      let newPilots = pilots.map(p => ({ ...p }));
      let promoted = null;

      if (!victory) {
        const defeater = fedSquad.reduce((top, f) => {
          const a = f._nemesis ? f._nemesis.atk : f.baseAtk;
          const ta = top._nemesis ? top._nemesis.atk : top.baseAtk;
          return a > ta ? f : top;
        });
        const idx = newNem.findIndex(n => n.fedPilotId === defeater.id);
        if (idx >= 0) {
          const rec = newNem[idx];
          rec.wins += 1;
          rec.mechTier = Math.min(4, rec.mechTier + 1);
          rec.mechName = defeater.mechPool[rec.mechTier - 1];
          rec.atk = defeater.baseAtk + rec.wins * 5;
          rec.def = defeater.baseDef + rec.wins * 5;
          rec.rank = NEMESIS_RANKS[Math.min(rec.wins, NEMESIS_RANKS.length - 1)];
          rec.dialogueIndex = Math.min(rec.mechTier - 1, 3);
          promoted = rec;
        } else if (newNem.filter(n => !n.eliminated).length < 4) {
          const rec = {
            fedPilotId: defeater.id, name: defeater.name,
            wins:1, rank:"ROOKIE", mechTier:1,
            mechName: defeater.mechPool[0],
            atk: defeater.baseAtk + 5, def: defeater.baseDef + 5,
            dialogueIndex:0, eliminated:false
          };
          newNem.push(rec);
          promoted = rec;
        }
        const details = promoted
          ? `DEFEATED BY ${promoted.name} · PROMOTED TO ${promoted.rank} · NOW PILOTING ${promoted.mechName}`
          : "DEFEAT. NO NEW NEMESIS DATA.";
        return { victory, newPilots, newNem, promoted, logEntry:{ turn, outcome:"DEFEAT", details } };
      } else {
        newPilots = pilots.map(p => {
          if (!p.selected) return p;
          const newXp = Math.min(p.xp + 80, 999);
          const rankIdx = RANK_XP.filter(t => newXp >= t).length - 1;
          const newRank = RANK_NAMES[Math.min(rankIdx, RANK_NAMES.length - 1)];
          const rankUp = newRank !== p.rank;
          return { ...p, xp: newXp, rank: newRank, atk: p.atk + (rankUp ? 5 : 0), def: p.def + (rankUp ? 5 : 0) };
        });
        newNem = newNem.map(n => {
          if (fedSquad.find(f => f.id === n.fedPilotId)) return { ...n, eliminated:true };
          return n;
        });
        const elim = newNem.filter(n => n.eliminated && fedSquad.find(f => f.id === n.fedPilotId));
        const details = elim.length > 0
          ? `VICTORY! NEMESIS ELIMINATED: ${elim.map(n => n.name).join(", ")}`
          : "VICTORY! ALL FEDERATION PILOTS REPELLED. +80 XP AWARDED.";
        return { victory, newPilots, newNem, promoted:null, logEntry:{ turn, outcome:"VICTORY", details } };
      }
    }

    // ─────────────────────────────────────────────
    // COMPONENTS
    // ─────────────────────────────────────────────

    function PilotCard({ pilot, onToggle }) {
      const tierColours = ["text-gray-400","text-blue-400","text-yellow-400","text-red-400"];
      return (
        <div
          onClick={() => onToggle(pilot.id)}
          className={`cursor-pointer border p-3 mb-2 font-mono transition-all duration-150
            ${pilot.selected
              ? "border-green-400 bg-zinc-800 ring-1 ring-green-400"
              : "border-green-900 bg-zinc-950 hover:border-green-700"}`}
        >
          <div className="flex justify-between items-center">
            <span className="text-green-300 text-sm font-bold">{pilot.name}</span>
            <span className="text-xs text-green-600">{pilot.selected ? "[ACTIVE]" : "[STANDBY]"}</span>
          </div>
          <div className="text-xs mt-1 text-green-600">
            RNK: <span className="text-green-400">{pilot.rank}</span>
            {"  "}ATK:<span className="text-green-300">{pilot.atk}</span>
            {"  "}DEF:<span className="text-green-300">{pilot.def}</span>
          </div>
          <div className={`text-xs mt-1 ${tierColours[pilot.mechTier - 1]}`}>
            [{pilot.mechName}] TIER-{pilot.mechTier}
          </div>
          <div className="text-xs mt-1 text-green-800 tracking-widest">
            XP {xpBar(pilot.xp, pilot.rank)}
          </div>
        </div>
      );
    }

    function NemesisCard({ record }) {
      const [showPromo, setShowPromo] = useState(false);
      useEffect(() => {
        if (record?._justPromoted) { setShowPromo(true); setTimeout(() => setShowPromo(false), 2500); }
      }, [record?._justPromoted, record?.wins]);

      if (!record || record.eliminated) {
        return (
          <div className="border border-red-950 bg-zinc-950 p-3 mb-2 font-mono text-xs text-red-900">
            {record?.eliminated
              ? `[ELIMINATED] ${record.name} — THREAT NEUTRALISED`
              : "— NO NEMESIS DATA —"}
          </div>
        );
      }
      return (
        <div className="border border-red-700 bg-zinc-950 p-3 mb-2 font-mono ring-1 ring-red-800">
          <div className="flex justify-between items-center">
            <span className="text-red-400 text-sm font-bold">{record.name}</span>
            {showPromo && <span className="text-yellow-400 text-xs animate-bounce">[!! PROMOTED !!]</span>}
          </div>
          <div className="text-xs mt-1 text-red-600">
            RNK: <span className="text-red-400">{record.rank}</span>
            {"  "}WINS VS YOU: <span className="text-red-300">{record.wins}</span>
          </div>
          <div className="text-xs mt-1 text-orange-500">MECH: {record.mechName} [TIER-{record.mechTier}]</div>
          <div className="text-xs mt-1 text-red-600">
            ATK:<span className="text-red-300">{record.atk}</span>
            {"  "}DEF:<span className="text-red-300">{record.def}</span>
          </div>
          <div className="text-xs mt-2 text-red-400 italic">{NEMESIS_DIALOGUES[record.dialogueIndex]}</div>
        </div>
      );
    }

    function SortieLogPanel({ log }) {
      return (
        <div className="font-mono text-xs overflow-y-auto max-h-64 space-y-1 pr-1">
          {log.length === 0 && <div className="text-green-800">— AWAITING FIRST SORTIE —</div>}
          {[...log].reverse().map((entry, i) => (
            <div key={i} className={`border-l-2 pl-2 ${entry.outcome === "VICTORY" ? "border-green-500 text-green-400" : "border-red-600 text-red-400"}`}>
              <span className="text-green-700">SORTIE {entry.turn} </span>
              <span className={entry.outcome === "VICTORY" ? "text-green-300" : "text-red-300"}>
                [{entry.outcome}]
              </span>
              <div className="text-green-700 text-xs">{entry.details}</div>
            </div>
          ))}
        </div>
      );
    }

    // ─────────────────────────────────────────────
    // APP ROOT
    // ─────────────────────────────────────────────

    function App() {
      const [pilots, setPilots] = useState(INITIAL_PILOTS.map(p => ({ ...p })));
      const [nemesisRecords, setNemesisRecords] = useState([]);
      const [sortieLog, setSortieLog] = useState([]);
      const [turn, setTurn] = useState(1);
      const [wins, setWins] = useState(0);
      const [losses, setLosses] = useState(0);
      const [flash, setFlash] = useState(null);
      const [promotedId, setPromotedId] = useState(null);
      const [errorMsg, setErrorMsg] = useState("");

      const togglePilot = (id) => {
        setPilots(prev => {
          const p = prev.find(p => p.id === id);
          const activeCount = prev.filter(p => p.selected).length;
          if (!p.selected && activeCount >= 3) return prev;
          return prev.map(p => p.id === id ? { ...p, selected: !p.selected } : p);
        });
      };

      const launchSortie = useCallback(() => {
        const result = runSortie(pilots, nemesisRecords, turn);
        if (result.error) { setErrorMsg(result.error); return; }
        setErrorMsg("");

        // Mark just-promoted for animation
        const updatedNem = result.newNem.map(n => ({
          ...n,
          _justPromoted: result.promoted && n.fedPilotId === result.promoted.fedPilotId
        }));

        setPilots(result.newPilots);
        setNemesisRecords(updatedNem);
        setSortieLog(prev => [...prev, result.logEntry]);
        setTurn(t => t + 1);
        if (result.victory) { setWins(w => w + 1); setFlash("VICTORY"); }
        else { setLosses(l => l + 1); setFlash("DEFEAT"); setPromotedId(result.promoted?.fedPilotId ?? null); }
        setTimeout(() => setFlash(null), 900);
      }, [pilots, nemesisRecords, turn]);

      const reset = () => {
        setPilots(INITIAL_PILOTS.map(p => ({ ...p })));
        setNemesisRecords([]);
        setSortieLog([]);
        setTurn(1);
        setWins(0);
        setLosses(0);
        setFlash(null);
        setPromotedId(null);
        setErrorMsg("");
      };

      return (
        <div className="min-h-screen bg-black text-green-400 font-mono crt p-2 md:p-4">
          {/* Flash overlay */}
          {flash && (
            <div className={`fixed inset-0 z-50 pointer-events-none transition-opacity
              ${flash === "VICTORY" ? "bg-green-900" : "bg-red-900"} opacity-30`} />
          )}

          {/* Header */}
          <div className="border border-green-700 p-3 mb-3 text-center">
            <div className="text-green-300 text-lg tracking-widest">⚡ ZEON NEMESIS SYSTEM</div>
            <div className="text-green-700 text-xs tracking-widest">OPERATION BRITISH · UC 0079 · PRINCIPALITY OF ZEON</div>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-2 gap-3">
            {/* LEFT: Roster */}
            <div className="border border-green-800 p-3">
              <div className="text-green-600 text-xs mb-3 border-b border-green-900 pb-1 tracking-widest">
                ▸ ZEON PILOT ROSTER — SELECT UP TO 3
              </div>
              {pilots.map(p => <PilotCard key={p.id} pilot={p} onToggle={togglePilot} />)}
            </div>

            {/* RIGHT: Nemesis + Log */}
            <div className="space-y-3">
              <div className="border border-red-900 p-3">
                <div className="text-red-600 text-xs mb-3 border-b border-red-950 pb-1 tracking-widest">
                  ▸ NEMESIS TRACKER — FEDERATION ACES
                </div>
                {nemesisRecords.length === 0 && (
                  <div className="text-red-900 text-xs">— NO NEMESIS CONTACTS REGISTERED —</div>
                )}
                {nemesisRecords.map((rec, i) => (
                  <NemesisCard key={rec.fedPilotId + i} record={rec} />
                ))}
                {Array.from({ length: Math.max(0, 2 - nemesisRecords.length) }).map((_, i) => (
                  <NemesisCard key={"empty" + i} record={null} />
                ))}
              </div>

              <div className="border border-green-900 p-3">
                <div className="text-green-600 text-xs mb-2 border-b border-green-900 pb-1 tracking-widest">
                  ▸ SORTIE OPERATIONAL LOG
                </div>
                <SortieLogPanel log={sortieLog} />
              </div>
            </div>
          </div>

          {/* Action bar */}
          <div className="border border-green-700 p-3 mt-3 flex flex-wrap items-center gap-4">
            <button
              onClick={launchSortie}
              disabled={pilots.filter(p => p.selected).length === 0}
              className="bg-green-900 hover:bg-green-700 disabled:bg-zinc-900 disabled:text-green-900
                text-green-200 text-sm px-6 py-2 border border-green-600 disabled:border-green-950
                tracking-widest transition-colors"
            >
              ▶ LAUNCH SORTIE
            </button>
            <button
              onClick={reset}
              className="bg-zinc-900 hover:bg-zinc-700 text-green-600 text-sm px-4 py-2
                border border-green-900 tracking-widest transition-colors"
            >
              ⟳ RESET CAMPAIGN
            </button>
            <div className="text-green-600 text-sm tracking-widest">
              RECORD: <span className="text-green-300">{wins}W</span> / <span className="text-red-400">{losses}L</span>
              {"  "}TURN: <span className="text-green-400">{turn}</span>
            </div>
            {errorMsg && <div className="text-red-500 text-xs animate-pulse">[!] {errorMsg}</div>}
          </div>
        </div>
      );
    }

    ReactDOM.createRoot(document.getElementById("root")).render(<App />);
  </script>
</body>
</html>
```

---

## Testing Scenarios

| Scenario | Steps | Expected Result |
|---|---|---|
| First defeat | Select 1 pilot, launch sortie, observe loss | New nemesis appears in Nemesis Tracker at ROOKIE rank with Tier-1 mech |
| Nemesis promotion | Lose 2 more sorties to same nemesis | Nemesis rank advances to VETERAN then ACE; mech upgrades; dialogue changes |
| Nemesis elimination | Win a sortie when nemesis is present | Nemesis card shows ELIMINATED; pilot XP increases |
| Rank up | Win multiple sorties with same pilot | Pilot rank advances from SOLDIER → VETERAN; ATK/DEF increase by 5 |
| Over-select pilots | Try selecting 4 pilots | 4th selection is blocked; max 3 enforced |
| Empty launch | Launch with 0 selected pilots | Error message shown; no sortie runs |
| Reset | Press RESET CAMPAIGN | All state returns to initial values |

---

## Extended Features (Optional Enhancements)

- **Rivalry system**: If a player defeats the same nemesis twice, the nemesis returns from "ELIMINATED" state at their previous rank but with even more aggressive dialogue.
- **Sortie report modal**: After each sortie a full-screen debriefing overlay shows the exact matchup stats.
- **Multiple nemesis slots expanded to 4** with unique faction flags.
- **Sound effects**: Web Audio API beeps/buzzes on victory/defeat using a simple tone oscillator.
- **Persistent state**: Add `localStorage` serialisation for campaign persistence across browser sessions.
