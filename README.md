# emulatorLauncher – keepuserconfig fork

Fork of [RetroBat-Official/emulatorlauncher](https://github.com/RetroBat-Official/emulatorlauncher) that adds two options to stop RetroBat from overwriting emulator configuration files.

> This is an unofficial fork. For the official RetroBat launcher, use the upstream repository.

## Why

RetroBat's emulatorLauncher regenerates the emulator configuration at every game launch. Any setting changed directly inside an emulator (and also exposed by RetroBat) is reset to RetroBat's value or default. This fork lets you keep your own emulator settings and/or your own controller configuration.

## Options

| Option | Effect |
|---|---|
| `keepuserconfig` | RetroBat only writes the controller configuration. Every other emulator setting (video, speed, paths, UI...) is left as you configured it in the emulator. |
| `keepusercontrols` | RetroBat does not write controller or hotkey configuration (same as *AUTOCONFIGURE CONTROLLERS = OFF*). |

Both options can be combined: with both enabled RetroBat does not touch the emulator configuration at all.

### Supported emulators (`keepuserconfig`)

| Emulator | Still written by RetroBat | Left untouched |
|---|---|---|
| PCSX2 | pads, guns, wheels | rest of `PCSX2.ini` (namco2x6 arcade excluded) |
| Eden | controls | rest of `qt-config.ini` |
| RPCS3 | `input_configs` | `config.yml`, `vfs.yml`, GUI settings, per-game custom configs kept active |
| DuckStation | pads, guns, mouse | rest of `settings.ini`, no bezel / ReShade injection |
| PPSSPP | `controls.ini` | `ppsspp.ini` |
| Dolphin | controller files, save state slot | `Dolphin.ini`, `GFX.ini`, achievements, Wii SYSCONF, no bezel (Triforce excluded) |
| shadPS4 | – | `config.json` |
| RetroArch (libretro) | `input_*` bindings, port device types | video, shaders, latency, core options, bezels |

`keepusercontrols` works with every emulator that honours RetroBat's `disableautocontrollers` option.

## How to enable

### From the RetroBat menus

The options are declared in `.emulationstation/es_features.cfg` and appear under **GENERAL SETTINGS** as *KEEP USER EMULATOR SETTINGS* and *KEEP USER CONTROLLER SETTINGS*:

- per system: game list → **SELECT** → *Advanced system options*
- per game: hold **South** on a game → *Advanced game options*

### Manually in `es_settings.cfg`

```xml
<!-- per emulator -->
<string name="global.keepuserconfig.pcsx2" value="1" />
<string name="global.keepusercontrols.duckstation" value="1" />

<!-- all emulators -->
<string name="global.keepuserconfig" value="1" />
```

Emulator names: `pcsx2`, `eden`, `rpcs3`, `duckstation`, `ppsspp`, `dolphin`, `shadps4`, `libretro` (RetroArch, also used for `angle` and `lr-*`).

### Precedence (most specific wins)

1. game: `<system>["<rom>"].<option>`
2. system: `<system>.<option>`
3. emulator: `global.<option>.<emulator>`
4. global: `global.<option>`

Edit `es_settings.cfg` only while RetroBat is closed.

## Notes

- With `keepuserconfig` enabled, settings that RetroBat used to force (fullscreen, exit confirmation, paths...) must be set once inside the emulator.
- With `keepusercontrols` enabled, RetroBat's exit hotkey (Hotkey + Start) is not written: configure your own exit shortcut in the emulator.
- The emulatorLauncher log (`emulationstation\emulatorLauncher.log`) shows `[KeepUserConfig]` and `[KeepUserControls]` lines for each launch.

## Build and install

1. Open `batocera-ports.sln` in Visual Studio.
2. Build the **emulatorLauncher** project in **Release | x64** (RetroBat is 64-bit; the AnyCPU configuration produces a 32-bit executable that cannot load SDL).
3. Copy `emulatorLauncher\bin\x64\Release\emulatorLauncher.exe` and `EmulatorLauncher.Common.dll` to `RetroBat\emulationstation\` (back up the originals first).
4. Copy `.emulationstation\es_features.cfg` to `RetroBat\emulationstation\.emulationstation\` to get the menu entries.

RetroBat updates replace these files: rebuild from an up-to-date fork after each update.

## Changes

All changes are in a single commit on the `keepuserconfig` branch:

- `emulatorLauncher/Common/Generator.cs` – `IsEmulatorOptionEnabled()`, `KeepUserConfig`, logging
- `emulatorLauncher/Program.cs` – `keepusercontrols` handling before configuration
- `emulatorLauncher/Generators/*.Generator.cs` – controller-only path for the supported emulators
- `.emulationstation/es_features.cfg` – menu entries
