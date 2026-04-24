# Kitty Conquest — Strategy Game Prototype

## READ THIS FIRST
Any task or conversation working on this game should read this file before making changes. It documents the current state, architecture, asset pipeline, and conventions.

---

## Status
Working prototype. Single HTML5 Canvas + vanilla JS file, no build step.

**Current version**: v3 — 6 factions, ~331 hex tiles, turn-based gameplay with personality-driven AI.

**File**: `kitty-conquest.html` — the complete game (open in browser to play)

## Core Reference
- **Worldbuilding**: `/Hitty Kitty Worldbuilding.md` (root of project) — faction lore, character roster, tone guide, story arcs. Read this for any narrative or design decisions.

---

## Architecture

Single-file HTML + JS + CSS. No framework, no build step. All game logic is inline.

### Key systems (in code order)
1. **Terrain** (`T`): plains, forest, mountain, water, city
2. **Factions** (`F`): kitty, ferret, meerkat, possum, crow, hyena — each with unique unit rosters and AI personality
3. **Heroes** (`HEROES`): 6 Core Six heroes with passive abilities (Rally, Retaliate, Foresight, Inspire, Panic, Reckless)
4. **Forge tree** (`FORGE`): 5-tier linear tech tree for kitty faction
5. **Map generation** (`genMap`): Procedural hex grid, radius 10, multi-octave pseudo-noise terrain
6. **Fog of war**: explored/visible/unexplored per tile, togglable
7. **AI system** (`runFactionAI`, `pickTarget`): Per-faction personality-driven targeting and movement
8. **Difficulty system**: Easy (15 turns peace), Normal (10), Hard (5), Crazy (0) — AI won't attack or cross borders during peace unless provoked
9. **Story events**: Condition-triggered narrative modals
10. **Rendering**: HTML5 Canvas with camera pan/zoom, minimap

### Conventions
- Currency: "catnips" (countable noun — "5 catnips", not "5 catnip")
- Unit names are intentionally misspelled: Fiter, Bloker, Shoter, Crusher
- Hex grid uses axial coordinates (pointy-top)
- Emoji icons as placeholders for all units, heroes, and terrain (to be replaced with sprite assets)

---

## Factions & Units (Complete Roster)

### Kitty City (player faction, pink #ff69b4)
| Unit    | Icon | HP | ATK | DEF | MV | RNG | Cost | Notes |
|---------|------|----|-----|-----|----|-----|------|-------|
| Fiter   | ⚔   | 10 | 3   | 1   | 2  | 1   | 4    | Melee fighter |
| Bloker  | 🛡   | 15 | 2   | 3   | 1  | 1   | 5    | Strong defense |
| Shoter  | 🏹   | 7  | 3   | 0   | 2  | 2   | 5    | Ranged. Forge unlock |
| Crusher | 🔨   | 12 | 5   | 1   | 1  | 1   | 8    | Siege. +3 vs cities. Forge unlock |

### Ferret City (purple #7c4dff) — AI: opportunist
| Unit     | Icon | HP | ATK | DEF | MV | RNG | Cost | Notes |
|----------|------|----|-----|-----|----|-----|------|-------|
| Thief    | 🗡   | 8  | 3   | 0   | 3  | 1   | 4    | Steals 2 catnips on kill |
| Skulker  | 👤   | 10 | 4   | 1   | 2  | 1   | 6    | +3 ATK from forest |
| Raider   | ⚡   | 9  | 3   | 1   | 3  | 1   | 5    | Fast strike |
| Snatcher | 🎒   | 6  | 1   | 0   | 4  | 1   | 3    | Steals city income |

### Meerkat Burrows (orange #ff9800) — AI: territorial
| Unit       | Icon | HP | ATK | DEF | MV | RNG | Cost | Notes |
|------------|------|----|-----|-----|----|-----|------|-------|
| Grunt      | 🐿   | 7  | 2   | 1   | 2  | 1   | 3    | Cheap, spammable |
| Burrower   | ⛏   | 9  | 3   | 2   | 2  | 1   | 5    | Mountains cost 1 move |
| Marshaller | 📯   | 8  | 2   | 2   | 1  | 1   | 6    | Adjacent meerkats +1 ATK/DEF |

### Possum Town (grey #78909c) — AI: scavenger
| Unit        | Icon | HP | ATK | DEF | MV | RNG | Cost | Notes |
|-------------|------|----|-----|-----|----|-----|------|-------|
| Scrapper    | 🐾   | 8  | 3   | 1   | 2  | 1   | 4    | Basic unit |
| Faker       | 💀   | 9  | 2   | 1   | 2  | 1   | 5    | Survives first lethal hit at 1 HP |
| Nightrunner | 🌙   | 7  | 4   | 0   | 3  | 1   | 5    | +2 ATK when initiating |

### Crow's Nest (blue-grey #607d8b) — AI: grudge
| Unit       | Icon | HP | ATK | DEF | MV | RNG | Cost | Notes |
|------------|------|----|-----|-----|----|-----|------|-------|
| Cawer      | 🐦   | 7  | 3   | 0   | 2  | 2   | 5    | Ranged attacker |
| Divebomber | 💨   | 8  | 5   | 0   | 3  | 1   | 6    | High ATK, fragile |
| Lookout    | 👁   | 6  | 2   | 0   | 2  | 3   | 6    | Long range, +3 vision |

### Hyena Heights (brown #8d6e63) — AI: dominant
| Unit       | Icon | HP | ATK | DEF | MV | RNG | Cost | Notes |
|------------|------|----|-----|-----|----|-----|------|-------|
| Brawler    | 💪   | 12 | 4   | 2   | 2  | 1   | 5    | Strong all-rounder |
| Howler     | 🔊   | 10 | 3   | 1   | 2  | 1   | 6    | Adjacent enemies -1 ATK |
| Pitfighter | 🩸   | 14 | 3   | 1   | 1  | 1   | 7    | +ATK as HP drops |

### Heroes (Core Six — kitty faction only)
| Hero ID | Name         | Icon | HP | ATK | DEF | MV | RNG | Ability    | Effect |
|---------|-------------|------|----|-----|-----|----|-----|------------|--------|
| hitty   | Hitty Kitty  | 😼   | 14 | 5   | 2   | 2  | 1   | Rally      | Adjacent allies +1 ATK |
| gritty  | Gritty Kitty | 😾   | 16 | 4   | 3   | 2  | 1   | Retaliate  | +2 counter-attack dmg |
| witty   | Witty Kitty  | 🧐   | 9  | 2   | 1   | 2  | 2   | Foresight  | +2 vision range |
| pritty  | Pritty Kitty | 💅   | 10 | 2   | 2   | 2  | 1   | Inspire    | Adjacent allies heal 2/turn |
| skitty  | Skitty Kitty | 😰   | 8  | 2   | 1   | 3  | 1   | Panic      | 25% chance 2x damage |
| shitty  | Shitty Kitty | 😒   | 11 | 4   | 1   | 2  | 1   | Reckless   | +2 ATK, 1 self-dmg |

---

## Asset System (In Progress)

### Folder structure
```
strategy/
  kitty-conquest.html
  assets/
    units/       ← unit sprites (PNG, 128×128, transparent bg)
    terrain/     ← terrain tile textures
    ui/          ← faction emblems, resource icons, UI elements
```

### Art direction
Target style: **Polytopia-inspired** — low-poly/flat vector, geometric characters with chunky proportions (oversized heads, small bodies). Simple, readable at small sizes. Not pixel art.

### Faction coloring approach (Polytopia hybrid model)
Each unit type has a **base silhouette** that's consistent across factions (a melee fighter always reads as a melee fighter). Each faction variant has:
- Faction-specific **head/helmet/cultural motifs** (ferret thief wears different gear than kitty fiter)
- A designated **team color region** on the body that can be tinted at runtime via HSL shift

For the MVP, use **runtime tinting on base sprites** — one sprite per unit type with a team-color region. This means ~15 base unit sprites + 6 hero sprites + ~5 terrain tiles. Faction-unique variants can come later.

### Sprite spec
- Format: PNG, transparent background
- Size: 128×128 pixels (rendered smaller on canvas, but 128 gives crisp scaling)
- Orientation: Front-facing, centered in frame
- Team color region: Use a specific hue (e.g., magenta #ff00ff) as a mask — the game code replaces this hue with the faction color at load time

### Art pipeline (recommended)
1. **Style definition**: Generate 2-3 reference sprites in Midjourney using --sref for style lock
2. **Batch production**: Train a Scenario.gg generator on the reference sprites, then batch-generate remaining units
3. **Post-processing**: Resize to 128×128, clean edges, ensure transparency in Aseprite or Photoshop
4. **Integration**: Drop PNGs into assets/units/, game code loads them on init

### Asset loading in code (not yet implemented)
When sprites are ready, the game will:
1. Preload all images at startup into an `SPRITES` map
2. Replace `fillText(emoji)` calls with `drawImage(sprite)` in the render loop
3. Apply faction color tinting via offscreen canvas HSL manipulation

---

## Gameplay Settings

### Difficulty levels
| Level  | Peace turns | Description |
|--------|-------------|-------------|
| Easy   | 15          | Long buildup, explore freely |
| Normal | 10          | Standard pace |
| Hard   | 5           | Quick aggression |
| Crazy  | 0           | Immediate warfare |

### AI modes (independent of difficulty)
- **Defensive**: AI stays near home, expands slowly, intercepts threats
- **Aggressive**: AI actively hunts targets, pushes for capitals

### Victory/defeat
- **Win**: Capture all 5 rival capitals
- **Lose**: Lose Kitty City capital

---

## Controls
- Click: Select hex/unit
- Drag: Pan camera
- Scroll: Zoom
- E key: End turn
- Toolbar buttons: Forge, Fog toggle, AI mode toggle, Difficulty toggle

---

## Known Issues / Next Steps
- Emoji placeholder art (sprite system designed but not yet wired up)
- No sound effects or music
- No save/load
- Story events are minimal (opening, first contact, Shitty Kitty incident, hero death, victory)
- No unit animations (static positions)
- AI could be smarter about when to train vs when to save resources
- No diplomacy or alliance mechanics
- Improvements system (farm, workshop, watchtower) exists but is basic
