# gpSP for MiyooCFW
An enhanced version of gpSP for low-level ARM devices.

## How to install
*Note: in these instructions, it is presumed you use the [MiyooCFW](https://github.com/TriForceX/MiyooCFW/) custom firmware on your device.*
1. Download the latest release ZIP-file or IPK-package over at [Releases](https://github.com/Apaczer/gpsp/releases/latest).
2. **ZIP:** Extract the `gpsp*.zip` content to ``$HOME`` directory on your device (possibly Powkiddy as button mapping matches for those devices).  

	**IPK:** Launch gpsp.ipk from GMenu2X's Explorer.
3. It is mandatory to include ``gba_bios.bin`` in working directory to make any ROM launch (emulator comes bundled with fake BIOS).

## Changelog
### v1.2.3
#### Improvements
- use newer GCC and revert to using staticly linked musl libraries for an output binary

### v1.2.2
#### Fixes
- correct button mapping to accommodate for latest 2.0 CFW (universal aka PocketGO)

### v1.2.1
#### Features
- add "borders" option to this port, that is triggering overlay image over unscaled video screen with border.png put inside working directory (set ``Display scalling->unscaled 3:2`` in menu to make it visible).

#### Fixes
- reduce tearing by enabling Doublebuffering in all video modes (hardcoded)
- eliminate audio underruns in sound_callback (reduces audio freezes)

### v1.2
#### Improvements
- Improve unfiltered video upscaling

### v1.1
#### New features
- Automatically exit menu on saving state
- Add setting to display or hide state screenshots
- Optimize the order of menu items

### v1.0
#### New features
- Map save/load state to buttons
- Auto save state feature (automatically save a state when exiting gpSP and open state when starting the emulator)
- Autofire A/B button (can be set in button mapping) ([based phantuanphong's solution](https://github.com/phantuanphong/gpsp-powkiddy))

#### Bugfixes
- Fix bug when loading or saving states didn't always use the displayed save slot
- Fix option to disable screen filter ([thanks to user @drowsnug on Discord](https://discord.com/channels/529983248114122762/540168599063756802/819836183105765406))
- Fix some minor text related issues in gpSP's GUI

#### PowKiddy specific changes
- Fix for A and B buttons being reversed in gpSP's GUI
- Fix mapping for A and B buttons being reversed

### pre v1.0
See [./readme.txt](./readme.txt)

## Thanks to
- gameblabla for writing the initial [Bittboy gpSP port](https://github.com/bittboy/gpsp)
- the other people mentioned in the changelog who shared improvements for gpSP

## How to build
1. Build the (preferably) static SDK from [buildroot](https://github.com/MiyooCFW/buildroot) or use the docker containters.
2. You can now build gpSP from `./miyoo/Makefile` with `make` (dependencies are: SDL-1.2, zlib)
3. Be sure to include `game_config.txt` and `gba_bios.bin`

### Optimizing your build
The release `gpsp` binaries have been optimized with PGO by using manually generated *.gcda files. Use `PROFILE=YES` make flag to generate those at runtime, or `PROFILE=APPLY` to optimize your output build after profiling.

### Debuging your build
Use `DEBUG=YES` make flag in `./miyoo/Makefile` to output unstripped build with debug info. 
For local PC testing there is `./x86/Makefile` which is only to be used within 32-bit platforms due to x86 assembly code. 