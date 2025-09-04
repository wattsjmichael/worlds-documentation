# Beginner WaveSpawner Tutorial

## Goal
This script creates a **spawner** in Horizon Worlds that automatically generates enemies at a fixed interval (every 1 second).

---

## 1. Understanding the Script

### Importing Dependencies
```ts
import { Component, PropTypes, Asset } from 'horizon/core';
```
- `Component`: lets us define custom behavior for an entity.  
- `PropTypes`: allows us to define **editable properties** (e.g., assigning a prefab in the editor).  
- `Asset`: represents a prefab or saved object in Horizon Worlds.  

---

### Defining the Component
```ts
export class WaveSpawner extends Component<typeof WaveSpawner> {
  static propsDefinition = {
    enemyPrefab: { type: PropTypes.Asset },
  };
```
- Creates a new component called `WaveSpawner`.  
- Adds **one property**: `enemyPrefab`, which must be an **Asset** (your enemy prefab).  

---

### Start Method
```ts
override start() {
  if (!this.props.enemyPrefab) {
    console.error("WaveSpawner: 'enemyPrefab' property is not set.");
    return;
  }

  this.async.setInterval(() => {
    this.spawnEnemy();
  }, 1000);
}
```
- Runs when the component starts.  
- Checks if `enemyPrefab` has been assigned.  
- If yes, sets up a **timer** to call `spawnEnemy()` every 1000 ms (1 sec).  

---

### Spawn Logic
```ts
private spawnEnemy() {
  const spawnPosition = this.entity.position.get();
  const spawnRotation = this.entity.rotation.get();

  this.world.spawnAsset(
    this.props.enemyPrefab!,
    spawnPosition,
    spawnRotation
  ).then(entities => {
    if (entities && entities.length > 0) {
      // console.log(`Spawned entity with ID: ${entities[0].id}`);
    }
  }).catch(error => {
    console.error("Failed to spawn enemy prefab:", error);
  });
}
```
- Uses the **entity’s position and rotation** as the spawn point.  
- Spawns the assigned prefab in the world.  
- Logs errors if spawning fails.  

---

### Register the Component
```ts
Component.register(WaveSpawner);
```
- Makes the component available in Horizon Worlds’ editor.  

---

## 2. Using the Spawner

1. **Create an enemy prefab**  
   - Build your enemy (model + scripts).  
   - Save it as an **Template Asset**.  

2. **Create a spawner entity**  
   - Add an empty object (or marker) where you want enemies to appear.  

3. **Attach the WaveSpawner script**  
   - Select your spawner entity.  
   - Add the `WaveSpawner` component.  

4. **Assign the prefab**  
   - In the inspector, set **enemyPrefab** to your enemy Asset.  

5. **Test**  
   - Press Play.  
   - An enemy spawns **every second** at the spawner’s location.  

---

## 3. Key Notes
- If no prefab is assigned, the script safely stops and prints an error.  
- Enemies will spawn forever until you stop the world or remove the spawner.  
- Be mindful of **performance** if too many enemies spawn.  

```ts
import { Component, PropTypes, Asset } from 'horizon/core';

export class WaveSpawner extends Component<typeof WaveSpawner> {
  static propsDefinition = {
    enemyPrefab: { type: PropTypes.Asset },
  };

  override start() {
    // Check if the enemyPrefab property has been assigned in the editor.
    if (!this.props.enemyPrefab) {
      console.error("WaveSpawner: 'enemyPrefab' property is not set.");
      return;
    }

    // Set up a timer that calls the spawnEnemy function every 1000 milliseconds (1 second).
    this.async.setInterval(() => {
      this.spawnEnemy();
    }, 1000);
  }

  private spawnEnemy() {
    // Get the position and rotation of the spawner entity.
    const spawnPosition = this.entity.position.get();
    const spawnRotation = this.entity.rotation.get();

    // Spawn the asset from the prefab property.
    this.world.spawnAsset(
      this.props.enemyPrefab!,
      spawnPosition,
      spawnRotation
    ).then(entities => {
      if (entities && entities.length > 0) {
        // console.log(`Spawned entity with ID: ${entities[0].id}`);
      }
    }).catch(error => {
      console.error("Failed to spawn enemy prefab:", error);
    });
  }
}

Component.register(WaveSpawner);
```

# AdvWaveSpawnerOnePoint — Next Level Tutorial
**One spawn point, three mobs, adjustable spread**

This step builds on the basic `WaveSpawner` by letting you spawn **up to three prefabs at once** from a **single spawn point**, with an adjustable **side‑to‑side spread** so they don’t overlap (perfect for “three soldiers abreast”).

---

## What you’ll get
- Spawn **1–3 prefabs simultaneously** on a fixed interval.
- Use a **single spawn point** (either the entity this script is on, or a separate marker via `spawnPoint`).
- Control **spacing** with a `spread` value (meters): left / center / right.

---

## The Code (drop in as `AdvWaveSpawnerOnePoint.ts`)
```ts
import { Component, PropTypes, Asset, Entity } from 'horizon/core';

export class AdvWaveSpawnerOnePoint extends Component<typeof AdvWaveSpawnerOnePoint> {
  static propsDefinition = {
    // Up to three prefabs; assign 1–3
    enemyPrefab1: { type: PropTypes.Asset },
    enemyPrefab2: { type: PropTypes.Asset },
    enemyPrefab3: { type: PropTypes.Asset },

    // Optional explicit spawn point; if not set, uses this.entity
    spawnPoint: { type: PropTypes.Entity },

    // Interval between spawns (ms)
    spawnMs: { type: PropTypes.Number, default: 1000 },

    // Horizontal spread (meters) to avoid overlap; 0 = same position
    spread: { type: PropTypes.Number, default: 0 },
  };

  private timerId: number | null = null;

  override start() {
    const anyPrefab =
      this.props.enemyPrefab1 || this.props.enemyPrefab2 || this.props.enemyPrefab3;

    if (!anyPrefab) {
      console.error("AdvWaveSpawnerOnePoint: No enemy prefab assigned.");
      return;
    }

    this.timerId = this.async.setInterval(() => {
      this.spawnGroup();
    }, this.props.spawnMs);
  }

  private spawnGroup() {
    const point = this.props.spawnPoint ?? this.entity;

    const basePos = point.position.get();
    const baseRot = point.rotation.get();

    const slots: Array<{ prefab?: Asset; offsetX: number }> = [
      { prefab: this.props.enemyPrefab1, offsetX: -this.props.spread }, // left
      { prefab: this.props.enemyPrefab2, offsetX: 0 },                  // center
      { prefab: this.props.enemyPrefab3, offsetX: +this.props.spread }, // right
    ];

    for (const slot of slots) {
      if (!slot.prefab) continue;

      // Apply a simple world-space X offset to reduce overlap
      const pos = new (basePos as any).constructor(
        basePos.x + (slot.offsetX || 0),
        basePos.y,
        basePos.z
      );

      this.world.spawnAsset(slot.prefab, pos, baseRot).catch((err) => {
        console.error("AdvWaveSpawnerOnePoint: Failed to spawn prefab:", err);
      });
    }
  }

}

Component.register(AdvWaveSpawnerOnePoint);
```

> Note: The `(basePos as any).constructor(...)` pattern builds a new position in the same vector type that `Horizon` uses.

---

## How to set it up

1. **Create or choose a spawn point**
   - Option A: Place this component on the entity that marks your spawn location.
   - Option B: Create an empty marker entity and assign it to the `spawnPoint` property.

2. **Assign prefabs**
   - Set `enemyPrefab1`, `enemyPrefab2`, `enemyPrefab3` to your mob Assets.
   - You can assign **one, two, or three** prefabs. Empty slots are ignored.

3. **Tune cadence and spacing**
   - `spawnMs`: interval between spawns (e.g., `1000` for 1 second).
   - `spread`: horizontal separation in meters. Try `0.5` to `1.0` for visible spacing.

4. **Press Play**
   - Watch your trio spawn in formation at the chosen point on each tick.

---

## Tips
- **Uniform trio**: assign the **same prefab** to all three slots.
- **Varied trio**: mix different prefabs across the three slots.
- **Marching behavior**: give each prefab its own movement/AI to walk forward after spawning.
- **Performance**: add despawn logic to enemies (on death or after N seconds) to avoid buildup.

---

## Troubleshooting
- *Nothing spawns*: ensure at least one `enemyPrefabX` is assigned.
- *Spawning at wrong place*: verify `spawnPoint` or the host entity transform.
- *Overlap too tight*: increase `spread` or add slight Z offsets inside the loop if needed.


# 🎵 Super Advanced BPM Wave Spawner Tutorial

## Goal
This component lets you spawn **up to 3 mobs** at a spawn point, but instead of a fixed interval it synchronizes spawning to **musical tempo (BPM)**.  
It supports:
- Changing BPM live
- Tap-tempo input
- Swing feel (jazz shuffle)
- Subdivisions (quarter, eighths, sixteenths, etc.)
- Spawn gating (every N beats)
- Smooth BPM transitions

Perfect for rhythm‑based gameplay synced to music.

---

## The Code

```ts
import { Component, PropTypes, Asset, Entity } from 'horizon/core';

class BPMWaveSpawner extends Component<typeof BPMWaveSpawner> {
  static propsDefinition = {
    enemyPrefab1: { type: PropTypes.Asset },
    enemyPrefab2: { type: PropTypes.Asset },
    enemyPrefab3: { type: PropTypes.Asset },
    spawnPoint: { type: PropTypes.Entity },
    spread: { type: PropTypes.Number, default: 0.6 },

    bpm: { type: PropTypes.Number, default: 120 },
    initialSubdivision: { type: PropTypes.Number, default: 1 },
    spawnEveryNBeats: { type: PropTypes.Number, default: 1 },
    swingPercent: { type: PropTypes.Number, default: 0 },
    smoothing: { type: PropTypes.Number, default: 0.2 },
    minIntervalMs: { type: PropTypes.Number, default: 40 },
  };

  private timerRunning = false;
  private beatTimeoutId: number | null = null;

  private currentBpm!: number;
  private targetBpm!: number;
  private beatIndex = 0;
  private swingToggle = false;

  private subdivisionRt!: number;
  private spawnEveryNBeatsRt!: number;

  private tapTimes: number[] = [];
  private maxTaps = 6;
  private tapTimeoutMs = 2500;

  override start() {
    const anyPrefab = this.props.enemyPrefab1 || this.props.enemyPrefab2 || this.props.enemyPrefab3;
    if (!anyPrefab) {
      console.error("BPMWaveSpawner: assign at least one enemy prefab.");
      return;
    }

    const startBpm = Math.max(1, this.props.bpm || 120);
    this.currentBpm = startBpm;
    this.targetBpm = startBpm;

    this.subdivisionRt = Math.max(1, Math.floor(this.props.initialSubdivision || 1));
    this.spawnEveryNBeatsRt = Math.max(1, Math.floor(this.props.spawnEveryNBeats || 1));

    this.timerRunning = true;
    this.scheduleNextBeat();
  }

  override onDestroy() {
    this.timerRunning = false;
    if (this.beatTimeoutId !== null) {
      this.async.clearTimeout
        ? this.async.clearTimeout(this.beatTimeoutId)
        : this.async.clearInterval(this.beatTimeoutId);
      this.beatTimeoutId = null;
    }
  }

  // Public API
  public setBpm(newBpm: number) { if (newBpm > 0) this.targetBpm = newBpm; }
  public setSubdivision(n: number) { if (n > 0) this.subdivisionRt = Math.floor(n); }
  public setSpawnEveryNBeats(n: number) { if (n > 0) this.spawnEveryNBeatsRt = Math.floor(n); }
  public resetPhase() { this.beatIndex = 0; this.swingToggle = false; }

  public tapTempo() {
    const now = Date.now();
    if (this.tapTimes.length && now - this.tapTimes[this.tapTimes.length - 1] > this.tapTimeoutMs) {
      this.tapTimes = [];
    }
    this.tapTimes.push(now);
    if (this.tapTimes.length > this.maxTaps) this.tapTimes.shift();

    if (this.tapTimes.length >= 2) {
      const intervals = [];
      for (let i = 1; i < this.tapTimes.length; i++) {
        intervals.push(this.tapTimes[i] - this.tapTimes[i - 1]);
      }
      const avgMs = intervals.reduce((a, b) => a + b, 0) / intervals.length;
      const tappedBpm = 60000 / Math.max(avgMs, 1);
      this.setBpm(tappedBpm);
    }
  }

  private scheduleNextBeat() {
    if (!this.timerRunning) return;

    this.currentBpm = this.lerp(this.currentBpm, this.targetBpm, this.clamp01(this.props.smoothing));

    const baseBeatMs = 60000 / Math.max(this.currentBpm, 1);
    const subdivMs = baseBeatMs / Math.max(this.subdivisionRt, 1);

    const swing = this.clamp(this.props.swingPercent, 0, 0.9);
    const intervalMs = swing > 0
      ? (this.swingToggle ? subdivMs * (1 - swing) : subdivMs * (1 + swing))
      : subdivMs;

    this.swingToggle = !this.swingToggle;
    const waitMs = Math.max(intervalMs, this.props.minIntervalMs);

    this.beatTimeoutId = this.async.setTimeout
      ? this.async.setTimeout(() => this.onBeat(), waitMs)
      : this.async.setInterval(() => this.onBeat(true), waitMs);
  }

  private onBeat(clearIntervalFallback = false) {
    if (!this.timerRunning) return;

    const shouldSpawn = (this.beatIndex % Math.max(this.spawnEveryNBeatsRt, 1)) === 0;
    if (shouldSpawn) this.spawnTrio();
    this.beatIndex++;

    if (clearIntervalFallback && this.beatTimeoutId !== null) {
      this.async.clearInterval(this.beatTimeoutId);
      this.beatTimeoutId = null;
    }
    this.scheduleNextBeat();
  }

  private spawnTrio() {
    const point = this.props.spawnPoint ?? this.entity;
    const basePos = point.position.get();
    const baseRot = point.rotation.get();

    const slots = [
      { prefab: this.props.enemyPrefab1, offsetX: -this.props.spread },
      { prefab: this.props.enemyPrefab2, offsetX: 0 },
      { prefab: this.props.enemyPrefab3, offsetX: +this.props.spread },
    ];

    for (const s of slots) {
      if (!s.prefab) continue;
      const pos = new (basePos as any).constructor(basePos.x + (s.offsetX || 0), basePos.y, basePos.z);
      this.world.spawnAsset(s.prefab, pos, baseRot).catch((err) => {
        console.error("BPMWaveSpawner: spawn failed:", err);
      });
    }
  }

  private clamp01(v: number) { return Math.max(0, Math.min(1, v)); }
  private clamp(v: number, lo: number, hi: number) { return Math.max(lo, Math.min(hi, v)); }
  private lerp(a: number, b: number, t: number) { return a + (b - a) * this.clamp01(t); }
}

Component.register(BPMWaveSpawner);
```

---

## Setup Guide

1. **Create your spawn point**
   - Place an empty entity in your world.
   - Add `BPMWaveSpawner` to it (or assign it via `spawnPoint`).

2. **Assign prefabs**
   - Set `enemyPrefab1–3` to any prefabs (same or different).
   - Leave empty slots blank.

3. **Tune musical properties**
   - `bpm`: starting tempo
   - `initialSubdivision`: 1=quarter, 2=eighths, 4=sixteenths…
   - `spawnEveryNBeats`: e.g., 4 → only spawn once per bar (at 4/4)
   - `swingPercent`: 0.15–0.25 for a swung feel
   - `spread`: offset mobs left/center/right

---

## Runtime Controls

- `setBpm(140)` → smoothly shift toward 140 BPM
- `setSubdivision(4)` → spawn on 16th notes
- `setSpawnEveryNBeats(2)` → spawn only on every 2nd beat
- `resetPhase()` → line up to the start of a song
- `tapTempo()` → call this on user taps to detect BPM

---

## Example Use Cases
- **Rhythm shooter**: spawn waves synced to music beats
- **Dance game**: enemies appear on downbeats / offbeats
- **Boss fights**: tempo ramps up as phase progresses
- **Interactive music**: let players tap tempo to drive enemy waves

---

## Tips
- To **sync with a song**, call `resetPhase()` on the first downbeat.
- Use **tap tempo** to align to live DJ/Band performance.
- Use **smoothing** for gentle tempo drifts, or set it to `1.0` for instant BPM jumps.
- Keep `minIntervalMs` ≥ 40 to avoid stutter at very high BPMs.

---

Enjoy creating BPM‑driven rhythm gameplay! 
