# Design Document

## Overview

This design document specifies enhancements to Super Kiro World that add persistent score tracking and visual particle effects. The enhancements integrate seamlessly with the existing game architecture while maintaining the single-file HTML structure and vanilla JavaScript approach.

The design introduces two major systems:
1. **Save System**: Manages persistent storage of high scores and game history using browser localStorage
2. **Particle System**: Provides visual feedback through trail particles, explosions, sparkles, and confetti effects

Both systems are designed to be lightweight, performant, and non-intrusive to the existing game loop.

## Architecture

### High-Level Structure

The enhancements follow the existing single-file architecture pattern:

```
index.html
├── HTML Structure (existing + history UI)
├── CSS Styles (existing + particle styles)
└── JavaScript
    ├── Existing Game Systems
    │   ├── Game State
    │   ├── Player System
    │   ├── Platform System
    │   ├── Enemy System
    │   └── Collectible System
    └── New Systems
        ├── Save System
        │   ├── LocalStorage Manager
        │   ├── High Score Tracker
        │   └── Game History Manager
        └── Particle System
            ├── Particle Manager
            ├── Trail Particles
            ├── Explosion Particles
            ├── Sparkle Particles
            └── Confetti Particles
```

### Integration Points

The new systems integrate with existing code at these key points:

1. **Game Initialization**: Load saved data from localStorage
2. **Game Over / Level Complete**: Save game session to history and update high score
3. **Player Movement**: Spawn trail particles
4. **Enemy Collision**: Trigger explosion effect
5. **Collectible Collection**: Trigger sparkle effect
6. **New High Score**: Trigger confetti effect
7. **Game Loop**: Update and render all particles

## Components and Interfaces

### Save System Components

#### LocalStorage Manager

Handles all interactions with browser localStorage API.

```javascript
const SaveManager = {
    STORAGE_KEY: 'superKiroWorld_saveData',
    
    // Load save data from localStorage
    load() {
        try {
            const data = localStorage.getItem(this.STORAGE_KEY);
            return data ? JSON.parse(data) : this.getDefaultData();
        } catch (e) {
            console.error('Failed to load save data:', e);
            return this.getDefaultData();
        }
    },
    
    // Save data to localStorage
    save(data) {
        try {
            localStorage.setItem(this.STORAGE_KEY, JSON.stringify(data));
            return true;
        } catch (e) {
            console.error('Failed to save data:', e);
            return false;
        }
    },
    
    // Get default save structure
    getDefaultData() {
        return {
            highScore: 0,
            history: []
        };
    }
};
```

#### High Score Tracker

Manages high score logic and updates.

```javascript
const HighScoreManager = {
    currentHighScore: 0,
    
    // Initialize from saved data
    init(savedData) {
        this.currentHighScore = savedData.highScore || 0;
    },
    
    // Check if score is a new high score
    isNewHighScore(score) {
        return score > this.currentHighScore;
    },
    
    // Update high score
    updateHighScore(score) {
        if (this.isNewHighScore(score)) {
            this.currentHighScore = score;
            return true; // Indicates new high score
        }
        return false;
    },
    
    // Get current high score
    getHighScore() {
        return this.currentHighScore;
    }
};
```

#### Game History Manager

Manages the list of past game sessions.

```javascript
const HistoryManager = {
    MAX_HISTORY_ENTRIES: 10,
    history: [],
    
    // Initialize from saved data
    init(savedData) {
        this.history = savedData.history || [];
    },
    
    // Add new game session
    addSession(sessionData) {
        const entry = {
            score: sessionData.score,
            lives: sessionData.lives,
            collectibles: sessionData.collectedItems,
            totalCollectibles: sessionData.totalItems,
            completed: sessionData.levelComplete,
            timestamp: Date.now()
        };
        
        this.history.unshift(entry); // Add to beginning
        
        // Keep only last 10 entries
        if (this.history.length > this.MAX_HISTORY_ENTRIES) {
            this.history = this.history.slice(0, this.MAX_HISTORY_ENTRIES);
        }
    },
    
    // Get formatted history for display
    getFormattedHistory() {
        return this.history.map(entry => ({
            ...entry,
            date: new Date(entry.timestamp).toLocaleDateString(),
            time: new Date(entry.timestamp).toLocaleTimeString()
        }));
    },
    
    // Get history array
    getHistory() {
        return this.history;
    }
};
```

### Particle System Components

#### Particle Manager

Central manager for all particle effects.

```javascript
const ParticleManager = {
    particles: [],
    
    // Add particle to system
    add(particle) {
        this.particles.push(particle);
    },
    
    // Update all particles
    update() {
        for (let i = this.particles.length - 1; i >= 0; i--) {
            const particle = this.particles[i];
            particle.update();
            
            // Remove expired particles
            if (particle.isDead()) {
                this.particles.splice(i, 1);
            }
        }
    },
    
    // Draw all particles
    draw(ctx, cameraX) {
        for (let particle of this.particles) {
            particle.draw(ctx, cameraX);
        }
    },
    
    // Clear all particles
    clear() {
        this.particles = [];
    }
};
```

#### Base Particle Class

Base structure for all particle types.

```javascript
class Particle {
    constructor(x, y, velocityX, velocityY, color, size, lifetime) {
        this.x = x;
        this.y = y;
        this.velocityX = velocityX;
        this.velocityY = velocityY;
        this.color = color;
        this.size = size;
        this.lifetime = lifetime;
        this.age = 0;
        this.rotation = Math.random() * Math.PI * 2;
        this.rotationSpeed = (Math.random() - 0.5) * 0.2;
    }
    
    update() {
        this.x += this.velocityX;
        this.y += this.velocityY;
        this.rotation += this.rotationSpeed;
        this.age++;
    }
    
    isDead() {
        return this.age >= this.lifetime;
    }
    
    getOpacity() {
        return 1 - (this.age / this.lifetime);
    }
    
    draw(ctx, cameraX) {
        ctx.save();
        ctx.globalAlpha = this.getOpacity();
        ctx.fillStyle = this.color;
        ctx.translate(this.x - cameraX, this.y);
        ctx.rotate(this.rotation);
        ctx.fillRect(-this.size / 2, -this.size / 2, this.size, this.size);
        ctx.restore();
    }
}
```

#### Trail Particle System

Creates particles that follow the player.

```javascript
const TrailParticles = {
    frameCounter: 0,
    SPAWN_RATE: 3, // Spawn every 3 frames
    
    // Spawn trail particle
    spawn(player, cameraX) {
        this.frameCounter++;
        
        // Only spawn when moving and at specified rate
        if (this.frameCounter % this.SPAWN_RATE === 0 && 
            (Math.abs(player.velocityX) > 0.5 || !player.onGround)) {
            
            const particle = new Particle(
                player.x + player.width / 2,
                player.y + player.height / 2,
                (Math.random() - 0.5) * 2,
                (Math.random() - 0.5) * 2,
                '#790ECB',
                6,
                30 // 30 frame lifetime
            );
            
            ParticleManager.add(particle);
        }
    }
};
```

#### Explosion Particle System

Creates burst effects on collisions.

```javascript
const ExplosionParticles = {
    // Create explosion at position
    create(x, y) {
        const particleCount = 15 + Math.floor(Math.random() * 10); // 15-25 particles
        
        for (let i = 0; i < particleCount; i++) {
            const angle = (Math.PI * 2 * i) / particleCount;
            const speed = 2 + Math.random() * 3;
            
            const particle = new ExplosionParticle(
                x,
                y,
                Math.cos(angle) * speed,
                Math.sin(angle) * speed,
                '#ff3333',
                4 + Math.random() * 4,
                40
            );
            
            ParticleManager.add(particle);
        }
    }
};

class ExplosionParticle extends Particle {
    update() {
        super.update();
        this.velocityY += 0.2; // Gravity
        this.velocityX *= 0.98; // Friction
        this.velocityY *= 0.98;
    }
}
```

#### Sparkle Particle System

Creates upward-floating sparkles for collectibles.

```javascript
const SparkleParticles = {
    // Create sparkle at position
    create(x, y) {
        const particleCount = 8 + Math.floor(Math.random() * 4); // 8-12 particles
        
        for (let i = 0; i < particleCount; i++) {
            const angle = (Math.PI * 2 * i) / particleCount;
            const speed = 1 + Math.random() * 2;
            
            const colors = ['#790ECB', '#ffffff'];
            const color = colors[i % 2];
            
            const particle = new SparkleParticle(
                x,
                y,
                Math.cos(angle) * speed,
                Math.sin(angle) * speed - 2, // Upward bias
                color,
                3 + Math.random() * 3,
                30
            );
            
            ParticleManager.add(particle);
        }
    }
};

class SparkleParticle extends Particle {
    update() {
        super.update();
        this.velocityY -= 0.1; // Float upward
        this.velocityX *= 0.95;
    }
}
```

#### Confetti Particle System

Creates celebratory confetti for new high scores.

```javascript
const ConfettiParticles = {
    // Create confetti across screen
    create(cameraX, canvasWidth) {
        const particleCount = 50 + Math.floor(Math.random() * 30); // 50-80 particles
        
        const colors = ['#790ECB', '#9a3de0', '#ffffff', '#FFD700', '#ff69b4'];
        
        for (let i = 0; i < particleCount; i++) {
            const x = cameraX + Math.random() * canvasWidth;
            const y = -20 - Math.random() * 100;
            
            const particle = new ConfettiParticle(
                x,
                y,
                (Math.random() - 0.5) * 4,
                Math.random() * 2 + 1,
                colors[Math.floor(Math.random() * colors.length)],
                4 + Math.random() * 6,
                120
            );
            
            ParticleManager.add(particle);
        }
    }
};

class ConfettiParticle extends Particle {
    update() {
        super.update();
        this.velocityY += 0.15; // Gravity
        this.velocityX += (Math.random() - 0.5) * 0.2; // Drift
        this.rotationSpeed = 0.1;
    }
    
    isDead() {
        return this.age >= this.lifetime || this.y > 650; // Remove when off screen
    }
}
```

## Data Models

### Save Data Structure

```javascript
{
    highScore: Number,        // Highest score achieved
    history: [                // Array of game sessions
        {
            score: Number,
            lives: Number,
            collectibles: Number,
            totalCollectibles: Number,
            completed: Boolean,
            timestamp: Number  // Unix timestamp
        }
    ]
}
```

### Particle Data Structure

```javascript
{
    x: Number,              // World position X
    y: Number,              // World position Y
    velocityX: Number,      // Horizontal velocity
    velocityY: Number,      // Vertical velocity
    color: String,          // CSS color
    size: Number,           // Particle size in pixels
    lifetime: Number,       // Total frames to live
    age: Number,            // Current age in frames
    rotation: Number,       // Current rotation angle
    rotationSpeed: Number   // Rotation speed per frame
}
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system-essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*


### Save System Properties

Property 1: Save data persistence
*For any* game session with a final score, when the session ends, retrieving data from localStorage should return an entry containing that score with a valid timestamp
**Validates: Requirements 1.1**

Property 2: High score initialization from storage
*For any* high score value stored in localStorage, when the game loads, the displayed high score should match the stored value
**Validates: Requirements 1.2**

Property 3: High score update on new record
*For any* current high score and new score where new score > current high score, the system should update the stored high score to the new value
**Validates: Requirements 1.3**

Property 4: Save data round-trip consistency
*For any* valid game session data, serializing to JSON then deserializing should produce equivalent data with all fields intact
**Validates: Requirements 1.5**

Property 5: History entry completeness
*For any* completed game session, the history entry should contain score, lives, collectibles, totalCollectibles, completed status, and timestamp fields
**Validates: Requirements 2.1**

Property 6: History size limit enforcement
*For any* history array, after adding entries, the array length should never exceed 10 entries, with oldest entries removed first
**Validates: Requirements 2.2, 2.4**

Property 7: History formatting completeness
*For any* history entry, the formatted output should contain a date string, score value, and completion status
**Validates: Requirements 2.3**

Property 8: Corrupted data graceful handling
*For any* invalid or corrupted localStorage data (invalid JSON, missing fields, wrong types), the system should not crash and should return default data
**Validates: Requirements 2.5**

### Particle System Properties

Property 9: Trail particle spawning on movement
*For any* player state where horizontal velocity is non-zero or player is airborne, trail particles should be spawned behind the player
**Validates: Requirements 3.1**

Property 10: Trail particle visual properties
*For any* trail particle, it should have purple color (#790ECB) and opacity should decrease as age increases
**Validates: Requirements 3.2**

Property 11: Particle lifetime removal
*For any* particle that has aged beyond its specified lifetime, it should be removed from the particle array
**Validates: Requirements 3.3, 3.4, 4.5, 5.5**

Property 12: Trail particle spawn rate
*For any* sequence of frames where the player is airborne, trail particles should spawn at a rate of approximately one particle every 3 frames
**Validates: Requirements 3.5**

Property 13: Explosion triggering on collision
*For any* collision between player and enemy, an explosion effect should be spawned at the collision coordinates
**Validates: Requirements 4.1**

Property 14: Explosion particle count range
*For any* explosion effect created, the number of particles generated should be between 15 and 25 inclusive
**Validates: Requirements 4.2**

Property 15: Explosion particle velocity distribution
*For any* explosion effect, particles should have velocities distributed across all directions (0 to 2π radians)
**Validates: Requirements 4.3**

Property 16: Explosion particle physics
*For any* explosion particle, after update, velocityY should increase (gravity) and both velocityX and velocityY should decrease in magnitude (decay)
**Validates: Requirements 4.4**

Property 17: Sparkle triggering on collection
*For any* collectible item collection event, a sparkle effect should be spawned at the item's location
**Validates: Requirements 5.1**

Property 18: Sparkle particle count and direction
*For any* sparkle effect created, the number of particles should be between 8 and 12, and all particles should have negative velocityY (upward)
**Validates: Requirements 5.2**

Property 19: Sparkle particle color alternation
*For any* sparkle effect, particles should alternate between purple (#790ECB) and white (#ffffff) colors
**Validates: Requirements 5.3**

Property 20: Sparkle particle upward drift
*For any* sparkle particle, after update, velocityY should become more negative (upward drift)
**Validates: Requirements 5.4**

Property 21: Confetti triggering on new high score
*For any* score that exceeds the current high score, a confetti effect should be spawned
**Validates: Requirements 6.1**

Property 22: Confetti particle count and variety
*For any* confetti effect created, the number of particles should be between 50 and 80, and at least 3 different colors should be used
**Validates: Requirements 6.2, 6.3**

Property 23: Confetti particle physics
*For any* confetti particle, after update, velocityY should increase (gravity), velocityX should vary (drift), and rotation should change
**Validates: Requirements 6.4**

Property 24: Confetti boundary cleanup
*For any* confetti particle with y-coordinate below canvas height, it should be removed from the particle array
**Validates: Requirements 6.5**

Property 25: Unified particle array
*For any* combination of particle effects triggered (trail, explosion, sparkle, confetti), all particles should exist in the same managed array
**Validates: Requirements 7.1**

Property 26: Particle system update and cleanup
*For any* particle array state, after calling update, all particles should have increased age, and particles exceeding lifetime should be removed
**Validates: Requirements 7.2, 7.4**

Property 27: Particle system reset
*For any* particle array state, after game restart, the particle array should be empty
**Validates: Requirements 7.5**

## Error Handling

### Save System Error Handling

1. **localStorage Unavailable**: If localStorage is not available (private browsing, quota exceeded), the game should continue functioning without save features and log a warning to console.

2. **Corrupted Save Data**: If saved data cannot be parsed or has invalid structure, initialize with default data (high score: 0, empty history).

3. **Save Failure**: If saving fails (quota exceeded), log error but don't interrupt gameplay.

4. **Missing Fields**: If loaded data is missing expected fields, fill in with default values.

### Particle System Error Handling

1. **Invalid Particle Parameters**: If particle creation receives invalid parameters (NaN, undefined), skip particle creation and log warning.

2. **Excessive Particle Count**: If particle array exceeds 1000 particles, stop creating new particles until count drops below threshold.

3. **Rendering Errors**: Wrap particle rendering in try-catch to prevent single particle from crashing entire render loop.

## Testing Strategy

### Unit Testing Approach

Unit tests will verify specific examples and edge cases:

**Save System Unit Tests:**
- Test saving and loading with empty localStorage
- Test high score update with specific values (0 → 100, 50 → 25 should not update)
- Test history with exactly 10 entries
- Test history with 15 entries (should keep only 10)
- Test corrupted JSON strings
- Test missing fields in save data

**Particle System Unit Tests:**
- Test particle creation with specific parameters
- Test particle update for 1 frame
- Test particle removal at exact lifetime
- Test explosion with specific collision point
- Test sparkle with specific collectible position
- Test confetti with specific camera position

### Property-Based Testing Approach

Property-based tests will verify universal properties across many random inputs using **fast-check** library for JavaScript.

**Configuration:**
- Minimum 100 iterations per property test
- Use appropriate generators for each data type
- Shrink failing cases to minimal examples

**Save System Property Tests:**

*Property 1: Save data persistence*
- Generator: Random game states (score 0-10000, lives 0-5, collectibles 0-10)
- Test: End session, verify localStorage contains matching data
- Tag: `// Feature: game-enhancements, Property 1: Save data persistence`

*Property 3: High score update on new record*
- Generator: Random pairs of (currentHighScore, newScore) where newScore > currentHighScore
- Test: Update high score, verify stored value equals newScore
- Tag: `// Feature: game-enhancements, Property 3: High score update on new record`

*Property 4: Save data round-trip consistency*
- Generator: Random valid save data objects
- Test: JSON.stringify then JSON.parse, verify deep equality
- Tag: `// Feature: game-enhancements, Property 4: Save data round-trip consistency`

*Property 6: History size limit enforcement*
- Generator: Random arrays of 11-20 game sessions
- Test: Add all to history, verify length ≤ 10 and most recent entries kept
- Tag: `// Feature: game-enhancements, Property 6: History size limit enforcement`

*Property 8: Corrupted data graceful handling*
- Generator: Invalid data (malformed JSON, wrong types, missing fields)
- Test: Load data, verify no crash and returns default data
- Tag: `// Feature: game-enhancements, Property 8: Corrupted data graceful handling`

**Particle System Property Tests:**

*Property 11: Particle lifetime removal*
- Generator: Random particles with random lifetimes (10-100 frames)
- Test: Age particle beyond lifetime, call update, verify removed from array
- Tag: `// Feature: game-enhancements, Property 11: Particle lifetime removal`

*Property 14: Explosion particle count range*
- Generator: Random explosion positions
- Test: Create explosion, count particles, verify 15 ≤ count ≤ 25
- Tag: `// Feature: game-enhancements, Property 14: Explosion particle count range`

*Property 15: Explosion particle velocity distribution*
- Generator: Random explosion positions
- Test: Create explosion, calculate angles of all velocities, verify coverage of all quadrants
- Tag: `// Feature: game-enhancements, Property 15: Explosion particle velocity distribution`

*Property 18: Sparkle particle count and direction*
- Generator: Random sparkle positions
- Test: Create sparkle, verify 8 ≤ count ≤ 12 and all velocityY < 0
- Tag: `// Feature: game-enhancements, Property 18: Sparkle particle count and direction`

*Property 22: Confetti particle count and variety*
- Generator: Random camera positions and canvas widths
- Test: Create confetti, verify 50 ≤ count ≤ 80 and ≥ 3 unique colors
- Tag: `// Feature: game-enhancements, Property 22: Confetti particle count and variety`

*Property 26: Particle system update and cleanup*
- Generator: Random arrays of particles with varied ages and lifetimes
- Test: Call update, verify all ages increased by 1 and expired particles removed
- Tag: `// Feature: game-enhancements, Property 26: Particle system update and cleanup`

### Testing Generators

Custom generators for property-based testing:

```javascript
// Game state generator
const gameStateGen = fc.record({
    score: fc.integer({ min: 0, max: 10000 }),
    lives: fc.integer({ min: 0, max: 5 }),
    collectedItems: fc.integer({ min: 0, max: 10 }),
    totalItems: fc.constant(10),
    levelComplete: fc.boolean()
});

// Particle generator
const particleGen = fc.record({
    x: fc.float({ min: 0, max: 3200 }),
    y: fc.float({ min: 0, max: 600 }),
    velocityX: fc.float({ min: -10, max: 10 }),
    velocityY: fc.float({ min: -10, max: 10 }),
    lifetime: fc.integer({ min: 10, max: 100 }),
    age: fc.integer({ min: 0, max: 50 })
});

// Corrupted data generator
const corruptedDataGen = fc.oneof(
    fc.constant('invalid json{'),
    fc.constant('{"highScore": "not a number"}'),
    fc.constant('{"history": "not an array"}'),
    fc.constant('{}'), // Missing fields
    fc.constant(null)
);
```

## Implementation Notes

### Integration with Existing Code

1. **Initialization**: Add save system initialization at game start, before game loop begins
2. **Game Over Hook**: Call save functions in existing `gameOver()` and `levelComplete()` functions
3. **Particle Updates**: Add particle system update/draw calls in existing `gameLoop()` function
4. **Effect Triggers**: Add particle spawn calls at existing collision/collection points
5. **UI Updates**: Add high score display to existing UI panel
6. **History Display**: Add collapsible history panel below game canvas

### Performance Considerations

1. **Particle Limit**: Cap total particles at 1000 to prevent performance degradation
2. **Particle Cleanup**: Remove particles immediately when off-screen or expired
3. **localStorage Throttling**: Only save on game end, not every frame
4. **Efficient Rendering**: Use globalAlpha for fading instead of recalculating colors
5. **Array Operations**: Use splice for removal, unshift for adding to history

### Browser Compatibility

- localStorage: Supported in all modern browsers (IE8+)
- Canvas API: Supported in all modern browsers
- JSON methods: Supported in all modern browsers
- No external dependencies required

### Future Enhancements

Potential future improvements not in current scope:
- Cloud save synchronization
- Leaderboard system
- More particle effect types (smoke, fire, etc.)
- Particle pooling for better performance
- Sound effects for particle events
- Customizable particle colors/styles
