# Red Witness in a Gi

**Status (2026-11-16): first build.** The second card of the WARDROBE collection: the gi of Humanoid in a Gi, drafted
afresh for the Red Witness's own body and checked in every clip (`../docs/STUDY-GI.md`, section 13; QA verdict below).

The Red Witness (the figure from `../red_witness_helmet`, rigged on the kit in `../red_witness_rig`) wearing the
jiu-jitsu gi J-01: jacket, pants, collar band and black belt.

## How it is made

- **Body, skeleton and motions: the rigged Red Witness's**, holding its arms 10 degrees out from its sides at rest and
  in every motion (`red_witness.variant(10)`): the bare figure's forearms hang 4 cm from its thigh muscles, and a sleeve,
  a skirt and the pants need about 11 cm. Plus one bone, `gi`, that never moves.
- **The gi is drafted from the Red Witness's measurements and draped** by `../gi_j01/tools/drape.py -- red_witness_gi
  pants|jacket` (it drapes for the `HOST` this module names), with this host's own fit: the collision proxy takes each
  body point for the nearest of the bones that move it, and each ring is the convex hull of its slab of the body (the
  cloth bridges the hollows between the muscle bundles); the jacket is built with the arms 12 degrees further out than
  they settle (not 25: the shoulders buckled), in stiffer cloth.
- **Skinned, layered and hidden by the kit's dress step** (`../kit/menagerie/dress.py`, shared with Humanoid in a Gi):
  the robust transfer layer by layer (pants from the body, jacket from the body and the pants, band and belt from the
  jacket); the sleeves follow the arm's bones only; below the crotch the skirt follows the leg under it; the belt's
  tails copy the skirt they lie on, and the collar band is removed where the belt lies on it; the pants under the
  skirt and the body under the gi are removed, except where the
  body can be seen: only edges with no other layer behind or over them count as openings, and the chest seen through
  the V of the lapels (which stand 11 to 13 cm off this deep chest) stays.
- Materials: the figure's own (ivory bone, black muscle, graphite joints, gold), the AUREL palette, the helmet's baked
  set; matte cotton twill (`C_gi`), black cotton (`C_belt`). One 4K set.

```sh
blender -b --factory-startup -P ../gi_j01/tools/drape.py -- red_witness_gi pants      # then jacket; copy the four
                                                                                      # RedWitnessRigA10_*.npz into data/
blender -b --factory-startup -P scripts/build.py -- 4096 render
blender -b --factory-startup -P ../gi_j01/tools/motion_check.py -- module=red_witness_gi/scripts name=red_witness_gi stress
```

## Checked in motion

The motion check (numpy skinning, as an engine plays it) in every clip and five stress poses:

| | body through the gi | pants through the jacket | belt tails into the skirt | stretch p99 (pants / jacket) |
|---|---|---|---|---|
| At rest | none | none | none | 1.0 / 1.0 |
| Idle, Wave, Crouch | none | none | Crouch: 14 points, 9 mm | 1.0 to 1.6 / 1.1 to 3.5 |
| Walk, Jog, Run | none | 1 to 3 points, 42 to 51 mm, behind two layers (not seen when the pants are drawn red) | none | 2.2 to 2.5 / 2.1 to 2.9 |
| Box, Jump | none | 1 to 3 points, 12 to 24 mm | Jump: 64 points, 11 mm | 1.4 to 2.2 / 3.6 to 5.2 |
| Ukemi (four rolls) | none; the back roll 2 points (a neck strap; probably the check, see below) | 3 to 6 points, 27 to 48 mm | 57 to 64 points, 12 to 15 mm | 2.4 to 3.6 / 4.0 to 6.0 |
| Stress: arms up / forward | 32 / 1 points (the pectorals in the V push a lapel out) | none | none | 1.0 / 4.5 to 6.0 |

## QA verdict (2026-11-16)

The gate of `../docs/QA.md`.

**Result: SHIP WITH NOTES.** No blockers.

**Budgets:** LOD0 98,657 triangles, inside the hero character budget (50,000 to 100,000); the gi is about 58,000 of
them, the helmet 18,600, the hands and feet 10,400. LOD1 / 2 / 3: 49,310 / 24,589 / 11,534 (50 / 25 / 12%). Texture
7.6 px/cm at 4K.

**Checks:** rebuild baseline saved (`kit/baselines/RedWitnessGi.json`; Humanoid H-01, its hand and Humanoid in a Gi
unchanged); the viewer loads and plays all 12 motions without errors; LODs pass (silhouette 0.977 or better, no new
holes); the motion check above.

**Screenshots:** `renders/RedWitnessGi_hero.png`, `_front`, `_left`, `_rear`, `_head`; contact sheets
`renders/strips/` (walk, run, back roll, wave); `renders/lod_check.png`; the reference comparison on the results page
(`../gi_j01/index.html`).

**Majors (with the reason or the plan):**
- In the rolls the gi goes 9 to 12 cm under the floor: the rolls were recorded for the bare figure (its own lowest
  point 4 cm under), and the gi stands 6 to 10 cm off its back. Plan: lift the root, frame by frame, by what the gi
  adds, in the clips recorded for the bare figure.
- The look: the jacket is boxy and the sleeves big next to a real gi (a barrel chest and thick arms, plus ease); one
  deep diagonal fold across the back; the pants balloon at the knee over the big calves. Plan: a tighter cut for a
  heavy host (less ease, a smaller sleeve), a smooth undersuit for the back.
- The arms are held 10 degrees out in every motion, which the bare figure does not do (the price of room for the
  sleeves).

**Minors:** pants points through the front flaps in the rolls (white through white, up to 5 cm); the belt's tails
sink a centimetre into the skirt in the rolls and the jump; the pectorals in the V push a lapel out with the arms fully
raised (a stress pose); in the back roll 2 body points read 95 mm through: a neck strap tied to a patch 11 cm off it,
very likely the check's own error (not confirmed by a picture; the roll turns the figure past the fixed close-up
cameras); small crumples at the skirt's vents in the walk.

**Notes:** nothing swings (skinning only); the collar wraps the base of the helmet with no stand to spare (a 5 cm band
on a short neck).

**Retest after:** any change to the drape, the dress step or the motions: the motion check, the ground check, the LOD
check and the viewer test.

## Credits

Red Witness models, the AUREL hand and foot, and the action showcase motions: Jelaludo
(https://jelaludo.github.io/SentryTowers_A6/). The gi's construction and research: `../docs/STUDY-GI.md`.
