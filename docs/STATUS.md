# Status and where to continue

[← Documentation](README.md)

What development main has, what is open, and how to pick each thread up. Updated 2026-10-28.
The plan as a checklist is the [roadmap](ROADMAP.md).

## Releases

| Version | Highlights |
|---|---|
| 0.9.0 RC 2 | Online play; the save editor (items, weapons, armor, gems); the launcher's new look, Saves and Trophies tabs; in-game menus in its colours; a Metal abort on the status page fixed; a faster translator; macOS 15 |
| 0.9.0 RC 1 | Native Metal renderer by default (MoltenVK as the fallback); MetalFX on the native frame; button prompts for the device in use; keyboard and mouse in the in-game menu |
| 0.8.3 | Mods and Patches pages fixed; character editing in every language; optional FSR 4.1.1 on Metal; trophy notices drawn by the game; instanced tessellated draws on Metal |
| 0.8.2 | High-resolution character lighting fixed after texture collection |
| 0.8.1 | GX worker crash fixed; MetalFX option; tessellation shader and recurring shader-cache rebuild fixed |
| 0.8.0 | Motion-vector crash fixed; settings shared with the in-game menu; outputs above 1080p; the experimental Metal renderer; FPS counter sizes; memory estimate; trophy notices only over the game |
| 0.7.0 | Stretched geometry fixed; local trophies |
| 0.6.0 | Profiles and saves; the opening-video crash fixed |

Imported bbport compatibility: 0.51, plus parts of 0.6 (below).

## On main

| Area | State |
|---|---|
| bbport 0.6 | Ported: renderer fixes, the present's wait, frame rate through hooks (no measurable cost; F3 for 30 FPS), Laurence's cinematic fixes, live effects, aspect ratios, keyboard and mouse, button prompts for the device in use, and online (Seamless Hunt and The Hunter's Dream, without seamless co-op, BBSC) |
| Hooks in the game's code | bbport's `InstallGuestCodeHook` interface on bbcpu: a stub `int3; replaced instructions; jmp back`, every register available to the hook |
| Metal renderer | Native Metal is the default on Apple Silicon; MoltenVK remains selectable until M4 acceptance. The native path links or maps neither Vulkan nor MoltenVK. In one copied-save A/B pair it measured 34.4 FPS versus 33.2 on main, with the same 28.2 ms median and 32.1 versus 35.4 ms p90. The maintainer's play sessions passed (2026-10-26); acceptance waits for players' reports on the 0.9.0 release candidate, including M1/M2 capability comparisons |
| FSR 4.1.1 on Metal | AMD's neural upscaler in Metal on Apple Silicon (runtime #1, from fsr4-native-macos), offered when the Mac can run it and the player has added AMD's model files from their own DLL. M3 Pro, 1080p Balanced: 4.2–4.8 ms of GPU time a frame, a few FPS below FSR 3.1 |
| High-resolution character lighting | 0.8.2 keeps GPU-written images whose contents cannot safely be downloaded. Native 1440p clothing highlights returned on Metal and MoltenVK in copied-save runs. On main they are freed again over the critical memory mark (memory before lighting); native 1440p on the 18 GB M3 Pro stays about 2.4 GB below it |
| Trophy notifications | The game draws notices in its ImGui overlay after a persisted unlock; the launcher imports and lists unlocked trophies |
| Save editor | One editor for a character: attributes, Blood Echoes and Insight; item amounts; adding items, weapons, armor and gems; a weapon's level and imprint, its gem slots following them as in the game; gems in and out of slots, and their effects' strength. Names, icons and values come from the player's game files. Backed up and staged; an edited save earns no trophies and plays offline only. A save with an added gem was loaded and written back by the game ([launcher docs](https://github.com/bloodborne-native/bloodborne-launcher/blob/main/docs/SAVE_MANAGER.md)) |
| Launcher | Blue-slate and gold, a calm Home, a grouped menu with Saves and Trophies tabs, a fixed window size; the in-game menus use the same colours. Platform-neutral: system differences in `platform.rs` (macOS, Linux, Windows; CI on all three), texts that never name a system, a layout from phone width up |
| CPU | The translator translates the instructions it used to interpret most, and calls the guarded command-buffer functions without the dispatcher (bloodborne-recomp #15 and #16, FraSharp: 67.9 to 71.9 FPS in the Hunter's Dream on an M1 Max, same CPU time); a larger indirect-branch cache and host calls without the dispatcher (bloodborne-recomp #17), and the runtime's own `sinf`/`cosf`, `expf`, `tanf`, `atan2f` and wide-string calls (bloodborne-runtime #7), both FraSharp: 3.6% less CPU per frame with the translator on an M3 Pro, the recompiled library the same) |
| R4 (names for the engine's render code) | 80% `████████████████░░░░` of its steps (R4.1 to R4.4 done; R4.5 not started). Started before 0.9.0 final. bloodborne-recomp's `annotations/` names 184 functions and describes the engine's draw state, shaders, models, meshes, materials (their textures and constants) and shader programs (CoreGraphics2 is modelled on Direct3D 11); the generator writes the names into the generated C. Counted in the game with `BB_DRAW_CENSUS=1`: 85% of libGnm's draws go through CoreGraphics2's seven draw methods, and of their calls 65% come from the mesh draw, 24% from deferred lighting and 7.5% from an upload-ring draw used by eight renderers that write their vertices each frame, most likely the visual effects ([RECOMPILATION.md](https://github.com/bloodborne-native/bloodborne-recomp/blob/main/docs/RECOMPILATION.md#annotations)). CoreGraphics2's own compute dispatches are buffer and texture copies, fills and clears. The engine's synchronization primitives are named and counted (Dantelion2's light mutex is taken about 158,700 times a second in a level). Next: the hottest functions' signatures |
| Docs | One English set per repository; wikis mirror them |

## Open threads

**After 0.8.2.** The repeatable GX worker crash on Metal and MoltenVK was the live dynamic-shadow
setting re-enabling shadows in a secondary context without a shadow target. A separate null guest
virtual call seen once on Metal has not recurred. A
[GXRenderThread null read in Cathedral Ward](https://github.com/bloodborne-native/bloodborne-mac/issues/3)
could not be reproduced and was closed until it happens again; so was a
[crash on death in Central Yharnam](https://github.com/bloodborne-native/bloodborne-mac/issues/5)
(a depth buffer viewed as colour on Metal) after its reporter did not answer.
Check the frame-rate hooks in play (running above 90 FPS, cloth, Laurence's cinematic; needs a
save near it).

**Metal renderer, M4 pending acceptance.** The
[render adapter](https://github.com/bloodborne-native/bloodborne-runtime/blob/main/docs/RENDER_ADAPTER.md)
runs Metal natively on macOS without linking or mapping the Vulkan loader or MoltenVK. The
Metal backend now owns the device, queue, resources, synchronization, submissions and MetalFX.
The MoltenVK renderer remains in the app as a fallback and loads the Vulkan driver only when
selected. Linux keeps its Vulkan backend. In one copied-save A/B pair (native 1080p, upscaler
off), the new path measured 34.4 FPS versus 33.2 on main; median 28.2 ms both, p90 32.1 versus
35.4 ms. A separate save and a MetalFX Balanced run reached gameplay without faults. The
manual Linux workflow [run 40314805527](https://github.com/bloodborne-native/bloodborne-runtime/actions/runs/40314805527)
built the Vulkan backend and passed its tests. An explicit native 1080p test override had
selected the scaled UI path and exposed the item atlas; the renderer now treats that value as
native. Copied-save frames were clean with the override on Metal and MoltenVK, and at normal
native 1440p on Metal. The
capability comparisons on M1 and M2 Macs and the maintainer's release-candidate play session
are still required to accept M4.
Shaders ahead of time follow (N2). The shader compiler, rewrites, pipeline cache and format/state
tables came from [Huax-1839/bloodborne-macos](https://github.com/Huax-1839/bloodborne-macos/commit/0f0cf54),
through [Wooloomooloo2/bloodborne_mac_port](https://github.com/Wooloomooloo2/bloodborne_mac_port)
(we took `0b50589` and `78b59f5`; its "M2" `3ee456f` creates resources in Metal).

**Recompiled code speed (R3, done).** The library runs the whole game with less CPU time than
the translator: 344% of one core against 402% at the 1st Floor Sickroom lamp, 357% against 392%
at the test save's place in Cathedral Ward (bloodborne-recomp #9–#12, 2026-10-25), at the same
frame rate. R3's follow-ups (plain accesses to a function's own frame, the calls still going into
the translator, a 0.5 FPS gap at the Sickroom) are in bloodborne-recomp's docs.

**The recompiled library from the launcher (on main).** The launcher's *Recompiled code* page
(Settings) builds the library on the player's device and offers Translated or Recompiled. It
downloads LLVM 23.1.4's clang and `ld64.lld` (bloodborne-recomp's `toolchain-llvm-23.1.4` release,
70 MB, SHA-256 pinned) and runs the app's recompiler kit (`build_library.py`): no Xcode or macOS
SDK. From the installed kit on an M3 Pro (10 jobs): 13 min 35 s, 241,821 functions, a 330 MB
library; the game ran it with 240,978 functions replaced, no faults, median frame 26.7 ms (26.3
with the library built by Xcode's clang). Not `-ffreestanding`: it turns built-ins off, the code
grew 32% and frames took 42.4 ms. The library is used only while it matches the game's executable
and the kit's version
([launcher docs](https://github.com/bloodborne-native/bloodborne-launcher/blob/main/docs/RECOMPILED_CODE.md)).

**Watch list.** A short white flash in the opening video and an enemy's body drawn as colour
noise have not recurred in recent play. Neither has a confirmed fix. Older rare assertions are in
the runtime's [stability notes](https://github.com/bloodborne-native/bloodborne-runtime/blob/main/docs/STABILITY.md).

**Online.** The module ships (libbbnet20, shadPS4 0.20's network code; The Hunter's Dream part
makes it GPL-3.0-or-later). It was tested here without an account; players test both pools with
the 0.9.0 release candidate. Seamless co-op (BBSC) is not supported.

## Keep in step

- Memory: `run.sh` sizes the game's memory; the launcher (`src/lib/data/memory.ts`) and the
  in-game menu (`MemoryEstimate` in `bbport_overlay.cpp`) estimate it the same way.
- Settings the menu shows: the runtime's `bbport_settings.cpp` and the launcher's
  `settings_store.rs` (`GAME_SYNCED`).
- Colours: the launcher's `src/index.css` tokens and the overlay's `Theme` in `bbport_overlay.cpp`
  (the settings menu, keyboard, FPS counter, banners and trophy notices).
- Versions: the app and the launcher, and the tag of a release. `bash packaging/version.sh X.Y.Z`
  writes the number everywhere and `--check` compares the files (and the tag, in the workflow).

## Test notes

- The build is `bash runtime/build.sh`, and `--test` runs the tests through `ctest`
  (`docs/BUILDING.md`). Five GPU tests (motion-shader, scene-resolution, taa-shader,
  camera-motion, translation-memory) stopped compiling when the code moved under them and are
  listed in `runtime/gpu/CMakeLists.txt` rather than run: repairing them is open work.

- Test games are background-only apps (`SDL_MAC_BACKGROUND_APP=1`): they never become the active
  app, so focus and Cmd+Q behave unlike a launcher game. `BB_BENCH_FOREGROUND=1` for a test the
  maintainer plays in.
- The runtime's macOS CI runs on every push and cancels older runs; wait for a run whose result
  matters before pushing again. Linux CI is started by hand for now, while the work is on macOS.
