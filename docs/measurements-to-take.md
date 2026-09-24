# Measurements to take before the CAD is finalised

Six of these gate the model; the last two only change parameters. Items 1–4 need the case with its
top panel off and a ruler or digital caliper — about 10 minutes. Items 5–6 need the carrier and
adapter in hand (buy one of each first if you have not yet).

Fill the answers in as a table and the parameter block in
[`feasibility-lancool-207-top-bay.md` §7](feasibility-lancool-207-top-bay.md#7-parameters-for-the-model)
can be completed without guesswork.

---

## Case — top bay

| # | Measure | How | Pass condition |
|---|---|---|---|
| 1 | **Usable flat width under the top panel**, at the front of the bay, between the inner faces of any folded side lips | Tape across the bay, top panel off | **≥ 168 mm** for v2 (carriers), **≥ 155 mm** for v1. Expected 180–195 mm. |
| 2 | **Top mount holes** — pick the front-most of the three 120 mm positions: (a) round Ø4.5 or obround? (b) if obround, slot length and which way it runs, (c) hole-to-hole = 105 mm? (d) distance from the group's centre to the **front panel inner face**, (e) distance to the **inner side wall** | Caliper | Tells us where the plate's slotted holes go. |
| 3 | **Vertical clearance** from the top frame's mounting face straight down, at several points along the front 300 mm — to the board's top edge, EPS connectors, VRM heatsink, RAM tops, CPU cooler | Steel rule held vertical | **≥ 40 mm across the whole footprint.** A 360 radiator + fan is claimed to need up to 70 mm, so this should be comfortable. |
| 4 | **Top-frame lip / rib depth** — how far the top panel's folded reinforcement hangs below the frame | Caliper | Sets the standoff between the module lid and the frame. |
| 5 | **Bay length** — from the front-most usable point (where a fan/rad screw can actually be driven) to the rearmost usable hole | Tape | **≥ 310 mm** for v2 (298.8 mm model), **≥ 255 mm** for v1. |

## Parts

| # | Measure | How | Why |
|---|---|---|---|
| 6 | **Dell carrier**: outer L and H (W is known = 76.2 mm), plus the **rear opening's clear width/height** and how far the plastic corner wraps intrude | Caliper, one carrier | Confirms `CARRIER_L`/`CARRIER_H` (currently estimates: 122 / 20 mm) and proves the SFF-8482 plug clears the wraps. |
| 7 | **SAS→SATA adapter**: rearward protrusion past the drive's connector face, body width/height, **which way the cable leaves it**, and the plug+bend radius you will actually use | Caliper + a cable bend | Sets the saddle pocket and the plenum depth (currently 25 mm adapter + 20 mm bend). |
| 8 | **Drive thickness**: 7 / 9.5 / 15 mm | Caliper | Sets `DRIVE_T`. SAS enterprise drives are usually 15 mm. |

---

## Also tell me

* ~~HBA model and port type~~ — **resolved: Dell PERC H200i, 2 × SFF-8087, PCIe 2.0 x8.** See
  [`parts-and-cabling.md §2`](parts-and-cabling.md#2-the-controller-dell-perc-h200i) — the short
  version is that it fits a normal PCIe slot, but you want IT firmware on it.
* **Adapter variant**: 90° or 180°, PCB-only or integral cable, and which direction the cable
  leaves it — this sets the saddle pocket and the plenum (item 7). **I could not read the photo you
  attached: the vision service was saturated for this entire session.**
* **Route A or Route B for v1** — bare drives with a SFF-8087→4×SFF-8482 breakout (no adapters at
  all, dodges the non-medium-error gotcha), or drives in carriers with the adapters and a
  SFF-8087→4×SATA fanout.
* **Whether v1 (bare drives) is acceptable as the first build**, or whether the model should go
  straight to the carrier-ready envelope.

---

## Why so much measuring — the state of the public data

Lian Li publishes **no panel drawing** for this case, the **official manual is bot-walled** on
every mirror, and the review pages that carry interior measurements (TechPowerUp "A Closer Look")
are WAF-blocked and not archived. Seven Lian Li marketing graphics were downloaded and inspected —
none carries a dimension callout. Everything *else* in this project is sourced: the bay's 360 mm of
120 mm pitch, the 60–70 mm radiator+fan thickness budget, the case dimensions, the drive
dimensions, the carrier's 76.2 mm width, and both fan screw patterns.

The measurements above are the difference between a sourced envelope and a fabricated one.
