# Flashing Guide — AMD Polaris (RX 400 / 500)

This is the procedure Renewable Revolt uses to recover and convert Polaris cards
on its refurbishment line.  Read it once before your first flash.

---

## 1. Pick the right amdvbflash version

The tool version matters more than anything else on Polaris, and the wrong one
wastes an afternoon:

| Version | Use for | Notes |
|---------|---------|-------|
| **2.93** | Polaris and older | Has the `-f` force / signature-bypass; best for modded mining BIOSes |
| **3.15** | Polaris + Navi | Win11-friendly, has `-unlockrom`; the external build's `-f` SSID bypass is unreliable |
| **5.x** (5.0.905) | Navi and newer only | **Does not support Polaris** — returns "Unsupported GPU" / "VBIOS related functionality not supported" for `0x67DF` |

Work from the version's own folder — the CLI executable needs its companion
`ULPSCtrl.dll` beside it.  Run `AMDVBFlashDriverInstaller` as Administrator first
if the tool reports "cannot load driver."

---

## 2. Prepare

- Boot the system on **integrated graphics** so the target card is free and you
  keep a display if the flash leaves the card dark.
- Open the command prompt **as Administrator** ("Access is denied" means you
  didn't).

## 3. Confirm, back up, flash

```
amdvbflash -i                        # the card must appear here first
amdvbflash -s 0 backup.rom           # ALWAYS back up the current image
amdvbflash -p 0 <matched_image>.rom  # add -f (v2.93) if it balks at the structure
```

A clean result ends with `programmed` **and** `verified`, both at `80000/80000h`,
and `Restart System To Complete VBIOS Update`.  The `verified` line is the one
that matters — it means the chip's contents match the image bit-for-bit.

## 4. First boot

On the first pure-UEFI boot after adding a GOP, expect a single video-error beep
followed by an automatic reboot into a clean POST.  The board had the card
cached as legacy-only; the first attempt can't hand off video, it resets, and
the second attempt sees the new GOP.  **This is normal and self-healing** —
budget for it on every card you convert.

Confirm success in GPU-Z: the **UEFI** box should now be checked, and the compute
APIs (OpenCL, Vulkan, DirectCompute, DirectML, OpenGL) should all be lit.

---

## 5. If the card won't enumerate — CH341A

If `amdvbflash -i` reports **"Adapter not found"**, software can't reach the card
and no retry changes that.  Recover it with a **CH341A SPI programmer**, which
talks to the BIOS chip directly regardless of GPU state:

- **Check the chip voltage first.** Many GPU SPI chips are 1.8 V; a stock CH341A
  drives 3.3 V on the data lines and can damage a 1.8 V chip.  Identify the chip
  markings and use a 1.8 V adapter or a known-safe programmer revision.
- Clip onto the SPI flash, **read the existing image first** (even a corrupt one
  usually still carries a readable config string that names the memory vendor),
  then write the matched known-good image.

## 6. Code 43 after a clean, matched flash

If Windows still shows **Code 43** after a clean flash of the correctly-matched
image, the cause is the modded mining BIOS failing the driver's signature check,
not a mismatch.  Run the **AMD/ATI Pixel Clock Patcher** (atikmdag-patcher) to
bypass the signature check on the driver side.  If Code 43 persists *and* the
card artifacts, you have the wrong memory vendor — reflash the matching folder.

---

## Diagnostic signatures, quick reference

- **Enumerates (even as Basic Display / Code 43) + `amdvbflash -i` sees it** →
  BIOS problem, software-recoverable.
- **"Adapter not found" + an unknown PCI / Basic Display device in Windows** →
  VBIOS too corrupt for software to bind; CH341A.
- **Nothing in Device Manager at all** → CH341A attempt; if a clean write still
  doesn't revive it, suspect hardware (core, VRAM, VRM).
- **Artifacting that tracks the memory clock, clean at stock** → wrong-vendor
  strap set; reflash the matching folder.
