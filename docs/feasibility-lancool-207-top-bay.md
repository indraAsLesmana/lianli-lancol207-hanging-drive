# Feasibility report — 4 × 2.5" SAS/SATA module in the LANCOOL 207 top radiator bay

**Question:** can a 3D-printed module hold **4 × 2.5" SAS drives, laid flat, arranged 2|2
(2 across × 2 deep)**, hanging in the LANCOOL 207's *unused* top radiator bay, cabled to an
in-case SAS HBA — with the drives either bare or inside **Dell 2.5" hot-swap carriers**?

**Verdict: yes, both variants fit, with margin on every axis.** The binding constraints are not
the obvious ones: v1 (bare drives) has 120 mm of length slack, 39 mm of height slack and 43 mm of
width slack. v2 (drives in Dell carriers) sees the length margin fall to **61 mm** and adds a
cable-routing problem over its own rear row — so v2 is feasible but should be built second.

The one thing that is *not* possible in this bay is **hot-swap**, and that follows from the
geometry, not from the design: see [§6](#6-service-model-and-why-hot-swap-is-impossible).

Research date: 2026-09-25. Every number is traced to a source or to arithmetic shown here.

---

## 1. Confirmed case facts

### 1.1 Manufacturer spec (LIAN LI product page, specification block)

| Item | Value |
|---|---|
| Dimensions (D × W × H) | **455.6 × 219 × 456 mm** (45.5 L) |
| Motherboard | ATX (max width 244 mm) / μATX / ITX |
| **Top fans** | **3 × 120 mm or 2 × 140 mm (optional — none pre-installed)** |
| Front fans | 2 × 140 mm ARGB, pre-installed |
| Bottom (above shroud) | 2 × 120 mm PWM, pre-installed |
| Rear fan | 1 × 120 mm (optional) |
| **Top radiator** | **360 / 280 / 240 mm — the only radiator position Lian Li claims** |
| CPU cooler height | 180 mm (Lian Li) / **167 mm** (TechPowerUp, GamersNexus) |
| GPU length | 375 mm (Lian Li) / 410 mm (GamersNexus, TechPowerUp) |
| PSU | ATX, **≤ 160 mm deep** (reviews recommend ≤ 150 mm) |
| Storage | **2 × 3.5" *or* 2.5"** — in the floor, vibration-damped |

Lian Li, verbatim: *"The top of the case supports a 360mm push-pull radiator configuration
thanks to the offset motherboard tray design."*

### 1.2 Measured / review-verified facts

| Fact | Source |
|---|---|
| *"This case supports 280 mm radiator/fan combinations **up to 60 mm thick**, and 360 mm radiator/fan combinations **up to 70 mm thick** without interfering with other hardware."* | OC3D |
| With a 360 mm AIO fitted: *"lots of room to reach any headers at the top edge of the board and the rear fan mounting position remains completely accessible."* | TechPowerUp, Assembly |
| Motherboard tray **dropped and recessed** specifically for *"improved top radiator and 140 mm fan compatibility"* | KitGuru |
| Tray offset gives **≈ 10 mm of clearance behind the motherboard tray** | GamersNexus |
| Top panel is **removable** — snaps off on heavy-duty studs, folded steel reinforcement | GamersNexus |
| **No removable top radiator/fan bracket** — the top mount frame is fixed to the chassis | KitGuru |
| **No radiator support anywhere except the top** (front is N/A per Lian Li) | GamersNexus |
| Lian Li supplies **16 × 6#32 × 28 mm** long screws for fan swaps | LIAN LI |
| Cable hooks in the "top trench" are mediocre — a top cable trench exists | TechPowerUp |
| Floor storage: SATA cables *"have to be bent at an uncomfortable angle"* | GamersNexus |

### 1.3 What could **not** be verified

* **TechPowerUp pages 3 & 4 ("A Closer Look")** — the pages carrying interior measurements — are
  WAF-blocked live (403 to curl, to self-hosted Firecrawl, and to a text proxy) and are **absent
  from the Wayback Machine** (only 1, 2, 6, 7, 8, 9, 10 are archived).
* **The official 20-page LANCOOL 207 manual** is bot-walled on every mirror tried
  (manuals.plus Cloudflare interstitial, manua.ls, manualzz, manuals.co.uk). The one mirror that
  rendered (manua.ls) contains assembly steps only, no clearance data.
* **Lian Li's marketing graphics carry no dimension callouts.** Seven were downloaded and
  inspected; the only numbers on any of them are a temperature/noise table.

So the **envelope** is multiply sourced; the **exact hole positions under the top panel are not**.
Section 7 of [`measurements-to-take.md`](measurements-to-take.md) closes that gap.

---

## 2. The bay, as far as it can be derived

```
TOP RADIATOR BAY — what exists when no radiator is fitted
                    (front of case → is left, glass side is toward the viewer)

  case depth 455.6 mm
 |<-------------------------------------------------------------------->|
 |  front panel |                                                       |
 |   + 2x140    |        TOP MOUNT FRAME: 3 x 120 mm  or  2 x 140 mm     |
 |   front fans |        (fixed stamped-steel frame, no removable tray)  |
 |              |        ~360 mm of 120 mm fan pitch  (or 280 for 2x140)|
 |<-- ~20-40? -->|<---------------------- 360 --------------------->|     |
                                     |
        VERTICAL, from the mount face DOWN to the first obstruction:
        •  280 mm rad + fan ≤ 60 mm      (OC3D)
        •  360 mm rad + fan ≤ 70 mm      (OC3D)
        •  360 push-pull (25+30+25 = 80 mm) claimed by Lian Li
        •  After fitting a 360 AIO the top-edge headers stayed reachable (TPU)

        LATERAL, across the 219 mm case width:
        219 − 4 (glass) − ~1 (panel) − ~10 (behind-tray cable space)
            − 1.2 (tray) − 6.5 (standoffs) ≈ 196 mm free above the motherboard
        (derived, NOT measured — GN gives only the "≈10 mm behind tray" figure)
```

Fan geometry you can rely on without measuring:

| Fan | Frame | Screw hole pattern | Hole Ø |
|---|---|---|---|
| 120 mm | 120 × 120 × 25 | **105 × 105 mm** square | 4.5 mm |
| 140 mm | 140 × 140 × 25 | **124.5 × 124.5 mm** square | 4.5 mm |

The case's top frame accepts either size, so its holes are almost certainly **obround slots** —
good news for a printed mounting plate, because slots forgive several millimetres of position error.

---

## 3. What the brief requires

**2.5" drive, SFF nominal (SFF-8201 / EIA-720):** 69.85 × 100.45 mm footprint, height **9.5 mm**
(standard HDD), **7 mm** (slim) or **15 mm** (SAS enterprise HDD — assume 15 mm for SAS).

**Dell 2.5" hot-swap carrier (G176J / 8FKXC):** holds one 2.5" × 15 mm drive. **Width is sourced
at 76.2 mm** (the carrier is for the "**3.0" wide** hot-swap bay"). Length and height are
**estimates** — ~122 mm and ~20 mm — and must be calipered when the carrier arrives.
Confirmed from Dell's own documentation: *"the back of the hard drive is flush with the back of
the hard-drive carrier"* and the carrier's plastic *"wraps slightly around the rear corners"* —
i.e. **the drive's own SFF-8482 connector stays exposed at the carrier's rear**, which is what
lets the SAS→SATA adapter plug onto it.

**SFF-8482 → SATA adapter:** the PCB type is roughly 40 × 25 mm with a 20–30 mm rearward
protrusion. A straight (180°) variant is the safe choice in a carrier — a 90° variant turns the
cable sideways into the carrier's corner wraps. Either way the adapter is **held tight to the
carrier in a printed saddle** at the rear of each pocket, so the drive + carrier + adapter is one
rigid cartridge that lifts in and out as a unit (see
[parts-and-cabling.md §5](parts-and-cabling.md#5-holding-the-adapter-tight-to-the-carrier-your-requirement)).

### Why the drives cannot be arranged any other way

| Layout | Width needed | Verdict |
|---|---|---|
| 2|2 flat, long axis front-to-back *(the brief)* | 147 mm bare / 160 mm carried | **fits** (~190 mm free) |
| 2|2 flat, long axis across the case | 207 mm bare | too wide |
| 1 row of 4 flat, across the case | 279 mm bare / 305 mm carried | too wide |
| 4 standing on an edge | 100.45 mm tall | taller than the 60–70 mm bay |

The 2|2 flat layout is forced by the arithmetic, not by preference.

---

## 4. Fit check

Derived parameters: wall 2.5, centre rib 1.5, 0.4 mm per-drive clearance, 2.5 floor, 3.0 lid,
6.0 mm of wire/plug headroom above the drive plane, adapter + bend allowance 25 + 20 mm,
cable attic 8 mm.

```
v1  bare drives, one plenum at each end
    W = 2×69.85 + 2×2.5 + 1.5 + 2×0.4                       = 147.0 mm
    L = 2×100.45 + 2×2.5 + 1.5 + 2×0.4 + 2×16                = 240.2 mm
    H = 2.5 + 9.5 + 6.0 + 3.0                                =  21.0 mm

v2  Dell carriers, all connectors rear + cable attic over the rear row
    W = 2×76.2 + 2×2.5 + 1.5 + 2×0.4                         = 159.7 mm
    L = 2×122 + 2×2.5 + 1.5 + 2×0.4 + (25+20) + 2.5          = 298.8 mm
    H = 2.5 + 20 + 8 + 3.0                                   =  33.5 mm
```

| Axis | v1 | v2 | Bay provides | v1 margin | v2 margin |
|---|---|---|---|---|---|
| Front→back | 240.2 | 298.8 | ~360 mm | **+120 mm** | **+61 mm** |
| Downward | 21.0 | 33.5 | 60 mm (280-rad budget) | **+39 mm** | **+26.5 mm** |
| Across | 147.0 | 159.7 | ~190 mm | **+43 mm** | **+30 mm** |
| Screw interface | 2 × 105 mm patterns = 8 screws | same | 3 fan positions ⇒ 4 hole groups | — | — |

**Rejected v2 sub-variant:** carriers with *mirrored* rows (front row's connectors facing front,
rear row's facing rear) gives 341 mm — **95 % of the bay, only 19 mm of slack**. Too close to the
edge given that the front-most usable slot position is unmeasured. Hence the "all connectors rear
+ cable attic" arrangement at 298.8 mm.

**Load path.** 4 SSDs ≈ 180 g; 4 × 15 mm SAS drives ≈ 600–760 g; 4 carriers ≈ 725 g; printed
shell ≈ 220 g as printed. Worst case hanging mass ≈ **1.0 kg**, spread over 4–8 M4 screws on a
105 mm square, with the centre of mass ~13 mm below the mount plane — trivial shear, negligible
moment. The real risk is **overtightening a printed boss against thin stamped steel** → washers,
moderate torque, or heat-set inserts.

---

## 5. Mounting concept

The top panel snaps off and the top frame is fixed, so both options are bolt-style:

**Option A — screw from above (recommended).** The module's lid *is* the mounting plate, with
**obround holes 5.5 × 11 mm** on both the 105 × 105 and 124.5 × 124.5 patterns. Screws go down
through the frame's slots into M4 heat-set inserts in the plate, tightened from above with the
top panel off — the same motion as fitting a radiator.

**Option B — fixed shelf + slide-on body.** A thin plate bolts to the frame (4 screws) and carries
rails; the drive body slides on and clicks — the screwless idiom used in
[3D-models](https://github.com/indraAsLesmana/3D-models). More print time, less tolerance for a
wrong slot position, so it is a v2 refinement.

**Placement along the bay:** the module occupies the **front ~240 mm (v1) / ~300 mm (v2)**,
leaving the rearmost top slot (above the CPU / EPS headers) free. Vertical extraction only —
there is no horizontal way out of a top bay.

---

## 6. Service model, and why hot-swap is impossible

The bay is 60–70 mm tall, so every drive must lie **flat** (a drive standing on its 100.45 mm edge
does not fit). In any 2-deep flat arrangement, **the front row blocks the rear row** — no carrier
can slide past it, from any direction: the front is the front panel, the sides are the glass and
the tray, and above is the case's top panel. So the module is a **top-bay drawer**:

1. top panel off (snap-off),
2. 4 screws out,
3. lower the module ~35 mm into the case interior (there is room above the board),
4. open the lid, lift the carrier/drive out.

≈ 1 minute per swap, cold, no tools beyond a screwdriver. Design consequences: the lid should be
a **slide-and-click part** (as in 3D-models) so step 4 is quick, and the drive pockets must be
open enough to grip a carrier with two fingers.

---

## 7. Parameters for the model

```python
CASE_D, CASE_W, CASE_H = 455.6, 219.0, 456.0   # LIAN LI published

# --- measure before finalising (docs/measurements-to-take.md) ---
TOP_BAY_W       = None   # usable flat width under the top panel
SLOT_FROM_FRONT = None   # front-most mount hole centre, from the front panel inner face
SLOT_FROM_SIDE  = None   # front-most mount hole centre, from the inner side face
BAY_DEPTH       = None   # vertical clearance, frame face down to obstruction
CARRIER_W, CARRIER_L, CARRIER_H = 76.2, None, None   # W sourced (3.0" bay), L/H to measure
ADAPTER_W, ADAPTER_H = None, None   # adapter body, for the saddle pocket
ADAPTER_L       = None   # rearward protrusion of the SFF-8482->SATA adapter
ADAPTER_EXIT    = None   # 180 straight / 90 sideways - sets the plenum direction
SADDLE_LIP      = 1.2    # printed lip retaining the adapter against the carrier

# --- drive ---
DRIVE_W, DRIVE_L, DRIVE_T = 69.85, 100.45, 15.0   # SFF-8201 nominal; 15 mm for SAS

# --- derived, never hand-edited ---
USE_CARRIER = False           # v1 = bare, v2 = carrier
WALL, RIB, DRIVE_CLEAR = 2.5, 1.5, 0.4
FLOOR_T, LID_T, WIRE_H, ATTIC_H = 2.5, 3.0, 6.0, 8.0
PLENUM, ADAPTER_BEND = 16.0, 20.0
PATTERN_120, PATTERN_140 = 105.0, 124.5    # fan screw squares
SLOT_L, SLOT_W = 11.0, 5.5                 # obround mounting slots
```

The script must **assert** the fit rather than describe it —
`ENC_W <= TOP_BAY_W - 8`, `ENC_L <= 360`, `ENC_H + STANDOFF <= BAY_DEPTH - 5` — and fail loudly
if a measurement comes back too small, instead of silently shrinking the drives.

---

## 8. Sources

| # | Source | Used for |
|---|---|---|
| 1 | LIAN LI LANCOOL 207 product page — https://lian-li.com/product/lancool-207/ | case dimensions, top fan & radiator support, CPU/GPU/PSU limits, push-pull claim, 6#32 screws |
| 2 | TechPowerUp review — https://www.techpowerup.com/review/lian-li-lancool-207/ (pages 1 + 6 via Wayback snapshots 2025-10-27 / 2024-12-12) | spec table (167 mm CPU, 410 mm GPU, 9.25 kg, 2× internal 2.5/3.5"), header access with a 360 AIO, top cable trench |
| 3 | GamersNexus review — https://gamersnexus.net/cases/lian-li-lancool-207-airflow-case-review-cable-management-build-quality-benchmarks | panel construction, ~10 mm behind tray, offset tray, floor drive-mount SATA criticism, no front radiator support |
| 4 | KitGuru review — https://www.kitguru.net/components/cases/james-dawson/lian-li-lancool-207-case-review/ | dropped/recessed tray, no removable top radiator bracket |
| 5 | OC3D review — https://overclock3d.net/reviews/cases_cooling/lian-li-lancool-207-pc-case-review/ | **the 60 mm / 70 mm radiator-plus-fan thickness budget** |
| 6 | Dell 2.5" carrier listings (G176J, 8FKXC) — e.g. https://www.disctech.com/Dell-8FKXC-2-5-SAS-Hard-Drive-Tray-Caddy , https://www.amazon.com/clp/B0837SWX8Y | 3.0"-wide-bay carrier, 15 mm drive capacity, 11G–13G compatibility (R610/R710/R620/R720/R630/R730 + T-series) |
| 7 | Dell community + PowerEdge R730 manual, via search — https://www.dell.com/community/en/conversations/poweredge-hardware-general/dell-25-mkc9m-drive-carrier-documentation/6a3445996e63161cd1f4c9a4 | drive back **flush** with the carrier's back, plastic wraps only the rear corners ⇒ the drive's SFF-8482 connector remains exposed |
| 8 | SFF-8201 / EIA-720 | drive 69.85 × 100.45 mm, 7 / 9.5 / 15 mm heights |
| 9 | Standard 120 / 140 mm fan geometry | 105 × 105 and 124.5 × 124.5 mm screw squares, Ø4.5 |

Blocked and *not* used: TechPowerUp pages 3–4, the official manual, and Lian Li's marketing
graphics (verified to carry no dimensions). The AliExpress adapter listing was reachable only via
a text proxy (title only) — its description section could not be extracted; the adapter's
behaviour is covered from the gotchas write-up in
[`parts-and-cabling.md`](parts-and-cabling.md#2-the-two-gotchas).
