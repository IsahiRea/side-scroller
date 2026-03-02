# Side-Scroller: Player Controller Implementation Plan

## Overview
Build a 2D player controller with movement, jumping, and physics - the foundation for the side-scroller game. Designed for pixel art with future support for melee + ranged combat.

## Architecture

### Scripts to Create
```
Assets/Scripts/
├── Player/
│   ├── PlayerController.cs      # Main controller (movement, input handling)
│   ├── PlayerGroundCheck.cs     # Ground detection component
│   └── PlayerAnimator.cs        # Animation state management
└── Core/
    └── GameInput.cs             # Input System wrapper (optional, for cleaner code)
```

### Component Structure
The Player GameObject will have:
- `Rigidbody2D` - Physics (Dynamic, Freeze Rotation Z)
- `BoxCollider2D` - Main collision
- `SpriteRenderer` - Pixel art sprite (Point filtering, no compression)
- `Animator` - Animation controller
- `PlayerController` - Custom movement script
- `PlayerGroundCheck` - Raycast/overlap ground detection

## PlayerController Features

### Movement
- Horizontal movement with acceleration/deceleration (feels responsive but not twitchy)
- Configurable speed via `[SerializeField]` fields
- Uses `Rigidbody2D.velocity` for physics-based movement

### Jumping
- Variable jump height (hold jump = higher, tap = shorter)
- Coyote time (~0.1s grace period after leaving ground)
- Jump buffering (register jump input slightly before landing)
- Single jump (can expand to double-jump later)

### Ground Detection
- Separate component using `Physics2D.OverlapBox` or raycast
- LayerMask for ground layers only
- Events for `OnLanded` / `OnLeftGround`

## Input System Setup
Use the existing `InputSystem_Actions.inputactions`:
- Map "Move" action → Vector2 for horizontal input
- Map "Jump" action → Button

## Pixel Art Settings
Configure for crisp pixel art:
- Sprites: Filter Mode = Point, Compression = None
- Camera: Pixel Perfect Camera component (from 2D package)
- Rigidbody2D: Interpolate for smooth movement

## Implementation Order
1. Create folder structure (`Assets/Scripts/Player/`)
2. Create basic `PlayerController.cs` with horizontal movement
3. Add `PlayerGroundCheck.cs` for ground detection
4. Add jumping with coyote time and jump buffering
5. Create placeholder sprite (colored square for now)
6. Set up Player prefab with all components
7. Configure Input System actions
8. Test in SampleScene

## Configurable Parameters (Inspector)
```csharp
[Header("Movement")]
[SerializeField] private float moveSpeed = 8f;
[SerializeField] private float acceleration = 50f;
[SerializeField] private float deceleration = 50f;

[Header("Jumping")]
[SerializeField] private float jumpForce = 12f;
[SerializeField] private float coyoteTime = 0.1f;
[SerializeField] private float jumpBufferTime = 0.1f;
[SerializeField] private float fallMultiplier = 2.5f;
[SerializeField] private float lowJumpMultiplier = 2f;
```

## Verification
1. Enter Play mode in Unity
2. Use A/D or Arrow keys to move left/right
3. Press Space to jump
4. Verify:
   - Player moves smoothly with acceleration
   - Jump height varies based on button hold duration
   - Can jump shortly after walking off a ledge (coyote time)
   - Jump registers if pressed just before landing (buffering)
   - Player doesn't rotate or behave erratically

## Future Expansion Points
- `PlayerCombat.cs` - Will handle melee/ranged attacks
- `PlayerHealth.cs` - Will handle damage and death
- Animation states: Idle, Run, Jump, Fall, Attack
