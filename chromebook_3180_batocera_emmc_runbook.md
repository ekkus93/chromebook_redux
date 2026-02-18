# Dell Chromebook 11 3180 (KEFKA) → Batocera on internal 16 GB eMMC (ARCADE appliance)

This runbook installs **Batocera x86_64** to the Chromebook’s **internal eMMC** so it boots like a console:
**Power on → EmulationStation → ARCADE gamelist** (no Linux commands for end users).

---

## What you need

- Dell Chromebook 11 3180 (board/firmware target: **KEFKA**)
- 16 GB+ **USB thumb drive** (installer/boot stick; 32 GB is fine)
- Phillips screwdriver (to open bottom cover)
- Internet access during install (Wi-Fi or USB-Ethernet dongle)
- Optional: 2nd USB drive (or another computer) to store the **firmware backup**

---

## High-level flow

1. Enable ChromeOS **Developer Mode**
2. Disable firmware **write protection** (WP screw)
3. Flash **MrChromebox UEFI Full ROM**
4. Create **Batocera USB boot stick**
5. Boot Batocera from USB
6. Use Batocera’s **Install Batocera on a new disk** → target internal eMMC
7. Reboot without USB → boot from internal eMMC
8. Configure Batocera to boot straight into **ARCADE gamelist** and lock down UI

---

## Part A — Convert Chromebook to UEFI (MrChromebox Full ROM)

### A1) Enable Developer Mode (wipes local ChromeOS state)
1. Power off the Chromebook.
2. Enter Recovery: hold **Esc + Refresh**, then tap **Power**.
3. Follow prompts to enable **Developer Mode**.
4. Let it complete the transition and reboot.

### A2) Remove firmware write protect screw (WP screw)
1. Power off completely and unplug charger.
2. Remove the bottom cover.
3. Locate and remove the **write-protect screw** (WP screw).
4. Reassemble enough to boot (you can fully reassemble later after you confirm success).

### A3) Flash MrChromebox UEFI (Full ROM)
1. Boot to the **ChromeOS login screen** (do not sign in).
2. Switch to VT2 terminal: **Ctrl + Alt + F2**
   - (On some Chromebooks, “F2” is a top-row key such as the right-arrow; use the key labeled F2 in VT.)
3. Log in as user `chronos` (no password).
4. Run the firmware script:
   - `cd; curl -LOf https://mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh`
5. Choose **Install/Update UEFI (Full ROM) Firmware**.
6. When prompted, **create and save a firmware backup** somewhere safe.
7. Follow prompts to finish. Power off when instructed.

✅ Result: Chromebook now behaves like a standard UEFI PC.

---

## Part B — Create Batocera USB boot stick

### B1) Download Batocera (x86_64)
1. Download the **Batocera x86_64** image (`.img.gz`) on another computer.

### B2) Flash image to USB
Use any image writer (Raspberry Pi Imager, balenaEtcher, etc.).
Example with Raspberry Pi Imager:
1. **CHOOSE OS** → **Use custom**
2. Select the Batocera `.img.gz`
3. **CHOOSE STORAGE** → select your USB stick
4. Write, wait for completion, safely eject.

---

## Part C — Install Batocera from USB onto internal eMMC

### C1) Boot Batocera from USB
1. Insert the Batocera USB stick.
2. Power on.
3. If needed, use the MrChromebox UEFI boot menu to select the USB device.
4. Let Batocera boot fully (first boot may take longer).

### C2) Ensure internet is working (required)
1. In Batocera, connect to Wi-Fi **or** plug in USB Ethernet.
2. Confirm it shows connected.

### C3) Install Batocera onto the internal disk (eMMC)
**This is the key step. Choose the correct target disk.**

1. Press **START** to open the Batocera main menu.
2. Go to **SYSTEM SETTINGS**.
3. Select **INSTALL BATOCERA ON A NEW DISK**.
4. Select the **destination disk** = internal eMMC:
   - Typically shows as **`mmcblk0`** around **14–16 GB** (often ~14.9 GiB).
   - Your USB installer is typically **`sda`** around **28–32 GB** (or larger).
5. Select architecture: **x86_64**.
6. Confirm the warnings and start installation.
7. Wait until it completes (do not interrupt).

#### Quick “pick the right disk” rule
- Internal eMMC: **~15 GiB**, device name often **mmcblk0**
- Installer USB: **~29 GiB**, device name often **sda**
- If you have a microSD inserted or multiple USB drives, remove them to avoid confusion.

### C4) Reboot onto internal eMMC
1. When the install completes: **Shutdown**.
2. Remove the USB stick.
3. Power on.
4. If you land in a UEFI boot menu, choose the internal “UEFI OS” / internal drive entry.

✅ Result: Batocera boots from internal eMMC.

---

## Part D — Configure “ARCADE appliance” UX (EmulationStation)

### D1) Boot straight into ARCADE gamelist
1. **MAIN MENU → UI SETTINGS → STARTUP SETTINGS**
2. Set:
   - **Start on System** = **ARCADE**
   - **Start on Gamelist** = **ON**

### D2) Hide everything except ARCADE
1. **MAIN MENU → UI SETTINGS → SYSTEMS DISPLAYED**
2. Disable all systems except **ARCADE**

### D3) Enable Kiosk mode (recommended)
1. **MAIN MENU → SYSTEM SETTINGS → UI MODE**
2. Set **Kiosk**
   - (Keep the “exit kiosk” key sequence noted somewhere for admins.)

---

## Part E — Add games (best arcade experience)

### E1) Put ROMs in ARCADE folder
- Copy ROM ZIPs to: `/userdata/roms/arcade/`

### E2) Refresh gamelists
- **MAIN MENU → GAME SETTINGS → UPDATE GAMELISTS**

### E3) Optional: scrape metadata/art (big UX improvement)
- **MAIN MENU → SCRAPER**
  - Scrape box art/snapshots/descriptions (avoid videos if storage is tight)

---

## Verification checklist (per unit)

- Boots directly into **ARCADE gamelist**
- Only ARCADE system is visible
- Controller works in menus
- Launch 3–5 known-good arcade titles
- Audio output works as expected

---

## Common gotchas

- **ROMset mismatch** is the #1 reason games don’t launch (especially MAME lineage).
- **16 GB eMMC is tight** if you scrape lots of videos; prefer images, curate ROMs.
- If it keeps booting USB: remove USB, then select internal boot entry in UEFI menu.

---

# Operator Card (for end users)

**Purpose:** This device is an arcade console. Use the controller to select and play games.

## Power on / Start playing
1. Press the Chromebook **Power** button.
2. Wait for the **ARCADE game list** to appear.
3. Use the D-pad / stick to select a game.
4. Press **A / Confirm** to launch.

## In-game controls (typical defaults)
- **Start game:** **Start** button
- **Insert coin/credit:** **Select** button
- **Quit game (back to list):** **Hotkey + Start**
  - *Hotkey is commonly mapped to the controller’s “Home/Guide” button, or Select acting as Hotkey on many setups.*

> If Hotkey+Start doesn’t work on your controller, notify the admin—this is configurable.

## Pause / menus
- If a **Pause** function exists, it’s usually **Hotkey + A** (varies by emulator/core).
- In kiosk mode, advanced settings/menus may be intentionally restricted.

## Shutdown (recommended)
1. From the game list, press **START** to open the menu.
2. Select **QUIT** (or **SHUTDOWN SYSTEM**) → **SHUTDOWN**.

**Do not** hold the power button to hard-power-off unless the system is frozen.

## If something goes wrong
- If a game doesn’t start, press **B / Back** to return to the list and try another title.
- If controls aren’t working, unplug/replug the controller (if USB) and restart the device.
- For repeated issues, contact the admin (ROM compatibility and controller mapping are the most common fixes).
