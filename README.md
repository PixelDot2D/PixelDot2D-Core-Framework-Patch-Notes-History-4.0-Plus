# PixelDot2D Core Framework Patch Notes History - 4.0+

Full Patch Note History for the PixelDot2D Core Framework, beginning with **Version 4.0**.

**Available on the:** [Unity Asset Store](https://assetstore.unity.com/packages/tools/utilities/pixeldot2d-core-framework-370674)

> [!IMPORTANT]
> **Version 4.0 represents a major architectural milestone for the PixelDot2D Framework.**
>
> This release introduces substantial framework refactoring, new modular systems, performance improvements, expanded combat architecture, data-driven character animation, crafting, merchants, and multi-currency economy support.
>
> Due to the scale of these changes, Version 4.0 begins a new patch-note history. Previous releases are archived separately in the **Pre-4.0 Patch Notes**.

> [!NOTE]
> **Looking for previous releases?**
>
> The complete patch-note history for Versions 2.x–3.x can be found here:
>
> **[PixelDot2D Core Framework Patch Notes History - Pre 4.0](https://github.com/PixelDot2D/PixelDot2D-Core-Framework-Patch-Notes-History-Pre-4.0)**

---

# Table Of Contents

- [Version 4.0 Overview](#version-40-overview)
- [Patch 4.0](#patch-40)
  - [Breaking Changes & Migration Notes](#breaking-changes--migration-notes-patch-40)
    - [Unity Version Requirement](#unity-version-requirement-patch-40)
    - [Older Engine Versions](#older-engine-versions-patch-40)
  - [Core Framework & Performance](#core-framework--performance-patch-40)
  - [Movement Updates](#movement-updates-patch-40)
  - [Combat Updates](#combat-updates-patch-40)
  - [ModularCharacter Updates](#modularcharacter-updates-patch-40)
  - [Items & Crafting Updates](#items--crafting-updates-patch-40)
  - [Inventory Manager Updates](#inventory-manager-updates-patch-40)
  - [Merchant & Economy Updates](#merchant--economy-updates-patch-40)


---

# Version 4.0 Overview

Version 4.0 is a major framework and architecture update focused on performance, decoupled systems, expanded combat capabilities, data-driven workflows, and new economy and crafting infrastructure.

> [!IMPORTANT]
> **Upgrade Notice:** This release raises the minimum supported Unity version and introduces several breaking changes. Please review the **[Breaking Changes & Migration Notes](#breaking-changes--migration-notes-patch-40)** section before upgrading.

### Framework

- **Unity 6.6 Minimum Baseline:** Raised the minimum supported Unity version from Unity 6.5 to Unity 6.6.
- **Native Dictionary Serialization:** Introduced native dictionary serialization to support more flexible data-driven configurations.
- **Allocation-Free Static Metadata:** Converted applicable static validation data to `ReadOnlySpan<T>`-based implementations to eliminate unnecessary heap allocations.
- **Collection Reuse:** Improved internal collection management to reduce unnecessary allocations and improve runtime efficiency.
- **2D Rotation Utilities:** Added high-efficiency 2D rotation and transformation utilities.
- **Animation Sequence Tracking:** Refactored `AnimationPlayer2D` sequence tracking into a simplified countdown-based system.

### Movement

- **Coordinate Delegate Refactor:** Updated movement coordinate delegates to use value-based `(bool isValidCoordinates, Vector2 coordinates)` results rather than requiring direct Unity object references.
- **Rotational Movement:** Added a new ScriptableObject-driven rotational movement state.
- **Waypoint Loop Modes:** Added `Loop`, `PingPong`, and `Continuous` waypoint behaviors.

### Combat

- **Expanded Modular Weapon Architecture:** Expanded the existing Lego-like weapon construction system with additional Aiming, Virtual Transform, Execution Gate, and Execution modules.
- **New Aiming & Virtual Transform Strategies:** Added additional building blocks for creating complex weapon behaviors.
- **Charge Gates:** Added configurable charge-based execution gates.
- **Contextual Cooldowns:** Added execution-locked and parallel cooldown behaviors.
- **Unified HitBox Execution:** Consolidated multiple legacy HitBox execution states into a single polymorphic architecture.
- **Decoupled HitBox & HitScan Visual Systems:** Separated visual rendering from core weapon simulation through ScriptableObject-driven visual proxies.
- **Global `IDamageable` Caching:** Added shared component caching to reduce repeated component lookups across weapon and projectile execution.
- **Projectile Visual State Machine:** Separated projectile visual behavior from the core projectile file into it's own State Machine.
- **Weapon Execution Feedback:** Added boolean execution feedback to allow systems to determine when a weapon successfully fired.

### ModularCharacter

- **No-Code Sequential Animation Chains:** Added ScriptableObject-driven animation transitions that can chain multiple states without additional code.
- **Dictionary-Based Animation Configuration:** Migrated animation transition data to native serialized dictionaries.
- **Deterministic Animation Fallbacks:** Added fallback handling for missing or incomplete animation transition configurations.
- **Controller Cleanup:** Streamlined internal controller state tracking and orchestration.
- **State Hierarchy Hardening:** Restricted runtime modification of the default character state to preserve deterministic state prioritization.

### Items & Economy

- **Universal `CraftingStation`:** Added a decoupled crafting system that can be attached to NPCs, world objects, containers, or other custom objects.
- **Multi-Inventory Material Processing:** Added support for gathering crafting materials across multiple inventories.
- **Inventory Query Improvements:** Added `HasSpaceForItem` and `GetItemStackCount` utilities.
- **Decentralized Currency Ledger:** Added a flexible multi-currency account system.
- **Multi-Currency Merchant Support:** Added ScriptableObject-driven merchant inventories and multi-token pricing.
- **Atomic Transactions:** Added all-or-nothing transaction processing to prevent partial currency deductions.
- **Save-System Integration:** Integrated currency accounts directly into the Core save/load architecture.
- **Integration Interfaces:** Added `IMerchant`, `IInventory`, `ICurrency`, and `ICraftingStation` interfaces for clean MonoBehaviour-to-C# system integration.

### Developer Action Required

Before upgrading to Version 4.0, review the following:

- **Unity 6.6 Requirement:** Unity 6.6 is now the minimum supported framework version.
- **ModularCharacter Migration:** Review the changes to `Base_Data_ModularCharacter_Player` and `Base_State_ModularCharacter_Player`.
- **Default State Restriction:** The ModularCharacter default state can no longer be changed dynamically at runtime.
- **Static Cache Cleanup:** Systems using the new static caching architecture must perform the required cleanup during scene transitions, level reloads, or other appropriate lifecycle events.
- **Merchant Requirements:** The new Merchant folder requires Unity 6.6.
- **Weapon Execution Migration:** Review any integrations that rely on the previous weapon execution classes.
- **Movement API Changes:** Review code using the previous `GetCoordinatesDelegate` signature.
- **Targeting API Changes:** Review code using the previous `SetTargetCoordinatesAction` signature.
- **Default State Access:** Review any systems that previously relied on dynamically modifying the ModularCharacter default state.


---

# Patch 4.0 <a name="patch-40"></a>

## Breaking Changes & Migration Notes <a name="breaking-changes--migration-notes-patch-40"></a>

### Unity Version Requirement <a name="unity-version-requirement-patch-40"></a>

The minimum supported Unity version has been raised from **Unity 6.5 → Unity 6.6**.

This update is required to support native dictionary serialization and continues the framework's long-term modernization toward the Unity 7/LTS roadmap.

### Older Engine Versions <a name="older-engine-versions-patch-40"></a>

Developers who remain on an older Unity version may be able to bypass some breaking script modifications by:

- Maintaining local copies of:
  - `Base_Data_ModularCharacter_Player.cs`
  - `Base_State_ModularCharacter_Player.cs`
- Skipping ScriptableObject asset re-imports originating from `ModularCharacter`.

However, upgrading to Unity 6.6 is strongly recommended.

> [!IMPORTANT]
> **Merchant Requirement:** The entire new **Merchant** folder also requires Unity 6.6.

The framework's architectural direction also prepares the codebase to take advantage of newer C# functionality, including `params ReadOnlySpan<T>` optimizations and other low-level performance techniques.


---

## Core Framework & Performance <a name="core-framework--performance-patch-40"></a>

### Allocation-Free Static Metadata

Static validation arrays have been converted to `ReadOnlySpan<T>`-based static metadata where applicable.

This eliminates unnecessary heap allocations by allowing the framework to reference read-only assembly metadata directly rather than creating runtime array instances.

### Collection Reuse

Internal state-tracking collections—including:

- Lists
- Dictionaries
- HashSets

have been refactored to utilize structural `readonly` fields where applicable.

This reduces unnecessary allocations and improves collection reuse throughout the framework.

### 2D Rotation System

Introduced a new high-efficiency 2D rotation extension suite designed to decouple 2D state and movement logic from Unity's default 3D Euler-angle conversion pipeline.

#### `Transform.GetLocalRotation2D()`

Added a pure math utility that reconstructs a 2D rotation angle directly from the underlying Quaternion components.

**Features:**

- Returns a continuous, normalized `0–360°` angle.
- Bypasses the additional engine boundary involved with `transform.localEulerAngles.z`.
- Reduces precision drift associated with repeated Quaternion-to-Euler conversions.

#### `Transform.Rotate2D(float targetAngle)`

Added an absolute 2D rotation setter that directly updates the underlying local rotation.

This avoids unnecessary 3D rotation handling and prevents common gimbal-lock and axis-flipping artifacts associated with Euler-based rotation workflows.

#### `Transform.Rotate2D(float speed, float deltaTime, float timeScale)`

Added an incremental rotation overload for continuously rotating objects.

### AnimationPlayer2D

Animation sequence tracking has been refactored from a count-up system into a countdown-based system.

#### Countdown Tracking

The sequence counter now counts down toward zero as the animation queue progresses.

When no animation is currently playing, the sequence returns:

```text
-1
```
This simplifies queue-state checks and makes event-driven logic easier to implement.
Simplified Event Handling

Developers can now use the m_OnFrameChange event to identify specific final animation sequences without needing to know how many sequences were originally added to the animation queue.

For example:
```text
if (sequence == 0 && frame == 3)
{
    // Play footstep sound
}
```
This allows developers to trigger events based directly on the current sequence and frame, without needing to calculate the original animation queue length.

---

### RB2DMovementManager <a name="movement-updates-patch-40"></a>

#### Coordinate Delegate Refactor

`GetCoordinatesDelegate` has been updated to return:

```text
(bool isValidCoordinates, Vector2 coordinates)
```

This architectural change removes the movement system's dependency on raw Unity `GameObject` references.

Developers can now provide custom spatial data such as:

- Tracking offsets
- Geometric centers
- Arbitrary spatial coordinates
- Dynamically generated positions

without requiring placeholder anchor `Transform` objects.

The boolean validity flag also acts as a lifecycle boundary. Returning `false` signals that the provided coordinates are invalid or unavailable, allowing the steering system to safely ignore that coordinate update.

### New Rotational Movement State

#### `State_RB2DMovement_Rotating`

Added a new ScriptableObject-driven rotational movement state.

**Features:**

- Configurable angular velocity.
- Optional forward propulsion.
- Uses `RB2DMovementManager` right-vector calculations to determine the propulsion direction.
- Can be layered with other movement states to create complex and highly customizable movement patterns.

When forward propulsion is enabled, the state uses the current right-vector calculated by `RB2DMovementManager` to determine the direction of linear movement while simultaneously applying the configured rotational velocity.

A preconfigured example has been added under:

Combat → SO → Weapons → Projectiles → Rotating

This provides a ready-to-use reference for combining rotational movement with projectile behavior.

### RB2DMovement_FollowPoints

Added configurable waypoint iteration modes through `Enum_RB2DMovementFollowPointsLoop`.

- Loop
Traverses the waypoint array sequentially.
Once the final waypoint is reached, traversal returns to element `0` and repeats the path.

- PingPong
Traverses the waypoint array forward until the final waypoint is reached, then reverses the traversal direction and moves back through the array.

- Continuous
Traverses the waypoint sequence normally, then dynamically recalculates all waypoint offsets relative to the final endpoint.
This allows the movement pattern to maintain an uninterrupted, infinitely extending trajectory rather than restarting from the original waypoint positions.


---


## Combat Updates <a name="combat-updates-patch-40"></a>

Version 4.0 introduces a major expansion of the **Weaponized Module architecture**.

### A Note on the Modular Weapon System

The Weaponized Module system is built around a Lego-like architecture. Each weapon is assembled from four independent ScriptableObject components:

- **Aiming**
- **Virtual Transform**
- **Execution Gate**
- **Execution**

Version 4.0 significantly expands the available options within each of these categories by introducing new modular components that can be freely combined with both existing and newly added modules.

Because each component can be mixed and matched independently, every new module exponentially expands the number of possible weapon configurations. Rather than adding a single new weapon behavior, these additions expand the building blocks available to the entire system, allowing developers to create increasingly complex, specialized, and unique weapons entirely through the Unity Editor using ScriptableObjects.

The result is a highly composable weapon system where new behaviors can be layered together without requiring a dedicated implementation for every individual weapon.

---

### Aiming Modules

**Base Class:** `Base_State_WeaponizedModule_Aiming`

#### Look At Source
- Tracks the spatial delta between the raw virtual origin and the final calculated execution point.
- Includes an **Inverse** option for easily switching between inward-facing and outward-facing behavior.

#### Look At Target
- Tracks dynamically supplied target coordinates through: `Func<(bool isValidCoordinates, Vector2 coordinates)>`
- Target coordinates are supplied through `SetTargetCoordinatesAction()`.

This allows aiming behavior to remain completely decoupled from direct scene-object references while supporting dynamically generated or externally managed target positions.

---

### Virtual Transform Strategies

**Base Class:** `Base_State_WeaponizedModule_VirtualTransform`

#### Orbit Around Source
- Provides continuous orbital movement using raw angle accumulation rather than direct Transform dependencies.
- This allows the virtual execution point to maintain orbital motion without requiring a physical Transform to act as the underlying movement reference.

#### Radius Anchored Orbit
- Projects a reference point along a relative vector toward a target.
- The system supports flexible `Vector2` radial profiles, allowing developers to create:
  - Asymmetrical paths
  - Skewed paths
  - Elliptical paths

#### Clamp 8-Directional
- Constrains placement vectors to a classic top-down eight-directional grid.
- This provides a clean directional filter for weapons or other systems that require discrete eight-way orientation.

#### Camera Position
- Anchors a virtual execution Transform to a specified camera position.
- Supports optional runtime positioning offsets for additional control over the final virtual execution position.

---

### Execution & Gating

#### `WeaponizedModule_Execution_HitBox`

Introduced a new unified execution architecture that replaces several legacy execution modules.

See the dedicated **[HitBox Execution System](#hitbox-execution-system-patch-40)** section below for the full architectural breakdown.

#### Charge Gate
- Added a configurable charging gate that controls weapon execution based on accumulated charge.
- The charge progresses whenever `TryExecute` returns `true` and authorizes execution once the configured charge requirement has been completed.

Supported behaviors include:

- **Automatic Charge Decay:** Charge can progressively decrease when the charging condition is no longer met.
- **Charge Retention:** Previously accumulated charge can be retained between charging attempts.
- **Complete Reset:** Charge can be completely reset when input is released.

---

### Contextual Cooldowns

#### `State_WeaponizedModule_ExecutionGate_Cooldown`
- Added contextual cooldown behavior through a new execution-lock configuration.

#### Execution-Locked Cooldowns
- When enabled, the cooldown timer pauses while the associated execution is active.
- This is particularly useful for:
  - Persistent field deployments.
  - Long-duration HitBox executions.
  - Delayed-delivery weapons.

With execution locking enabled, the cooldown does not continue progressing until the active execution has finished.

#### Parallel Cooldowns
- When execution locking is disabled, the cooldown continues counting down while the execution remains active.
- This preserves the traditional cooldown behavior, allowing the cooldown and execution processes to run in parallel.

---

### HitBox Execution System

#### Unified `WeaponizedModule_Execution_HitBox`

The following execution states have been consolidated:

- `State_WeaponizedModule_Execution_CircleCast`
- `State_WeaponizedModule_Execution_BoxCast`
- `State_WeaponizedModule_Execution_Swing`
- `State_WeaponizedModule_Execution_Capsule`

They are now handled by a single unified execution state:

`State_WeaponizedModule_Execution_HitBox`

The new system uses the Core's polymorphic `CollisionManager` architecture to handle different HitBox shapes without requiring modifications to the execution class itself.

**Benefits:**

- One unified execution pipeline.
- Polymorphic collision shape support.
- Simplified future shape expansion.
- Existing weapon ScriptableObjects have been migrated to the new configuration.
- Legacy execution classes have been removed.

---

### HitBox Update Modes

Added `Enum_HitBoxUpdateType` to control how HitBox position and rotation are tracked during execution.

HitBoxes can now use:

- **Continuous:** Continuously tracks the Virtual Transforms position and rotation.
- **Snapshotted:** Captures the position and/or rotation at the appropriate execution point and maintains those values throughout the execution.

This enables behaviors such as stationary four-laser cross patterns that lock their spawn position while continuing to rotate.

The implementation's design constraints and extension guidelines are documented directly within `State_WeaponizedModule_Execution_HitBox.cs`.

---

### HitBox Visual Proxy Architecture

Added:

`Base_SO_HitBoxVisual`

The new visual proxy system completely decouples HitBox rendering from the core simulation layer.

Developers can inject custom visual behavior into `State_WeaponizedModule_Execution_HitBox` without modifying the core execution system or introducing event-based dependencies.

Possible visual implementations include:

- Standard sprites.
- Animated sprites.
- Rotating sprites.
- Swinging weapons.
- Large energy beams.
- Custom graphical effects.

This architecture allows the execution system to remain completely unaware of how its HitBox is visually represented.

---

### Reference Implementations

Three production-ready reference configurations have been added:

- **Sprite Default:** Standard sprite-based HitBox visualization.
- **Animation:** Uses the Core's animation system for animated HitBox visuals.
- **Rotating:** Provides a continuously rotating sprite-based visualization.

---

### HitScan Visual Pipeline

#### `PooledObject_HitScanVisual`

The HitScan visual system has been completely redesigned.

The pooled object now acts as a lightweight, allocation-free hardware shell.

Procedural rendering and animation logic have been removed from the pooled object and moved into dedicated, preallocated C# state classes.

#### HitScan Proxy Pattern

Added:

`Base_SO_HitScanVisual`

The physics simulation is now completely independent of the visual implementation.

`State_WeaponizedModule_Execution_HitScan` passes generic spatial information to the visual proxy without having any knowledge of how that information is rendered.

Comprehensive XML documentation has also been added to `Base_SO_HitScanVisual`, including:

- Architectural design goals.
- Extension instructions.
- Three-tier framework relationships.
- Production implementation examples.

---

### WeaponizedModule Targeting API

`SetTargetCoordinatesAction` has been updated to use:

`Func<(bool isValidCoordinates, Vector2 coordinates)>`

This standardizes the targeting API with the movement system.

Developers can now supply:

- Dynamic spatial locations.
- Manual offsets.
- Geometric centers.
- Custom tracking systems.

without creating dummy `GameObject` or child `Transform` objects.

Returning `false` safely aborts tracking when the target becomes invalid.

---

### Weapon Execution Feedback

`TryExecute()` and `FixedUpdate()` now return a boolean indicating whether a weapon successfully fired during that execution cycle.

When using `WeaponManager`, the system returns `true` if at least one weapon successfully discharges.

This allows developers to reliably track weapon firing events without implementing separate detection logic.

---

### `RB2DMovement_CombatManager` Lifecycle Cleanup

Internal lifecycle logic has been streamlined.

When a movement pattern transitions to a new outer index, the manager now preserves the existing virtual weapon configuration rather than resetting it when the two sub-scripts are identical.

This prevents unnecessary weapon reinitialization and allows active execution gates—such as cooldowns—to retain their current progress.

> [!NOTE]
> No developer changes are required for this update.

---

### Global `IDamageable` Caching

A global `ComponentCache<T>` system has been integrated into:

`Base_State_WeaponizedModule_Execution`

All active execution modules now share a unified lookup pool for `IDamageable` references.

Secondary lookups can now be resolved through an O(1) cached lookup instead of repeatedly invoking expensive Unity component searches.

#### Projectile Integration

The same caching architecture has been integrated into:

`Base_ProjectileCollision`

Projectile impacts now route through the Weaponized Module's shared execution cache.

High-velocity projectiles therefore use and contribute to the same global lookup pool.

---

### Memory Management Requirement

The new static tracking architecture retains C# references to destroyed entities.

Because Unity objects can exhibit "fake null" behavior, stale references from destroyed or transient objects can remain within static structures.

Developers must therefore perform cache cleanup during appropriate lifecycle events, including:

- Scene transitions.
- Level reloads.
- Structural garbage-collection phases.

Use:

```text
WeaponizedModule.CleanCacheOfNulls();
```

This single centralized cleanup call clears stale references for both the Weaponized Module execution and projectile systems.

> [!WARNING]
> Because these caches use static tracking structures, failing to perform the required cleanup can cause destroyed object references to remain in memory.

---

### Projectile Architecture

#### `PooledObject_Projectile`

The projectile architecture has been refactored to eliminate redundant data storage.

Individual primitive tracking variables have been replaced where possible with unified blueprint references, providing broader system access while reducing structural memory overhead.

#### Decoupled Visual Lifecycle

Sprite and visual processing has been removed from the projectile's core execution logic.

Projectile visuals now operate through an independent visual state machine within `FixedUpdate`.

This separates projectile simulation from visual behavior while allowing visual states to be independently configured and extended.

#### Available Visual States

##### `Look_At_Velocity`
- Aligns the projectile sprite with the current velocity vector.
- Includes optional Y-axis flipping when the projectile is traveling leftward.

##### `Velocity_Sprite_Flip`
- Tracks horizontal velocity only and uses standard X-axis sprite flipping.

##### `Animation_Player_2D`
- Plays an Animation ScriptableObject through the Core's `AnimationPlayer2D` system.

##### `Continuous_Rotation`
- Applies constant rotation to the projectile sprite.


---


## ModularCharacter Updates <a name="modularcharacter-updates-patch-40"></a>

### No-Code Animation Chains

`Base_Data_ModularCharacter_Player` now supports context-aware, sequential animation chains entirely through ScriptableObject configuration.

Developers can create multi-stage animation transitions such as:

- `Descend → Land → Idle`
- `Descend → Roll → Run`

without writing any additional code.

This allows complex animation workflows to be configured directly through the Unity Inspector using the existing ScriptableObject-driven architecture.

---

### Native Dictionary Serialization

ModularCharacter data now takes advantage of native dictionary serialization.

This replaces the previous rigid animation assignment structure with a more flexible lookup-based configuration, allowing state-to-state animation relationships to be defined directly within the ScriptableObject.

---

### Deterministic Fallback Resolution

The new animation system includes multiple fallback layers to ensure that missing or incomplete animation configurations do not result in animationless states.

#### Zero-Size Dictionary

If the animation dictionary has zero dimensions, the system automatically falls back to the configured global fallback state asset.

#### Missing Key

If a specific state-to-state transition is not defined within the dictionary, the system automatically routes the transition to the configured fallback animation.

This ensures that a valid animation is always available when no explicit transition has been configured.

#### Internal State Handover

Animation fields can remain blank when external inversion controls or explicitly managed states are responsible for controlling the visual lifecycle independently.

---

### ModularCharacterController Refactor

The controller's internal orchestration has been streamlined and optimized.

Changes include:

- Removed redundant variables.
- Simplified core orchestration logic.
- Replaced boolean state flags with count-based list evaluation.
- Improved long-term scalability of internal state tracking.

> [!NOTE]
> These are internal architectural changes and do not alter existing public API behavior.



## Items & Crafting Updates <a name="items--crafting-updates-patch-40"></a>

*Section coming next.*


## Inventory Manager Updates <a name="inventory-manager-updates-patch-40"></a>

*Section coming next.*


## Merchant & Economy Updates <a name="merchant--economy-updates-patch-40"></a>

*Section coming next.*
