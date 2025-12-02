---
inclusion: always
---

# Technical Stack

## Core Technologies
- **Vanilla JavaScript** - No frameworks or build tools
- **HTML5 Canvas** - For game rendering
- **CSS3** - For UI styling and layout
- **Single HTML file** - Self-contained, opens directly in browser

## Game Architecture
- Canvas-based rendering with requestAnimationFrame game loop
- Entity-component pattern for game objects (player, enemies, collectibles, platforms)
- Camera system with horizontal scrolling following player
- Collision detection using AABB (Axis-Aligned Bounding Box)

## Physics Constants
- Gravity: 0.5
- Jump Power: 12
- Move Speed: 5
- Friction: 0.8
- Player Size: 40x40 pixels
- Canvas Size: 800x600 pixels

## Running the Game
Simply open `index.html` in any modern web browser. No build process or server required.

## Asset Requirements
- `kiro-logo.png` - Player character sprite (40x40px recommended)
- Fallback rendering if image fails to load
