# Implementation Plan

- [ ] 1. Implement Save System Core
- [ ] 1.1 Create SaveManager for localStorage operations
  - Implement load() method to retrieve save data from localStorage
  - Implement save() method to store data to localStorage
  - Implement getDefaultData() to return default save structure
  - Add error handling for localStorage unavailable or quota exceeded
  - _Requirements: 1.1, 1.2, 1.4, 1.5_

- [ ] 1.2 Create HighScoreManager
  - Implement init() to load high score from saved data
  - Implement isNewHighScore() to check if score beats current high score
  - Implement updateHighScore() to update and return whether it's a new record
  - Implement getHighScore() to retrieve current high score
  - _Requirements: 1.2, 1.3_

- [ ] 1.3 Create HistoryManager
  - Implement init() to load history from saved data
  - Implement addSession() to add new game session with all required fields
  - Implement size limit logic to keep only 10 most recent entries
  - Implement getFormattedHistory() to format entries with dates and times
  - _Requirements: 2.1, 2.2, 2.3, 2.4_

- [ ]* 1.4 Write property test for save data round-trip
  - **Property 4: Save data round-trip consistency**
  - **Validates: Requirements 1.5**

- [ ]* 1.5 Write property test for high score updates
  - **Property 3: High score update on new record**
  - **Validates: Requirements 1.3**

- [ ]* 1.6 Write property test for history size limit
  - **Property 6: History size limit enforcement**
  - **Validates: Requirements 2.2, 2.4**

- [ ]* 1.7 Write property test for corrupted data handling
  - **Property 8: Corrupted data graceful handling**
  - **Validates: Requirements 2.5**

- [ ] 2. Integrate Save System with Game
- [ ] 2.1 Initialize save system on game load
  - Call SaveManager.load() at game startup
  - Initialize HighScoreManager and HistoryManager with loaded data
  - Add high score display to UI panel
  - _Requirements: 1.2_

- [ ] 2.2 Hook save system into game over and level complete
  - Modify gameOver() to save session and check for new high score
  - Modify levelComplete() to save session and check for new high score
  - Trigger confetti effect if new high score achieved (placeholder for now)
  - _Requirements: 1.1, 1.3_

- [ ] 2.3 Add game history UI display
  - Create collapsible history panel below game canvas
  - Display formatted history entries (date, score, completion status)
  - Add toggle button to show/hide history
  - Style history panel to match game aesthetic
  - _Requirements: 2.2, 2.3_

- [ ]* 2.4 Write unit tests for save system integration
  - Test game initialization loads high score correctly
  - Test game over saves session data
  - Test level complete saves session data
  - _Requirements: 1.1, 1.2, 2.1_

- [x] 3. Implement Particle System Core





- [x] 3.1 Create base Particle class


  - Implement constructor with position, velocity, color, size, lifetime
  - Implement update() method to move particle and age it
  - Implement isDead() to check if particle exceeded lifetime
  - Implement getOpacity() to calculate fade based on age
  - Implement draw() method with camera offset support
  - _Requirements: 7.1, 7.2_

- [x] 3.2 Create ParticleManager


  - Implement add() to add particles to managed array
  - Implement update() to update all particles and remove expired ones
  - Implement draw() to render all particles with camera offset
  - Implement clear() to remove all particles
  - Add particle count limit (1000 max) for performance
  - _Requirements: 7.1, 7.2, 7.4, 7.5_

- [ ]* 3.3 Write property test for particle lifetime removal
  - **Property 11: Particle lifetime removal**
  - **Validates: Requirements 3.3, 3.4, 4.5, 5.5**

- [ ]* 3.4 Write property test for particle system update and cleanup
  - **Property 26: Particle system update and cleanup**
  - **Validates: Requirements 7.2, 7.4**

- [ ]* 3.5 Write property test for unified particle array
  - **Property 25: Unified particle array**
  - **Validates: Requirements 7.1**

- [ ] 4. Implement Particle Effects
- [ ] 4.1 Create TrailParticles system
  - Implement spawn() method with frame counter for spawn rate
  - Create trail particles with purple color and 30 frame lifetime
  - Add random velocity variation for natural look
  - Integrate into updatePlayer() to spawn during movement
  - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5_

- [ ]* 4.2 Write property test for trail particle spawning
  - **Property 9: Trail particle spawning on movement**
  - **Validates: Requirements 3.1**

- [ ]* 4.3 Write property test for trail particle spawn rate
  - **Property 12: Trail particle spawn rate**
  - **Validates: Requirements 3.5**

- [ ] 4.4 Create ExplosionParticles system
  - Implement ExplosionParticle class extending Particle with gravity and decay
  - Implement create() method to spawn 15-25 particles radiating outward
  - Add random velocities in all directions
  - Integrate into loseLife() to trigger on enemy collision
  - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5_

- [ ]* 4.5 Write property test for explosion particle count
  - **Property 14: Explosion particle count range**
  - **Validates: Requirements 4.2**

- [ ]* 4.6 Write property test for explosion velocity distribution
  - **Property 15: Explosion particle velocity distribution**
  - **Validates: Requirements 4.3**

- [ ]* 4.7 Write property test for explosion particle physics
  - **Property 16: Explosion particle physics**
  - **Validates: Requirements 4.4**

- [ ] 4.8 Create SparkleParticles system
  - Implement SparkleParticle class with upward drift
  - Implement create() method to spawn 8-12 particles with upward velocity
  - Alternate colors between purple and white
  - Integrate into updateCollectibles() to trigger on collection
  - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5_

- [ ]* 4.9 Write property test for sparkle particle count and direction
  - **Property 18: Sparkle particle count and direction**
  - **Validates: Requirements 5.2**

- [ ]* 4.10 Write property test for sparkle color alternation
  - **Property 19: Sparkle particle color alternation**
  - **Validates: Requirements 5.3**

- [ ] 4.11 Create ConfettiParticles system
  - Implement ConfettiParticle class with gravity, drift, and rotation
  - Implement create() method to spawn 50-80 particles across screen
  - Use varied colors (purple, white, gold, pink)
  - Integrate into high score achievement in save system integration
  - _Requirements: 6.1, 6.2, 6.3, 6.4, 6.5_

- [ ]* 4.12 Write property test for confetti particle count and variety
  - **Property 22: Confetti particle count and variety**
  - **Validates: Requirements 6.2, 6.3**

- [ ]* 4.13 Write property test for confetti particle physics
  - **Property 23: Confetti particle physics**
  - **Validates: Requirements 6.4**

- [ ] 5. Integrate Particle System with Game Loop
- [ ] 5.1 Add particle system to game loop
  - Call ParticleManager.update() in gameLoop()
  - Call ParticleManager.draw() in gameLoop() after drawing game objects
  - Call ParticleManager.clear() in restartGame()
  - _Requirements: 7.2, 7.3, 7.5_

- [ ] 5.2 Connect all particle triggers
  - Verify trail particles spawn during player movement
  - Verify explosion particles spawn on enemy collision
  - Verify sparkle particles spawn on collectible collection
  - Verify confetti particles spawn on new high score
  - _Requirements: 3.1, 4.1, 5.1, 6.1_

- [ ]* 5.3 Write unit tests for particle integration
  - Test particle system updates in game loop
  - Test particle system clears on restart
  - Test all particle effects trigger correctly
  - _Requirements: 7.2, 7.5_

- [x] 6. Implement Audio System (BONUS FEATURE)


- [x] 6.1 Create SoundEffects system with Web Audio API


  - Implement playJump() sound with upward pitch sweep (300Hz → 600Hz)
  - Implement playCollect() sound with bright chime (800Hz → 1600Hz)
  - Implement playStomp() sound with downward thump (200Hz → 50Hz)
  - Implement playDeath() sound with descending tone (400Hz → 100Hz)
  - Implement playVictory() fanfare with 4-note melody (C5-E5-G5-C6)
  - Use oscillators and gain nodes for retro 8-bit style sounds
  - _Bonus Feature: Enhanced game feel with audio feedback_

- [x] 6.2 Create BackgroundMusic system


  - Implement looping melody pattern with retro-style notes
  - Create start() method to begin music playback
  - Create stop() method to halt music
  - Implement playMelody() with recursive looping
  - Set low volume (0.08) to not overpower sound effects
  - _Bonus Feature: Continuous background music_

- [x] 6.3 Integrate audio into game events


  - Add jump sound to player jump action
  - Add collect sound to collectible pickup
  - Add stomp sound to enemy defeat
  - Add death sound to loseLife() function and falling
  - Add victory sound to goal reached
  - Stop background music on game over and level complete
  - Restart background music on game restart
  - _Bonus Feature: Complete audio integration_

- [x] 6.4 Add user interaction for audio start


  - Add click listener to canvas to start background music
  - Add visual prompt "Click on game to start music"
  - Hide prompt after music starts
  - Handle audio context autoplay restrictions
  - _Bonus Feature: Browser-compliant audio initialization_

- [x] 7. Implement Enhanced Gameplay Mechanics (BONUS FEATURES)


- [x] 7.1 Add Mario-style enemy stomping


  - Detect when player lands on enemy from above
  - Check player velocityY > 0 and position relative to enemy
  - Defeat enemy and set alive = false
  - Add bounce effect (velocityY = -8) after stomp
  - Award bonus points (+10) for enemy defeat
  - Play stomp sound effect
  - Skip dead enemies in update and draw loops
  - _Bonus Feature: Enhanced combat mechanics_

- [x] 7.2 Add death animation for falling


  - Trigger death animation when falling off screen
  - Play death sound when falling (same as enemy collision)
  - Apply spinning rotation effect
  - Deduct life and check for game over
  - _Bonus Feature: Consistent death feedback_

- [x] 7.3 Add left boundary wall


  - Prevent player from moving past x = 0
  - Reset velocityX to 0 at boundary
  - Ensure player stays within game bounds at start
  - _Bonus Feature: Better level boundaries_

- [x] 7.4 Add enemy alive state tracking


  - Add alive property to all enemies (default: true)
  - Reset alive state in restartGame()
  - Filter dead enemies from rendering and collision
  - _Bonus Feature: Persistent enemy defeat state_

- [ ] 8. Final Testing and Polish
- [ ] 8.1 Test complete save system workflow
  - Play game and verify score saves
  - Verify high score updates correctly
  - Verify history displays correctly
  - Test with corrupted localStorage data
  - _Requirements: 1.1, 1.2, 1.3, 2.1, 2.2, 2.3_

- [ ] 8.2 Test complete particle system workflow
  - Verify all particle effects render correctly
  - Test particle performance with many particles
  - Verify particles clean up properly
  - Test particle effects with camera scrolling
  - _Requirements: 3.1, 4.1, 5.1, 6.1, 7.1, 7.2, 7.4_

- [x] 8.3 Test audio system


  - Verify all sound effects play correctly
  - Test background music looping
  - Verify music stops on game over/complete
  - Test click-to-start music functionality
  - _Bonus Feature: Complete audio testing_

- [x] 8.4 Test enhanced gameplay mechanics


  - Verify enemy stomping works correctly
  - Test death animation on falling
  - Verify left boundary prevents movement
  - Test enemy defeat state persistence
  - _Bonus Feature: Enhanced mechanics testing_

- [ ] 8.5 Ensure all tests pass, ask the user if questions arise

- [x] 9. Documentation and Deployment (BONUS)


- [x] 9.1 Create comprehensive README.md


  - Document all features and gameplay mechanics
  - Add controls and how to play instructions
  - Include technical details and architecture
  - Add audio system documentation
  - Document enemy types and combat mechanics
  - Add project structure and future enhancements
  - _Bonus Feature: Complete documentation_

- [x] 9.2 Set up Git repository and push to GitHub


  - Initialize git repository
  - Create .gitignore for IDE and OS files
  - Add remote repository
  - Commit with detailed message
  - Resolve merge conflicts
  - Push to https://github.com/CristianHisas/super-kiro-world
  - _Bonus Feature: Version control and sharing_
