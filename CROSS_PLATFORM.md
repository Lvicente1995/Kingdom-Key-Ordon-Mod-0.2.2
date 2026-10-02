# Cross-platform build notes (v0.2.2)

This source follows Dusklight's current official mod-template target matrix:

- Windows AMD64
- Windows ARM64
- Linux x86_64 (including Steam Deck)
- Linux aarch64
- macOS Apple Silicon (arm64)
- macOS Intel (x86_64)
- iOS arm64
- Android aarch64 / arm64-v8a

## Portable custom audio

Version 0.2.2 removes the old Windows-only custom-audio gate. Dusklight 2.0.2 implements `JASCriticalSection` as the guard for its recursive host audio mutex on native targets. The mod resolves that host constructor/destructor through `HookService` using platform-independent display names first, then exact MSVC or Itanium C++ ABI names only as a fallback.

This keeps the Kingdom Key summon, dismiss and seven hit-family WAV cues synchronized with Dusklight's own JAS audio thread on supported targets. The audio resource registration itself continues to use `AudioResService`.

If a future Dusklight build has a missing/stale symbol manifest or an audio sample cannot be registered, custom audio now fails soft: the weapon, physics and visual effects remain enabled and the game uses its native sword sounds instead.

## GitHub Actions

`.github/workflows/build.yml` mirrors the official Dusklight mod-template platform matrix and merges successful per-platform bundles into one `mod-combined` artifact. It also includes `workflow_dispatch` so a build can be started manually from GitHub Actions.

The final combined `.dusk` should contain native libraries for each target under its `lib/` platform directory while sharing one copy of `res/` and `mod.json`.

`.gitattributes` and CMake's LF-normalized `mod.json` copy prevent the Windows CRLF mismatch that can otherwise make bundle merging fail.

## Notes

A GitHub Actions matrix build proves that the source compiles and packages for a target; actual runtime behavior still needs device testing, especially custom audio and KHIII visual effects. GPU/backend behavior can vary by device and driver.
