# mpv-android

**Android library powered by [mpv](https://mpv.io/), FFmpeg, and libplacebo.**

This fork provides native media playback and thumbnail-generation capabilities for Android applications through a Kotlin-friendly `MPVLib` interface. It is maintained as a prebuilt Android Archive (AAR) for direct integration into apps such as **mpvFlux**.

> **Release status:** v1.1.0 is being prepared. The four architecture builds have completed, but the combined release AAR and on-device playback compatibility should be verified before production use.

## Native dependency targets

| Component | Version |
| --- | --- |
| mpv | **0.42.0** |
| FFmpeg | **9.1** (`n9.1`) |
| libplacebo | **7.387.0** (`v7.387.0`) |
| Android library | **1.1.0** |

Versions above are the pinned build targets for v1.1.0; they are not a substitute for verifying the contents of the published binary.

## Features

- Native mpv playback integration through `MPVLib`
- Hardware-decoding and Vulkan-oriented native build configuration
- Video thumbnail extraction
- Playback commands, properties, and event observation
- Kotlin Flow and StateFlow-based property/event support
- Prebuilt AAR distribution through GitHub Releases
- Builds for four Android ABIs

## Supported architectures

| Android ABI | Build architecture |
| --- | --- |
| `arm64-v8a` | `arm64` |
| `armeabi-v7a` | `armv7l` |
| `x86` | `x86` |
| `x86_64` | `x86_64` |

## Requirements

- **Android 7.0 (API 24)** or later
- An Android project using Gradle
- Java 17 for building this library from source

## Installation

### Download the AAR

Once v1.1.0 has been published, download **`mpv-android-lib-v1.1.0.aar`** from the [GitHub Releases page](https://github.com/Muhammedahmed18/mpv-android/releases).

Place the file in your Android app module:

```text
app/
└── libs/
    └── mpv-android-lib-v1.1.0.aar
```

Create `app/libs/` if it does not exist.

In the app module's **Kotlin DSL** (`build.gradle.kts`):

```kotlin
dependencies {
    implementation(files("libs/mpv-android-lib-v1.1.0.aar"))
}
```

Or **Groovy DSL** (`build.gradle`):

```groovy
dependencies {
    implementation files('libs/mpv-android-lib-v1.1.0.aar')
}
```

Sync Gradle after adding the dependency. A direct AAR dependency does not automatically resolve transitive dependencies; ensure the consuming app provides any required Kotlin/coroutines dependencies and compatible Android configuration.

> **Distribution:** v1.1.0 is intended for GitHub Releases as an AAR. Maven Central installation instructions for earlier versions do not apply to this release.

## Kotlin API

The library exposes `is.xyz.mpv.MPVLib`. Its native methods cover lifecycle, playback commands, surfaces, properties, events, and thumbnail generation.

```kotlin
import is.xyz.mpv.MPVLib

// Call from your Android playback lifecycle after the required setup.
MPVLib.create(applicationContext)
MPVLib.init()
MPVLib.command("loadfile", videoPath)

// Playback commands
MPVLib.command("set", "pause", "yes")
MPVLib.command("set", "pause", "no")

// Release resources when playback ends.
MPVLib.destroy()
```

Attach and detach a valid Android `Surface` using `MPVLib.attachSurface(surface)` and `MPVLib.detachSurface()` as appropriate for the app's UI lifecycle. The snippet is an API illustration, not a complete player implementation; consult the existing Android integration for correct threading, surface lifecycle, and options.

Additional methods include `setOptionString`, `getPropertyString`, `setPropertyString`, `observeProperty`, `grabThumbnail`, and `grabThumbnailFast`.

## Building from source

The repository contains native build scripts under `buildscripts/` and GitHub Actions workflows under `.github/workflows/`.

### GitHub Actions

1. Push changes to your fork's default branch.
2. Open **Actions → build**.
3. Let the push-triggered build finish, or use **Run workflow** if manual dispatch is enabled.
4. Check that the four architecture jobs succeed.
5. Download the architecture-specific artifacts for inspection.

The **build** workflow produces per-architecture artifacts. These are not the final combined release AAR.

### Release AAR

1. Verify all four architecture builds and native dependency versions.
2. Create a Git tag named `v1.1.0` on the verified commit.
3. Publish the tag/release to trigger `.github/workflows/release.yml` (if configured for tag pushes).
4. Confirm the release workflow succeeds and attaches `mpv-android-lib-v1.1.0.aar`.
5. Inspect the AAR to ensure it contains native libraries for all four ABIs.

Publishing a GitHub Release alone does **not** prove that the AAR was successfully built or attached.

### Local native builds

On a supported Linux build environment, use the repository's scripts and required Android toolchain:

```bash
cd buildscripts
./download.sh
./buildall.sh --arch arm64 mpv
```

The repository also includes `buildall.sh` and `docker-build.sh` for its supported build process. Refer to the scripts for supported arguments and toolchain requirements.

## Verification

Before integrating a new AAR into an application:

- Confirm the source checkout tags for mpv, FFmpeg, and libplacebo.
- Inspect the resulting AAR and embedded `.so` files for the expected ABIs.
- Confirm compiled native versions where possible; build-log tag selection alone is not binary verification.
- Test playback, audio, subtitles, hardware decoding, Vulkan rendering, thumbnails, and network streams on actual Android devices.
- Check JNI/API compatibility with the consuming application, including mpvFlux.

## Project background and credits

This repository is a fork of the [marlboro-advance/mpv-android](https://github.com/marlboro-advance/mpv-android) project and builds on the work of the mpv and FFmpeg communities. The original Android library credits **Ilya Zhuravlev** and **sfan5**.

## License

See [LICENSE](LICENSE) for the repository's MIT license. Bundled native components and their dependencies have their **own licenses**, which must also be reviewed when redistributing compiled binaries.

- [mpv](https://github.com/mpv-player/mpv)
- [FFmpeg](https://ffmpeg.org/)
- [libplacebo](https://code.videolan.org/videolan/libplacebo)
