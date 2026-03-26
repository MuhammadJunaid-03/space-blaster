# Game Design Document — Space Blaster: Evolved

## Vision Statement

Space Blaster: Evolved is a vertical-scrolling space shooter with a **roguelike upgrade system** as its core differentiator. Each run is different because the player chooses from random upgrades that stack and combine in unique ways. Combined with meta-progression (crystal currency, ship unlocks), it creates "one more run" addiction.

---

## Core Loop

```
  START RUN
      │
      ▼
  ┌─────────────────────────────────────────┐
  │  Survive waves of enemies (auto-fire)   │◀──┐
  │  Collect XP, crystals, health pickups   │   │
  └─────────────┬───────────────────────────┘   │
                │                               │
        Every 3 waves                           │
                │                               │
                ▼                               │
  ┌─────────────────────────────────────────┐   │
  │  UPGRADE PICKER: Choose 1 of 3          │   │
  │  upgrades — weapon, defense, utility    │   │
  └─────────────┬───────────────────────────┘   │
                │                               │
                └───────────────────────────────┘
                │
        Player dies
                │
                ▼
  ┌─────────────────────────────────────────┐
  │  GAME OVER                              │
  │  Score → Crystals earned                │
  │  Achievement checks                     │
  │  Optional: watch ad for extra life      │
  └─────────────┬───────────────────────────┘
                │
                ▼
  ┌─────────────────────────────────────────┐
  │  HANGAR (Menu)                          │
  │  Spend crystals on new ships            │
  │  Select game mode                       │
  │  Start new run                          │
  └─────────────────────────────────────────┘
```

---

## Player Progression

### Within a Run (Roguelike)
- **Upgrades** offered every 3 waves (pick 1 of 3)
- **XP & leveling** from kills (exponential curve: `100 × 1.15^level`)
- **Score combo** builds with rapid kills, decays over time (max ×4.0)
- **Kill streaks** tracked and celebrated (sound + HUD indicator at 10+)

### Across Runs (Meta-progression)
- **Crystals** earned from every run: `score/100 + wave×5 + boss×50 + achievements`
- **Ship unlocks** purchased with crystals (500–3500💎)
- **Achievements** grant one-time crystal bonuses
- **High score** persists

---

## Economy & Balance

### Crystal Income Per Run
| Source | Typical Yield |
|--------|--------------|
| Score (÷100) | 200-2000 |
| Waves (×5) | 50-250 |
| Bosses (×50) | 50-500 |
| Achievements | 50-500 (one-time) |
| **Total typical** | **350-3250** |

### Ship Costs
| Ship | Cost | Runs to Earn (est.) |
|------|------|---------------------|
| Phantom | 500💎 | 1-2 runs |
| Titan | 1200💎 | 2-4 runs |
| Spectre | 2000💎 | 4-7 runs |
| Nova | 3500💎 | 7-12 runs |

This means unlocking all ships takes roughly 15-25 runs — enough to feel earned, not grindy.

---

## Difficulty Curve

### Enemy Scaling
- HP: `base + floor(wave / 3)`
- Speed: `base + wave × 0.03`
- Spawn interval: `max(300ms, 1300ms - wave × 40ms)`
- Enemy count per wave: `5 + (wave - 1) × 2`

### Boss Scaling
- HP: `base + wave × 8`
- Fire rate doubles when HP < 30% (enrage)

### Zone Progression
Every 5 waves changes the zone (visual theme). This creates visual variety and milestone feeling.

---

## Upgrade Design Philosophy

### Synergies
The upgrade system is designed so that certain combinations are significantly stronger:

| Combo | Effect |
|-------|--------|
| Side Cannons + Spread Shot | Massive bullet coverage |
| Homing + Piercing | Auto-aim through enemy lines |
| Volatile Rounds + Side Cannons | Explosions everywhere |
| Magnet + Fortune Module | More drops pulled from further |
| Nano Repair + Hull Plating | Tanky regen build |
| Critical + Power Rounds | Huge burst damage |
| Smart Bomb + Time Warp | Control the battlefield |
| Attack Drone + Rapid Fire | AI wingman adds DPS |

### Rarity Weighting
- **Common (1):** 5× chance — reliable, stackable, incremental
- **Rare (2):** 3× chance — meaningful power spikes
- **Epic (3):** 1× chance — build-defining game-changers

---

## Audio Design

All sounds are procedurally generated. No audio files.

### Sound Palette
| Sound | Feel | Technical |
|-------|------|-----------|
| Player shoot | Sharp, clean | Sawtooth 880→220Hz, 60ms |
| Enemy shoot | Threatening | Square 440→110Hz, 70ms |
| Explosion | Punchy | White noise + sine sweep |
| Boss explosion | Massive | Long noise + deep sine |
| Power-up | Rewarding | Ascending C-E-G chord |
| Level up | Triumphant | Four ascending tones |
| Upgrade pick | Satisfying | Two ascending pairs |
| Shield hit | Deflection | Upward sine sweep |
| Kill streak | Escalating | Pitch rises with streak count |

---

## UI/UX Design

### Menu Screen
```
┌─────────────────────┐
│   SPACE BLASTER     │
│      EVOLVED        │
│                     │
│   💎 1,250 Crystals │
│                     │
│   SELECT SHIP       │
│  [▲][▲][▲][▲][▲]   │
│                     │
│   GAME MODE         │
│ [Endless][Rush][Blz]│
│                     │
│   TAP TO LAUNCH     │
│                     │
│  DEVELOPED BY       │
│  MUHAMMAD JUNAID    │
└─────────────────────┘
```

### In-Game HUD
```
┌─────────────────────┐
│ ♥♥♥   WAVE 12  [⏸] │
│        Crimson Neb. │
│                     │
│ SHIELD 3s    24,500 │
│ SIDE x2       x2.1 │
│ HOMING x1  🔥15 STR│
│                     │
│      [GAMEPLAY]     │
│                     │
│      [GAMEPLAY]     │
│                     │
│      LVL 8          │
│ ████████░░░░░░░░░░  │
└─────────────────────┘
```

### Game Over Screen
```
┌─────────────────────┐
│   MISSION OVER      │
│  NEW HIGH SCORE!    │
│     142,500         │
│                     │
│ 24    187    3    8 │
│ Waves Kills Boss Upg│
│                     │
│  💎 +1,450 Earned   │
│  HIGH SCORE: 142,500│
│                     │
│ [WATCH AD: REVIVE]  │
│                     │
│   TAP TO PLAY AGAIN │
│      [HANGAR]       │
│                     │
│  DEVELOPED BY       │
│  MUHAMMAD JUNAID    │
└─────────────────────┘
```

---

## Monetization (CrazyGames)

| Ad Type | Trigger | Frequency |
|---------|---------|-----------|
| Midgame | Between waves | Every 4 waves (180s cooldown) |
| Rewarded | Extra life | Once per run, game over screen |

**Player respect rules:**
- Never interrupt gameplay
- Rewarded ads are optional and clearly beneficial
- 180-second cooldown prevents ad spam
- Ads only show when SDK is available (graceful degradation)

---

## Accessibility

- **Touch controls:** Large touch area, relative movement (finger doesn't block ship)
- **Visual clarity:** High-contrast neon colors on dark background
- **HP hearts:** Color + position (not color alone)
- **Sound toggle:** In pause menu
- **Safe area insets:** Respects device notches and rounded corners

---

## What Makes This Unique (vs. Generic Shooters)

1. **Roguelike upgrades** — Every run is different. 20 upgrades with 3 rarities and stacking.
2. **Build synergies** — Discover powerful combinations (homing+pierce, crit+explosions).
3. **Ship variety** — 5 ships with distinct playstyles, not just stat swaps.
4. **Meta-progression** — Crystal currency gives long-term goals beyond a single run.
5. **3 game modes** — Endless, Boss Rush, Blitz appeal to different play moods.
6. **Achievement system** — 15 goals that reward crystals and create challenges.
7. **8 enemy types** — Each requires different tactics (dodge kamikaze, break shields, avoid snipers).
8. **Phase-based bosses** — Not just bullet sponges; they change behavior.
9. **Combo system** — Rapid kills build score multiplier, rewarding aggression.
10. **Kill streaks** — Tracked and celebrated, adds excitement.
