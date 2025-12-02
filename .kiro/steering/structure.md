---
inclusion: always
---

# Project Structure

## Root Files
- `index.html` - Complete game implementation (HTML, CSS, JavaScript in single file)
- `kiro-logo.png` - Player character sprite

## Code Organization (within index.html)

### HTML Structure
- Game container with title
- UI panel (score, lives, collectibles counter)
- Canvas element (800x600)
- Message area for game over/level complete
- Controls instructions

### CSS Sections
- Reset and base styles
- Dark theme with Kiro purple accents (#790ECB)
- Game container and canvas styling
- UI panel with stats display
- Button styles with hover/active states

### JavaScript Architecture

**Game State**
- Score, lives, collected items tracking
- Game over and level complete flags
- Camera position for scrolling

**Player Object**
- Position, velocity, dimensions
- Facing direction and ground state
- Death animation state
- Image reference

**Game Entities**
- Platforms array (10 platforms with x, y, width, height)
- Collectibles array (10 items with position and collected state)
- Enemies array (3 enemies with movement patterns: horizontal, vertical, circular)
- Goal object (flag and castle at level end)

**Core Functions**
- `updatePlayer()` - Movement, physics, collision
- `updateEnemies()` - Enemy AI and movement patterns
- `updateCollectibles()` - Collection detection
- `updateGoal()` - Level completion check
- `draw*()` functions - Rendering for each entity type
- `gameLoop()` - Main update/render loop
- `loseLife()` / `gameOver()` / `levelComplete()` - Game state transitions
- `restartGame()` - Reset all game state

## Configuration Location
- `.kiro/steering/` - AI assistant steering rules
- `.vscode/` - VS Code workspace settings
