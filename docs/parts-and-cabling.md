# Parts, cabling and the SAS gotchas

You are moving 4 × 2.5" SAS drives off a PowerEdge and onto an in-case HBA, inside a printed
module. Three components decide whether it works: the **carrier**, the **SAS→SATA adapter**, and
the **power feed**. Two of them have documented failure modes that look exactly like dead hardware.

---

## 1. Bill of materials

| Item | Qty | Detail |
|---|---|---|
| **Dell 2.5" SFF carrier** | 4 | **G176J (0G176J)** — or **8FKXC (08FKXC / NTPP3)**, the 13G number; clones are equivalent. Both are the **3.0"-wide-bay** carrier covering **R610 / R710 / R620 / R720 / R630 / R730** (and T-series). Accepts 2.5" × 15 mm drives. |
| **SFF-8482 SAS 29-pin → SATA 22-pin adapter** | 4 | The item linked for this project. **Pick the 180° (straight) variant, not 90°** — in a carrier the connector sits between plastic wraps at the rear corners, and a sideways exit has nowhere to go. **Strongly prefer a version with an integral shielded data cable** (gotcha 1). |
| **SATA data cable, 7-pin, shielded** | 4 | Short: 300–400 mm reaches the HBA from the top bay. Flat/slim helps in the plenum. |
| **SATA power, 3.3 V-free** | 4 | Either **Molex→SATA** (Molex has no 3.3 V rail) or a **SATA extension with the 3.3 V wire cut** (gotcha 2). |
| **HBA breakout** | 1 | Depends on the HBA: **SFF-8087 → 4× SATA** or **SFF-8643 → 4× SATA** fanout, or the HBA's own SATA-style ports. *This is the one part I cannot pick for you — see §4.* |
| **M4 × 8–10 mm screws + heat-set inserts** | 4–8 | The module's lid bolts into the case's top fan/radiator frame. |
| Optional: M3 × 4 drive screws | 16 | Carriers usually ship with them. |
| Optional: soft pads (TPU/foam) | 4–8 | Under any mechanical drive, to stop vibration coupling into the case's large top panel. |

**Not needed:** a backplane. The adapters replace it. Also not needed: a 2.5" caddy screw kit
beyond what the carriers include.

---

## 2. The two gotchas

### Gotcha 1 — SAS non-medium errors from the adapter + a normal SATA cable

Documented on a SAS2008-in-IT-mode homelab NAS (the same architecture you are building): a
**bare PCB SFF-8482→SATA adapter plus an ordinary SATA data cable produced a stream of SAS
"non-medium errors"**. Cause, per that write-up: standard SATA data cables do not have
sufficient shielding, *and* the adapter adds a second physical connection that degrades the
signal. Swapping to an **adapter with an integral, SAS-grade shielded cable** eliminated the
errors entirely.

**Design consequence:** leave room for a real cable, not just a plug. The feasibility model allows
**25 mm of adapter + 20 mm of bend** per connector in the plenum. If you use the small PCB adapter,
budget for a *short, shielded* cable and anchor it — a cable flopping on a dangling adapter is
exactly the failure mode above.

### Gotcha 2 — the 3.3 V / PWDIS no-spin-up trap

From SAS-3 / SATA-3.3 onwards, **pin 3 of the 15-pin power connector stopped being +3.3 V and
became a Power Disable (PWDIS) signal**. Feed it 3.3 V from a normal SATA power lead and a
compliant drive reads that as a hard power-down command: it sits in an initialisation loop and
**never spins up**. The drive looks DOA — the same author sent two drives back before diagnosing it.

Fixes, cheapest first:

1. **Molex → SATA power adapter.** Molex has no 3.3 V rail, so pin 3 is simply absent.
2. **SATA extension with the 3.3 V wire cut.** Identify the 3.3 V wire — the one nearest the
   L-shaped side of the connector, industry colour orange, but grey on some cables. Cut it flush
   at both ends and insulate the plug end (it is live). Non-destructive to the PSU.
3. **Tape over pins 1–3** on the drive's power connector (many connectors bridge 1–3).
4. Cut the 3.3 V line on the PSU pigtail — works, but permanent.

**Reassurance from the same source:** no SATA or SAS drive has ever actually used 3.3 V — which is
why Molex→SATA power has always worked fine. Removing it costs nothing.

**Design consequence:** whichever fix you choose adds an inline connector or a modified cable
behind each drive. Both are small, but they need to live in the rear plenum with the adapters, and
you must be able to **unplug and remove a drive without disturbing its neighbours** — which the
per-slot plenum and the front/rear split already give you.

---

## 3. Cabling plan

```
                       ┌──────── module in the top bay ────────┐
   case front ◄────────┤ [front row 2× drive + adapter + 20 mm]├────────► case rear
                       │ [ plenum 16 mm : 2× data + 2× power   ]│
                       │ [rear row  2× drive + adapter + 20 mm ]│
                       │ [ plenum 16 mm : 2× data + 2× power   ]│
                       └───────────────────────────────────────┘
                                        │  cables drop down the back of the tray
                                        ▼
                    HBA (PCIe, rear of the case)  ◄── 4× SATA data
                    PSU (front-bottom, side-facing connectors) ◄── SATA power
```

Two things work in your favour in this case:

* the **PSU is front-mounted with side-facing connectors**, so the SATA power run to the front
  plenum is short;
* the **HBA sits in a PCIe slot at the rear**, so the data run drops straight down to it.

Per-slot separation matters more than neatness here: each drive gets **its own data and power pair
in its own plenum bay**, so a drive can be unplugged without dragging its neighbours' cables.

---

## 4. Open questions that change the CAD

| # | Question | Why it changes the design |
|---|---|---|
| 1 | **Which HBA (model + port type)?** SFF-8087 / SFF-8643 / individual SATA sockets? | Decides the breakout cable and its bend radius, i.e. the plenum width. A SFF-8087 fanout with 4 × SATA ends is a fat bundle right at the enclosure's rear. |
| 2 | **Are the drives 15 mm or 9.5 mm thick?** | Sets the slot depth and the whole lid height (`DRIVE_T`). SAS enterprise 10K/15K drives are usually 15 mm. |
| 3 | **Which adapter variant** — the 90° or the 180°, and does it have an integral cable? | 180° straight is the safe carrier choice; the integral-cable type is the safe signal choice. |
| 4 | **Do you need carriers at all in v1?** | v1 with bare drives is 60 mm shorter, 13 mm narrower and 12 mm lower, and it validates the adapter fit before you spend on carriers. |

Answer 1 and 2 and the model can be finalised; 3 and 4 only change parameters.
