# Parts, cabling and the SAS gotchas

The controller is a **Dell PERC H200i** (confirmed) and the drives are **2.5" SAS**. That
combination is fine — but it changes *which* fanout cable you want, and it decides whether you need
the SFF-8482→SATA adapter at all. Read §3 before ordering.

---

## 1. Bill of materials

| Item | Qty | Detail |
|---|---|---|
| **Controller** | 1 | Dell PERC H200i — already owned. See §2. |
| **Dell 2.5" SFF carrier** | 4 | **G176J (0G176J)** or **8FKXC (08FKXC / NTPP3)**, the 13G number; clones are equivalent. The **3.0"-wide-bay** carrier covering **R610 / R710 / R620 / R720 / R630 / R730** (and T-series). Accepts 2.5" × 15 mm drives. |
| **Fanout cable** | 1 | **Route A:** SFF-8087 → 4 × **SFF-8482**. **Route B:** SFF-8087 → 4 × **SATA** (7-pin). See §3 — a real choice, not a formality. |
| **SFF-8482 SAS 29-pin → SATA 22-pin adapter** | 4 | Route B only. The item linked for this project. **180° (straight) variant, not 90°** — in a carrier the connector sits between the plastic wraps at the rear corners. **Strongly prefer a version with an integral shielded data cable** (gotcha 1). |
| **SATA data cable, 7-pin, shielded** | 4 | Route B only. Short (300–400 mm reaches the HBA from the top bay); flat/slim helps in the plenum. |
| **SATA power, 3.3 V-free** | 4 | Both routes need this. **Molex→SATA** (Molex has no 3.3 V rail) or a **SATA extension with the 3.3 V wire cut** (gotcha 2). |
| **M4 × 8–10 mm screws + heat-set inserts** | 4–8 | The module's lid bolts into the case's top fan/radiator frame. |
| Optional: M3 × 4 drive screws | 16 | Carriers usually ship with them. |
| Optional: soft pads (TPU/foam) | 4–8 | Under any mechanical drive — the case's top panel is thin and large, and it will resonate. |

**Not needed:** a backplane. The adapter (or the SFF-8482 fanout) replaces it.

---

## 2. The controller: Dell PERC H200i

| | |
|---|---|
| Silicon | LSI **SAS2008** — same chip as the LSI 9211-8i / Dell H310 / PERC H200 |
| Form factor | **Half-height, half-length PCIe 2.0 x8 card** (Dell's own H200 datasheet lists the *Integrated* model as a "Half-Height, Half-Length PCI Adapter", not a proprietary mezzanine board) |
| Ports | **2 × SFF-8087** internal Mini-SAS = 8 lanes at 6 Gb/s |
| Ships as | Dell IR firmware, intended for the R610/R710 integrated storage slot |

**Two things follow from that.**

1. **It will physically go into a normal PCIe slot** — indeed that is how the community flashes it:
   *"Place the H200I into a regular PCIe slot... move it out of the integrated slot and into one of
   the generic PCIe slots."* So it can live in the LANCOOL 207 build.
2. **Flash it to IT mode.** Dell IR firmware is RAID-only; for ZFS / unRAID / JBOD passthrough you
   want LSI 9211-8i IT firmware (`2118it.bin`). The documented sequence for exactly this card:
   record the SAS address, `sas2flsh -o -e 6` to erase, flash `h200i.rom` + `h200i.fw`, restore the
   SAS address, then flash `2118p7.bin` and finally `mptsas2.rom` + `2118it.bin`; verify the
   firmware product ID reads `0x2213` (IT).

**Honest caveat.** The DOS flasher (`sas2flsh`) is known to fail on non-Dell hardware with
`ERROR: Failed to initialize PAL` — which is why that guide flashes inside an R710. If you hit it:
flash from **UEFI** with `sas2flash.efi`, or do the first flash in the server and move the card
afterwards (an IT-mode card is portable). If it still refuses to enumerate in the desktop, the
drop-in alternatives on the same 2 × SFF-8087 cabling are a **PERC H200** (the PCIe "Adapter"
version), a **Dell H310**, or an **LSI 9211-8i** — all SAS2008, all the same cables.

---

## 3. Which fanout cable — and whether you need the adapters at all

The drives are SAS, so they speak SAS to the HBA. The adapter's only job is to *re-present the plug*
in a form that fits where the drive is going.

### Route A — bare drives, no adapters (cleanest; recommended for v1)

```
PERC H200i ── SFF-8087 → 4 × SFF-8482 breakout ──► each SFF-8482 plug straight onto a drive
                                                    (its power tail goes to the PSU, 3.3 V-free)
```

* **No adapters and no extra connection** — which sidesteps gotcha 1 entirely: the non-medium-error
  problem was an adapter **plus** a normal SATA cable, so removing both removes the problem.
* Cheaper, fewer parts, fewer things to fail.
* **But:** the SFF-8482 plug is a chunky right-angle body (~30 mm wide) with a power pigtail, so it
  needs clear space directly behind the drive's connector. Fine for a bare drive in the printed
  cradle; **not** fine inside a Dell carrier, where the plastic rear corner wraps and the latch
  occupy exactly that space.

### Route B — drives in carriers, with adapters (your plan)

```
PERC H200i ── SFF-8087 → 4 × SATA fanout ──► adapter's SATA side, held tight on the carrier
                                             + 4 × SATA power (3.3 V-free) to the adapter
```

* Required if the drives stay in **Dell carriers**: the carrier's rear opening only clears the
  drive's own connector, so a slim adapter is the only thing that fits.
* Costs the extra connection → gotcha 1 applies; buy the **integral-cable** adapter if available, or
  use a short shielded cable and anchor it in the printed saddle (§5).
* Keeps the drives interchangeable between your R630/R720/R730 and this module — probably the real
  reason to want carriers.

Both routes are electrically identical to the HBA (SAS2008 + SAS drives; the SATA-form connector
carries the SAS primary channel — the same hybrid-controller arrangement as the write-up in §4,
where that chipset drives SAS HDDs through SATA-socket ports).

---

## 4. The two gotchas

### Gotcha 1 — SAS non-medium errors from adapter + ordinary SATA cable

On a SAS2008-in-IT-mode homelab NAS: a **bare PCB SFF-8482→SATA adapter plus an ordinary SATA data
cable produced a stream of SAS "non-medium errors"**. Cause, per that write-up: ordinary SATA cables
lack sufficient shielding, *and* the adapter adds a second physical connection that degrades the
signal. Switching to an **adapter with an integral, SAS-grade shielded cable** eliminated the errors.

**Design consequence:** Route B needs room for a real cable and a place to anchor it. The module's
rear plenum allows **25 mm of adapter + 20 mm of bend** per connector, and the saddle (§5) ties the
cable down so it cannot flap on a dangling adapter.

### Gotcha 2 — the 3.3 V / PWDIS no-spin-up trap

Since SAS-3 / SATA-3.3, **pin 3 of the 15-pin power connector is no longer +3.3 V — it is a Power
Disable (PWDIS) signal.** Feed it 3.3 V from a normal SATA power lead and a compliant drive reads
that as a hard power-down: it loops in initialisation and **never spins up**, looking DOA. The same
source RMA'd two drives before diagnosing it.

Fixes, cheapest first:

1. **Molex → SATA power adapter.** Molex has no 3.3 V rail, so pin 3 is simply absent.
2. **SATA extension with the 3.3 V wire cut** — the wire nearest the L-shaped side, orange by
   convention (grey on some cables). Cut flush at both ends, insulate the plug end (it is live).
3. **Tape over pins 1–3** on the drive's power connector.
4. Cut the 3.3 V line on the PSU pigtail — works, permanent.

Same source, same reassurance: **no SATA or SAS drive has ever used 3.3 V** — which is why
Molex→SATA power has always been fine. Removing it costs nothing. **Applies to both routes:** a
SFF-8482 breakout's power tails are SATA power connectors too.

---

## 5. Holding the adapter tight to the carrier (your requirement)

You want the adapter mounted *tight to the caddy*, not dangling on the drive's connector. That is
the right instinct and it is a printed part, not a cable-tie job:

* **A per-slot rear saddle** at the end of each carrier pocket — a shallow pocket that the adapter
  body sits in, sized to the adapter's own envelope (`ADAPTER_W/L/H`), so the drive + carrier +
  adapter becomes **one rigid cartridge** that lifts in and out as a unit.
* **Two lips, no screws.** The saddle's lips retain the adapter laterally and vertically while
  letting the SFF-8482 plug float the last ~1 mm onto the drive's connector. The adapter must never
  carry the drive's weight, and the drive must never hang on the adapter.
* **A cable anchor** just behind the saddle (a slot for a zip tie or a printed clamp) so the data
  and power leads take the strain, not the connector. This is the direct mitigation for gotcha 1.
* **Service access:** the saddle must be reachable with the lid off — it is, per the service model
  in the [feasibility report](feasibility-lancool-207-top-bay.md#6-service-model--why-this-cannot-be-hot-swap).

The saddle is why the model needs the adapter's **actual** body dimensions and **exit direction**
before the pocket can be drawn — item 7 on the measurements list.

---

## 6. Cabling plan

```
              ┌──────────── module in the top bay ────────────┐
 front ◄──────┤ front row : 2 × carrier + adapter (saddle)   ├──────► rear
 of case      │ plenum 16 mm : 2 data + 2 power, anchored    │
              │ rear row  : 2 × carrier + adapter (saddle)   │
              │ plenum 16 mm : 2 data + 2 power, anchored    │
              └──────────────────────────────────────────────┘
                              │  cables drop down the back of the tray
                              ▼
        PERC H200i (PCIe slot, rear)      ◄── SFF-8087 → 4× fanout
        PSU (front-bottom, side-facing)   ◄── SATA power, 3.3 V-free
```

Two things work in your favour: the **PSU is front-mounted with side-facing connectors** (short
power run to the front plenum) and the **HBA sits in a rear PCIe slot** (short, straight data run).
Each drive gets its own plenum bay, so a drive can be unplugged without dragging its neighbours'
cables.

---

## 7. Still open

| # | Question | Why it matters |
|---|---|---|
| 1 | **Adapter variant**: 90° or 180°, PCB-only or integral cable? | Sets the saddle pocket and the plenum depth. **I could not read the photo you attached — the vision service was saturated for this whole session.** Tell me which one it is, or re-send when it recovers. |
| 2 | **Drive thickness**: 15 mm or 9.5 mm? | Sets the slot depth and the lid height. SAS enterprise drives are usually 15 mm. |
| 3 | **Route A or Route B for v1?** | Route A needs no adapters at all and dodges gotcha 1; Route B keeps your carriers. |

---

## 8. Sources

| # | Source | Used for |
|---|---|---|
| 1 | Dell PERC H200 controller datasheet (form-factor table) — https://www.manualsdir.com/manuals/621732-dell-poweredge-raid-controller-h200.html?page=19 | *Integrated* H200 is a "Half-Height, Half-Length PCI Adapter"; SAS2008 / LSI IOC |
| 2 | PERC H200 Integrated stock listing — https://www.itcreations.com/product/40691 | 6 Gb/s, **PCIe 2.0 x8**, "(2) TWO INTERNAL MINI SAS **SFF-8087**" |
| 3 | PERC H200i → IT-mode flash guide — https://bradleyclayton.io/posts/homelab/perc_h200i_dell_r710_it_mode/ | the flash sequence, and that the card goes in a *generic PCIe slot* |
| 4 | r/homelab — https://www.reddit.com/r/homelab/comments/7p2lzq/how_to_keep_crossflashed_perc_h200_in_dell_r710/ | the `Failed to initialize PAL` failure when flashing outside a Dell |
| 5 | SAS-to-SATA gotchas — https://datahamster.com/2026/06/09/sas-to-sata-gotchas-including-the-3-3v-issue-and-the-best-fix-for-it/ | gotcha 1 (non-medium errors ⇒ integral shielded cable) and gotcha 2 (3.3 V / PWDIS) |
| 6 | Dell 2.5" carrier listings (G176J, 8FKXC) — e.g. https://www.disctech.com/Dell-8FKXC-2-5-SAS-Hard-Drive-Tray-Caddy | 3.0"-wide-bay carrier, 15 mm drive capacity, 11G–13G compatibility |
| 7 | Dell community + PowerEdge R730 manual, via search — https://www.dell.com/community/en/conversations/poweredge-hardware-general/dell-25-mkc9m-drive-carrier-documentation/6a3445996e63161cd1f4c9a4 | drive back **flush** with the carrier's back; plastic wraps only the rear corners ⇒ the drive's SFF-8482 connector stays exposed |
| 8 | AliExpress item 1005009141223153 (title recovered via a text proxy) | it is an SFF-8482 SAS → SATA converter, 90° / 180° variants |
