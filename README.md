# 🎮 Super Kiro World

A retro-style 2D platformer game built with vanilla JavaScript, HTML5 Canvas, and CSS. Guide Kiro through a challenging level filled with enemies, collectibles, and platforming action!

![Super Kiro World](kiro-logo.png)

## 🎯 Features

### Core Gameplay
- **Classic Platformer Mechanics**: Jump, run, and navigate through 10 challenging platforms
- **Enemy Combat**: Defeat enemies Mario-style by jumping on them from above
- **Collectibles**: Gather 10 purple stars scattered throughout the level
- **Lives System**: Start with 5 lives and try to reach the castle
- **Scoring**: Earn points for collecting stars (+1) and defeating enemies (+10)
- **Victory Animation**: Reach the flag and watch Kiro enter the castle!

### Visual Effects
- **Particle System**: Dynamic particle effects (ready for trail, explosion, sparkle, and confetti effects)
- **Smooth Animations**: Death animations with spinning effects
- **Camera System**: Smooth scrolling camera that follows the player
- **Retro Aesthetic**: Dark theme with Kiro purple branding (#790ECB)

### Audio System 🎵
- **Background Music**: Looping retro-style melody (click to start)
- **Jump Sound**: Upward pitch sweep when jumping
- **Collect Sound**: Bright chime when collecting stars
- **Stomp Sound**: Satisfying thump when defeating enemies
- **Death Sound**: Descending tone when losing a life
- **Victory Fanfare**: Triumphant 4-note melody when completing the level

### Enemy Types
- **Horizontal Patrol**: Enemies that move left and right
- **Vertical Patrol**: Enemies that move up and down
- **Circular Motion**: Enemies that move in circular patterns

## 🕹️ Controls

- **⬅️ ➡️ Arrow Keys**: Move left and right
- **SPACEBAR**: Jump
- **Mouse Click**: Start background music (required for audio)

## 🚀 How to Play

1. Open `index.html` in any modern web browser
2. Click on the game canvas to start the background music
3. Use arrow keys to move and spacebar to jump
4. Collect all purple stars for maximum points
5. Jump on enemies from above to defeat them
6. Avoid touching enemies from the side or falling off platforms
7. Reach the flag and castle at the end to win!

## 🎨 Game Mechanics

### Scoring
- **Collectible Star**: +1 point
- **Enemy Defeat**: +10 points
- **Level Complete Bonus**: +10 points per collectible gathered

### Combat
- **Jump on Enemy**: Defeat the enemy and bounce upward
- **Touch Enemy (side/below)**: Lose a life and trigger death animation
- **Fall Off Platform**: Lose a life with death animation

### Physics
- **Gravity**: 0.5
- **Jump Power**: 12
- **Move Speed**: 5
- **Friction**: 0.8

## 🛠️ Technical Details

### Built With
- **Vanilla JavaScript**: No frameworks or dependencies
- **HTML5 Canvas**: For game rendering
- **Web Audio API**: For procedural sound generation
- **CSS3**: For UI styling

### Architecture
- Single HTML file structure (self-contained)
- Entity-component pattern for game objects
- Particle system with manager pattern
- AABB collision detection
- RequestAnimationFrame game loop

### Browser Compatibility
- Works in all modern browsers (Chrome, Firefox, Safari, Edge)
- Requires JavaScript enabled
- Audio requires user interaction (click) to start

## 📁 Project Structure

```
super-kiro-world/
├── index.html          # Complete game (HTML + CSS + JavaScript)
├── kiro-logo.png       # Player character sprite
└── README.md           # This file
```

## 🎯 Future Enhancements

The game includes a particle system foundation ready for:
- Trail particles behind the player
- Explosion effects on enemy collision
- Sparkle effects when collecting items
- Confetti celebration on new high scores
- Save system for persistent high scores and game history

## 🏆 Credits

Created as part of the AWS Re:Invent workshop.

**Character**: Kiro mascot  
**Theme**: Kiro brand purple (#790ECB)  
**Inspiration**: Classic platformers like Super Mario Bros

## 📝 License

This project is open source and available for educational purposes.

## 🎮 Play Now!

Simply open `index.html` in your browser and start playing!

---

**Enjoy Super Kiro World!** 🎉
