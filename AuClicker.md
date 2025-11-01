# MULTIVERSAL AU CLICKER

## Overview
A clicker/idle game where players gather **AU Energy** by clicking a central orb. Energy is spent to unlock **Alternate Universes (AUs)**, each with unique multipliers, visuals, and mechanics. The ultimate goal is to climb the **Multiversal Leaderboard**.

---

## Core Gameplay Loop
1. **Click** → Gain AU Energy.
2. **Spend** → Unlock AUs, upgrades, and multipliers.
3. **Automate** → Unlock passive generators (auto‑clickers, AU reactors).
4. **Prestige** → Reset progress for permanent boosts (Fragments).
5. **Compete** → Compare AU Energy totals on leaderboard.

---

## Resources
- **AU Energy**: Primary currency, gained by clicking and generators.
- **Fragments**: Prestige currency, earned when resetting. Used for permanent upgrades.
- **Universes (AUs)**: Unlockable milestones that change visuals and multipliers.

---

## Progression
- **Base AU**: Start with x1 multiplier.
- **Unlock Thresholds**: New AUs unlock at exponential milestones (100, 1k, 10k, 100k…).
- **Each AU Adds**:
  - Visual theme (background, palette).
  - Multiplier to AU Energy gain.
  - Unique mechanic (e.g., passive gain, bonus clicks, random events).

---

## UI / UX
- **Main Click Orb**: Central glowing button, scales with AU level.
- **Energy Counter**: Large animated number at top.
- **Upgrade Panel**: List of AUs and upgrades with costs.
- **Leaderboard Button**: Opens leaderboard overlay.
- **Prestige Button**: Resets run, grants Fragments.

---

## Upgrades
- **Auto‑Clickers**: Generate clicks per second.
- **AU Reactors**: Multiply passive gain.
- **Orb Enhancements**: Increase click value.
- **Rare AUs**: Unlock hidden universes with unique mechanics.

---

## Milestones
- Unlocking an AU triggers a **procedural cutscene** (short text + glow).
- Rare AUs (e.g., “Glitchverse”, “Goldenverse”) have unique animations.

---

## Technical Notes
- **Persistence**: Save state in `localStorage`.
- **Rendering**: Minimal DOM + CSS transitions.
- **Scalability**: Exponential growth curves for AU Energy.
- **Security**: Validate leaderboard submissions server‑side.

---

## Example Numbers
- Click = 1 AU Energy (base).
- Auto‑Clicker = +1 AU Energy/sec (cost: 50).
- AU Reactor = x2 multiplier (cost: 500).
- Prestige = 1 Fragment per 1e6 AU Energy.

---

## Future Extensions
- Daily challenges.
- Seasonal AUs (Halloweenverse, Winterverse).
- Co‑op AU merging.
