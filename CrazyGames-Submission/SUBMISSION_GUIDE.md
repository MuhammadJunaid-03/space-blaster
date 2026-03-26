# CrazyGames Submission Guide

Step-by-step checklist for submitting Space Blaster: Evolved to CrazyGames.

---

## Pre-Submission Checklist

### SDK Integration Verification
- [x] `crazygames-sdk-v2.js` loaded via script tag
- [x] `gameplayStart()` called when gameplay begins
- [x] `gameplayStop()` called on pause, game over, upgrade picker
- [x] `happytime()` triggered on high score and boss kills
- [x] Midgame ads between waves (every 4 waves, 180s cooldown)
- [x] Rewarded ads for extra life (once per run)
- [x] Data module used for persistence (localStorage fallback)
- [x] All SDK calls wrapped in try/catch (graceful degradation)

### CrazyGames Compliance
- [x] No Escape key binding (CrazyGames uses ESC for fullscreen)
- [x] Arrow keys and Space default prevented
- [x] Right-click context menu disabled
- [x] Scroll/wheel events prevented
- [x] No audio autoplay (context created on first user gesture)
- [x] Game works without SDK (ads fail silently, localStorage fallback)
- [x] No external redirects or links in game UI
- [x] No inappropriate content

### Game Quality
- [x] 8 unique enemy types with distinct behaviors
- [x] 3 bosses with phase-based AI
- [x] 20 roguelike upgrades with rarity system
- [x] 5 unlockable ships
- [x] 3 game modes
- [x] 15 achievements
- [x] Meta-progression (crystals, ship unlocks)
- [x] Score combo and kill streak systems
- [x] 5 visual zones
- [x] Full procedural audio (12+ sound effects)
- [x] Smooth 60 FPS gameplay
- [x] Touch + mouse controls
- [x] Responsive to all screen sizes

---

## Submission Steps

### 1. Go to Developer Portal
URL: https://developer.crazygames.com

### 2. Create New Game
- **Title:** Space Blaster: Evolved
- **Category:** Arcade / Shooting
- **Tags:** space, shooter, roguelike, upgrade, mobile
- **Description:**

```
Space Blaster: Evolved is a vertical-scrolling space shooter with roguelike upgrade mechanics. Every 3 waves, choose 1 of 3 randomly offered upgrades that stack and combine — homing missiles, piercing rounds, attack drones, explosive bullets, and more. Build unique weapon loadouts every run.

Features:
• 5 unlockable ships with unique abilities
• 20 roguelike upgrades with Common/Rare/Epic rarities
• 8 enemy types with distinct AI behaviors
• 3 phase-based boss fights
• 3 game modes: Endless, Boss Rush, Blitz
• 15 achievements
• Crystal currency for meta-progression
• Score combo and kill streak systems
• 5 visual zone themes
• Procedural audio — no loading, instant play

Every run is different. What build will you discover?
```

### 3. Upload
- **Type:** HTML5
- **Upload method:** Upload `index.html` as a zip file
  ```bash
  cd space-blaster
  zip space-blaster.zip index.html
  ```
- Alternatively, provide the Vercel URL for iframe embedding

### 4. Game Settings
- **Orientation:** Portrait (game designed for 390×844)
- **Mobile support:** Yes
- **Multiplayer:** No
- **In-game purchases:** No (crystal currency is earned, not bought)

### 5. Thumbnail / Screenshots
Create screenshots showing:
1. **Main menu** with ship select and game modes
2. **Gameplay** with enemies, bullets, and HUD
3. **Boss fight** with HP bar and particle effects
4. **Upgrade picker** with 3 upgrade cards
5. **Game over** with stats and crystal earnings

Recommended thumbnail: 512×512 or 800×600, showing intense gameplay with multiple enemy types and upgrade indicators visible.

---

## Key Selling Points for Review

When CrazyGames reviews, they look for:

### 1. Uniqueness ← PRIMARY DIFFERENTIATOR
> "The roguelike upgrade system makes every run unique. 20 upgrades with 3 rarity tiers create thousands of possible build combinations. Players discover synergies like Homing+Piercing or Explosive+Side Cannons that fundamentally change gameplay."

### 2. Content Depth
> "8 enemy types, 3 bosses, 5 ships, 20 upgrades, 3 game modes, 5 zones, 15 achievements. This is not a generic shooter — it has more content variety than many premium mobile games."

### 3. Replayability
> "Crystal currency and ship unlocks give long-term goals. Achievement system rewards skillful play. Three game modes appeal to different moods. The roguelike upgrade picker ensures no two runs feel the same."

### 4. Polish
> "12+ procedural sound effects, parallax backgrounds, particle systems, screen shake, score popups, kill streak celebrations, combo multiplier display. Every action feels responsive and satisfying."

### 5. SDK Integration
> "Full CrazyGames SDK v2 integration: gameplay events, midgame ads (4-wave intervals), rewarded ads (extra life), and cloud data persistence for cross-device progress."

---

## If Rejected Again

Common rejection reasons and fixes:

| Reason | Fix |
|--------|-----|
| "Low content" | Highlight the 20 upgrades, 8 enemies, 3 bosses, 5 ships |
| "Not unique" | Emphasize roguelike upgrade picker — this is NOT a generic shooter |
| "Poor quality" | Add screenshots/video showing polish (particles, effects, UI) |
| "SDK issues" | Test with CrazyGames QA tool in developer portal |
| "Performance" | Game is 61KB, single file, object-pooled, 60FPS |

---

## Version History
- **v1.0:** Rejected — "low content / same items / not unique"
- **v2.0 (current):** Complete rewrite with roguelike upgrades, 8 enemies, 3 bosses, 5 ships, 3 modes, 15 achievements, meta-progression
