# Humanoid in a Gi

**Status (2026-11-16): third iteration.** Skinned with the robust transfer layer by layer, the covered pants and body
removed, and checked in every clip (`../docs/STUDY-GI.md` sections 11 and 12; QA verdict below).

Humanoid H-01 wearing the jiu-jitsu gi J-01: jacket, pants, collar band and black belt. The first card of the
WARDROBE collection.

## How it is made

- **The body, the skeleton and every motion are H-01's** (`../humanoid_h01`), plus one bone, `gi`, that never moves.
- **The gi was drafted from H-01's own measurements and draped with Blender's cloth** by `../gi_j01/tools/drape.py`
  (the method, the research and the numbers: `../docs/STUDY-GI.md`):
  - pants first: built closed round a smooth collision body in a construction pose (legs 12° apart), the waist
    cinched by a drawstring, draped back to rest;
  - then the jacket, cut the way a gi is cut (the shape measured from photographs, straight sleeves set on along a
    slit in the body's side), over the pants' envelope, the arms coming down from 25° to 8° open;
  - the collar band (5 cm by 1.3 cm) and the belt (4.5 cm by 5 mm, two turns, a square knot, 25 cm tails) built solid
    on the draped jacket.
- **Here** (`scripts/humanoid_gi.py`) each garment is skinned with the kit's robust transfer (`skinning.py`, after
  Abdrashitov et al. 2023), layer by layer: the pants from H-01's body, the jacket from the body and from the pants
  under its skirt (below the belt the skirt takes the pants' weights everywhere), the collar band and the belt from the
  jacket. A vertex copies the weights under it where that surface is within 5% of the garment's diagonal and faces
  within 35 degrees; the rest are solved smoothly; joint blends are widened on the pants and on the parts of the jacket
  not copied from a muscle or a layer; above the knee the pants follow the thigh, not the shin; the skirt copies the
  pants at its own height; left and right are equal within 10 cm of the centre front and back; at most four bones.
  Then each garment goes back from its drape pose to rest, gets 2.5 mm of thickness and UVs, and **what it covers is
  removed**: the body under the gi (where cloth is outside it, or all round it, or where the body already pokes through
  the cloth away from an opening), and the pants under the jacket except near the skirt's hem and vents.
- **The belt's tails** (2026-11-16, evening, after the user's "a bit of clipping of the pieces going down while
  walking"): solved smoothly down from the knot, they lagged the skirt they lie on, and its front swung through them;
  from the hip down they now copy the skirt's weights at their own height. And the collar band running down the front
  edge came through the left tail lying across it; the band is now removed where the belt lies on it. Tails sinking
  into the skirt: walk, jog and run 1 to 2 points (8 to 12 mm) before, none now; rolls 56 to 63 points (36 to 52 mm)
  before, 31 to 40 (35 to 41 mm) now. The dress step itself now lives in the kit (`../kit/menagerie/dress.py`), shared with Red Witness in a Gi;
  this model rebuilds identically from it.
- Materials: matte cotton twill (`C_gi`), black cotton (`C_belt`).

## Checked in motion

`../gi_j01/tools/motion_check.py -- stress` skins the model the way an engine does, in every clip and in five stress
poses, and counts body points and pants points that come through the cloth where it would show.

| | body through the gi | pants through the jacket | stretch, 99th percentile (pants / jacket) |
|---|---|---|---|
| At rest | none | none | 1.0 / 1.0 |
| Idle, Walk, Grasp | 0 to 3 points, at most 3 mm | 0 | 1.1 to 1.6 / 1.1 to 2.1 |
| Jog, Run | 1 point, 3 to 8 mm | 0 | 1.6 to 1.8 / 2.4 to 2.6 |
| Box, Wave | 3 to 5 points, 12 to 21 mm (back of the collar) | Box 1 point, 2 mm | 1.1 to 1.5 / 2.5 to 2.9 |
| Crouch | 0 | 0 | 2.2 / 1.7 |
| Jump | 9 points, 28 mm | 6 points, 35 mm | 2.6 / 3.6 |
| Ukemi (four rolls) | 10 to 16 points, 23 to 39 mm | 8 to 15 points, 28 to 57 mm (centre of the waist, under the flaps) | 2.4 to 2.6 / 3.0 to 4.2 |

Yesterday's version, same check: pants through the jacket 117 mm in the walk, 128 mm running, 188 mm crouching,
103 mm in the guard; body through 73 mm in the guard. Full table in the study, section 12.

The check gained a third test that evening, the belt's tails sinking into the skirt: none in the walk, jog, run, box
and wave; crouch and jump 26 and 32 points (to 36 mm); rolls 31 to 40 points (35 to 41 mm). It also now measures depth
along the cloth's smoothed normals and leaves out the head, hands and feet; the numbers above moved by a point at most
(`../gi_j01/renders/motion/motion_check_2026-11-16_evening.txt`).

## QA verdict (2026-11-16)

The gate of `../docs/QA.md` (adapted from arjun988/blender-skills, MIT).

**Result: SHIP WITH NOTES.** No blockers; the majors are known, measured and listed with a plan.

**Budgets:** LOD0 121,060 triangles against the hero character budget of 50,000 to 100,000: about a fifth over (H-01
alone is 101,264; the gi adds about 34,000 and hides 14,000 of the body). LOD1 / 2 / 3: 60,499 / 30,224 / 14,301
(50 / 25 / 12%). Texture 9.4 px/cm at 4K (H-01 alone 13.4).

**Checks:** rebuild baseline re-saved (intended change); the viewer loads all 14 motions without errors; LODs pass
(silhouette overlap with LOD0 0.991 to 0.999, no new holes); floor contact is H-01's own except in the rolls; the
motion check is the table above.

**Screenshots:** hero three-quarter and orthographic views (`renders/`), the viewer's beauty, wireframe and guard pose
(`../gi_j01/renders/qa/`), the LOD sheet (`renders/lod_check.png`), against the reference sculpt
(`../gi_j01/renders/reference_compare.png`).

**Blockers:** none.

**Majors:**
1. Over the triangle budget. Plan: ship LOD1 (60k) as the in-game default, or drop the cloth's inner skin where it is
   never seen.
2. The rolls (shipped clips): pants through the front flaps at the centre of the waist, 8 to 15 points up to 5.7 cm
   (white on white), and the body through in a few places by 2 to 4 cm, mostly at the back of the neck.
3. The wave and the guard: H-01's neck parts through the back of the collar by up to 2.1 cm (5 points, for a moment);
   in the guard 3 points up to 1.2 cm.
4. Against the reference the shoulders and the back read bulky and lumpy: H-01's deltoid and back actuators under the
   cloth, and the folds the drape leaves in the back. The front reads right.

**Minors:** the jump (6 pants points up to 3.5 cm, the head's base against the collar up to 2.8 cm); the armholes
stretch up to 3.9 times with the arms up (an arms-down bind pose); in the front roll the skirt sinks up to 4.5 cm
further into the floor than H-01 does (no floor collision for the cloth).

**Notes:** nothing swings (no secondary motion).

**Retest after** any change to the weights, the hiding or the drape: the motion check with `stress`, `kit/lod_check.py`,
the viewer test.

## Where it falls short

- **The gi does not swing.** It is skinned, not simulated: the skirt, the sleeves and the belt's tails move with
  the bones under them and nothing else. Baking each clip's cloth motion onto extra bones is the next stage.
- **The back of the collar:** H-01's neck actuators run from the shoulders to the head outside the collar ring, and
  show by up to 2 cm in the wave.
- **The rolls and the jump** still put a few pants points through the front flaps at the centre of the waist (8 to
  15 points, up to 5 cm; white through white) and the body through in a few places (2 to 4 cm), and the belt's tails
  sink up to 4 cm into the skirt in the rolls, the jump and the crouch.
- **The skirt stretches a little between the legs** in the walk: it follows the pants so the pants cannot come
  through it.
- **The armholes stretch with the arms up:** H-01 is skinned arms down; an A-pose bind would halve it.
- **The rolls** (the Ukemi clips) bend the cloth with the body; it was draped standing, so it creases and pinches
  where a real gi would slide.
- **The sleeves at rest** were draped with the arms 8° open and are brought in by the skinning; they may touch the
  jacket's sides under the arm.

## Rebuild

```
blender -b --factory-startup -P gi_j01/tools/drape.py -- humanoid_h01 pants
blender -b --factory-startup -P gi_j01/tools/drape.py -- humanoid_h01 jacket
cp gi_j01/renders/drape/HumanoidH01_{pants,jacket,collar,belt}.npz humanoid_gi/data/
blender -b --factory-startup -P humanoid_gi/scripts/build.py -- 4096 render
```
