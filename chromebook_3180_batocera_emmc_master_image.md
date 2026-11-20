# Dell Chromebook 11 3180 (KEFKA) — Master image workflow to flash internal 16 GB eMMC (Batocera)

This guide assumes you already know how to:
- Convert the Chromebook to **MrChromebox UEFI Full ROM**
- Install and configure **Batocera** on the internal eMMC on one “golden” unit

Goal: create a **master image** of the internal eMMC and **restore** it onto many Chromebooks quickly.

---

## What you’ll build

- A **golden Chromebook** with Batocera installed on internal eMMC and configured:
  - Boot straight to **ARCADE gamelist**
  - Only ARCADE shown
  - Kiosk Mode on (optional)
  - Controller mappings working
  - ROMs + scraped media (optional; see storage notes)

- A **disk image** of the internal eMMC (`/dev/mmcblk0`) created with one of:
  - **Rescuezilla** (GUI, easiest)
  - **Clonezilla Live** (text UI, widely used, flexible)

Then you’ll restore the image to each Chromebook’s internal eMMC.

---

## Critical constraints (read first)

### A) Destination must be same size or larger than source
Cloning tools generally require the destination disk size to be **≥** the source disk size.

On “16 GB” eMMC devices, reported size can vary slightly (GiB vs GB and vendor variance).  
To avoid “destination is smaller” failures:

- **Best practice:** choose as your golden unit the Chromebook whose eMMC reports the **smallest** capacity (if you have a pile).
- **Extra-safe practice:** shrink the last partition slightly (see “Partition size safety margin” below).

### B) Batocera on 16 GB eMMC is tight
- Avoid scraping **video snaps** on eMMC unless you keep ROMs very small/curated.
- If you plan to distribute large ROM sets, consider storing ROMs/media on USB storage later.

### C) You are imaging the entire internal disk
Everything on internal eMMC will be overwritten when restoring.

---

# Part 1 — Build the golden unit (one-time)

## 1. Install Batocera to internal eMMC
Follow your runbook to install Batocera onto internal eMMC and boot from it.

## 2. Apply your appliance settings
On the golden unit (booted from eMMC), ensure:
- **Start on System** = ARCADE  
- **Start on Gamelist** = ON  
- **Systems Displayed** = only ARCADE  
- **UI Mode** = Kiosk (optional)

## 3. Add ROMs and media (optional)
- Copy ROMs to: `/userdata/roms/arcade/`
- Scrape metadata/art if you want the premium UX.

## 4. Confirm it’s “golden”
Reboot the golden unit twice and verify:
- Boots directly into ARCADE list
- Controller works in menu + game
- Hotkey quit works
- Audio works
- A small set of known-good titles launch

---

# Part 2 — Partition size safety margin (recommended)

Because some “16 GB” eMMC chips report slightly different capacities, you may want to create a small **unallocated tail** at the end of the disk on the golden unit. This increases the chance your image restores to every unit.

## Option A (simple): pick the smallest eMMC unit as the golden unit
1. Boot each Chromebook to any Linux (Batocera USB is fine).
2. Check the reported internal disk size in the Batocera “Install on new disk” list.
3. Pick the **smallest** one and build the golden image on that.

## Option B (strongest): shrink the last partition slightly using GParted Live
This is a one-time step on the golden unit after Batocera is installed to eMMC.

**What you need**
- A GParted Live USB stick
- Keyboard/trackpad (or external keyboard)

**Steps**
1. Create a **GParted Live** USB.
2. Boot the golden Chromebook from the GParted USB (UEFI boot menu).
3. In GParted, select the internal disk (typically `/dev/mmcblk0`).
4. Identify the last partition (Batocera userdata/SHARE; usually the largest ext4 partition).
5. **Resize** it to be slightly smaller, leaving **~300–500 MB unallocated at the end**.
6. Apply changes and shut down.

After this, boot Batocera once to confirm it still works.

> Note: shrinking ext4 is generally safe when done carefully, but do it only on the golden unit and only after verifying you have backups of ROMs/media.

---

# Part 3 — Create the master image

You have two solid approaches. Pick ONE.

---

## Approach 1: Rescuezilla (recommended: easiest GUI)

### What you need
- Rescuezilla USB stick
- External USB drive to store the image (USB HDD/SSD recommended)

### A1) Create Rescuezilla USB
1. Download Rescuezilla ISO on another computer.
2. Flash it to a USB stick with your preferred tool (Rufus/balenaEtcher/etc.).

### A2) Boot Rescuezilla on the golden Chromebook
1. Plug in:
   - Rescuezilla USB (boot media)
   - External USB drive (image storage)
2. Boot the Chromebook and choose the USB device from the UEFI boot menu.

### A3) Create a backup image of the internal eMMC
1. In Rescuezilla, choose **Backup**.
2. Select **Source drive**:
   - Internal eMMC (typically **mmcblk0**, ~16 GB)
3. Select **Destination location**:
   - Your external USB drive
4. Choose options (recommended):
   - Compression: **gzip** (good balance) or **zstd** (faster, if available)
   - Enable verification if offered
5. Start the backup and wait for completion.

### A4) Label and archive
On your external drive, keep:
- Image folder name (include date + “3180_kefka_batocera_emmc_golden”)
- A text file with:
  - Batocera version
  - Any special settings
  - Controller type tested
  - ROM set notes

---

## Approach 2: Clonezilla Live (recommended: most controllable)

Clonezilla’s UI is text-based but very reliable.

### What you need
- Clonezilla Live USB stick
- External USB drive to store the image

### B1) Create Clonezilla USB
1. Download Clonezilla Live ISO.
2. Flash ISO to USB (Rufus/balenaEtcher/etc.).

### B2) Boot Clonezilla on the golden Chromebook
1. Plug in:
   - Clonezilla USB (boot media)
   - External USB drive (image storage)
2. Boot via UEFI boot menu into Clonezilla.

### B3) Save an image (device-image)
You will choose menu items similar to:

1. **Clonezilla live** → default settings (or “To RAM” if you want)
2. Choose language / keyboard
3. **Start Clonezilla**
4. Choose: **device-image** (work with disks/partitions using images)
5. Choose image repository: **local_dev** (external USB drive)
6. When prompted, plug in external drive (already plugged in is fine), then press Enter
7. Select the external drive partition to mount as `/home/partimag`
8. Choose mode:
   - **Beginner** (recommended)
9. Choose action:
   - **savedisk** (save local disk as an image)
10. Name the image (example):
    - `3180_kefka_batocera_emmc_YYYYMMDD`
11. Select source disk:
    - **mmcblk0** (internal eMMC)
12. Accept defaults (recommended), then confirm to proceed.

Wait for imaging to complete, then shutdown.

### B4) (Optional) Enable integrity check
Clonezilla can run checksums; if you have time, enable the verification/check option when prompted.

---

# Part 4 — Restore (flash) the master image onto each Chromebook

You can restore using the same tool you used to create the image.

---

## Restore with Rescuezilla (GUI)
For each Chromebook:

1. Boot Rescuezilla from USB.
2. Plug in the external drive that contains your image.
3. Choose **Restore**.
4. Select the image.
5. Select **target disk**:
   - Internal eMMC (`mmcblk0`, ~16 GB)
6. Confirm destructive warnings.
7. Restore and wait.
8. Shutdown, remove USBs, boot normally.

---

## Restore with Clonezilla (text UI)
For each Chromebook:

1. Boot Clonezilla USB.
2. Plug in the external drive containing images.
3. **device-image** → **local_dev** → mount external drive as `/home/partimag`
4. Choose **Beginner**
5. Choose **restoredisk**
6. Select the image name you created
7. Select target disk:
   - **mmcblk0** (internal eMMC)
8. Confirm warnings and proceed.
9. Shutdown, unplug, boot.

---

# Part 5 — Post-restore per-unit smoke test (fast)

On each restored Chromebook:

1. Boot to Batocera (should land on ARCADE gamelist)
2. Confirm:
   - Only ARCADE shown
   - Controller works in menu
3. Launch 2–3 known-good titles
4. Confirm quit hotkey returns to list
5. Confirm audio

If any unit fails:
- Check it actually restored to **mmcblk0** (not USB)
- Check you removed the boot USB before reboot
- If Clonezilla reports “destination smaller,” use the **safety margin** steps

---

# Optional: Speeding up even more (advanced)

## A) Multicast / mass restore with Clonezilla SE
If you are restoring a lot of units at once and have a wired network setup, Clonezilla SE can multicast the image to many machines simultaneously. This is more setup overhead but can be dramatically faster for bulk deployments.

## B) Keep ROMs/media off eMMC
If you want the image to be small and fast to deploy:
- Image only the base system + settings
- Keep ROMs and scraped media on an external USB drive that you duplicate separately

---

# Operator note: What to do if Batocera version changes
If you update Batocera on the golden unit, you should:
- Re-verify everything (controllers, boot, ARCADE list)
- Create a new image with a new dated name
- Keep older images as rollback

---

## Suggested naming convention
- `3180_kefka_batocera_emmc_v40_YYYYMMDD`
- `3180_kefka_batocera_emmc_v41_YYYYMMDD`

Include Batocera version and date so you can track what’s deployed.

---
