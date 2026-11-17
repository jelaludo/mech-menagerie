# Method

How a concept sheet becomes a rigged, textured game asset. Everything is a script run by headless Blender, so an asset
can always be rebuilt, and a change is a number in a file.

## Conventions

- **Units and axes.** Metres. +Z up, ground at z = 0. The animal faces −Y; its left is +X. Only the left side is
  authored; the right is mirrored. glTF and FBX exports are Y-up.
- **Segments.** A model is a set of rigid segments (`pelvis`, `chest`, `forearm`, …), each with its origin on its
  joint and parented in a chain. The same names are the bone names.
- **Materials.** Six shared names: `C_carbon` (frame), `C_sleeve` (braided actuator), `C_steel` (ferrules, bearing
  faces), `C_dark` (housings, toes), `C_claw`, `C_lens` (lit optics). After baking, the asset has one material.
- **Textures.** `BaseColor` (sRGB), `Normal` (OpenGL, +Y) and `Normal_DX`, `ORM` (occlusion, roughness, metallic in
  R, G, B), `Emissive`.
- **Tessellation.** Every builder takes `q` (1.0 = LOD0). LOD1–3 are automatic reductions of LOD0 that keep the UVs.

## The steps

1. **Read the sheet.** Take the side view as the truth for proportions. Convert pixels to metres with the stated
   shoulder height, and note where the sheet's numbers disagree with its drawing.
2. **Place landmarks.** Joint centres for one front and one hind leg, the keel line as (y, z) points, the trunk
   section at about ten stations (half width and belly line), the head pivot and tilt, the tail line.
3. **Block out in clay.** `build.py -- clay render view=left view=hero` builds in seconds. Compare with the sheet; fix
   proportions now. Expect two or three rounds.
4. **Add the frame.** Keel, hoops, sternum, link plates between joints, joint modules, A-frames over shoulder and hip,
   skull, mandible, digits, tail.
5. **Add the muscles.** One table: start, end, radius, bow, and the two bones it spans. Rows inside the trunk come
   from the trunk section. Check clearance against frames.
6. **Unwrap and pack.** Every part already carries metric UVs. The pipeline equalises island scale, boosts hero parts
   (head ×1.45), and packs one set.
7. **Bake.** Procedural weathered materials are baked onto the game mesh itself: normal, AO, colour, roughness/metal,
   emission. There is no separate high-poly model.
8. **LODs and exports.** Decimate to 50%, 25%, 12%. Export static glTF per LOD, a GLB, an FBX.
9. **Rig.** Bones at the segment pivots, weights from the part tags, one skinned mesh per LOD.
10. **Motions.** Stock clips from `motions.Quadruped`, tuned by a few numbers; new clips where the animal needs them.
    Check with `clay anim`.
11. **Viewer and card.** The pipeline copies the shared viewer, writes `config.json` and `stats.json`, and renders the
    card image. Add or update the entry in `catalog.json`.
12. **Verify.** Re-import the GLB, run the viewer in a headless browser, look at motion frames, and list what was not
    checked.

## Adding an animal: checklist

- [ ] `mkdir <animal>/{scripts,reference}`; copy the concept sheet into `reference/`.
- [ ] `scripts/<animal>.py`: `NAME`, `TITLE`, `SHORT`, `U`, landmarks, `TRUNK`, `HEAD`, frames, `build(q)`, `PIVOTS`,
      `PARENT`, `RIG`, `MOTIONS`, `DENSITY`, `FOCUS`.
- [ ] `scripts/build.py`: put `scripts/` and `kit/` on the path, `pipeline.run(<animal>, ROOT)`.
- [ ] Clay rounds until the side and hero views match the sheet.
- [ ] Clearance check of muscles against frames.
- [ ] `kit/strip.py` for every clip, then `kit/ground.py` and `kit/overlap.py`; fix reach problems (feet leaving the ground, joints folding the wrong way) and try numbers with `kit/tune.py`.
- [ ] Full build: `4096 render anim`.
- [ ] Browser test of the viewer; re-import of the GLB.
- [ ] `catalog.json` entry set to `built`; README in the animal folder; new lessons into `LESSONS.md`.

## How things are checked

| Question | Check |
|---|---|
| Is the shape right? | clay renders next to the concept views |
| Did a kit change alter an existing animal? | build it both ways and compare vertices; the feline must match exactly |
| Do muscles hit the frame? | sleeve vertices under a frame bar, gap to the bar's inner face, at rest |
| Do textures still fit after a rebuild? | geometry unchanged → `nobake` is safe; otherwise rebake |
| Do the motions work? | a contact sheet per clip from `kit/strip.py`: twelve or more frames, side, front and three-quarter views, and exact times for fast actions; then `kit/ground.py` and `kit/overlap.py` |
| Does the page work? | headless Chromium: load, switch motions and LODs, open the export menu, start a download |
| Is the export sound? | re-import the GLB: mesh count, triangles, textures, joints, animations |
