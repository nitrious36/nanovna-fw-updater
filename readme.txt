==========================================================
  NanoVNA Firmware Updater  v1.0.5   -   by ZS6ORB
==========================================================

Detects a connected NanoVNA, reads its firmware, and updates
it to the latest matching build. One tool for the whole family:

  NanoVNA-H        STM32   ->  dfu-util   (GitHub)
  NanoVNA-H4       STM32   ->  dfu-util   (GitHub)
  NanoVNA (gen1)   STM32   ->  dfu-util   (GitHub)
  NanoVNA V2/SAA-2 GD32    ->  built-in serial bootloader (nanorfe.com)

For the STM32 boards it detects, and lets you choose between,
the DiSlord and Hugen firmware forks (Hugen builds are also
matched to your clock chip: MS5351M / Si5351 / clone).

----------------------------------------------------------
QUICK START
----------------------------------------------------------

WINDOWS
  1. Connect the NanoVNA in NORMAL mode (so it can be read).
  2. Run  NanoVnaUpdater.exe  (no install, no .NET needed).
  3. Follow the prompts. When asked, put the unit into
     DFU / bootloader mode and press ENTER.

  Driver note: flashing the STM32 boards uses dfu-util, which
  needs the "STM32 Bootloader" (WinUSB) driver on the DFU device.
  Windows normally installs it BY ITSELF from Windows Update the
  first time the unit enters DFU mode - nothing to do. If the
  flash step reports it cannot OPEN the DFU device (common on
  corporate PCs that block driver updates), fix it with the
  unit still in DFU mode: run the bundled Driver\zadig-2.9.exe
  (or download from https://zadig.akeo.ie) ->
  Options -> List All Devices -> select "STM32 BOOTLOADER"
  (0483 DF11) -> choose WinUSB -> Install/Replace Driver, then
  re-run the updater. Alternatively try Device Manager -> "STM32
  BOOTLOADER" (warning icon) -> Update driver -> Search
  automatically. The bundled Driver\
  folder (ST DfuSe driver) is only for ST's own DfuSe tools -
  dfu-util does not use it. The flasher dfu-util-static.exe
  ships in Firmware\ (and is downloaded automatically if
  missing). The V2 flashes over its normal serial port and
  needs no driver.

LINUX / MACOS
  chmod +x NanoVnaUpdater
  sudo apt install dfu-util      (Linux - needed for H / H4 / gen1)
  brew install dfu-util          (macOS)
  ./NanoVnaUpdater
  (dfu-util is NOT needed for the V2 - its flasher is built in.
   If dfu-util cannot OPEN the device, it is usually permissions:
   re-run with sudo, or add a udev rule for 0483:df11.
   macOS: if Gatekeeper blocks the unsigned binary, run
   xattr -d com.apple.quarantine NanoVnaUpdater)

----------------------------------------------------------
THE FIRMWARE FOLDER
----------------------------------------------------------

The Firmware folder is optional. Run the lone NanoVnaUpdater.exe
and it creates the folder, then downloads the firmware .bin and
dfu-util-static.exe as needed. A pre-filled Firmware folder (as
shipped) is reused, so the bundled package also works offline.
It also carries optional manual-flash helpers (DFU_LOAD_BIN.bat,
HEX2DFU.exe) that the app itself does not use.

----------------------------------------------------------
ENTERING UPDATE MODE
----------------------------------------------------------

H / gen1 (2.8" screen):
  On the unit:  CONFIG -> DFU -> RESET AND ENTER DFU
  (firmware > 0.2), or power off, short BOOT0 to VDD,
  power on.  The screen goes blank.

H4 (4" screen) - either method:
  - Power OFF, HOLD the multifunction switch, power ON, or
  - CONFIG -> EXPERT SETTINGS -> DFU -> RESET AND ENTER DFU
  The screen stays BLACK - this is correct, not a fault.

V2 / SAA-2:
  Power OFF, HOLD the LEFT button, power ON, then release.
  The screen stays WHITE - it is now in bootloader mode.

A blank/black (or white, on V2) screen means the unit is
waiting for firmware - it is NOT dead. Do not disconnect
or power-cycle during flashing; if a flash fails, the unit
stays in update mode, so just retry.

----------------------------------------------------------
OPTIONS
----------------------------------------------------------

  [folder]     Firmware folder (default: 'Firmware' next to exe)
  --h --h4 --gen1 --v2          Force the board
  --v2-2 --v2plus --v2plus4     Force the V2 sub-model
  --dislord --hugen             Choose the firmware fork (STM32)
  --ms --si --zk                Hugen clock-chip variant (STM32)
  --port P     Serial port (COMx / /dev/ttyACMx)
  --no-check   Skip the serial version check
  --offline    Flash already-downloaded firmware (no internet)
  --no-update-check  Skip the check for a newer updater build
  -f --force   Flash even if already on the latest build
  --no-flash   Download only, do not flash
  -y --yes     Do not prompt before flashing
  -v --version Print the version and exit
  -h --help    Show help

Examples:
  NanoVnaUpdater                 auto-detect everything
  NanoVnaUpdater --h4 --dislord  force an H4 with DiSlord
  NanoVnaUpdater --hugen --ms    Hugen build for an MS5351M board
  NanoVnaUpdater --v2plus4       NanoVNA V2 Plus4 from nanorfe.com

----------------------------------------------------------
CREDITS
----------------------------------------------------------

Firmware belongs to its authors: DiSlord (NanoVNA-D),
hugen79 (NanoVNA-H), and NanoRFE / OwOComm (NanoVNA V2 /
SAA-2). This tool only downloads and flashes their official
builds. Trademarks are the property of their owners.
Zadig (bundled driver installer) is by Pete Batard, GPLv3 -
https://zadig.akeo.ie

Updater by ZS6ORB.

----------------------------------------------------------

This is a personal tool by Wayne Bevan (ZS6ORB), shared as-is.
Comments, suggestions or feature ideas? Drop me a mail:
wayne.bevan@yahoo.co.uk   73!
