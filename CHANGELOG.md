# Changelog

All notable changes to Space Blaster: Evolved are documented here.

---

## [2.0.0] — 2026-03-26 — "Evolved" Complete Rewrite

### Why
CrazyGames rejected v1.0 for "low content / same items / not unique." This is a ground-up rebuild with massive content depth and unique roguelike mechanics.

### Added
- **5 ships** with unique stats, specials, and crystal unlock costs (Striker, Phantom, Titan, Spectre, Nova)
- **20 roguelike upgrades** with rarity tiers (Common/Rare/Epic), stacking, and build synergies
- **Upgrade picker UI** — choose 1 of 3 every 3 waves
- **8 enemy types** with distinct AI: basic, zigzag, tank, kamikaze, splitter, shielded, sniper, swarm
- **3 boss fights** with phase-based AI: Destroyer, Hivemind (spawns minions), Void Lord (teleports)
- **5 visual zones** that cycle every 5 waves with unique color palettes
- **3 game modes:** Endless, Boss Rush, Blitz (15-wave speedrun)
- **15 achievements** with crystal rewards
- **Crystal currency** — meta-progression for ship unlocks across runs
- **XP/leveling system** with exponential curve
- **Score combo system** (up to ×4.0) rewarding rapid kills
- **Kill streak tracking** with audio escalation
- **Score popups** showing points earned per kill
- **Homing missile** upgrade with real-time nearest-enemy targeting
- **Orbiter** upgrade — rotating shield-damage orbs
- **Attack Drone** upgrade — AI wingman
- **Smart Bomb** upgrade — screen-clear cooldown
- **Time Warp** upgrade — slow field around player
- **Auto-Shield** upgrade — periodic shield regeneration
- **Rewarded ad** for extra life on game over (once per run)
- **Run statistics** on game over: waves, kills, bosses, upgrades
- **Ship select UI** on main menu
- **Game mode select UI** on main menu
- **Crystal display** on main menu and game over
- Additional procedural sounds: missile, bomb, streak, shield hit, upgrade, level up

### Changed
- Complete code rewrite (~690 lines of dense JS)
- Enemy HP and speed now scale with wave number
- Boss HP scales with wave number (+8 per wave)
- Boss fire rate doubles when HP < 30% (enrage mechanic)
- Spawn interval decreases with wave: `max(300ms, 1300ms - wave×40ms)`
- Enemies per wave increases: `5 + (wave-1) × 2`
- Touch controls remain relative-drag model
- CrazyGames SDK integration expanded (Data module for persistence)

### Removed
- Simple power-up system (replaced by roguelike upgrades)
- Single ship design (replaced by 5-ship roster)
- Single game mode (replaced by 3 modes)

---

## [1.1.0] — 2026-03-26 — CrazyGames SDK Integration

### Added
- CrazyGames SDK v2 integration
- `gameplayStart()` / `gameplayStop()` lifecycle events
- Midgame ad requests between waves
- Rewarded ad for extra life
- `happytime()` on new high scores
- Removed Escape key binding (CrazyGames compliance)
- Arrow/Space default prevented
- Right-click and scroll disabled

---

## [1.0.0] — 2026-03-26 — Initial Release

### Added
- Vertical scrolling space shooter
- HTML5 Canvas 2D rendering
- Touch drag-to-move controls
- Auto-fire with basic bullet system
- 3 enemy types
- 1 boss fight
- Basic power-ups (health, shield, rapid fire)
- Wave system
- Score and high score tracking
- Parallax starfield background
- Procedural audio via Web Audio API
- Responsive canvas scaling (390×844 logical resolution)
- Vercel deployment configuration
- "GAME DEVELOPED BY MUHAMMAD JUNAID" credits
