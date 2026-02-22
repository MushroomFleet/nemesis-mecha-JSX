# FantasyNemesisMecha-JSX

A self-contained, single-file interactive demo that implements the **Nemesis System** — the persistent antagonist escalation mechanic pioneered in *Middle-earth: Shadow of Mordor* — reframed as a **Zeon Fantasy Football Manager** set in the Universal Century Gundam universe.

**[Play the Live Demo](https://www.scuffedepoch.com/nemesis-mecha/)**

---

## What is the Nemesis System?

The Nemesis System is a game design pattern where enemies **remember** the player. When an enemy defeats the player, that enemy is promoted — gaining new ranks, better equipment, evolving dialogue, and escalating stats. They return in future encounters as a persistent, personal antagonist. Victories and defeats create emergent narrative arcs that are unique to each playthrough.

This project demonstrates that the Nemesis System is not limited to action games. It translates directly into strategy, management, and any genre where the player faces recurring opponents.

## How This Demo Works

The player manages a squad of Zeon ace pilots and launches simulated sorties against a Federation squad. The core loop:

```
Select Zeon pilots → Launch Sortie → Win or Lose
       ↑                                    ↓
 Roster XP gain ←── Victory       Defeat ──→ Federation pilot promoted
                                              + new mech tier awarded
                                              + revenge dialogue unlocked
                                              + returns in next sortie
```

### Nemesis Escalation

Each time a Federation pilot defeats the player, they progress through ranks and equipment tiers:

| Rank | Mech Tier | Behaviour |
|---|---|---|
| ROOKIE | Tier I (GM) | First appearance, baseline stats |
| VETERAN | Tier II (GM Command) | +5 ATK/DEF per win, new dialogue |
| ACE | Tier III (RX-78-2 Gundam) | Guaranteed sortie appearance |
| ELITE ACE | Tier IV (Full Armour Gundam) | Peak stats, menacing dialogue |
| NEMESIS | Tier IV | Maximum threat level |

Nemeses are **eliminated** when the player wins a sortie they appear in — but while they live, they are guaranteed to keep showing up.

### Player Progression

Zeon pilots gain XP on victory and rank up through CADET → SOLDIER → VETERAN → ACE → RED COMET, gaining +5 ATK/DEF at each threshold.

## Intended Use: Integration With or Without UI

This demo includes a full retro CRT terminal interface for demonstration purposes, but the **Nemesis System engine is intentionally decoupled from the UI**. The core functions — `runSortie()`, the nemesis record model, rank progression arrays, and stat escalation logic — are pure JavaScript with no DOM dependencies.

This means the system can be integrated into:

- **Any game engine** — extract the data models and sortie logic into your engine's scripting layer (Unity C#, Unreal Blueprints, Godot GDScript, etc.)
- **Headless simulations** — run campaigns without rendering for AI training, balance testing, or procedural narrative generation
- **Backend services** — persist nemesis state server-side for multiplayer or cross-session campaigns
- **Other UI frameworks** — the React/JSX frontend is one possible presentation layer; the engine works with any or none

The data models (`ZeonPilot`, `FederationPilot`, `NemesisRecord`, `SortieLogEntry`) and the sortie engine algorithm are fully documented in the [implementation spec](FantasyNemesisMecha-JSX.md).

## Technology

Single HTML5 file, zero build step, zero backend:

- React 18 (CDN)
- Babel standalone (CDN, in-browser JSX transpilation)
- Tailwind CSS (CDN)
- All state in React `useState` — no localStorage, no external data

## Running Locally

1. Clone this repository
2. Open `FantasyNemesisMecha.html` in any modern browser
3. Select up to 3 Zeon pilots and launch sorties

No install, no build, no server required.

## Visual Style

Military-green CRT terminal aesthetic — monospace typography, sharp borders, green-on-black palette. Victory flashes green, defeat flashes red. Nemesis cards pulse with red borders and display escalating revenge dialogue. Promoted nemeses show an animated `[!! PROMOTED !!]` badge.

---

## 📚 Citation

### Academic Citation

If you use this codebase in your research or project, please cite:

```bibtex
@software{nemesis_mecha_jsx,
  title = {FantasyNemesisMecha-JSX: Nemesis System Demo for Game Design Integration},
  author = {Drift Johnson},
  year = {2025},
  url = {https://github.com/MushroomFleet/nemesis-mecha-JSX},
  version = {1.0.0}
}
```

### Donate:

[![Ko-Fi](https://cdn.ko-fi.com/cdn/kofi3.png?v=3)](https://ko-fi.com/driftjohnson)
