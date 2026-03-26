# Space Blaster: Evolved 🚀

A feature-rich HTML5 vertical-scrolling space shooter with roguelike upgrade mechanics, built for [CrazyGames](https://www.crazygames.com/) publishing.

**GAME DEVELOPED BY MUHAMMAD JUNAID**

## Live Demo

- **Vercel:** [space-blaster.vercel.app](https://space-blaster-omega.vercel.app/)
- **GitHub Repo:** [github.com/waseemnasir2k26/space-blaster](https://github.com/waseemnasir2k26/space-blaster)

---

## Game Features

### Core Gameplay
- Vertical scrolling space shooter with auto-fire
- Touch drag-to-move controls (relative movement, not absolute)
- Mouse support for desktop play
- 60 FPS game loop with delta-time normalization
- Screen shake, particle effects, score popups

### 5 Unlockable Ships
| Ship | HP | Speed | Damage | Cost | Special |
|------|-----|-------|--------|------|---------|
| Striker | 3 | 35 | 1 | Free | Balanced fighter |
| Phantom | 2 | 48 | 1 | 500💎 | Dash ability, fast & fragile |
| Titan | 5 | 25 | 2 | 1200💎 | Ram ability, tanky bruiser |
| Spectre | 3 | 33 | 1 | 2000💎 | Auto-shield specialist |
| Nova | 1 | 38 | 3 | 3500💎 | Critical hit glass cannon |

### 8 Enemy Types
| Enemy | Behavior |
|-------|----------|
| Basic | Moves straight down |
| Zigzag | Sine-wave horizontal movement |
| Tank | Stops at top, shoots aimed bullets |
| Kamikaze | Accelerates toward player |
| Splitter | Splits into 2 swarm enemies on death |
| Shielded | Extra shield HP that must be broken first |
| Sniper | Camps at top, fires precise aimed shots |
| Swarm | Small, fast, tracks player loosely |

### 3 Boss Fights
| Boss | HP | Mechanic |
|------|-----|----------|
| Destroyer | 30+ | 3 phases — sweep, aimed fire, spiral bullets |
| Hivemind | 45+ | 4 phases — spawns swarm minions every 5s |
| Void Lord | 60+ | 3 phases — teleports randomly, enrages at low HP |

All bosses scale with wave number (+8 HP per wave).

### 20 Roguelike Upgrades
Every 3 waves, pick 1 of 3 randomly offered upgrades. Weighted by rarity:

**Weapons (8):** Side Cannons, Rear Shot, Piercing Rounds, Homing Missiles, Spread Shot, Rapid Fire, Power Rounds, Volatile Rounds

**Defense (4):** Orbiters, Nano Repair, Hull Plating, Energy Shield

**Utility (5):** Magnet Field, Neural Link (XP), Fortune Module (luck), Thrusters, Weak Points (crit)

**Special (3):** Smart Bomb, Time Warp (slow field), Attack Drone

Rarities: Common (×5 weight), Rare (×3 weight), Epic (×1 weight)

### 3 Game Modes
- **Endless:** Survive infinite waves, boss every 5 waves
- **Boss Rush:** Boss after boss, no regular waves
- **Blitz:** 15-wave speedrun, boss every 3 waves

### 5 Visual Zones
Zones cycle every 5 waves with unique color palettes:
1. Asteroid Belt (blue)
2. Crimson Nebula (red/pink)
3. Frozen Depths (cyan)
4. Dark Matter (purple)
5. Alien Core (green)

### 15 Achievements
Achievements award bonus crystals. Examples:
- First Blood, Centurion (100 kills), Destroyer (500 kills)
- Veteran (wave 10), Elite (wave 25), Legend (wave 50)
- Boss Slayer, Boss Hunter (5 bosses), Untouchable (no-hit boss)
- Combo Master (×4.0), High Roller (50k), Millionaire (200k)
- Rampage (20 kill streak), Evolved (10 upgrades), Collector (all ships)

### Meta-Progression
- **Crystal currency** earned from runs (score/100 + wave×5 + boss×50)
- **Persistent ship unlocks** saved across sessions
- **High score** tracking per device
- All data persists via CrazyGames Data module (with localStorage fallback)

---

## Technical Architecture

### Single-File Architecture
The entire game is a single `index.html` file (~690 lines). Zero dependencies, zero build step, zero external assets.

### Tech Stack
- **Rendering:** HTML5 Canvas 2D
- **Audio:** Web Audio API (procedural sound synthesis — no audio files)
- **Platform SDK:** CrazyGames SDK v2
- **Deployment:** Vercel (static)
- **Language:** Vanilla JavaScript (ES6+)

### Key Classes

```
index.html
├── CrazySDK          — CrazyGames SDK wrapper (ads, data persistence, gameplay events)
├── Audio              — Procedural sound effects via Web Audio API oscillators/noise
├── PPool              — Particle pool (400 max, object pooling)
├── Bullets            — Bullet pool (300 max, supports pierce/explode/homing)
├── Player             — Player ship with full upgrade state, XP/level, kill streaks
├── Enemy              — 8 enemy type AI behaviors
├── Boss               — Phase-based boss AI (sweep, aimed, spiral, rage)
├── PU                 — Power-up pickups (health, XP, crystals)
├── Game               — Main game controller, state machine, collision, rendering, HUD
└── Utilities          — ch() collision, menu() toggle, roundRect polyfill
```

### Game State Machine
```
  MENU ──── startGame() ────▶ PLAYING
   ▲                           │ │ │
   │                     pause │ │ │ death
   │                           ▼ │ │
   │                        PAUSED │ │
   │                           │   │ │
   │                    resume │   │ │
   │                           ▼   │ │
   │◀── goMenu() ────── PLAYING ◀──┘ │
   │                                  ▼
   │                              DYING (700ms)
   │                                  │
   │                                  ▼
   │◀── goMenu() ────── GAMEOVER ─── revive() ──▶ PLAYING
   │                       │
   │◀── goMenu() ──────────┘
   │
   │                    showUpgradePicker()
   PLAYING ──────────────▶ UPGRADE
      ▲                      │
      │◀── pick upgrade ─────┘
```

### Rendering Pipeline
```
  loop(t) ──▶ update(dt) ──▶ render()
                │                │
                ├─ Player input  ├─ Zone background gradient
                ├─ Auto-fire     ├─ Parallax starfield (3 layers)
                ├─ Enemy AI      ├─ Power-ups
                ├─ Boss AI       ├─ Enemies (unique shapes per type)
                ├─ Bullet pool   ├─ Boss
                ├─ Particles     ├─ Bullets (glow effects)
                ├─ Power-ups     ├─ Player ship (engines, cockpit, shield, orbiters)
                ├─ Collisions    ├─ Particles
                ├─ Wave spawner  ├─ Score popups
                └─ Shake/popups  └─ HUD (score, combo, HP, XP bar, wave, zone, upgrades)
```

### Collision Detection
Circle-vs-circle: `dx²+dy²<(r1+r2)²` for all entities.

### Performance Optimizations
- **Object pooling** for bullets (300) and particles (400) — zero GC allocation during gameplay
- **Fixed logical resolution** (390×844) with CSS scaling — consistent performance on any device
- **Delta-time normalization** — smooth gameplay regardless of frame rate
- **Early exit** on off-screen entities

### Input Model
Touch uses **relative drag** (not absolute position), preventing the player's finger from blocking the ship. Mouse also supported for desktop.

---

## CrazyGames SDK Integration

### Features Used
| Feature | Implementation |
|---------|---------------|
| `game.gameplayStart()` | Called on game start and resume |
| `game.gameplayStop()` | Called on pause, game over, upgrade picker |
| `game.happytime()` | Triggered on new high score and boss kills |
| `ad.requestAd('midgame')` | Every 4 waves (180s cooldown) |
| `ad.requestAd('rewarded')` | Extra life on game over (one per run) |
| `data.set() / data.get()` | Save/load crystals, ships, high score, achievements |

### CrazyGames Compliance
- No Escape key binding (CrazyGames uses Escape for fullscreen exit)
- Arrow keys and Space default prevented
- Right-click disabled
- Scroll/wheel disabled
- Graceful adblock handling (SDK methods wrapped in try/catch)
- localStorage fallback when SDK unavailable

---

## Project Structure

```
space-blaster/
├── index.html              # The entire game (single file)
├── vercel.json             # Vercel deployment config
├── .gitignore              # Standard ignores
├── README.md               # This file
├── ARCHITECTURE.md         # Deep technical documentation
├── CHANGELOG.md            # Version history
├── GAME_DESIGN.md          # Game design document
├── CONTRIBUTING.md         # How to contribute
└── CrazyGames-Submission/
    └── SUBMISSION_GUIDE.md # CrazyGames publishing checklist
```

---

## Local Development

No build step required. Just open `index.html` in a browser:

```bash
# Clone
git clone https://github.com/waseemnasir2k26/space-blaster.git
cd space-blaster

# Option 1: Direct open
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux

# Option 2: Local server (recommended for CrazyGames SDK)
npx serve .
# or
python -m http.server 8000
```

The CrazyGames SDK features (ads, data persistence) only work when hosted on CrazyGames. Locally, the game falls back to localStorage.

---

## Deployment

### Vercel (Current)
Push to `main` — auto-deploys via Vercel Git integration. Config in `vercel.json`:
- No build command
- Output directory: `.`
- Cache headers: 1 hour

### CrazyGames
See `CrazyGames-Submission/SUBMISSION_GUIDE.md` for the full publishing checklist.

---

## Credits

- **Game Design & Development:** Muhammad Junaid
- **AI Pair Programming:** Claude (Anthropic)
- **Platform SDK:** CrazyGames SDK v2
- **Audio:** Procedurally generated via Web Audio API
- **Graphics:** Canvas 2D procedural rendering (no external assets)

---

## License

All rights reserved. Game developed by Muhammad Junaid.
