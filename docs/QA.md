# QA gate

A model is not finished until it has passed this gate and been given a verdict. The structure (budgets, a checklist,
screenshots, defects by severity, an explicit SHIP / SHIP WITH NOTES / NO-SHIP) is adapted from the `qa-review`,
`asset-optimization` references (validation checklist, polycount budgets), `lod-pipeline`, `rigging` and `cloth-sim`
skills of [arjun988/blender-skills](https://github.com/arjun988/blender-skills) (MIT licence, copyright 2026
blender-skills contributors; notice in `.claude/skills/THIRD_PARTY.md`). Their checks are mapped here onto our own
headless tools; their naming conventions (`SM_`, `L_arm_upper`) and their MCP workflow are not used.

## 1. Budgets

| Kind | Triangles at LOD0 | Notes |
|---|---|---|
| Hero character | 50,000 to 100,000 | the skills' AAA-informed default; most of ours sit at 70k to 100k |
| Standard character | 20,000 to 40,000 | |
| Hero prop | 15,000 to 50,000 | |
| LOD1 / LOD2 / LOD3 | about 50% / 25% / 10% of LOD0 | the silhouette must hold at each |

Texture: one 4K set per model today (`texture.px_per_cm` in `stats.json`); a model under about 3 px/cm needs a second
set or a decision.

## 2. Checks, and the tool for each

| Check | Tool | Pass |
|---|---|---|
| Rebuilds the same | `kit/regress.py` | identical to its baseline, or the change is intended and the baseline re-saved |
| Feet on the floor | `kit/ground.py` | no clip below about -10 mm except where the motion means it |
| Muscles through the body | `kit/overlap.py` | none in the shipped clips |
| Loads and plays | `kit/viewer_test.py` | every motion button, no errors |
| Looks right in motion | `kit/strip.py` contact sheets | looked at, every clip |
| Extreme poses | the rigging skill's list: arms up, arms out, a deep crouch, a high knee, the head turned | looked at; worn things checked with the tool below |
| Clothes and worn things | `gi_j01/tools/motion_check.py` (for the gi; to generalise) | at rest, nothing through where it shows; body and lower layers through: none in the everyday clips; stretch 99th percentile under about 2 |
| LODs | `kit/lod_check.py` | silhouette overlap with LOD0 at least 0.95 (front and side); no new holes; same materials (our GLBs carry none: the viewer applies one baked set to every LOD) |
| Topology and data | the pipeline's PACK line and `stats.json` | max 4 bone influences; no loose or non-manifold geometry the asset did not mean |

## 3. Screenshots a verdict needs

1. Hero three-quarter (the pipeline's hero render).
2. Front and side (orthographic contact sheet, `kit/strip.py view=front view=left`).
3. Beauty, as a player sees it (the viewer, `kit/viewer_test.py`).
4. Wire overview (the viewer's WIRE button).
5. Against the reference (the concept sheet, or for clothes a reference garment, from the same angle).

## 4. Severity

| Level | Meaning |
|---|---|
| Blocker | wrong scale, broken export, the body visibly through clothing in an everyday clip, over budget badly without a reason |
| Major | silhouette or readability wrong, clipping in a shipped clip, a LOD that changes the shape |
| Minor | small clips in extreme clips, naming, small shading faults |
| Note | polish |

The result follows: any blocker is NO-SHIP; majors with a stated reason or plan are SHIP WITH NOTES; only minors and
notes is SHIP.

## 5. Verdict template

```markdown
## QA Verdict: [Model]

**Result:** SHIP / SHIP WITH NOTES / NO-SHIP
**Budgets:** [tris at LOD0..3] vs target; [texture px/cm]
**Screenshots:** [listed]
**Blockers:** ...
**Majors:** ...
**Minors:** ...
**Notes:** ...
**Retest after:** ...
```

A visual claim needs a screenshot; a SHIP after fixes needs the checks run again.
