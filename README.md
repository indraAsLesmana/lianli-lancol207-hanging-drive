# lianli-lancol207-hanging-drive

A 3D-printed module that hangs **4 × 2.5" SAS/SATA drives** in the **empty top radiator bay**
of a **Lian Li LANCOOL 207**, feeding a SAS HBA in the same case.

> **Status: research + feasibility done, CAD not started.** The geometry is designed around
> sourced case dimensions and sourced drive/carrier/connector standards; four case dimensions
> and two part dimensions still need to be measured before the parametric model is finalised.
> See [`docs/measurements-to-take.md`](docs/measurements-to-take.md).

Layout diagram (to scale, both variants): [`docs/layout-lancool-207-top-bay.html`](docs/layout-lancool-207-top-bay.html)

---

## The idea

The LANCOOL 207 ships with **no top fan and no top radiator** — Lian Li only supports
3 × 120 mm / 2 × 140 mm fans or a 240/280/360 radiator up there, and it is the case's only
radiator position. If you are not water-cooling, that is ~360 × 140 × 60–70 mm of dead space
sitting in the case's exhaust stream. This project puts four 2.5" drives in it instead of a
radiator: flat, in a 2 × 2 grid, fed from the HBA, with the heat convecting straight out through
the case's mesh top.

## Verdict — feasible, with real margin

| Axis | v1 (bare drives) | v2 (Dell carriers) | Bay provides | v1 margin |
|---|---|---|---|---|
| Front→back | **240 mm** | 299 mm | ~360 mm of 120 mm fan pitch | **+120 mm** |
| Downward | **21 mm** | 33.5 mm | 60–70 mm (radiator + fan budget) | **+39 mm** |
| Across | **147 mm** | 160 mm | ~190 mm free above the board | **+43 mm** |

Both variants fit. **v2 (carriers) costs 60 mm of length and 13 mm of width and must route its
cables over the rear row**, which is why v1 is what gets built first.

## Two build variants, one parametric script

| | v1 — bare drives *(build this first)* | v2 — Dell 2.5" carriers |
|---|---|---|
| What slides in | the drive, with the SFF-8482→SATA adapter plugged on | the drive **inside a Dell G176J / 8FKXC carrier** |
| Envelope | 147 × 240 × 21 mm | 160 × 299 × 33.5 mm |
| Cable plenums | one at each end (16 mm) — front row faces front, rear row faces rear | one at the rear (25 mm) + a cable attic over the rear row |
| Slot width | 70.65 mm | 76.2 mm (carrier width, 3.0" bay) |
| Why | proves the concept and the adapter fit, uses 2/3 of the bay | lets a drive move between this module and a PowerEdge server |

The slots stay **76.2 mm wide in both variants**, so one enclosure accepts bare drives now and
carriers later with a printed shim — the difference is only in length and lid height, both of
which are parameters.

## How it mounts

The case's top panel is a **snap-off part** and the top fan/radiator frame is **fixed to the
chassis** (KitGuru complains there is no removable radiator bracket — which is exactly what we
want here). So the enclosure's lid is a **mounting plate with obround holes** on both the
105 × 105 mm (120 mm fan) and 124.5 × 124.5 mm (140 mm fan) patterns; it bolts up into the
frame from above, the same motion as fitting a radiator, using 4–8 M4 screws into heat-set
inserts. Slotted holes absorb the fact that Lian Li publishes no panel drawing.

## Service model — read this before buying carriers

The module is **not hot-swap, and cannot be**: the bay is only ~60–70 mm tall, so drives must lie
flat, and any 2-deep arrangement is blocked by its own front row. Service is:

1. top panel off (snap-off),
2. 4 screws out,
3. lower the module ~35 mm into the case interior (there is room),
4. open the lid, swap the carrier/drive.

That is a ~1 minute cold swap. If you need true hot-swap, this bay is the wrong place for drives.

## Parts

Controller: **Dell PERC H200i** (owned) — a half-height PCIe 2.0 x8 card with 2 × SFF-8087, on the
LSI SAS2008. It fits a normal PCIe slot; flash it to IT firmware.

Carrier: **Dell G176J (0G176J) or 8FKXC (08FKXC / NTPP3)** 2.5" SFF carrier — the 3.0"-wide bay
carrier that covers R610 / R710 / R620 / R720 / R630 / R730 (and the T-series).

Cable: **one SFF-8087 fanout**, but which one is a real decision — **SFF-8087 → 4 × SFF-8482** if
the drives go in bare (no adapters needed at all), or **SFF-8087 → 4 × SATA** if they stay in
carriers and use the SFF-8482→SATA adapters. Adapter: prefer the variant with an **integral
shielded cable**. Full BOM, part numbers, the adapter saddle that keeps the adapter tight to the
carrier, and the two electrical gotchas (SAS non-medium errors; the 3.3 V / PWDIS spin-up trap) are
in [`docs/parts-and-cabling.md`](docs/parts-and-cabling.md).

## Repo layout

```
README.md                              this file
docs/feasibility-lancool-207-top-bay.md  full research report: bay geometry, fit math, sources
docs/parts-and-cabling.md                BOM, adapter/HBA/carrier choices, cabling, gotchas
docs/measurements-to-take.md             the 4 case + 2 part measurements that gate the CAD
docs/layout-lancool-207-top-bay.html     to-scale top view + front section, both variants
```

CAD will land as a single parametric FreeCAD script in the style of
[3D-models](https://github.com/indraAsLesmana/3D-models): one parameter block at the top,
`freecadcmd` in, STEP + STL + a verification report out, no number hand-typed twice.

## Sources

Lian Li product page and OC3D / GamersNexus / KitGuru / TechPowerUp reviews for the case; the
manufacturer part listings for the carrier; the SAS-to-SATA gotchas write-up for the electrical
behaviour. Every claim is traced in
[`docs/feasibility-lancool-207-top-bay.md`](docs/feasibility-lancool-207-top-bay.md#8-sources).

## License

MIT
