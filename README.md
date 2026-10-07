# GASShooter Tactics

A tactical multiplayer first/third-person shooter prototype engineered on Unreal Engine's **Gameplay Ability System (GAS)** by **Vivekanand Rajbhar (WebSpider Studios)**.

This project was developed as a deep technical dive into Epic's Gameplay Ability System, focusing on client-side prediction, weapon handling, networked attribute sets, dynamic damage execution calculations, and multiplayer replication.

---

## ⚡ Technical Breakdown

### 1. Client-Side Prediction & Weapon Firing
* **Predicted Fire Execution:** Weapon firing is implemented as a predicted `UGameplayAbility`. When the player pulls the trigger, muzzle flash, recoil, and line traces fire locally on the client with zero latency feeling. A `Server_SendTargetData` RPC validates the trace on the server, reconciling any network drift.
* **Burst, Auto & Semi Fire Modes:** Configurable firing loops handling fire-rate delays, ammo depletion, and automatic reload triggers when magazines empty.
* **Camera Recoil & Bloom:** Dynamic crosshair bloom and camera punch that recovers smoothly over time based on character stance (standing vs crouched vs sprinting).

### 2. Attribute Sets & Damage Calculations
* **`GSAttributeSetBase`:** Encapsulates replicated attributes:
  * Health / MaxHealth
  * Shield / MaxShield (includes automatic shield regeneration timers triggered after taking damage)
  * Stamina / MaxStamina (drained by sprinting, jumping, and vaulting)
  * Ammo pools (magazine current and reserve reserves per weapon type)
* **`GSDamageExecutionCalc`:** Custom `UGameplayEffectExecutionCalculation` that parses incoming physical damage, checks target shield values, mitigates direct damage through armor curves, and deducts remaining points from health.

### 3. Decoupled Audio & VFX via Gameplay Cues
* Bullet impacts, ricochets, shield break explosions, and weapon discharges trigger through `GameplayCueNotify_Static` and `GameplayCueNotify_Actor`.
* Keeps gameplay logic strictly decoupled from visual presentation, allowing easy asset swaps without modifying C++ weapon classes.

### 4. Replicated Inventory & Weapon Slots
* Multi-slot weapon inventory supporting primary rifle, secondary handgun, and equipment.
* Seamless socket attachment to character meshes with weapon state replication (holstered, drawn, aiming, reloading).

---

## 📁 Source Layout

```
Source/GASShooter/
├── Private/
│   ├── Characters/
│   │   ├── Abilities/       # Gameplay abilities (Fire, Reload, Jump, Sprint)
│   │   ├── AttributeSets/   # Health, Shield, and Ammo attribute sets
│   │   └── Heroes/          # GSHeroCharacter and Player Controller
│   ├── Items/               # Pickups for ammo, health kits, and weapons
│   ├── UI/                  # UMG widgets bound to attribute change delegates
│   └── Weapons/             # Weapon base actor, projectiles, and attachment sockets
└── Public/                  # Header declarations and exported types
```

---

## 🛠️ How to Build & Run

### Requirements
* Unreal Engine 5.8 (or 5.7 / 5.3)
* Visual Studio 2022 (with *Game Development with C++* workload)
* Windows 10/11 64-bit

### Steps
1. Clone the repository:
   ```bash
   git clone https://github.com/VR-WebSpider/GASShooterTactics.git
   ```
2. Right-click `GASShooter.uproject` → **Generate Visual Studio project files**.
3. Open `GASShooter.sln` in Visual Studio 2022.
4. Set build configuration to **Development Editor** | **Win64**.
5. Build and launch (F5).
6. In Unreal Editor, open any map in `/Game/GASShooter/Maps/` and test Play in Editor (set Number of Players to 2 with "Play as Listen Server" to inspect network replication).

---

## 📜 Credits & License

* Developed and extended by **Vivekanand Rajbhar (WebSpider Studios)**.
* Built upon foundational GAS shooter architecture created by **Dan Kestranek (tranek)**.
* Licensed under the [MIT License](LICENSE).