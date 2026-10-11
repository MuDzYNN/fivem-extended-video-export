# Extended Video Export for FiveM

High quality Rockstar Editor exports for FiveM: custom encoder settings, custom frame rate, motion blur and OpenEXR output.

This is a fork of [ali-alidoust/gta5-extended-video-export](https://github.com/ali-alidoust/gta5-extended-video-export), based on its v0.5.0 experimental release. The original mod stopped loading on newer FiveM game builds; this fork keeps it working there.

## What the fork changes

- The plugin declares support for current FiveM game builds, so FiveM loads it instead of skipping it
- It is built to work with the older C++ runtime (`msvcp140.dll` 14.34) that FiveM ships
- The frame rate from the config file is used for the exported video
- Audio is dropped only when `fps * (motion_blur_samples + 1)` goes above 60
- The DLLs the plugin needs are copied into `EVE\dlls` by the build

## Supported game builds

FiveM only loads an ASI plugin on the game builds it lists. This one lists:

`1604`, `2060`, `2189`, `2372`, `2545`, `2612`, `2699`, `2802`, `2944`, `3095`, `3258`, `3323`, `3407`, `3570`, `3751`, `3788`, `3889`

On any other build FiveM will not load the plugin. To add one, add an `FX_ASI_BUILD` entry to [resource.rc](gta5-extended-video-export/resource.rc) and rebuild.

## Requirements

- [FiveM](https://fivem.net/)
- [Voukoder 13.4.1](https://archive.org/details/voukoder-13.4.1_202505) (the classic one, not Voukoder Pro) - the plugin encodes through it and will not export without it. Its repository was removed, so the link points to an archived copy

ScriptHookV is not needed on FiveM, it has its own compatible implementation.

## Installation

1. Install Voukoder.
2. Download the package from [Releases](https://github.com/MuDzYNN/fivem-extended-video-export/releases), or [build it yourself](#building).
3. Open the FiveM plugins folder. Press `Win + R` and paste:

   ```
   %localappdata%\FiveM\FiveM.app
   ```

   Create the `plugins` folder if it does not exist.
4. Copy `ExtendedVideoExport.asi` and the whole `EVE` folder into it:

   ```
   FiveM.app\plugins\
   ├── ExtendedVideoExport.asi
   └── EVE\
       ├── ExtendedVideoExport.ini
       ├── preset.json
       └── dlls\
   ```

5. Open `EVE\ExtendedVideoExport.ini` and set `output_folder` to an existing folder for the exported videos, for example:

   ```ini
   output_folder = C:\gtavvideo
   ```

6. Start FiveM, open the Rockstar Editor and export a clip as usual.

The video is saved as `EVE-<date><time>.mp4` in `output_folder`. The file that Rockstar Editor itself writes stays empty, that is expected.

### Uninstalling

Delete `ExtendedVideoExport.asi` and the `EVE` folder from `plugins`.

## Configuration

### `EVE\ExtendedVideoExport.ini`

| Option | Default | Description |
| --- | --- | --- |
| `enable_mod` | `true` | Turns the plugin on or off. Needs a game restart. |
| `auto_reload_config` | `true` | Reloads the config before every export. |
| `output_folder` | empty | Where videos are saved. Empty means your Videos folder. |
| `log_level` | `error` | `error`, `warn`, `info`, `debug` or `trace`. Use `trace` when reporting a bug. |
| `fps` | `30` | Export frame rate. A number (`60`, `23.976`) or a fraction (`30000/1001`). |
| `motion_blur_samples` | `0` | Extra frames blended into each output frame, 0-255. `0` disables motion blur. |
| `motion_blur_strength` | `0.5` | How much of each frame's time the shutter stays open, 0-1. |
| `export_openexr` | `false` | Also writes every frame as an HDR `.exr` file. Much slower. |

The last four options go under the `[EXPORT]` section.

The game renders `fps * (motion_blur_samples + 1)` frames per second of video. Above 60 the export is saved without sound. `fps = 60` with no motion blur keeps audio; `fps = 60` with any motion blur does not.

### `EVE\preset.json`

Encoder settings in Voukoder's format: container, video encoder, audio encoder and their options. The default is H.264 (`libx264`, CRF 17) with AAC 320 kbps in an MP4 container.

### Log

`EVE\ExtendedVideoExport.log` is written next to the config. If an export fails, set `log_level = trace`, export again and look there first.

## Building

You need:

- Visual Studio 2022 with the "Desktop development with C++" workload (it brings CMake and the Windows SDK)
- Git

1. Clone the repository with its submodule:

   ```
   git clone --recursive https://github.com/MuDzYNN/fivem-extended-video-export.git
   cd fivem-extended-video-export
   ```

2. Download the [ScriptHookV SDK](http://www.dev-c.com/gtav/scripthookv/) and extract it into `ScriptHookV`, so that `ScriptHookV\inc` and `ScriptHookV\lib\ScriptHookV.lib` exist.
3. Put `VoukoderTypeLib_h.h` and `VoukoderTypeLib_i.c` into a `voukoder` folder in the repository root. They are generated from Voukoder's `VoukoderTypeLib.idl`.
4. Open "x64 Native Tools Command Prompt for VS 2022" in the repository folder and run:

   ```
   cmake -S . -B out\build -G "Visual Studio 17 2022" -A x64
   cmake --build out\build --config Release
   ```

   The first configure downloads and builds the dependencies through vcpkg, which takes a while.

The result is in `x64\Release`: `ExtendedVideoExport.asi` and the `EVE` folder, ready to copy into `plugins`.

## Credits

- [Ali Alidoust](https://github.com/ali-alidoust) - the original Extended Video Export
- [Voukoder](https://archive.org/details/voukoder-13.4.1_202505) - encoding
- [Alexander Blade](http://www.dev-c.com/gtav/scripthookv/) - ScriptHookV SDK

## License

[Apache License 2.0](LICENSE.md), same as the original project.
