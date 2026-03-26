# Contributing to Space Blaster: Evolved

## Quick Start

```bash
git clone https://github.com/waseemnasir2k26/space-blaster.git
cd space-blaster
# Open index.html in browser — that's it. No build step.
```

## Project Structure

This is a **single-file game**. Everything lives in `index.html`:

```
Line 1-62:      CSS (styles, animations, UI components)
Line 63-115:    HTML (canvas, overlays: menu, pause, game over, upgrade picker, ad overlay)
Line 116:       CrazyGames SDK script tag
Line 117-130:   CrazySDK class (SDK wrapper)
Line 132-209:   Game data constants (SHIPS, UPGRADES, ENEMY_TYPES, ZONES, BOSSES, ACHIEVEMENTS)
Line 211-232:   Audio class (procedural sounds)
Line 234-258:   PPool + Bullets (object pools)
Line 260-325:   Player class
Line 327-361:   Enemy class (8 types)
Line 363-400:   Boss class (3 bosses)
Line 402-408:   Power-ups (PU class + tryDrop)
Line 410-687:   Game class (main controller)
Line 683-687:   Utilities + initialization
```

## Architecture Rules

1. **Single file only.** Do not split into multiple files. CrazyGames and Vercel deployment depend on this.
2. **Zero dependencies.** No npm packages, no external libraries (except CrazyGames SDK script).
3. **Zero build step.** Must work by opening `index.html` directly.
4. **Zero external assets.** No images, no audio files. Everything is procedural.
5. **Object pooling.** Never create bullets or particles with `new` during gameplay. Use the pre-allocated pools.
6. **Delta-time everything.** All movement and timers must use the `dt` parameter, not wall-clock assumptions.

## Code Style

- Minified-ish style for game code (dense but readable)
- Class-based architecture
- `const` for immutable data, `let` for mutable
- Short variable names in hot paths are acceptable (this is a game, performance matters)
- Comments for section headers using `═══` separator blocks

## Testing Locally

The CrazyGames SDK features (ads, cloud saves) only work on CrazyGames. Locally:
- Ads silently fail (returns `false`)
- Data persists to `localStorage` instead
- Game is fully playable

For proper SDK testing, use the [CrazyGames Developer Portal QA tool](https://developer.crazygames.com).

## Adding New Content

### New Enemy Type
1. Add entry to `ENEMY_TYPES` array (line ~168)
2. Add AI behavior in `Enemy.update()` (line ~332)
3. Add visual shape in `Enemy.draw()` (line ~345)
4. Add to spawn pool in `Game.getEType()` (line ~518)

### New Upgrade
1. Add entry to `UPGRADES` array (line ~145)
2. Add state variable in `Player.constructor()` (line ~266)
3. Add application logic in `Player.applyUpgrade()` (line ~276)
4. Add gameplay effect in relevant update/fire methods
5. Add HUD indicator in `Game.drawHUD()` if visible (line ~650)
6. Add availability check in `Game.showUpgradePicker()` (line ~520)

### New Boss
1. Add entry to `BOSSES` array (line ~187)
2. Add special mechanic in `Boss.update()` (line ~368)
3. Bosses auto-scale with wave number

### New Achievement
1. Add entry to `ACHIEVEMENTS` array (line ~193)
2. The `check` function receives run stats — just write the condition
3. Achievement checking happens automatically in `Game.gameOver()`

### New Ship
1. Add entry to `SHIPS` array (line ~137)
2. If the special ability needs new behavior, add it to `Player.update()` or `Player.fire()`

## Commit Messages

Format: `type: description`

Types:
- `feat:` New feature
- `fix:` Bug fix
- `balance:` Game balance change
- `content:` New game content (enemy, upgrade, boss, etc.)
- `ui:` UI/UX change
- `audio:` Sound design change
- `docs:` Documentation only
- `refactor:` Code change that doesn't add features or fix bugs

## Contact

**Game Developer:** Muhammad Junaid
**GitHub:** [@waseemnasir2k26](https://github.com/waseemnasir2k26)
