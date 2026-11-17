# Study: a jiu-jitsu gi, tailored to any host

Research for the next challenge: one reusable BJJ gi (jacket, pants, belt), made from sewing-pattern panels, draped
with Blender's cloth simulation, and tailored to bodies as different as H-01, the gorilla G-02 and the toad. Written
2026-11-15 before any garment code. The user's starting notes are in `gi_j01/reference/user_notes_blender_gi_physics.txt`
and the chart they supplied is `gi_j01/reference/size_chart_and_flats.png`.

The results, with pictures and the checks in motion: [the gi results page](../gi_j01/).

## 1. What the chart is, and what it is not

The supplied image is a **technical flat and a size chart**: drawings of the finished garment (front, open, back,
pants front and side, belt) and eleven sizes from M0 to A5. It is not a sewing pattern; it shows no cut pieces. What
it does give, and what we keep:

- the garment's look: lapel running to the hem, quilted arcs on the upper back and shoulders, reinforced side vents
  with a small square at the top, cuff and hem tapes; pants with a drawstring through loops at the front, knee
  panels with vertical stitch rows, side seams; a belt with a rank bar near one end;
- six measurements per size: jacket length A, half chest B, arm-to-arm C, cuff hole D; pants waist E, length F,
  cuff hole G. For H-01 (1.89 m) these land between A2 and A4 (section 4), which becomes the first check on our
  own drafting.

## 2. How a gi is built

From the sources (section 8), with what is our own reading marked as such.

**Rules (IBJJF, adults).** Jacket reaches the thighs. Sleeves end no more than 2 cm from the wrist with the arm held
straight out. Sleeve opening at full extension 7 cm (the gap round the arm). Lapel 1.3 cm thick, collar 5 cm wide.
Pants end no more than 2 cm above the ankle bone. Belt 4 to 5 cm wide, wrapped twice, double knot, each end hanging
20 to 30 cm; a black bar near the tip (white or red on a black belt). Cotton or cotton-like woven fabric only;
white, royal blue or black, top and pants the same colour. EVA allowed inside the collar.

**Judo regulations and history (IJF, judogi).** The jacket is named in three parts: skirt (suso), lapels (eri),
sleeves (sode). Upper jacket in heavy sashiko weave, the skirt lighter (diamond weave). Double-weave jackets have a
seam down the centre of the back joining two halves (the IJF limited its overlap to 3 cm in 2005); single-weave ones
often have a one-piece back. Pants carry a gusset at the crotch and double-layered knees. Reinforcement tape on
the sleeve edge, the leg edge and the seam between upper and lower jacket. Fabric weights: single weave 300 to 550
g/m², double weave 650 to 1050 g/m².

**Commercial BJJ gis.** Vents at the jacket sides and the pant hips; a roomy crotch gusset; a rope or ripstop
drawstring through 4 to 6 loops; knee panels running from above the knee to mid-shin or to the cuff; inseam strips
on some.

**Cut list we will draft** (the pieces are standard; how the jacket body goes over the shoulder is our reading of
kimono construction, from which the judogi comes, not something the sources state):

| Garment | Piece | Count | Notes |
|---|---|---|---|
| Jacket | Body half | 2 | front hem, over the shoulder (no shoulder seam), down to the back hem; the two join at a centre-back seam. The front edge is slanted for the crossover; left over right |
| | Sleeve | 2 | a tapered tube with an underarm seam, set straight into the body's side (the T cut) |
| | Underarm gusset | 2 | a small square where the sleeve seam meets the side seam (our reading: common on judogi, not in the sources) |
| | Collar band (eri) | 1 | a strip folded over a 1.3 cm core, quilted with lengthwise rows; from one front hem round the back of the neck to the other |
| | Upper-back and shoulder layer | 1 | a second layer quilted in arcs (the arcs on the chart) |
| | Vent reinforcements, tapes | | at the vents, the hem and the cuffs |
| Pants | Leg | 2 | each one tube with an inseam |
| | Crotch gusset | 1 | a diamond between the two inseams |
| | Waistband channel | 1 | the top folded over a rope drawstring, out through 4 to 6 loops at the front |
| | Knee panel | 2 | a second layer from above the knee to mid-shin, vertical stitch rows |
| Belt | Belt | 1 | many layers stitched in 8 to 10 lengthwise rows; 4 to 5 cm wide; length = two wraps + knot + two 25 cm tails |

## 3. What Blender 5.3 offers, checked

The 5.3 Python API (`gi_j01/tools`, checked in this session) still has the classic cloth solver with everything a
garment needs: tension, compression, shear and bending stiffness (each with a vertex-group maximum); **sewing springs**
(`use_sewing_springs`, `sewing_force_max`); **shrink** (`shrink_min`, `shrink_max`, a vertex group); **pinning**
(`vertex_group_mass`, `pin_stiffness`); **dynamic mesh** (cloth whose rest shape follows an armature above it);
internal springs; pressure; self-collision; and a collision modifier with inner and outer thickness and friction.
There is no new garment solver in 5.3.

**Spike: sewing from flat panels against building the garment closed.** `gi_j01/tools/spike_wrap_vs_sew.py`, picture
in `gi_j01/renders/spike_wrap_vs_sew.png`. Two panels round a torso stand-in, shoulders pinned, 90 frames:

| | A: flat panels 0.8 m apart, sewing springs, gravity eased in | B: panels wrapped onto a cylinder round the body, seams welded |
|---|---|---|
| seam gap at the end | 9 to 24 cm (never closed) | 0 |
| fabric stretch, 99th percentile / worst | 19% / 94% | 0.4% / 1% |
| result | crumpled and fell | hangs cleanly |
| time | 1.9 s | 2.1 s (2,300 vertices) |

**Conclusion: build each garment already closed round the body.** Every piece is a developable surface (a cylinder
or a cone, which unroll to the flat pattern piece with no stretch), placed a few centimetres outside the body with its
seams shared, so the simulation only has to drape. Sewing springs are kept for short closures, if any. This is also
how the GarmentCode work turns patterns into simulation meshes ("box mesh" with merged seams). The flat pattern
sheet is then drawn from the same pieces: the panels unrolled, labelled, with measurements.

## 4. Three hosts, measured

`gi_j01/tools/measure_host.py` measures a host from its built parts at rest (girths are convex hulls of the parts,
so they include the muscles; envelope = widest point round the bone's middle 80%):

| | H-01 | Gorilla G-02 | Toad |
|---|---|---|---|
| height | 1.89 m | 1.38 m, on its knuckles | 0.21 m |
| trunk axis | upright | leaning, 34° from vertical | horizontal |
| chest girth | 1.05 m (1.54 m over the deltoids) | 2.42 m | 0.60 m |
| shoulder joints apart | 0.47 m | 0.78 m | 0.19 m |
| arm, shoulder to wrist | 0.58 m | 1.00 m | 0.11 m |
| upper-arm envelope radius | 0.11 m | 0.18 m | 0.03 m |
| leg, hip to ankle | 0.90 m | 0.68 m | folded sideways |
| hip joint height | 0.98 m | 0.70 m | 0.08 m |

H-01 against the chart: wrist to wrist is 0.47 + 2 × 0.58 = 1.64 m, so arm-to-arm with sleeves 2 cm short of the
wrist is about 1.6 to 1.7 m, size **A2 to A3**; jacket length to mid-thigh about 0.85 m, **A3 to A4**. So H-01 is a
tall, long-legged A3. The gorilla needs twice a human chest, sleeves of a metre and short wide pants: nothing on the
chart. The toad's jacket is 0.6 m round a horizontal body with 11 cm arms.

**One gi cannot be scaled to fit all three.** It has to be drafted from each host's own measurements, along its own
body axes (the toad's "up" for the jacket is its spine, which is horizontal). That is tailoring, and it is what the
pattern code will do.

## 5. Plan

**The kit gets `garment.py`:**

1. `measure(host)`: the measurements above, from any model module.
2. `proxy(host)`: a smooth collision body. The models are open cages, joints, rings and claws, which cloth would
   snag on and sink into; the proxy is a tapered tube per bone whose cross-section is the envelope of that bone's
   parts (radius per angle, smoothed), rigid to its bone and skinned to the host's armature. It must enclose the real
   body, so nothing pokes through the gi afterwards.
3. `Gi(host, size options)`: drafts the pieces from the measurements plus ease (the chart's numbers are the check),
   builds them closed round the proxy in a **construction pose** (arms out, legs a little apart; per host), and
   writes the flat pattern sheet.
4. `drape()`: cloth simulation: settle under gravity in the construction pose, move the host to its rest pose over
   about two seconds, settle again. Heavy cotton: high tension, moderate shear and bending; the collar band and the
   belt are stiff solid parts built on the draped result, not simulated cloth.
5. **Export:** the draped gi is skinned to the host's own armature (weights from the proxy tubes, blended across the
   joints) and exported with the host as one model through the existing pipeline. It moves with every clip the host
   already has.
6. **Secondary motion (second stage):** the skirt, sleeve ends, pant cuffs and belt tails get a few extra bones each;
   each clip is simulated in Blender and the cloth's motion is fitted to those bones by least squares (the
   technique of the commercial BoneBake and of DemBones, written in our kit). Loops are run twice and the second
   cycle kept, blended into the first frame. The result is ordinary bone animation, so it plays in the viewer and in
   any engine.

**Checks:** the existing ones, plus three for cloth: body vertices outside the gi (poke-through), fabric stretch
against the pattern (heavy cotton should stretch under 3%), and cloth inside the proxy.

**Order:** H-01's pants (the simplest closed garment, and the crotch gusset), then the jacket (body, sleeves, collar,
vents), then the belt; skin and export "Humanoid in a Gi"; the standalone gi card with its pattern sheet; then the
gorilla, then the toad; then secondary motion.

## 6. Risks

- **Construction pose to rest pose.** Lowering the arms through the cloth is where simulations break. Mitigations:
  slow over 60 frames, high collision quality, the proxy slightly fatter during the move.
- **The crossover and the belt** need self-collision, which is slow and fiddly. The belt is built solid on the draped
  jacket; the front overlap starts already layered, left over right, a few millimetres apart.
- **The gorilla's knuckle stance and the toad's sprawl** put the "trunk" off vertical and the limbs sideways. The
  drafting works in each host's body frame, not in world axes; the toad's folded hind legs make its pants the
  hardest piece in the plan.
- **Scale.** Real cotton on a 0.2 m toad drapes like a doll's clothes (stiff, few folds). Physically true; it may not
  read as a gi. Fabric bending can be scaled with the garment if it does not.
- **Texture budget.** One 4K set is shared by the body and the gi; the gi covers most of the body. Covered parts
  could be dropped from the export, or the gi given its own set.
- **Clip loops** with cloth: the end state differs from the start; handled by simulating twice and blending.

## 7. Decisions taken by default (change them if you like)

- Colour: white first; royal blue and black as variants (the IBJJF's three).
- One-piece back (single weave look), with the centre-back seam as an option.
- The gi cards go in a new collection on the landing page, **WARDROBE**: the gi on its own with its pattern sheet,
  then each host wearing it.

## 8. Progress, 2026-11-15 (first build session)

Built in `kit/menagerie/garment.py`, run with the tools in `gi_j01/tools/` (all headless Blender 5.3):

| Piece | State | Numbers on H-01 |
|---|---|---|
| `Host` and `measure()` | done | the table in section 4 |
| Collision proxy | done, all three hosts | body outside the proxy: H-01 1.1%, gorilla 1.5%, toad 3.0% (worst 2 to 3.5 cm); pictures `gi_j01/renders/proxy/` |
| Exact proxy slicing (`Slicer`) | done | sections are cut from the proxy's edges, not sampled from its vertices (the vertex version collapsed rings where tubes slant) |
| Pants | **good first version** | 6,000 vertices; 20 s to drape; stretch 99th percentile 1.06, worst 1.31; cloth inside the proxy 10 of 3,015; body poking through 3 points, worst 2.6 mm. Drawstring cinch (shape key on the pinned waistband), diamond gusset, legs tapered from the seat |
| Jacket, first version | **drapes and holds, but bulky** (superseded the same day, below) | 6,800 vertices; 23 s. Wrap left over right, V lapels, side vents, armholes round a ring about the arm root, sleeves, belt cinch, collar pinned at the neck. Stretch 99th percentile 1.73 (round the armpits); body poking through 88 points, worst 68 mm (H-01's chest actuators push the lapels apart); shoulders bunch up |
| Wearing both | preview only | `gi_j01/renders/dressed/HumanoidH01_pants_jacket_sheet.png`; the pants show through the jacket skirt in places because the jacket is draped on the bare body |
| Layering (jacket over draped pants) | first try failed (the skirt started inside the pants and stayed there, or snagged on them); **solved in the second version** with the pants' envelope |
| Belt, collar band | **done in the second version** | |
| Drawstring and loops, knee panels | not started | |
| Skinning and export, viewer card | **done: Humanoid in a Gi** (`humanoid_gi/`, WARDROBE on the landing page) | 159,000 triangles at LOD0; weights copied from the body under the cloth; all of H-01's 13 clips |
| Gorilla, toad | proxies only | |
| Secondary motion | not started | |

What the session established, beyond the spike:

- **A construction pose is required, and it must be small.** The arms are raised 25° for the jacket and the legs spread
  12° for the pants. A bigger pose (45°) means a bigger move back, and the cloth round the armpit is dragged.
- **Some bodies leave no room for cloth.** At rest H-01's arm and torso proxies overlap by about 5 cm at the armpit.
  The jacket is draped with the arms 8° open (`drape_pose`); skinning will have to bring the sleeves in.
- **Pins are what hold a garment up.** The jacket slid off the shoulders until the collar rows were pinned; the
  pants hang from their pinned, cinched waistband.
- **Every starting vertex must be outside the proxy,** pushed out along its own ring's axis (`push_out`), or the
  simulation explodes; pushes larger than about 6 cm mean the construction geometry is wrong somewhere.

Done since: the proxy no longer shaves off thin parts (body outside it 0.5%, worst 12 mm; H-01's chest actuators
stay under the jacket), the collar back at the neck, a square knot, and the first export (`humanoid_gi/`). Next: the
cloth's own motion baked onto extra bones per clip, the gorilla, the toad.

### The second jacket (same day), after section 9

Rebuilt on what the references taught:

- **Cut from the gi's own measured shape.** Each row is a rounded rectangle with the half width and half depth of
  `GI_PROFILE` (senshu-jerseys' medians), its neck, shoulder point, armpit, belt and hem mapped onto the host's own
  heights; width and depth are each enlarged only where the host's trunk needs it, and the arm roots under the dropped
  shoulder are covered locally. A short-necked host (H-01) gets the collar raised just clear of its shoulder muscles.
- **T-cut sleeves.** The armhole is a slit in the body's side plane where the sleeve's straight tube crosses it, from
  the shoulder point down to the armpit; the sleeve keeps its girth (upper arm plus 12 cm) to 85% at the cuff.
- **Skirt over the pants.** The skirt is cut, and draped, over a smooth envelope of the draped pants (`envelope`):
  over the crumpled pants themselves the skirt snagged and shredded within 20 frames.
- **Solid collar band and belt** (`Jacket.finish`): the band 5 cm by 1.3 cm along the draped edge, hem to hem round the
  neck; the belt 4.5 cm by 5 mm, two turns, a knot, two 25 cm tails lying on the skirt.

On H-01, over the draped pants: 6,900 vertices, 20 s; stretch 99th percentile 1.10, worst 1.68 (the first jacket:
1.73 and 4.4); 143 vertices pushed out at the start (391 before). The silhouette check (`drape.py`) puts the shoulders
at 0.98 to 1.00 of the measured width and the skirt at 0.91 to 1.06; the chest and depth come out 1.1 to 1.5 times
the human proportions, which is H-01's own bulk. Still wrong: the collar stands tall at the back (the raised
neckline), the tips of H-01's chest actuators poke through the left front (55 body points, worst 68 mm), the knot is
a plain band, not a square knot. Pictures: `gi_j01/renders/dressed/`.

### Clipping in motion and the collar (same day, after the user looked at it in the viewer)

The user saw the body through the pants at the calves (in the crouch), something cutting through the front of the
jacket at the waist when running, and a collar standing far off the neck.

- **The collar.** The yoke now closes from the shoulder line to a ring round the neck, 12 mm outside the neck's proxy,
  each point lifted onto the shoulders' top (rays cast down on the proxy with the arms raised and down), and the
  collar stands two rows up the neck. From above it is a ring round the neck column, inside H-01's four neck
  actuators; those run from the shoulders to the head and come out over the collar at the front and the back.
- **What cut through the front was not the collar band but the skirt.** H-01's hands hang beside the skirt; for the
  skirt vertices nearest them all ten nearest body points were hand points, which carry no bone the jacket may follow,
  so the weights fell back to plain distance and took the forearm. When the arm swung, a strip of skirt went with it.
  It took four wrong guesses to find; a numeric check found it at once (skin the gi at the frame in numpy, list the
  most stretched edges with their vertices' weights). Fix: search further for the nearest *allowed* body points.
- The same check then found, one after another: the jacket body below the armpit taking arm weights (now never the
  forearm, and the upper arm only fading in above the armpit), the sleeve cut sharply between upper arm and forearm
  at the elbow (now a smooth blend across it), the armpit end of the sleeve counted as below the elbow because on this
  body it is level with it (now: near the armhole, the upper arm), and the armhole seam itself (each sleeve vertex now
  starts with the weights of the body vertex it is sewn to and hands over to its own over 12 cm). Worst stretch at
  the run frame went from 32 times to under 10, all of the rest at the knee crease and in millimetre edges.
- **The body under the gi is removed where the gi covers it** (12,000 faces, about a tenth of H-01), except near the
  cuffs, the neck and the lapels. What is not there cannot come through. The pelvis is covered entirely and keeps one
  face so the pipeline has a mesh to work on.
- The collar band and the belt take their weights from the jacket body (never a sleeve beside them).

### The last iteration before shelving (same day)

The user found it promising but still very messy and asked for one more pass, leaning towards shelving the gi and
trying again later with another method. The mess was mostly surface noise: a thin, soft simulation left many small
crinkles and frayed edges where a heavy gi hangs in a few broad folds.

- **Heavier, stiffer cloth:** bending stiffness 4 times (pants) and 3 times (jacket), more solver steps (15), a
  longer settle (60 frames).
- **A smoothing pass after the drape** (`garment.relax`, Taubin smoothing): the small crinkles go, the broad folds
  and the size stay; open edges are smoothed along themselves so hems and cuffs hang where they did; then everything
  is pushed back outside the body and the pants (113 jacket vertices).
- Result on H-01: pants stretch 99th percentile 1.12, worst 1.34, nothing of the body through them; the jacket's
  surface reads as heavy cotton with a clean hem and vents. In motion the worst stretch is at the back of the knee
  (6.5 times on 9 mm edges). Still wrong: the shoulders are large, the raised arm in Box crumples at the shoulder,
  H-01's neck actuators show over the collar, nothing swings.

## 9. Learning from other gi models (2026-11-15)

Three references the user pointed to. Credits in `gi_j01/reference/external/CREDITS.md`.

**senshu-jerseys, judogi (John Morris, MIT).** A 2.5D SVG drawing of the jacket, trousers and belt, not 3D cloth, but
its proportions are *measured*: landmarks marked on six photographs, divided by each photograph's jacket length L
(collar top to hem), and the drawing checked by overlaying it on the photographs (outline overlap 0.79 to 0.93).
Medians, as fractions of L:

| | of L | for H-01's jacket (L about 0.86 m) |
|---|---|---|
| shoulders, sleeve point to sleeve point | 0.62 | 0.53 m |
| shoulder drop, collar top to shoulder point | 0.14 | 0.12 m |
| chest, armpit to armpit (flat) | 0.44 | 0.38 m |
| waist, belted (flat) | 0.46 | 0.40 m |
| hem (flat) | 0.53 | 0.46 m |
| depth, chest to hem | 0.70 of the width (0.5 to 0.55 at the shoulders) | |
| sleeve angle from the body, hanging | 14° | |
| trousers, flat | 1.10 to 1.14 of L | |

These are for people; H-01 is far bulkier at the chest (1.16 m round), so they are a shape to scale and fit over
the body, not sizes to copy. The construction points that matter most for us:

- **Sleeves are straight bands set on at the shoulder points, as wide under the arm as at the cuff,** and the
  armhole is long: from the shoulder point down to the armpit, about 0.34 L. That is the T cut. Our jacket sets a
  round sleeve into a tight ring about the arm root (a Western set-in sleeve) under a yoke drawn from the body's
  outline, which is why its shoulders bunch.
- **The jacket is taken in hard at the belted waist and flares below,** and the skirt is cut deeper than the
  trousers "so trousers worn under it never show through": the answer to our layering failure is to cut the skirt
  from the draped pants, not to push it out of them.
- **The collar is a solid band**, a stitched roll filled with rubber, standing round the back of the neck and running
  down the front to the hem (judo 4.7 cm by 9 mm, six rows; the IBJJF's 5 cm by 1.3 cm). Built solid it also stops
  our lapels falling open.
- **Belt:** 4.2 cm (colours) or 4.5 cm (black) wide, 4 to 5 mm thick, 10 stitched rows; length twice the waist plus
  90 to 100 cm; two turns, a square knot, ends of 20 to 30 cm that lie on the belt, then the skirt, and hang straight
  past the hem.
- **Trousers:** a deep waistband gathered by a flat braided drawstring about 1 cm wide tied in a bow; a second layer
  stitched round each knee; a turned hem with a break above the foot.
- **A test worth copying:** compare our silhouette, front and side, with references, as a number.

**Sloyd "MartialArtsGi" (EdgeForge, CC BY 4.0).** A free, AI-generated suit: jacket, belt with knot and ends,
trousers, 1.70 m, as a hollow shell with no body and no rig: one mesh of 25,151 vertices and 39,176 triangles, one
2048 texture, 10,490 open edges. Not usable as geometry, for cloth or for skinning; useful as a silhouette target
(sloping shoulders with the sleeve seam dropped past the shoulder, a crisp lapel band, the skirt flaring under the
belt, straight wide trousers) and as a budget reference (about 40,000 triangles for a whole suit).

**"Brazilian Jiu-jitsu BJJ Kimono Free" (Tulio Portela, CC BY 4.0).** A sculpt of a man in a gi, 1.90 m: three meshes,
218,000 triangles, more than a thousand loose shells, no rig. Not usable as geometry. What it shows, measured on it:

- **The collar hugs the neck.** At the collar the garment is 16 to 19 cm across, a centimetre or two outside the
  neck all round; the band stands up round the back of the neck and sits on the trapezius, then runs down as the
  lapel. This is the user's point about our collar, and why the second jacket's yoke closed to a ring at the neck
  (below).
- **The shoulders fit; the sleeves are loose.** The jacket lies close over the shoulders and chest with few folds
  until the belt; the volume is in the sleeves and the skirt.
- **The V is deep and the lapel crosses to the opposite hip,** as ours does.

**CGTrader "jiu-jitsu" listing.** Could not be read (the site returned an empty page to the fetcher). Product links
pasted by hand can be read.

## 10. If we try again another way

What this attempt established, and what to change:

- **What worked and is worth keeping:** measuring the host and drafting from its own measurements; building garments
  closed round a collision body instead of sewing flat panels; slicing the proxy for exact sections; the envelope of
  one garment as the collider for the next; copying skin weights from the body under the cloth, from the nearest
  points that carry an allowed bone; culling the body under the cloth; checking skinning numerically (stretch per
  edge at a frame) instead of by eye; the silhouette check against measured proportions.
- **What fought us:** a robot body is not a human body (bulging actuators, a short neck with muscles up to the head,
  hands hanging beside the skirt), so every human assumption needed a special case; a hand-built mesh per row with
  zipper triangles; Blender's cloth with pins and collisions, tuned by trial; skinning a draped garment onto a body
  whose arms pass through where the sleeves hang.
- **Other ways to do it, best first:**
  1. **A garment tool, then skinning.** Make the gi once in Marvelous Designer or CLO (or with GarmentCode and its
     GPU simulator, which is open source) on a mannequin of each host's measurements, export the draped mesh and the
     flat pattern, and keep our parts of the pipeline: the weight transfer, the culling, the solid collar and belt,
     the checks, the export. The drape and the panels are what those tools do best and what cost us most.
  2. **Model it by hand on the body, then simulate only the loose parts.** A clean quad mesh shaped over H-01's
     proxy (the yoke, the collar and the chest fitted, as the sculpted references are), skinned with our transfer;
     the skirt, the sleeves below the elbow, the pant legs and the belt tails simulated per clip and baked to a few
     bones each (the secondary-motion stage of section 5).
  3. **Dress a smoother body.** Give each host a "fitted undersuit" proxy for clothing (the neck actuators and the
     chest pistons tucked in), so the gi drapes over a body shaped like a person's.
  4. **A stronger model or more Blender skill** helps most with option 2: the hand-shaping and the cloth tuning.
- **Keep for any method:** the IBJJF and judogi numbers (section 2), the measured proportions (section 9), the user's
  feedback (a tight collar round the neck; no clipping in the run and the crouch).

## 11. Research: clothes on an existing model without clipping (2026-11-16)

What production pipelines do, and what we took:

- **Hide what is covered.** Engines remove the geometry of a layer, or of the body, wherever another layer covers it
  (Roblox's layered clothing calls it hidden surface removal; character creators tag body regions a garment hides).
  We already removed the body under the gi; now the pants under the jacket's skirt go too, except near the skirt's
  real openings (its hem and vents).
- **Weights from the body, but only where they are trustworthy.** The standard tool copies each garment vertex's
  weights from the closest point of the body, and fails on loose clothing: sleeves take the torso's weights, a skirt
  takes the nearest leg's, the bottom of flared trousers the other leg's. Epic Games' fix (Abdrashitov, Raichstat,
  Monsen and Hill, *Robust Skin Weights Transfer via Weight Inpainting*, SIGGRAPH Asia 2023 Technical Communications;
  MIT reference code) copies only where the closest body point is within 5% of the garment's bounding-box diagonal and
  its normal within 35 degrees (30 in their code), solves every other vertex by minimising Dirichlet plus Laplacian
  energy with the copied ones held, clamps, and smooths 10 times over the solved ones (alpha 0.2). Collars are masked
  to be solved, or they follow the jaw. Our "hands beside the skirt" bug of the last round was exactly the failure it
  fixes. Implemented as `kit/menagerie/skinning.py` (numpy conjugate gradients: Blender has no scipy).
- **Layers that touch must share their influences.** The forum advice for skirts that clip legs is to make the skirt
  behave like the legs; the strongest rule seen (identical influences on matching vertices) is the reason layered
  garments slip when they do not. Our version: each layer copies its weights from the layer directly under it.
- **Bind pose.** Shoulders deform best when skinned in an A-pose (about 45 degrees) rather than arms down; our rig's
  rest is arms down, so the raised-arm poses stretch the armholes most. Noted, not changed (it is H-01's rig).
- **From the skills (arjun988/blender-skills, MIT), without installing them:** soft pins with a falloff ("hard
  binary pins crease badly"), a pre-roll, gradient weights at joints with no hard 0/1 at the bend, at most four
  influences, testing extreme poses, a QA gate with budgets, screenshots and an explicit verdict, and LOD silhouette
  checks. Adapted into `docs/QA.md`, `kit/lod_check.py` and two project skills (`.claude/skills/menagerie-qa`,
  `menagerie-garments`), with the licence notice.

## 12. The iteration of 2026-11-16

Goal (the user's): H-01 in a gi that feels tailor-made and natural.

**Fit.** Ease cut from 6 cm to 3 cm a side (the old figure added about 35 cm round the chest); sleeves 8 cm over the
upper arm instead of 12. The lapels now cross part way up the armhole (a table on the body's landmarks: hem, belt,
armpit, crossing, neck), and an inside tie (soft pins along both front edges for 10 cm below the crossing) keeps the
fronts closed over H-01's chest instead of sliding apart as the cloth settles. The collar stands one row up the neck,
not two (the jaw is close). Soft pins everywhere.

**Bugs found on the way.** The push out of the body used one ray for "inside": it grazed an edge of the hip tube and
sent a cuff vertex a metre up to the hip. Now three rays and a majority, a bounding box, and a 8 cm cap.

**Skinning, by measurement.** The robust transfer alone made the pants stretch 3.5 times at the front of the knee in a
walk: H-01's own weights change from thigh to shin over 2 to 3 cm (rigid parts), and the cloth copied that. So the
weights are widened along the garment (60 diffusion steps) except where the jacket copied them from a muscle or a
layer (widening those let the sleeve lag the biceps). The skirt then still passed through the pants: the jacket was
not allowed the shin, which H-01's thigh actuators carry up to the hip; and at the centre front a pants point
followed the right leg while the skirt over it followed the left. Now the jacket, band and belt may follow the shin,
the skirt takes the pants' weights everywhere below the belt, and left and right are blended equal within 10 cm of the
mid-plane (above the crotch only: the inner thighs are near the middle too, and blending them made the pants stretch
3 times). Last, the rolls: the pants at the back of the thigh carried 15 to 25% of the shin 30 cm above the knee
(copied from H-01's hamstring actuators, which are hidden under the pants anyway), so above the knee the shin's
weight now goes to the thigh (from 6 to 20 cm above it). That showed the next fault: at the centre of the waist the
double front flap stands 6 cm off the pants, where the pants' weights change fast with height (pelvis 0.51 to 0.29
in 2 cm), and the flap copied from the pants 1 to 2 cm lower, so it followed the legs while the pants under it
followed the pelvis. The skirt now copies the pants at its own height (height counts three times in "closest";
`skinning.copy_closest(zscale=3)`). Moving the thigh's weight to the pelvis above the hip, tried on the way, made it
worse and was dropped.

**Hiding.** The body is culled where cloth is outside it along its own limb's axis, or where cloth surrounds it (4 of
6 directions within 50 cm: the inside of an arm faces the torso, and its own ray ran into the jacket). The pants under
the skirt are removed except within 8 cm of the hem and vents. Last, a body point already outside the cloth's surface
at rest goes with its faces, however near an opening, unless the cloth nearest it is at the opening's edge: the tip
of a pectoral actuator showed through the left breast, kept because it was within the margin of the V.

**The check** (`gi_j01/tools/motion_check.py`, numpy skinning as an engine does it). It had to be fixed twice: a ray
from the bone's axis misreads points near openings, and the nearest face switches between the jacket and the sleeve
at the armpit. Now each point is tied to its own patch of cloth at rest, counts only if it comes in front of that same
patch, within 3 cm of it sideways, with no other cloth in front of it or around it. It follows only points that start
under the cloth, so a blemish there in every frame (the pectoral tip) passed every clip; it now also counts body
points already through the cloth at rest where they show: 23 points, up to 29 mm (the neck actuators through the
back of the yoke), on yesterday's version; none now. A third fix: the pants faces under the skirt are removed but
their vertices stay in the mesh, and the check counted them; half its "pants under the jacket" were never drawn. It
now counts only vertices still in a face (the numbers below; yesterday's version removed nothing, so its column is
unchanged).

| | yesterday's commit | now |
|---|---|---|
| body through the cloth at rest, where it shows | 23 points, 29 mm | none |
| pants through the jacket, walk | 77 points, 117 mm | 0 |
| ... run (the user's "around the waist when running") | 39 points, 128 mm | 0 |
| ... crouch | 72 points, 188 mm | 0 |
| ... box (the guard) | 38 points, 103 mm | 1 point, 2 mm |
| ... jump | 107 points, 209 mm | 6 points, 35 mm |
| ... rolls (the four Ukemi) | 83 to 112 points, 181 to 248 mm | 8 to 15 points, 28 to 57 mm |
| body through the gi, walk / run / box | 0 / 21 mm / 73 mm | 0 / 3 mm / 12 mm |
| body through the gi, rolls | 75 to 117 mm | 23 to 39 mm |
| stretch 99th percentile, pants: walk / jump / rolls | 3.1 / 5.2 / 5.1 to 5.4 | 1.6 / 2.6 / 2.4 to 2.6 |
| stretch 99th percentile, jacket: walk / wave / box / arms up | 2.5 / 6.3 / 6.0 / 8.4 | 1.8 / 2.5 / 2.9 / 3.9 |

Still wrong: the rolls and the jump put a few pants points through the front flaps at the centre of the waist (8 to
15 points, up to 6 cm; white through white) and the body through in a few places by 2 to 4 cm (mostly the back of
the neck); the back of the collar shows H-01's neck actuators by up to 2 cm in the wave;
the skirt stretches a little between the legs in the walk (it follows the pants); the armholes stretch with the arms
up (the arms-down bind pose); the shoulders are big (H-01's deltoid actuators); nothing swings.

## 13. A second host: the Red Witness (2026-11-16, evening)

The user, after looking at the third iteration: "much better than the previous one"; a little clipping at the neck on
H-01's back and neck actuators ("tricky, the most non-human part of our model"); the belt better, no clipping at the
waist; "only a bit of clipping of the pieces going down while walking"; and "try to put on a gi on the Red Witness, it
might have an easier body shape to drape".

**The Red Witness on the kit** (`red_witness_rig`). The figure was static, with its own scripts. It now has the kit's
skeleton at its own joints, which are the action showcase's joints (the showcase was recorded on this figure: leg
scale 1.000, same ankles and soles), its rigid parts skinned to their bones and the bundles that cross a joint
stretching between two, the AUREL hands and feet in their palette colours, and the helmet's baked texture set carried
through the kit's bake by sampling it through the helmet's own UVs. 95,523 triangles, as the static figure.

**Easier? No: harder in a different place.** The neck is not the problem it is on H-01 (the collar wraps the base of
the helmet cleanly). The arms are. Measured at rest, the forearms hang 4 cm from the thigh muscles, and a sleeve, a
skirt and the pants need about 11 cm; on H-01 the gap is 8 to 13 cm. The first jacket came out a sack with balloon
sleeves, and the pants came through the sleeves in Idle. Four faults, each found by measuring:

- the proxy gave each body point to the nearest bone, so the forearms took the pelvis wings beside them (sleeves cut
  round those). Giving each point to the bone that moves it made the arm take the latissimus that runs to it. What
  worked: the nearest of the bones that move it (`PROXY_BY = "moved"`);
- no room for the sleeves: the dressed figure holds its arms 10 degrees out, at rest and in every motion (as a heavy
  body in a thick gi does; `red_witness.variant(10)`). Cloth inside the body after the drape: 488 sample points, then 5;
  body poking through: 91, then 5;
- a lumpy drape: each proxy ring is now the convex hull of its slab of the body (the cloth bridges the hollows between
  the bundles); and the jacket is built with the arms only 12 degrees further out than they settle (25 made the
  shoulders buckle into a fold across the back; worst stretch 35, then 11). Stiffer cloth (bending 40) changed little;
- the jacket's back still shows one broad diagonal fold, shoulder blade to armpit, as a real gi does with the arms down,
  only deeper on this barrel of a back.

**Then the dress step** (now `kit/menagerie/dress.py`, shared with Humanoid in a Gi, which rebuilds identically):

- the motion check itself was wrong twice more: depth was measured along a 2 cm triangle's own normal, which swings
  round when its corners move apart (a pants point behind two layers of cloth read as 10 cm through); now the smoothed
  vertex normals. And the helmet beside the collar was tested as if under it; parts the gi never covers are not tested;
- the abdominals came through the crossed fronts in the rolls: the cull kept the body near every lapel edge, even
  edges lying on the other front with two layers over the body. Now only edges with no other layer behind or over them
  are openings (`openings="seen"`);
- that hid the chest in the V: on this deep chest the lapels stand 11 to 13 cm off it, so the chest was culled and the
  V showed the inside of the jacket. A body point that sees a lapel or neck edge within 15 cm, with no cloth between,
  now stays (the pectorals, ribs and sternum show in the V, as a chest does);
- the spine's rod runs up into the helmet but was rigid to the spine bone: it came through the back of the collar when
  the head tucked. It now bends with the chest and the neck; the neck straps follow the neck, as the collar does;
- the lower sleeves had copied up to 24% of the thigh's weight from the hip beside them, and bunched at the elbow in
  the jog: the sleeves now follow the arm's bones only (`sleeve_bones`);
- below the crotch the skirt's centre blend is only 3 cm wide (the pants there follow one leg each);
- **the user's "pieces going down while walking" were the belt's tails**, two faults. In motion: solved smoothly down
  from the knot, the tails lagged the skirt they lie on, and its front swung through them; from the hip down they now
  copy the skirt's weights at their own height (blended in over 10 cm, one weight across the ribbon's thickness: the
  first try, per vertex over 5 cm, kinked a tail lying over the double front flap). At rest: the collar band running
  down the front edge (1.3 cm thick) came through the tail lying across it; the band is now removed where a piece of
  the belt lies on it. Measured with a new test in the check (tails sinking into the skirt):

| belt tails into the skirt | H-01, before | H-01, now | Red Witness, before | Red Witness, now |
|---|---|---|---|---|
| walk / jog / run | 1 / 2 / 1 points, 8 to 12 mm | none | 0 / 8 / 4 points, to 16 mm | none |
| crouch, jump | 40, 58 points, 35 to 40 mm | 26, 32 points, 35 to 36 mm | 50, 97 points, 23 to 38 mm | 14, 64 points, 9 to 11 mm |
| rolls | 56 to 63 points, 36 to 52 mm | 31 to 40 points, 35 to 41 mm | 89 to 99 points, 46 to 57 mm | 57 to 64 points, 12 to 15 mm |

**The Red Witness in its gi, checked** (`motion_check.py -- module=red_witness_gi/scripts name=red_witness_gi stress`):

| | body through the gi | pants through the jacket | stretch p99 (pants / jacket) |
|---|---|---|---|
| at rest | none | none | 1.0 / 1.0 |
| Idle, Wave, Crouch | none | none | 1.0 to 1.6 / 1.1 to 3.5 |
| Walk, Jog, Run | none | 1 to 3 points, 42 to 51 mm (behind two layers: not seen in a red-pants render) | 2.2 to 2.5 / 2.1 to 2.9 |
| Box, Jump | none | 1 to 3 points, 12 to 24 mm | 1.4 to 2.2 / 3.6 to 5.2 |
| rolls | none, but 2 points in the back roll (95 mm; the neck strap tied to a patch 11 cm off: probably the check) | 3 to 6 points, 27 to 48 mm | 2.4 to 3.6 / 4.0 to 6.0 |
| stress: arms up / arms forward | 32 / 1 points (the pectorals in the V push a lapel out) | none | 1.0 / 4.5 to 6.0 |

LOD0 98,657 triangles (inside the hero budget; Humanoid in a Gi is 121,060), LODs 50 / 25 / 12% with silhouettes at
0.977 or better; 7.6 px/cm. On the floor the rolls put the gi 9 to 12 cm under it (recorded for the bare figure, whose
own lowest point is 4 cm under; the gi stands off its back by 6 to 10 cm).

Still wrong: the jacket reads boxy and the sleeves big (the figure's barrel chest and thick arms, plus ease); the back
fold is deep; the pants balloon at the knee over the big calves; nothing swings; the rolls sink the gi into the floor;
the arms are held 10 degrees out in every motion, which the bare figure does not do.

## 14. The host changed: H-01's neck (2026-11-17)

The user's first note on the third iteration was "a little bit of clipping at the neck on the back/neck actuators, but
these are tricky and the most non-human like part of our model". The next day they changed the host rather than the
gi: H-01's trapezius rose at about 49 degrees from the top of the shoulder to the back of the skull, and the
sternomastoid from the sternum to behind the ear, so four actuators passed up through the collar ring. Both pairs now
take hold of the foot of the neck column (the top of the spine): the trapezius runs almost level, about 10 degrees,
and the sternomastoid runs up from the sternum to the front of the column (`humanoid_h01/README.md`). Then the gi was
checked twice, the same check as section 12:

| | 2026-11-16 (old neck) | new neck, jacket as draped before | new neck, jacket draped on it (built) |
|---|---|---|---|
| body through the gi: Box, Wave | 2 and 5 points, to 21 mm (back of the collar) | none | none |
| body through the gi: arms up, out, forward | 4 to 6 points, to 48 mm | 0 to 2 points | none |
| body through the gi: rolls | 10 to 15 points, to 38 mm | 4 to 6 points, to 22 mm | 4 to 7 points, to 38 mm |
| pants through the jacket: rolls; jump | 8 to 14 points, to 53 mm; 7 points, 39 mm | the same | 4 to 7 points, to 34 mm; 1 point, 25 mm |
| belt tails into the skirt: rolls; crouch | 31 to 40 points, to 41 mm; 26 points, 35 mm | the same | 21 to 33 points, to 21 mm; 14 points, 7 mm |
| clips with anything through (of 13) | 10 | 5 | 5 |

The neck alone emptied the collar: nothing comes through it in any everyday clip or stress pose. The redrape, made
round the new neck, gave a smaller neckline (the collar band 1,200 vertices against 1,360) and halved the layer and
belt problems, which shows the old jacket had been shaped round the old actuators all the way down. What the rolls
still show sits in two places, and only at their extremes: the wrist collar, which moves with the hand, under the end
of the sleeve when the wrist bends hard; and the trapezius's inner ends, now under the back of the collar, when the
neck bends hard in the front and back rolls. Pictures: `gi_j01/renders/qa/h01_collar_before_*.png`,
`h01_collar_after_*.png`; numbers: `gi_j01/renders/motion/motion_check_2026-11-17_newneck.txt`.

## 15. Sources

- [IBJJF uniform rules](https://ibjjf.com/uniform)
- [IJF, Judogi: much more than armour](https://www.ijf.org/history/judo-culture/2244)
- [Judogi (Wikipedia)](https://en.wikipedia.org/wiki/Judogi), [Keikogi (Wikipedia)](https://en.wikipedia.org/wiki/Keikogi), [Brazilian jiu-jitsu gi (Wikipedia)](https://en.wikipedia.org/wiki/Brazilian_jiu-jitsu_gi)
- [IJF judogi manufacture regulation, 2015 (PDF)](https://78884ca60822a34fb0e6-082b8fd5551e97bc65e327988b444396.ssl.cf3.rackcdn.com/up/2020/03/DOC_JUDOGI_ENG_24_10_2014-1583-1583493489.pdf)
- [M. Mueller & Sohn, unisex judo jacket pattern](https://www.muellerundsohn.com/en/shop/pattern-unisex-judo-jacket/)
- Commercial gi construction: [93 Brand](https://93brand.com/products/slate-camo-black-jiu-jitsu-gi), [Breaking Muscle gi-pants review](https://breakingmuscle.com/submission-fight-co-aura-gi-pants-product-review/)
- Blender manual: [cloth shape (pinning, sewing)](https://docs.blender.org/manual/en/4.1/physics/cloth/settings/shape.html), [physical properties (5.0)](https://docs.blender.org/manual/fi/5.0/physics/cloth/settings/physical_properties.html)
- [Robust Skin Weights Transfer via Weight Inpainting (Abdrashitov et al., 2023)](https://www.dgp.toronto.edu/~rinat/projects/RobustSkinWeightsTransfer/), [reference code (MIT)](https://github.com/rin-23/RobustSkinWeightsTransferCode)
- [Roblox layered clothing: caging and hidden surface removal](https://create.roblox.com/docs/avatar/layered-accessories/specifications), [layered clothing example](https://create.roblox.com/docs/resources/beyond-the-dark/layered-clothing)
- A-pose and shoulders: [Reallusion skin rigging notes](https://manual.reallusion.com/Character-Creator-4/Content/ENU/4.0/05_Character/Skin-Rigging-Improvements.htm), [Neural4D comparison](https://blog.neural4d.com/comparisons/a-pose-vs-t-pose/)
- [arjun988/blender-skills (MIT)](https://github.com/arjun988/blender-skills): cloth-sim, rigging, qa-review, lod-pipeline, validation checklist, polycount budgets
- [GarmentCode: programming parametric sewing patterns (Korosteleva and Sorkine-Hornung, 2023)](https://arxiv.org/abs/2306.03642)
- [senshu-jerseys judogi (John Morris, MIT)](https://github.com/johnmorrisdotca/senshu-jerseys/blob/main/docs/judogi.md); [Sloyd MartialArtsGi (EdgeForge, CC BY 4.0)](https://www.sloyd.ai/free-3d-models/model/martialartsgi-5wsmizl3)
- Baking cloth to bones: [BoneBake](https://superhivemarket.com/products/bonebake), [Blender DemBones](https://blendermarket.com/products/blender-dembones)
