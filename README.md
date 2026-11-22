# Renewable Revolt — Polaris VBIOS Vault

Recovered, UEFI-converted, and validated AMD Polaris graphics BIOSes, with the
documentation to flash them, maintained by **Renewable Revolt, Inc.** — a
veteran-owned 501(c)(3) nonprofit that recovers enterprise and mining e-waste
and rebuilds it into refurbished PCs for veterans, students, and underserved
families.

These cards were pulled from mining service with legacy-only or mismatched
firmware that kept them off modern UEFI-only boards and, in several cases, kept
them from POSTing at all.  Every file here represents a card brought back to a
shippable state — UEFI-capable, correctly matched to its memory, and validated
under sustained load before it goes into a build.

---

## What's here

| Card | PCB / Model | Silicon | Memory variants | Status |
|------|-------------|---------|-----------------|--------|
| [AMD RX 580 8GB reference](amd-rx580-reference-c940/) | 109-C94037-00_02 (C940) | Polaris 20/10, `67DF` | **Micron** and **Samsung** | GOP-injected, UEFI, validated 99th pct |
| [XFX RX 580 GTS 8GB](xfx-rx580-gts/) | XFX-113-58085 | Polaris 20, `67DF` | Samsung | Dual-BIOS recovery + tune |
| [MSI RX 470 Miner 4GB](msi-rx470-miner-4g-msv341/) | MS-V341 ver 2.1 | Polaris 10, `67DF` | Samsung | Identification + gaming-BIOS map + tune |

Each card folder carries its own release notes — identification, exactly what
was done to the firmware, how to flash it, and how it was validated.

---

## The one rule that matters: match the memory vendor

Every failure in this collection traces back to a single mistake — flashing a
BIOS strapped for one memory vendor onto a card whose physical chips are
another.  A Samsung-strapped BIOS on physically Micron memory (or the reverse)
produces artifacting, instability, or an outright Code 43 / no-POST.  One of the
reference 580s in this repo sat dead for eighteen months for exactly this
reason, and came back the instant it received the image matched to its real
chips.

**GPU-Z reports the memory vendor the BIOS *claims*, not the chips that are
physically on the board.**  If a card has the wrong BIOS on it, GPU-Z will
confirm the wrong vendor.  When in doubt, read the die markings directly:

- **Samsung** — `K4G80325FB` (8 Gb GDDR5)
- **Micron** — `MT51J256M32HF`
- **Hynix** — `H5GQ8H24MJR`

Flash the folder that matches the chips, not the sticker and not GPU-Z.

---

## Flashing, in brief

Full procedure, the amdvbflash version matrix, CH341A hardware recovery, and the
Code 43 fix are in [`docs/flashing-guide.md`](docs/flashing-guide.md).  The short
version, run from an Administrator command prompt in the amdvbflash 3.15 folder,
with the system booted on integrated graphics so the target card is free:

```
amdvbflash -i                       # confirm the card is seen
amdvbflash -s 0 backup.rom          # back up what's on it now
amdvbflash -p 0 <matched_image>.rom # flash the matched image (-f if it balks)
```

Then reboot.  The card throws a one-time video-error beep and auto-retries on
the first pure-UEFI boot — that is the GOP re-enumerating, not a fault, and it
clears on the reboot.

The post-flash tuning standard — a conservative undervolt and a quiet fan curve
for cards headed into donated machines — is in
[`docs/tuning-guide.md`](docs/tuning-guide.md).

---

## Provenance and license

The **documentation, recovery methodology, and firmware modifications** in this
repository (the GOP injection process, the memory-vendor mapping, the tuning
profiles, and all written notes) are the original work of Renewable Revolt, Inc.
and are released under the [MIT License](LICENSE).

The `.rom` binaries themselves contain firmware whose copyright belongs to
**AMD** and the respective add-in-board partners, including the UEFI GOP module,
which is AMD's.  Those binaries are redistributed here for interoperability and
repair, in keeping with the long-standing practice of the TechPowerUp VGA BIOS
Collection.  The MIT grant covers Renewable Revolt's additions and
documentation; it does not, and cannot, relicense the underlying vendor
firmware.

## Disclaimer

Flashing a graphics BIOS carries a real risk of a non-POSTing card.  Always keep
a backup of the original image, and keep a CH341A SPI programmer on hand as the
hardware recovery path.  These files are provided **as-is, without warranty**;
you flash at your own risk.  Renewable Revolt uses them on its own refurbishment
line and shares them in that spirit.
