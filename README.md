# NanoVNA Firmware Updater

A single-file, cross-platform console tool (**Windows, Linux and macOS — x64 and
ARM64**) that detects a connected **NanoVNA**, reads its installed firmware, and
updates it to the latest matching build — by **ZS6ORB**.

It handles the whole fragmented NanoVNA family from one program:

| Board | Chip | Flash method | Firmware source |
|-------|------|--------------|-----------------|
| NanoVNA-H | STM32F072/303 | USB DFU (`dfu-util`) | GitHub Releases |
| NanoVNA-H4 | STM32F303 | USB DFU (`dfu-util`) | GitHub Releases |
| NanoVNA (gen1) | STM32F072 | USB DFU (`dfu-util`) | GitHub Releases |
| NanoVNA V2 / SAA-2 | GD32F303 | Native serial bootloader | nanorfe.com |

For the STM32 boards it also distinguishes — and lets you **choose between** — the
two firmware forks:

- **DiSlord** (`DiSlord/NanoVNA-D`) — one `.bin` per board, auto-detects the clock chip.
- **Hugen** (`hugen79/NanoVNA-H`) — per-clock-chip builds (MS5351M / Si5351 / clone).

---

## Download

Grab the self-contained build for your machine from the **[`binaries/`](binaries/)**
folder — the .NET runtime is bundled, so **no .NET install is required**:

| OS | x64 | ARM64 |
|----|-----|-------|
| **Windows** | [`binaries/windows/x64/NanoVnaUpdater.exe`](binaries/windows/x64/NanoVnaUpdater.exe) | [`binaries/windows/arm64/NanoVnaUpdater.exe`](binaries/windows/arm64/NanoVnaUpdater.exe) |
| **Linux** | [`binaries/linux/x64/NanoVnaUpdater`](binaries/linux/x64/NanoVnaUpdater) | [`binaries/linux/arm64/NanoVnaUpdater`](binaries/linux/arm64/NanoVnaUpdater) |
| **macOS** | [`binaries/macos/x64/NanoVnaUpdater`](binaries/macos/x64/NanoVnaUpdater) | [`binaries/macos/arm64/NanoVnaUpdater`](binaries/macos/arm64/NanoVnaUpdater) |

Prefer a packaged download? Each platform also has a zip on the
[latest release](https://github.com/nitrious36/nanovna-fw-updater/releases/latest),
plus a combined `nanovna-fw-updater.zip` that includes a pre-populated `Firmware/`
folder for offline use. See [`binaries/README.md`](binaries/README.md) for
per-platform notes.

---

## Quick start

### Windows

Windows SmartScreen may warn that this is an unrecognised app (it's an unsigned personal tool). Click More info → Run anyway. Verify the download with the SHA-256 in the release notes.

1. Double-click **`binaries\windows\x64\NanoVnaUpdater.exe`** (or the `arm64` build on
   an ARM PC) — no install, no .NET runtime needed.
2. Connect the NanoVNA in **normal mode** first so it can read the version.
3. Follow the prompts. When asked, put the unit into DFU / bootloader mode and press ENTER.

> **Driver note (Windows):** the STM32 boards expose a standard USB CDC serial port
> (recognised by Windows automatically) and, in DFU mode, a separate **DFU** device.
> Flashing that DFU device with `dfu-util` needs the **"STM32 Bootloader" (WinUSB)**
> driver — Windows normally installs it **by itself from Windows Update** the first
> time the unit enters DFU mode, so there is usually nothing to do. If the flash step
> reports it **cannot open** the DFU device (common on corporate PCs that block
> driver updates), fix it with the unit still in DFU mode: run **Zadig** (bundled
> as **`Driver\zadig-2.9.exe`**, or from `https://zadig.akeo.ie`) → Options → List All Devices → select **STM32
> BOOTLOADER** (0483 DF11) → choose **WinUSB** → Install/Replace Driver, then re-run the updater.
> Alternatively try **Device Manager → "STM32 BOOTLOADER"** (warning icon) **→
> Update driver → Search automatically**. The bundled `Driver\` folder (ST DfuSe
> driver) is only for ST's own DfuSe tools — `dfu-util` does not use it. The flasher
> itself, `dfu-util-static.exe`, ships in the `Firmware\` folder of the combined
> download (and is fetched automatically if absent). The **V2** flashes over its
> normal serial port, so it needs no driver.

### Linux

Builds are under **`binaries/linux/x64/`** and **`binaries/linux/arm64/`**:

```bash
chmod +x NanoVnaUpdater
sudo apt install dfu-util        # required for the STM32 boards (H / H4 / gen1)
./NanoVnaUpdater
```

`dfu-util` is only needed for the STM32 boards. The **V2** flasher is built in and
needs nothing extra. On Linux you may need udev rules (or `sudo`) for serial/DFU
access.

### macOS

Builds are under **`binaries/macos/x64/`** (Intel) and **`binaries/macos/arm64/`**
(Apple Silicon):

```bash
brew install dfu-util            # required for the STM32 boards (H / H4 / gen1)
chmod +x NanoVnaUpdater
xattr -d com.apple.quarantine NanoVnaUpdater   # clear Gatekeeper quarantine (unsigned)
./NanoVnaUpdater
```

As on Linux, `dfu-util` is only needed for the STM32 boards; the **V2** flasher is
built in. The binary is unsigned — if Gatekeeper blocks it, allow it under
**System Settings → Privacy & Security**. The NanoVNA appears as `/dev/tty.usbmodem…`;
pass it with `--port` if auto-detect misses it.

---

## How it works

```
[1/5]  Check the internet (GitHub / nanorfe.com).  Falls back to OFFLINE.
[2/5]  Detect the board over USB serial (version + info).
[3/5]  Find the latest matching firmware (fork-aware for STM32, model-aware for V2).
[4/5]  Download it (or reuse an already-downloaded copy), and fetch dfu-util if needed.
[5/5]  Put the unit in DFU / bootloader mode and flash it.
```

- **Auto-detect:** reads `version` and `info` over the serial port to identify the
  board (H / H4 / gen1 / V2), the running fork (DiSlord vs Hugen) and the installed
  version. If it can't, it falls back to a menu.
- **Version match:** for STM32 it compares the installed version against the latest
  release from the **same fork** and skips flashing if you're already current
  (override with `--force`).
- **Fork choice:** even when a fork is detected you're offered the choice of DiSlord
  or Hugen (the detected one is the default).
- **Offline mode:** with `--offline` (or if the network is down) it flashes a
  firmware file you've already downloaded into the `Firmware` folder.
- **Self-provisioning:** the `Firmware` folder is optional — run the lone
  `NanoVnaUpdater` and it creates the folder, then downloads the firmware `.bin`
  and `dfu-util-static.exe` as needed. A pre-populated `Firmware` folder (as shipped
  in the combined zip) is reused instead, so a bundled package also works with no
  internet. The folder also carries optional manual-flash helpers (`DFU_LOAD_BIN.bat`,
  `HEX2DFU.exe`) that the app itself doesn't use.
- **End-of-run summary:** every step is collected and printed as a single summary,
  and the window stays open until you press **[Q]**.

### Entering update mode

**NanoVNA-H / gen1 (2.8"):** on the unit, `CONFIG → DFU → RESET AND ENTER DFU`
(firmware > 0.2), or power off, short **BOOT0** to VDD and power on. The screen goes blank.

**NanoVNA-H4 (4"):** either hold the multifunction (jog) switch **down** while powering
on, **or** use `CONFIG → EXPERT SETTINGS → DFU → RESET AND ENTER DFU`. The screen stays
**black** — that's correct, not a fault.

**V2 / SAA-2:** switch the unit **OFF**, **hold the LEFT button**, switch it **ON**,
then release. The screen stays **white** — it's now in serial-bootloader mode.

A blank/black (or white, on V2) screen means the unit is waiting for firmware — it is
**not** dead. Don't disconnect or power-cycle during flashing; if a flash fails, the
unit stays in update mode, so just retry.

---

## Command-line options

```
Usage:  NanoVnaUpdater [folder] [options]

  [folder]        Firmware folder (default: a 'Firmware' folder next to the exe)
      --h         Force NanoVNA-H        --h4    Force NanoVNA-H4
      --gen1      Force NanoVNA gen1     --v2    Force NanoVNA V2
      --v2-2 | --v2plus | --v2plus4    V2 sub-model (skip the V2 menu)
      --dislord   Use DiSlord firmware   --hugen Use Hugen firmware
      --ms|--si|--zk   Hugen clock-chip variant (STM32 only)
      --port P    Serial port (COMx / /dev/ttyACMx)
      --no-check  Skip the serial version check
      --offline   Flash already-downloaded firmware (skip the internet)
      --no-update-check  Skip the startup check for a newer updater build
  -f, --force     Flash even if already on the latest build
      --no-flash  Download only, do not flash
  -y, --yes       Do not prompt before flashing
  -v, --version   Print the version and exit
  -h, --help      Show this help
```

Examples:

```bash
NanoVnaUpdater                 # auto-detect everything, prompt before flashing
NanoVnaUpdater --h4 --dislord  # force an H4 with DiSlord firmware
NanoVnaUpdater --hugen --ms    # Hugen build for an MS5351M board
NanoVnaUpdater --v2plus4       # NanoVNA V2 Plus4 from nanorfe.com
NanoVnaUpdater --offline --h4  # flash a previously-downloaded H4 .bin
NanoVnaUpdater --no-flash      # just download the latest, don't flash
```

---

## NanoVNA V2 / SAA-2 details

The V2 family uses a GD32F303, which has **no USB DFU**. This tool ships a native
C# port of the project's `bootload_firmware.py` register protocol, so no Python and
no `dfu-util` are required:

- Firmware is downloaded from **nanorfe.com** (the version is the build date).
- Models: **V2.2 / S-A-A-2** (`v2_2`), **V2 Plus / V2.3** (`v2plus`),
  **V2 Plus4 / Plus4 Pro** (`v2plus45`).
- Flashing streams the `.bin` to flash start **0x08004000** in device-acked blocks,
  then reboots the unit.
- Before writing anything it confirms the unit is genuinely in the bootloader
  (firmware-major register reads `0xFF`), so pointing it at the wrong serial port
  fails safely instead of writing garbage.

> **Status:** the V2 backend is code-complete and verified end-to-end for detection
> and download against nanorfe.com, but the **physical flash has not been tested on
> real V2 hardware** (development was done with a NanoVNA-H4). Use with appropriate
> care on a V2, and keep a known-good firmware to recover with.

---

## Credits & trademarks

Firmware is the work of its respective authors — **DiSlord** (NanoVNA-D),
**hugen79** (NanoVNA-H), and the **NanoRFE / OwOComm** team (NanoVNA V2 / SAA-2).
This updater only downloads and flashes their official builds. NanoVNA, SAA-2 and
related names are the property of their respective owners.

dfu-util (used for flashing) is © its authors and licensed under GPLv2 — https://dfu-util.sourceforge.net.
Zadig (bundled driver installer) is by Pete Batard, GPLv3 — https://zadig.akeo.ie.

Updater by **ZS6ORB**.

---

This is a personal tool by **Wayne Bevan (ZS6ORB)**, shared as-is. Comments,
suggestions or feature ideas? Drop me a mail: <wayne.bevan@yahoo.co.uk> — 73!
