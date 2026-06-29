==========================================================
  NanoVNA Firmware Updater  v1.0.1   -   by ZS6ORB
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
  1. First time only (STM32 boards): run  Driver\dpinst_amd64.exe
     to install the bundled ST DfuSe USB driver.
  2. Connect the NanoVNA in NORMAL mode (so it can be read).
  3. Run  NanoVnaUpdater.exe  (no install, no .NET needed).
  4. Follow the prompts. When asked, put the unit into
     DFU / bootloader mode and press ENTER.

  Driver note: the bundled ST DfuSe driver (run Driver\dpinst_amd64.exe
  once) lets Windows recognise the STM32 board in DFU mode so dfu-util
  can flash it. The flasher dfu-util-static.exe ships in Firmware\ (and
  is downloaded automatically if missing). If dfu-util reports "No DFU
  capable USB device", install WinUSB for the 0483:df11 device with
  Zadig (https://zadig.akeo.ie) as a fallback. The V2 flashes over its
  normal serial port and needs no driver.

LINUX (in the Linux/ folder)
  chmod +x NanoVnaUpdater
  sudo apt install dfu-util      (needed for H / H4 / gen1)
  ./NanoVnaUpdater
  (dfu-util is NOT needed for the V2 - its flasher is built in.)

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

STM32 (H / H4 / gen1):
  On the unit:  CONFIG -> DFU -> RESET AND ENTER DFU
  (firmware > 0.2), or power on with BOOT0 tied to VDD.
  The screen goes blank.

V2 / SAA-2:
  Switch OFF, HOLD the LEFT button, switch ON, release.
  The screen stays WHITE - it is now in bootloader mode.

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

Updater by ZS6ORB.

----------------------------------------------------------

This is a personal tool by Wayne Bevan (ZS6ORB), shared as-is.
Comments, suggestions or feature ideas? Drop me a mail:
wayne.bevan@yahoo.co.uk   73!
