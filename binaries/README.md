# Binaries

Pre-built, self-contained NanoVNA Firmware Updater executables (v1.0.3). Each binary
bundles the .NET runtime, so **no .NET installation is required** — download the one
for your machine and run it.

| OS | Architecture | File |
|----|--------------|------|
| Windows | x64 (Intel/AMD) | [`windows/x64/NanoVnaUpdater.exe`](windows/x64/NanoVnaUpdater.exe) |
| Windows | ARM64 | [`windows/arm64/NanoVnaUpdater.exe`](windows/arm64/NanoVnaUpdater.exe) |
| Linux | x64 | [`linux/x64/NanoVnaUpdater`](linux/x64/NanoVnaUpdater) |
| Linux | ARM64 (e.g. Raspberry Pi 64-bit) | [`linux/arm64/NanoVnaUpdater`](linux/arm64/NanoVnaUpdater) |
| macOS | x64 (Intel) | [`macos/x64/NanoVnaUpdater`](macos/x64/NanoVnaUpdater) |
| macOS | ARM64 (Apple Silicon) | [`macos/arm64/NanoVnaUpdater`](macos/arm64/NanoVnaUpdater) |

## Flashing prerequisites

- **STM32 boards (NanoVNA-H / -H4 / gen1)** flash over USB DFU with `dfu-util`:
  - **Windows** — `dfu-util-static.exe` is fetched automatically (and is bundled in
    the `Firmware\` folder of the combined release zip). The "STM32 Bootloader"
    (WinUSB) driver normally installs itself from Windows Update the first time the
    unit enters DFU mode; if the flash step reports it cannot open the DFU device,
    see the driver note in the top-level README.
  - **Linux** — `sudo apt install dfu-util` (or `dnf` / `pacman`).
  - **macOS** — `brew install dfu-util`.
- **NanoVNA V2 / SAA-2** uses a built-in native serial bootloader — **no `dfu-util`
  needed** on any platform.

## Running on Linux / macOS

```sh
chmod +x NanoVnaUpdater      # if the executable bit was lost on download
./NanoVnaUpdater
```

On macOS the binary is unsigned; if Gatekeeper blocks it, clear the quarantine
attribute with `xattr -d com.apple.quarantine NanoVnaUpdater` (or allow it under
System Settings → Privacy & Security).

See the top-level `README.md` / `readme.txt` for full usage, board selection, the
DiSlord/Hugen fork choice, and the V2 details.
