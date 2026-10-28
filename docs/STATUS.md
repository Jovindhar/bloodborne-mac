# Status

[← Documentation](README.md)

Where the whole project stands: what is released, what is on main, what is being worked on and
what comes next. Updated 2026-10-28. The plan in order: [Roadmap](ROADMAP.md). Each repository's
own status: [runtime](https://github.com/bloodborne-native/bloodborne-runtime/blob/main/docs/NATIVE_RENDERER.md#progress),
[recompiler](https://github.com/bloodborne-native/bloodborne-recomp/blob/main/docs/RECOMPILATION.md#milestones),
[launcher](https://github.com/bloodborne-native/bloodborne-launcher/blob/main/README.md#status).

## At a glance

- **Released:** 0.9.0 RC 2 (pre-release), on 2026-10-28. The latest final release is 0.8.3.
- **Playable** on Apple Silicon: about 40 FPS in play at native 1080p on an M3 Pro with the Metal
  renderer ([performance](guide/performance.md)).
- **Now:** deciding 0.9.0 final; accepting the native Metal renderer (N5).
- **Next:** N6 (the native renderer takes the engine's draws), then the recompiler's CPU work.

## Milestones

Each bar has 20 blocks, one per 5% of the milestone's defined steps.

| Milestone | Progress | State |
|---|---|---|
| **CPU** (bloodborne-recomp) | | |
| R0–R2 · groundwork, prototype, every function recompiled | 100% `████████████████████` | The game runs on 240,978 recompiled functions; the translator remains the fallback |
| R3 · speed | 100% `████████████████████` | 14% less CPU time than the translator at the same frame rate |
| The library built from the launcher | 100% `████████████████████` | 13.5 minutes on an M3 Pro, no Xcode needed |
| R4 · names for the engine's code | 100% `████████████████████` | 204 functions named; the draw state, models, materials and shaders described |
| The generator's CPU work | 0% `░░░░░░░░░░░░░░░░░░░░` | Next after N6 |
| **Graphics** (bloodborne-runtime) | | |
| N0 · scope, N1 · frame planning | 100% `████████████████████` | Deferred clears |
| N4 · render adapter | 100% `████████████████████` | The renderer on the project's own GPU interface |
| N5 · Metal backend | 95% `███████████████████░` | The default since 0.9.0 RC 1; the maintainer's play sessions passed; acceptance waits for players' reports on the release candidates and M1/M2 comparisons |
| N6 · the engine's render boundary | 0% `░░░░░░░░░░░░░░░░░░░░` | Next; R4 found the boundary |
| N2 · shaders ahead of time, N3 · textures as objects | 0% `░░░░░░░░░░░░░░░░░░░░` | After N6 |
| **Platforms** | | |
| macOS (Apple Silicon) | | Released |
| iOS | proof of concept | 23 FPS on an iPhone 17 Pro Max; the steps to a real app are in the roadmap |
| Linux | runtime only | The Vulkan backend builds and passes its tests (manual CI); no app |

## Releases

| Version | Highlights |
|---|---|
| 0.9.0 RC 2 | Online play; the save editor; the launcher's new look with Saves and Trophies tabs; in-game menus in its colours; a faster translator; macOS 15 |
| 0.9.0 RC 1 | The native Metal renderer by default (MoltenVK as the fallback); MetalFX on the native frame; button prompts for the device in use; keyboard and mouse in the in-game menu |
| 0.8.3 | Mods and Patches pages fixed; character editing in every language; optional FSR 4.1.1 on Metal; trophy notices drawn by the game |
| 0.8.2 | High-resolution character lighting fixed |
| 0.8.1 | GX worker crash fixed; MetalFX; the shader cache no longer rebuilds every launch |
| 0.8.0 | Motion-vector crash fixed; settings shared with the in-game menu; outputs above 1080p; the experimental Metal renderer |
| 0.7.0 | Stretched geometry fixed; local trophies |
| 0.6.0 | Profiles and saves; the opening-video crash fixed |

Release notes: [docs/releases](releases/). bbport compatibility: 0.51, plus parts of 0.6 ported by
hand.

## On main

What main has beyond 0.9.0 RC 2:

- **R4** (bloodborne-recomp's `annotations/`): the engine's render boundary, the draw state,
  models, meshes, materials, shader programs, the synchronization primitives and the 20 busiest
  functions, named and described for N6 and the generator. `BB_DRAW_CENSUS=1` (runtime) counts the
  game's draws and locks at that boundary.
- **The recompiled library from the launcher:** the *Recompiled code* page builds it on the
  player's device with a downloaded LLVM toolchain and offers *Translated* or *Recompiled*
  ([player guide](guide/performance.md#recompiled-code)).
- **A faster translator** (bloodborne-recomp #17) and the runtime's own math and wide-string
  calls (bloodborne-runtime #7): 3.6% less CPU per frame with the translator.
- **The game's threads at macOS's user-interactive QoS** (bloodborne-runtime #8): kept on
  performance cores; +2 FPS where the CPU sets the frame rate (an M1 Max in Yahar'gul), no change
  on the GPU-bound M3 Pro. A translator fix for one float-to-integer conversion (bloodborne-recomp
  #18).

## Areas

| Area | State |
|---|---|
| CPU | bbcpu translates the game's x86-64 code to arm64 as it runs (the default); the recompiled library runs it compiled ahead of time from the player's own copy, with 14% less CPU time ([Recompilation](https://github.com/bloodborne-native/bloodborne-recomp/blob/main/docs/RECOMPILATION.md)) |
| Graphics | Native Metal by default, without Vulkan or MoltenVK; MoltenVK selectable until N5 is accepted; Vulkan on Linux. Upscalers: MetalFX with the Metal renderer; FSR 3.1 and FSR 4.1.1 (run on the Mac's GPU) with MoltenVK ([native renderer](https://github.com/bloodborne-native/bloodborne-runtime/blob/main/docs/NATIVE_RENDERER.md)) |
| Game features | bbport 0.6's frame rate through hooks (F3 for 30 FPS), live effects, aspect ratios, keyboard and mouse, Laurence's cinematic above 30 FPS |
| Online | Seamless Hunt and The Hunter's Dream (the runtime's `bbnet20`, GPL-3.0-or-later); tested here without an account, players are testing both pools with 0.9.0 RC 2. Seamless co-op (BBSC) is not supported |
| Launcher | Profiles and saves with backups; trophies; the save editor (attributes, items, weapons, armor, gems; an edited save earns no trophies and plays offline only); settings shared with the in-game menu; mods and patches; 20 languages; platform-neutral code with CI on macOS, Linux and Windows ([launcher docs](https://github.com/bloodborne-native/bloodborne-launcher/tree/main/docs)) |

## Open issues

- **Known bugs:** blood on already killed enemies can look pale after a save reload; the camera
  after a cutscene sometimes starts in front of the character. Causes not isolated.
- **Watch list:** a short white flash in the opening video and an enemy drawn as colour noise
  have not recurred, without a confirmed fix. A GXRenderThread crash in Cathedral Ward
  ([#3](https://github.com/bloodborne-native/bloodborne-mac/issues/3)) and a crash on death in
  Central Yharnam ([#5](https://github.com/bloodborne-native/bloodborne-mac/issues/5)) could not be
  reproduced and are closed until they happen again. Older rare assertions: the runtime's
  [stability notes](https://github.com/bloodborne-native/bloodborne-runtime/blob/main/docs/STABILITY.md).
- **Only one Mac measured** (M3 Pro); results from M1, M2 and M4 Macs are needed for N5's
  acceptance.
- **Tests:** five GPU tests no longer compile and are not run (roadmap, Later).
