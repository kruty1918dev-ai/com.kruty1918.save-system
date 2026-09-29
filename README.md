# Kruty1918 Save System

Binary save-file pipeline for Unity: module contracts, block codec with CRC32,
module registry and execution ordering, atomic writes, slot policy and
inspector services. The host game owns orchestration (`SaveService`),
signals and concrete modules — this package is the engine-agnostic plumbing.

## Install (Unity Package Manager)

Package Manager → **+** → **Add package from git URL**:

```
https://github.com/kruty1918dev-ai/com.kruty1918.save-system.git
```

or in `Packages/manifest.json`:

```json
"com.kruty1918.save-system": "https://github.com/kruty1918dev-ai/com.kruty1918.save-system.git#v0.1.0"
```

The repository is private — Git credentials (PAT / Git Credential Manager)
are required on every machine that resolves the package.

## Layout

| Folder | Contents |
|---|---|
| `Runtime/` | Contracts, codec, registry, atomic file writer, slot policy |

## API surface

| Type | Purpose |
|---|---|
| `ISaveService` / `ISaveLoadService` / `ISaveWriteService` | Save/load orchestration contracts |
| `ISaveModule` / `IStagedSaveModule` | Per-domain save data module; staged variant for multi-pass writes |
| `ISaveModuleRegistry` / `ISaveModuleExecutionOrder` | Module lookup and ordering policy |
| `ISaveContext` | Context passed to modules (paths, launch data) |
| `SaveModuleIdAttribute` | Stable module id for ordering/registry keys |
| `SaveSlotInfo` | Save slot descriptor |
| `GameLaunchContext` | Static launch-intent handoff between menu and gameplay scenes |

## Model

- Modules serialize into named blocks inside a single binary file; each block
  carries a CRC32 so corruption is detected per-block, not per-file.
- Writes are atomic (temp file + rename) — a crash mid-write never leaves a
  truncated save.
- Module execution order is explicit via `ISaveModuleExecutionOrder`, not
  registration order.
- `GameLaunchContext` carries the pending launch options (world settings,
  multiplayer role, turn rules, save-to-load) from the menu scene into the
  gameplay scene without scene-object wiring.

## Requirements

Unity 6.x. No external package dependencies.
