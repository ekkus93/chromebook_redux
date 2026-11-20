# Dell Chromebook 11 3180 → Commodore 64 “appliance” (Batocera + VICE)

This runbook turns a Dell Chromebook 11 3180 into a **Commodore 64** machine that boots straight into the **C64 game/software list** (EmulationStation).  
No Linux prompt is required for normal use.

---

## Assumptions / prerequisites

- Your 3180 is already converted to **MrChromebox UEFI Full ROM** (so it boots like a normal PC).
- You already have **Batocera x86_64** installed (recommended: on internal eMMC using your existing runbook).

If Batocera is not installed yet, do that first, then come back here.

---

## Part 1 — Enable “boot straight to C64” (UI-only)

### 1. Boot into Batocera
Power on and let Batocera fully boot into EmulationStation.

### 2. Set startup to Commodore 64 (c64) and land on the gamelist
1. Press **START** to open the main menu.
2. Go to **UI SETTINGS → STARTUP SETTINGS**.
3. Set:
   - **Start on System** = **Commodore 64**
   - **Start on Gamelist** = **ON**  
   (These settings are documented under Startup Settings.)  
   Ref: https://wiki.batocera.org/menu_tree  (Start on System / Start on Gamelist)  

### 3. Make it “C64-only” (optional but recommended for appliance feel)
1. **UI SETTINGS → SYSTEMS DISPLAYED**
2. Hide everything except **Commodore 64**  
Ref: https://wiki.batocera.org/menu_tree  (Systems displayed)

### 4. Lock down UI (optional)
Enable **Kiosk mode** if you want fewer settings exposed.  
(If you do this, verify that “Map/Configure controller” is still reachable in your theme/menu.)

---

## Part 2 — Add C64 games and software

### 5. Copy ROMs/software into the C64 folder
Batocera C64 folder:  
- **`/userdata/roms/c64/`**  
Ref: https://wiki.batocera.org/systems:c64  (Folder: /userdata/roms/c64)

Accepted formats include: **.d64, .d81, .crt, .prg, .tap, .t64, .m3u, .zip, .7z**  
Ref: https://wiki.batocera.org/systems:c64  (Accepted ROM formats)

You can copy files via:
- Batocera network share (from another PC) **`\\BATOCERA\share\roms\c64`**
- Or by opening the file manager on the Batocera box (often **F1** on a keyboard).

### 6. Refresh the gamelist
- **START → GAME SETTINGS → UPDATE GAMELISTS**

Now C64 titles should appear under the Commodore 64 system.

---

## Part 3 — BASIC programming setup (READY prompt + saving)

You can absolutely program in BASIC. The key is: launch something that **doesn’t auto-run** and gives you the standard BASIC prompt.

### 7. Create a “BASIC Work Disk” image (recommended)
Make a blank disk image on another computer:

- Create a blank **D64** and name it:  
  `BASIC WORK DISK.d64`

Copy it to:
- `/userdata/roms/c64/`

### 8. Start BASIC
1. From the C64 gamelist, launch **BASIC WORK DISK.d64**.
2. You should land at the **READY.** prompt.

### 9. Save and load programs
On the C64 prompt:

- Save:  
  `SAVE "MYPROG",8`
- Load:  
  `LOAD "MYPROG",8`
- Run:  
  `RUN`

Tip: If you want multiple users, create multiple work disks (one per user or per machine).

---

## Part 4 — Special key mapping cheat sheet (C64 “weird keys”)

Batocera’s C64 system documentation and related mapping notes cover these controls.

### A) Controller-level special keys (recommended: consistent across random controllers)

Common C64 special actions in Batocera C64 setups:
- **Run/Stop** = **L1**  
- **Restore** = **R1**
- **Shift (C64 Shift modifier)** = **R2** (useful for F2/F4/F6/F8 equivalents)
- **Virtual keyboard** = **R3**
- **Switch joystick port 1/2** = **L3**
- **Warp (fast-forward)** = **L2** (common for fast disk loads)

Refs:
- https://wiki.batocera.org/systems:c64  (C64 system docs; see controls section in the PDF export)
- https://github.com/batocera-linux/batocera.linux/issues/4801  (documents the standard mapping set used/desired)

### B) Chromebook keyboard notes
The built-in Chromebook keyboard usually works fine in Batocera.  
However, C64-specific keys like **RUN/STOP** and **RESTORE** don’t exist on modern keyboards and are typically mapped by the emulator.

If you ever need those actions and don’t have a pad handy:
- Use the **virtual keyboard** (R3 on controller)  
Ref: https://wiki.batocera.org/systems:c64  (virtual keyboard)

---

# Operator Card (for end users)

## Purpose
This device is a **Commodore 64**. Choose games/software from the list, or open BASIC to program.

## Power on / Start
1. Press the Chromebook **Power** button.
2. Wait for the **Commodore 64** game list.

## Controller Setup (plug in anything)
### If your controller “just works”
- Use D-pad/stick to navigate.
- Press **A/Confirm** to launch.

### If you are prompted to map buttons
1. Follow the on-screen mapping prompts (press the requested buttons).
2. When finished, select **SAVE**.

### If you are NOT prompted but the controller doesn’t work
1. Press **START** (on a working controller or the keyboard) to open the menu.
2. Choose **MAP/CONFIGURE A CONTROLLER** and follow the wizard.  
Ref: https://wiki.batocera.org/configure_a_controller

### If someone mapped a controller wrong (admin fix)
Batocera stores controller mappings in:
- `/userdata/system/configs/emulationstation/es_input.cfg`  
Ref: https://wiki.batocera.org/configure_a_controller

Fast reset method:
1. Press **F1** (open file manager).
2. Navigate to `/userdata/system/configs/emulationstation/`
3. Delete `es_input.cfg`
4. Reboot and re-map the controller  
Community ref: https://www.reddit.com/r/batocera/comments/1jr6ix3/resetting_global_controller_settings/

## Launch a game/software
1. Select a title in the list.
2. Press **A/Confirm**.

## Quit back to the list
- Use the standard Batocera hotkey combo for your controller (varies by pad).
- If you can’t quit, use the **VICE menu** (commonly mapped via Start or virtual keyboard depending on emulator/core).

## BASIC programming (READY prompt)
1. Select **BASIC WORK DISK.d64**
2. Type your program.
3. Save with: `SAVE "NAME",8`
4. Load with: `LOAD "NAME",8`
5. Run with: `RUN`

## Shutdown (recommended)
1. From the game list, press **START**
2. Choose **QUIT / SHUTDOWN SYSTEM → SHUTDOWN**
