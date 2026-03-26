# Architecture Deep Dive

Technical documentation for developers working on Space Blaster: Evolved.

---

## System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      index.html                             │
│                                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────┐  │
│  │ CrazySDK │  │  Audio   │  │  PPool   │  │  Bullets  │  │
│  │ (SDK v2) │  │ (WebAud) │  │ (400 obj)│  │ (300 obj) │  │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └─────┬─────┘  │
│       │              │             │               │        │
│  ┌────┴──────────────┴─────────────┴───────────────┴────┐   │
│  │                    Game (Main Controller)             │   │
│  │  ┌────────┐  ┌────────┐  ┌──────┐  ┌─────┐  ┌────┐  │   │
│  │  │ Player │  │ Enemy  │  │ Boss │  │ PU  │  │ HUD│  │   │
│  │  │(1 inst)│  │(array) │  │(0-1) │  │(arr)│  │    │  │   │
│  │  └────────┘  └────────┘  └──────┘  └─────┘  └────┘  │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐   │
│  │                  HTML/CSS UI Layer                    │   │
│  │  menuScr │ pauseScr │ overScr │ upgPick │ adOv      │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐   │
│  │                 Canvas Rendering                      │   │
│  │  Background → Stars → Entities → Particles → HUD     │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## Class Reference

### `CrazySDK` (lines 122-130)
Wraps CrazyGames SDK v2 with graceful fallbacks.

**Methods:**
| Method | Purpose | Fallback |
|--------|---------|----------|
| `gpStart()` | Signal gameplay started | No-op |
| `gpStop()` | Signal gameplay stopped | No-op |
| `happy()` | Trigger happytime event | No-op |
| `midAd()` | Request midgame ad (180s cooldown) | Returns false |
| `rewAd()` | Request rewarded ad | Returns false |
| `save(key, value)` | Persist data | localStorage |
| `load(key)` | Load data | localStorage |

**Ad cooldown:** 180 seconds between midgame ads. Enforced client-side.

---

### `Audio` (lines 214-232)
Procedural sound synthesis using Web Audio API. No audio files needed.

**Sound Design:**
```
shoot()      — Sawtooth 880Hz→220Hz, 60ms
missile()    — Sawtooth 200Hz→600Hz, 120ms
eShoot()     — Square 440Hz→110Hz, 70ms
explode()    — White noise 220ms + sine 80Hz→20Hz
bigExplode() — White noise 450ms + sine 60Hz→15Hz (boss death)
powerUp()    — Three ascending sine tones: C5→E5→G5
hit()        — Noise burst 80ms + sawtooth 150Hz→40Hz
shieldHit()  — Sine 1200Hz→2400Hz, 70ms (upward sweep)
upgrade()    — Two ascending sine pairs
levelUp()    — Four ascending sine tones: A4→C#5→E5→A5
bomb()       — Heavy noise 600ms + low sine 40Hz→10Hz
streak(n)    — Pitch scales with streak count
```

**Autoplay handling:** Audio context created on first user gesture (`unlock()` called from touch/click handlers).

---

### `PPool` — Particle Pool (lines 237-241)
Pre-allocated pool of 400 particles. No garbage collection during gameplay.

**Particle properties:** `x, y, vx, vy, life, maxLife, size, color, active`

**Usage patterns:**
- Enemy death: 14 particles, color matches enemy
- Boss death: 45 + 30 particles (double burst)
- Bullet impact: 3-4 particles
- Power-up collect: 8 particles
- Player death: 40 + 25 particles

---

### `Bullets` (lines 251-258)
Pre-allocated pool of 300 bullets shared between player and enemies.

**Bullet properties:**
- `pl` — boolean, true = player bullet, false = enemy bullet
- `d` — damage value
- `pierce` — number of enemies this bullet can pass through
- `explode` — boolean, creates explosion particles on impact
- `home` — boolean, homing behavior toward nearest enemy
- `c` — color string

**Homing algorithm:** Each frame, find nearest alive enemy, apply acceleration toward it (0.3 per frame), cap speed at 8.

---

### `Player` (lines 263-325)
Player ship with full upgrade state tracking.

**Upgrade State Map:**
```
sideGuns: 0-3       rearGun: 0-1        piercing: 0-2
homing: 0-2         spread: 0-3         fireRateBonus: 0-5
dmgBonus: 0-3       orbiters: 0-2       regen: 0-2
magnet: 0-2         xpMult: 1.0-1.6     luckMult: 1.0-1.6
bomb: 0-1           slowField: 0-1      drone: 0-2
hpBonus: 0-3        speedBonus: 0-3     critChance: 0-0.30
explodeShot: bool   autoShield: 0-2
```

**XP curve:** `xpNext = 100 * (1.15 ^ level)` — exponential scaling.

**Firing pattern (per shot):**
1. Main forward shot
2. Spread shots (±angle per stack)
3. Side cannon diagonals (per stack)
4. Rear shot (if unlocked)
5. Homing missiles (per stack)
6. Drone shots (every ~500ms)

**Invincibility:** 1200ms after hit, 3000ms after revive. Visual: blinking sprite.

---

### `Enemy` (lines 330-361)
8 enemy types with scaling: `HP + floor(wave/3)`, `speed + wave*0.03`.

**AI Behaviors:**
```
basic     →  Straight down
zigzag    →  sin(t*3.5) × 50 amplitude, straight down
tank      →  Stops at y=120, strafes, fires aimed bullets every 1800ms
kamikaze  →  Tracks player with increasing acceleration
splitter  →  Straight down, spawns 2 swarm on death
shielded  →  Straight down, shield HP absorbs damage first
sniper    →  Stops at y=100, fires precise aimed shots every 2500ms
swarm     →  Loosely tracks player + sine wobble, fast, small
```

**Shield mechanic (shielded type):** Shield HP is separate from main HP. Damage overflows from shield to main HP.

---

### `Boss` (lines 366-400)
Phase-based AI cycling every 3500ms.

**Phase behaviors:**
```
Phase 0 (Sweep):   Moves left/right, fires fan of bullets
Phase 1 (Aimed):   Tracks player X, fires aimed bullets
Phase 2 (Spiral):  Circular motion, fires 8-way radial bullets
Phase 3 (Rage):    Faster sweep, dense bullet fan (Hivemind only)
```

**Boss-specific mechanics:**
- **Destroyer:** Standard 3-phase cycle
- **Hivemind:** Spawns 2 swarm enemies every 5 seconds
- **Void Lord:** Teleports to random X every 4 seconds

**Enrage:** When HP < 30%, fire rate doubles (700ms → 400ms).

**HP scaling:** `base HP + (wave × 8)`. Wave 5 Destroyer = 70 HP. Wave 50 Destroyer = 430 HP.

---

### `Game` (lines 413-687)
Main controller class. Manages everything.

**Key data stored:**
```
Persistent (saved):
  crystals       — Currency for ship unlocks
  unlockedShips  — Array of ship IDs owned
  hs             — All-time high score
  earnedAch      — Array of achievement IDs completed

Per-run (reset each game):
  score, combo, kills, bosses, wave, upgCount, maxCombo, maxStreak
  noHitBoss, crystalsEarned, extraUsed, adWvCnt
```

---

## Wave Spawning System

```
Wave N starts:
  ├─ Is boss wave? (every 5th in Endless, every 3rd in Blitz, always in Boss Rush)
  │   ├─ YES → 2s delay → spawn Boss(wave/5 - 1)
  │   └─ NO  → spawn (5 + (wave-1)*2) enemies
  │             ├─ Spawn interval: max(300ms, 1300ms - wave*40)
  │             └─ Enemy type weighted by wave number
  │
  └─ All enemies dead / boss killed:
      ├─ Every 3 waves OR after boss → show upgrade picker
      ├─ Every 4 waves → midgame ad
      └─ Blitz mode + wave 15 → game over
```

**Enemy type availability by wave:**
```
Wave 1-2:   basic only
Wave 3-4:   basic (50%), zigzag (30%), swarm (20%)
Wave 5-7:   basic (30%), zigzag (20%), kamikaze (20%), swarm (15%), tank (15%)
Wave 8-11:  basic (20%), zigzag (15%), kamikaze (15%), tank (15%), splitter (15%), shielded (10%), sniper (10%)
Wave 12+:   all 8 types equally weighted (12.5% each)
```

---

## Collision System

```
  Player Bullets ──vs──▶ Enemies
  Player Bullets ──vs──▶ Boss
  Enemy Bullets  ──vs──▶ Player
  Enemy Bodies   ──vs──▶ Player
  Boss Body      ──vs──▶ Player
  Orbiters       ──vs──▶ Enemies
  Player         ──vs──▶ Power-ups (enlarged radius +10)
```

All use circle-circle: `(x1-x2)² + (y1-y2)² < (r1+r2)²`

---

## Rendering Order (back to front)

1. Background gradient (zone-specific)
2. Parallax starfield (3 layers: 50 slow, 30 medium, 18 fast)
3. Screen shake transform applied
4. Power-ups
5. Enemies (unique polygon per type)
6. Boss (complex polygon + HP bar)
7. Bullets (glow effect via shadowBlur)
8. Player ship (engines, body, cockpit, shield, orbiters, slow field)
9. Particles
10. Score popups (floating text)
11. Screen shake transform removed
12. HUD overlay (score, combo, streak, HP hearts, wave, zone, XP bar, upgrade indicators, boss HP, wave text, blitz timer)

---

## Canvas Scaling

```
Logical resolution: 390 × 844 (mobile-first)

Scale factor: min(window.width / 390, window.height / 844)

Canvas is CSS-scaled to fit any screen while maintaining aspect ratio.
Black bars (letterbox/pillarbox) fill remaining space.
Touch/mouse coords converted via: (clientX - offsetX) / scale
```

---

## Data Persistence

```
save('sb_data', {
  c: crystals,           // number
  s: unlockedShips,      // string[]
  h: highScore,          // number
  a: earnedAchievements  // string[]
})
```

**Priority:** CrazyGames Data module → localStorage fallback.
**Save triggers:** Game over, ship purchase.
**Load triggers:** Game initialization.

---

## Sound Design Reference

All audio is generated at runtime using the Web Audio API. No files loaded.

**Techniques used:**
- **Oscillators:** Frequency sweeps (high→low for shots, low→high for power-ups)
- **White noise:** Buffer of random samples for explosions
- **Lowpass filter:** Applied to noise for muffled explosion feel
- **Gain envelope:** All sounds fade to 0.001 (exponential ramp)
- **Type variety:** `sine` (clean), `square` (8-bit), `sawtooth` (aggressive)

---

## Performance Budget

| Resource | Budget |
|----------|--------|
| Bullet pool | 300 objects |
| Particle pool | 400 objects |
| Max enemies on screen | ~40 (wave-dependent) |
| Canvas resolution | 390×844 |
| Target frame rate | 60 FPS |
| File size | ~61 KB (single file) |
| External requests | 1 (CrazyGames SDK script) |
| Audio files | 0 (all procedural) |
| Image files | 0 (all canvas-drawn) |
