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
  - [Core Updates](#core-updates-patch-40)
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





--------------------------------------




## Combat Updates <a name="combat-updates-patch-40"></a>

*Section coming next.*


## ModularCharacter Updates <a name="modularcharacter-updates-patch-40"></a>

*Section coming next.*


## Items & Crafting Updates <a name="items--crafting-updates-patch-40"></a>

*Section coming next.*


## Inventory Manager Updates <a name="inventory-manager-updates-patch-40"></a>

*Section coming next.*


## Merchant & Economy Updates <a name="merchant--economy-updates-patch-40"></a>

*Section coming next.*
