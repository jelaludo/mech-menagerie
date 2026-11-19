# Kit reference

The shared Python package is `kit/menagerie/`. An animal imports it and supplies numbers.

| Module | Holds |
|---|---|
| `core.py` | mesh container `MD` (its `vw` holds explicit skin weights for chosen vertices, carried through `merge`, `transformed` and mirroring; they replace what the tags give), interpolation, plates, lathes, tubes |
| `parts.py` | the mechanical parts |
| `anatomy.py` | assemblies made of parts; `meander_band` (a band round a limb, or an arc of one, with a raised Greek key) and `horn` (a curved carbon horn on a root coupler, a reinforcement ring and a braided bundle) came with the Minotaur, `cloven_hoof` (two claws with pads, dew claws) with the stag, `hoof_block` (the Minotaur's hoof unit, out of its file); with the Medusa: `meander_band(..., squash=)` (an oval band: a waist's belt), `meander_strip` (the key down a flat panel) and `shield(windows=0)` (a plain dome); with the Iguana: `crest_blade` (a swept blade of a lizard's crest, a yellow streak down its leading edge if asked) and `dewlap` (a pleated fan under the throat, its plates either side of the mid-plane, on a bone that swings it out) |
| `materials.py` | weathered materials and the shared palette |
| `rigging.py` | skeleton, skin, leg solver, clip baking; `Rig.fk(pose, loc)`: where every bone is for a pose (world rotation and head), as the baked armature puts it |
| `structure.py` | things the animals stand beside: `bundle_post`, `girder`, `plinth` |
| `manus.py` | the hand: `Hand` (its numbers, bones, geometry, contact points and collision shapes) |
| `pes.py` | the foot: `Foot`, a `Hand` whose toes lie on the ground under an ankle, with a heel, an arch and an opposable great toe |
| `materials.py` (cloth) | `C_gi` (matte cotton twill) and `C_belt` (black cotton) join the palette for clothes |
| `materials.py` (paint) | `C_paint` (`parts.PAINT`): worn yellow paint over steel, chipped to the metal at its edges (the Iguana's streaks) |
| `skinning.py` | robust skin-weight transfer for anything worn (after Abdrashitov et al. 2023): `body_source` (a host's parts as a weighted surface), `garment_source`, `match` (within 5% of the garment's diagonal and 35 degrees), `inpaint` (Dirichlet plus Laplacian energy, numpy conjugate gradients), `smooth`, `spread` (widen joint blends), `centre_blend`, `copy_closest` (`zscale`: copy from the same height), `limit` (4 bones), `transfer` (all of it, layer by layer), `pose_points` / `unpose_points` |
| `garment.py` | clothes drafted from a host's own measurements and draped with Blender's cloth (see `docs/STUDY-GI.md`): `Host` (a built model as a body: segments, `fk`, `measure`), `Host.proxy` (smooth collision tubes round the covered bones), `Slicer` (exact proxy sections), `Pants` and `Jacket` (a gi, built closed round the proxy in a construction pose), `push_out`, `drape` (hold, move to the drape pose, settle), `envelope` (a smooth tube round a draped garment, for the next layer), `relax` (Taubin smoothing of a drape: crinkles out, folds kept), `Jacket.finish` (collar band and belt). `Rig.skin` takes a part's explicit `weights` (cloth). `inside_mesh` (three-ray majority), soft pins, an inside tie, the lapel table on body landmarks; `Host.fk` takes root and bone offsets. A host module may set `PROXY_BY = "moved"` (each body point belongs to the nearest of the bones that move it: a forearm hanging beside a wide pelvis does not take the pelvis) and `PROXY = dict(hull=True, smooth=4)` (each proxy ring the convex hull of its slab: cloth bridges the hollows between muscle bundles) |
| `dress.py` | a host wearing a draped garment set: `Dress(H, data, prefix, ...)` skins every layer with the robust transfer (pants from the body, jacket from the body and the pants, band and belt from the jacket), unposes it to the rest pose, gives it thickness and UVs, removes the pants under the skirt and the body under the gi (`cull`), and its `build` is a dressed model's; settings for a host: `knee_fade`, `skirt_centre_low`, `sleeve_bones` (the sleeves follow the arm only), `openings="seen"` (only edges that show the body are openings; a body point that sees a lapel or neck edge within 15 cm stays). `rig(H, u)` adds the `gi` bone. Used by `humanoid_gi` and `red_witness_gi` |
| `cockpit.py` | `Cockpit`: a pilot's pod for a seated H-01-sized humanoid (octagonal ribbed shell, reclined seat, harness, grips, footrest); origin at the pilot's hip point. `seated_pose(H)` seats H-01 in it; `Passenger(H, pose, hip_at)` carries a humanoid inside another model, bound in that pose, its bones renamed and hung from a host bone, with `pose(T)` to animate it with poses written for its own skeleton |
| `modules.py` | parts that fit more than one body: `Claw`, `Pincer`, `StingTail`, `Tentacle`, their motion objects, `equip` / `loadout` / `frame` / `mount`, `coupling`, and `Equipped`, which lays module poses over a host's clips |
| `arma.py` | weapons and shields: `spear` (carbon shaft, braided grip, a leaf-shaped steel head with barbs, a butt spike) and `round_shield` (domed perforated carbon, ribs, a steel rim and boss, grip posts behind); each on a bone of its own, hung from the hand or forearm that holds it (`PARENT["spear"] = "forepaw.R"`: a one-sided bone may name a mirrored bone's side) |
| `feet.py` | every foot behind one interface: `feet.make(name, k, splay, detail)` for `plain`, `hoof`, `cloven`, `hoof_block`, `dragon`, `paw`, `pes`, `ape`, `heel` gives a `Footing`: `h` (the ankle height it needs), `limbs`, `build(seg, q, ankle)`, `mount(PIVOTS, PARENT, ankle)`, `contacts`, `tips`, `reach` (how far a step should roll over it) and `fit(MOTIONS, RIG, ankle, air=())` (toes kept down, a rigid foot planted, a floating clip settled, but not the flights named in `air`). A host's `foot` socket is three lines; a variant is `modules.equip(host, foot=name)` (the Oni's) |
| `calceus.py` | the high-heeled boot: `Boot(k, ball, toe, back, rise, width, above, shaft, key)` in the kit's foot frame (`pes`'s: origin at the ankle, +Z to the toes, +Y up); `pivots(M)`, `parents(root)`, `build(seg, q, M, shin=)`, `tips(M)` (for `GroundedToes`), `contacts(M)` (for `Settled`); `standing(boot)` stands it under an ankle. Worn by the Medusa; a host stands on it by lifting everything above the ankle (the Valkyrie's `foot="heel"`) |
| `studies.py` | a kit part shown on its own (the hoof, cloven hoof, hoof block, toad's foot, cat's paw, dragon's foot, raptor's foot, the boot): `Study(part, joint, stub, up, toes, extras, ...)` stands it under a stub of its limb and gives it bones (`pelvis` the stub, `foot` the part, `toes` beyond a plane, extras); `module(NAME, U)` gives a model file `build`, `PIVOTS`, `PARENT`, `RIG` and `MOTIONS` (`motions.ExtremityStudy`) |
| `oculus.py` | the kit's eyes as modules: `eye(name, c, axis, r, q)` gives (housing, lens) for `skull` (OCULUS-01, the skull tables' optic), `mask` (OCULUS-02, the Medusa's flush optic, also `mask_optic`) and `compound` (OCULUS-C1) and `iris` (OCULUS-03, blades fixed open); `Iris(c, axis, r)` is the Beholder's iris eye in parts (`glass`, `shutter`, `back`, `blades` each for a bone of its own at `pivots[k]`, `close`), posed by `motions.aperture(out, bones, close, axis, e)` and timed by `motions.blinks(t, times)`; `Study(name, r)` stands one on a gimbal mount (a flange, a post with braided struts, a turntable `pan` with a yoke and lit pivots, the eye tilting on `eye`, its lens sliding on `lens`), `module(NAME, U)` gives a model file its names; `EyeStudy`: idle (quick looks held still), scan, track (a figure of eight, refocusing), focus; `IrisStudy` (the iris eye's): idle with blinks, blink, scan, track (stopping down), aperture |
| `lacerta.py` | a lizard's hand or foot: `Foot(k, digits, h, ball, heel, palm, toe_bone)` drawn standing (the joint at (0, 0, h), toes towards -Y); a carbon sole over metapodial rods and a heel pad on the foot's bone, five long clawed toes on a bone of their own hinged at the ball; `HIND` (the long fourth toe, the fifth set back and turned out, in the desert iguana's proportions) and `FORE` tables; `place(joint, splay)`, `pivots(M)`, `parents(root)`, `build(seg, q, M)`, `contacts(M)`, `tips(M)`. The Iguana's hands and feet |
| `crinis.py` | (`head_fn(snake, q)` draws something else at each chain's end: the Beholder's eye units) hair of snakes: `SnakeHair(paths, modules, bones, r, head, jaw, q, head_q, tall)` lays a small snake along each rest centre line (a lightened Snake S-01 module: a steel ring at each hinge, a braided core, a carbon band with a steel back plate; the Snake's skull tables, `tall` for a viper's deeper skull, the jaw open), each on its own chain of bones weighted smoothly along it, plus a head and a jaw bone; `pivots`, `parents(root)`, `bones`, `build(seg, q)` |
| `antler.py` | a stag's crown: `Antler(beam, tines, r, lattice, fork)` builds a main beam (a lattice of two rails and a zig-zag brace over part of it, a ribbed carbon shell elsewhere), tines branching off it (long ones forked), titanium ribs riveted in brass, lit sensor nodes, the root interface stack |
| `talon.py` | a raptor's foot built from the hand: `Talon` (three toes forward and splayed, a hallux back, three bones each with the hand's actuators and tendons, scutes, a hooked claw, a hub with a flexor under each toe) and `TalonMotion` (poses `stand`, `relax`, `open`, `grip`, `clench`, or any {toe: (spread, curl 1, curl 2, curl 3)}; each turn about the toe's own axis) |
| `pluma.py` | feathers: `feather` (a perforated carbon vane on a steel spine), `covert` (a braided actuator pressed flat into a leaf on a surface), `plumage` (rows of coverts over a `Column`) |
| `ala.py` | the bat's wing for a dragon or a wyvern: `Wing` (an arm of two caged bones, a hand of finger spars, a sail of braided sleeve with actuator bundles on it, a perforated frame along the scalloped trailing edge, claws, a hooked thumb), its bones, and `pose(state)` for any state of the wing (see below) |
| `motions.py` | stock motion sets: `Quadruped`, `Canine`, `Shisa`, `Ape`, `Silverback`, `Equine`, `Biped`; `Sprawler`, `Anuran` (the toad: walk, hop, jump, tongue), `Theropod` (the raptor: walk, run, slash, pounce), `Ursine`, `Mantid`, `Culicid` (the mosquito: six legs and flight); `Manus` and `ApeManus` (hands: poses, solved pinches and grips, the knuckle stance), `Pes` (the foot: ground contact, step, tiptoe, clamp grip), `WithHands` (adds mounted hands to any set); `Arachnid` (the eight-leg walk) with `Salticid`, `Scorpion`, `Octopus` and `Hexapod` (the six-legged crawler); `Serpent`; `Dragon` (a long chain body on four sprawling legs: walk, run, roar, take off, fly as a travelling wave, lie down; for a longer one, `tail_lift` and a sideways wave `fly_side` in flight, `rest_name="Sleep"`, and a snake's wave down the chain on the ground and in the WingedDragon's flight: `wave_yaw`, `wave_pitch` (degrees), `wave_lam` (metres), `wave_body` (how much the bones between the legs take), `wave_walk`/`wave_run`/`wave_idle`/`wave_fly`; off by default); `WingedDragon` (the Dragon with a pair of `ala.Wing`s: folded on the ground, thrown open to roar, opened and closed (`Unfurl`), beating in flight with the body held straight; `TakeOff` leaps, climbs, flies and lands); `Aquiline` (an eagle: the raptor's planar four-joint legs, a `FeatherWing`, talons; idle, walk, mantle, take-off, fly, glide, strike, a talon showcase, sleep); `WingWalker` (a wyvern: the wings are its fore legs, each arm reached to a clawed hand on the ground with the elbow up, the fan folded behind the forearm; it rears to spread them, takes off and lands on its hands); `Frame` (a piloted frame: the biped set slowed, plus a hatch); `Taurus` (a heavy hoofed biped, the Minotaur: walk, charge with the head down, roar, stomp, smash; hooves land flat and roll about the toe); `TalonStudy` (a study talon on the ground: idle, showcase, step, knead, strike, grip); `WingedSpearman` (the Spearman with `FeatherWing`s on its back: every clip with the wings folded, plus Unfurl, Fly and Glide, flying lying forward); `GroundedToes` (over any set: the toes of a worn foot turn up at the root just enough to keep their tips on the ground as the heel rises; H-02's PES-01); `Cervine` (the stag: the horse's gaits plus an alert idle, graze, bugle, spar and a bounding leap with the legs tucked); `Boar` (the Rhino's set at a boar's numbers, after docs/STUDY-BOAR.md: idle with a snout toss, walk, trot, charge, gore (the head lowered, a lunge, slashes up to each side), root); `Rhino` (after docs/STUDY-RHINOCEROS.md: short pillar legs on toed feet carried level; idle, walk, trot, charge (a rotary gallop with suspension, the head lowered), ram (lunge, impact, the horn hooked up), scrape (the hind feet dragged back in turn), graze); `Stegosaur` (after docs/STUDY-STEGOSAURUS.md: short forelegs and long hind legs, the big feet carried level in the swing (`toe` < 0, `follow` 0); `tail(out, yaw, pitch, curve, ...)` swings a long tail as a whole; idle, walk, charge, graze, swipe (the tail slashed across from the hips), threat); `Giraffe` (after docs/STUDY-GIRAFFE.md: the horse's legs, the neck on four bones bent by `cervix`; a walk in lateral couplets with the neck swinging twice a stride, no trot, a rotary gallop with the hind legs carried wide and swinging outside the forelegs, browse, drink (each foreleg solved long in its plane, then rolled out onto the floor), survey); `Spearman` (a biped with a spear and a shield: locomotion from another set, recorded or not, with the arms held; its own idle, point ("stop"), toss (into the javelin's hold), thrust and salute; `arm` sets an arm by where the upper arm, forearm and hand point, which way the shield faces and the spear lies; two grips, carried and overhand (end for end in the fist), changed only in the air by `flight`, which tosses the prop's bone from hand to hand: the hand opens, the prop rises and falls under gravity, drifts and turns early with its butt swung out, and drops into the catching hand; `blend` turns a hand the short way round as a whole); `Recorded` (with `scale` for a larger figure and `only` to pick clips) and `Combined` for retargeted clips; `Oni` (the Minotaur's `Taurus` with a club on its own bone in the right fist, carried by a wrist angle in every clip, a one-handed `Sweep`, and `reach`, how far a step rolls over the foot it stands on); `TailAbove` (over any set: each tail bone turned up about its root when it would sink, root first); `Planted` (over any set: a foot turned up about its ankle, easing in, whenever some of it would go through the floor); `Settled` (over any set: each clip lowered by a constant so its lowest contact just touches: a walk recorded for flat feet worn on heels; `air=` names flights it leaves alone); `ExtremityStudy` (a part on its own under a stub: idle, step, flex, stamp, its lowest point kept on the floor); `Reaching` (what `Spearman` is built on: a biped whose arms are set by direction, `arm`, `blend`, `seq`, `hold`, `carried`); `Heads` (several heads on necks of their own, the Cerberus's and the Hydra's: each turns to a plan of marks and holds them (`marks`), weaves on a slow wave up its neck (`sway`), is turned by `gaze(out, i, yaw, pitch, roll, jaw)` on top of the body's pose; `every` and `cyc` time rhythms that loop; `copy_base` gives every head the base set's nod for the first); `Cerberus` (the dog's gaits with three heads, each with its own plan, sniffs, panting and snaps; idle, walk, trot, run, bark, guard); `Hydra` (the Dragon's body and gaits with three necks: idle, walk, run, acquire, roar, sleep); `Coiling` (the Medusa's snakes' code as a mixin: chains of bones that `writhe` and are `aimed` at a mark, coiled or struck out, posed together by `snakes`; Gorgon and Beholder use it); `Beholder` (a hovering sphere: the body carried by `loc`, an `eye` that aims, a `jaw` that drops, eye-stalks by `Coiling`, and a lid that is a camera iris diaphragm, `lids` = (blade bones, closing turn): `aperture(out, e)` turns every blade about its pin round the eye's axis, `blinks(t, times)` snaps it shut and open; hover, blink, scan, aim, drift, gaze, bite); `Recon` (B-REK44, after the user's B-REK43: a `Reaching` biped with recorded walk, jog and run leaned forward, a lowered stealth walk of its own, idle, survey, observe (a low crouch) and a halt signal that brings the forearm forward before raising the arm, so the elbow's bend turns with it); `Pegasus` (the horse's gaits with a pair of `ala.FeatherWing`s carried on the ground, folded in the gallop; a rear with them thrown up, unfurl, take-off on the stag's leap and fold, flight and glide; `AIRBORNE`); `Archfiend` (the Great Devil: `Taurus` with a pair of `ala.Wing`s mounted down the spine and a tail; the wings in a mantle on the ground, folded like a cloak in the charge, thrown up in the roar and smash; its own idle, display, take-off (crouch, leap, climb, hover, land) and flight; `wpose(s, side, flap, lift)` turns a whole wing about the body's long axis or up in the frontal plane whatever its state; `AIRBORNE` names its flight for the floor wrappers); `Iguana` (a `Sprawler` on lizard's feet, after docs/STUDY-IGUANA.md: each foot placed by its ball, turned out on it and its heel lifted late in the stance, the toes counter-turned flat until it leaves; the hips and shoulders each furthest back just before their foot lifts; the head steadied; walk, pause (breathing and a signature bob between bouts), run, display (push-up, dewlap, roll, shudder, signature bob), tail whip, bask); `Gorgon` (the Medusa: `Reaching` with a `crinis.SnakeHair`: every clip writhes the snakes, waves running down each with neighbours nearly in step, each wave a whole number of turns in its clip; `aimed` rears a snake at a mark, coiled in an S or struck out at it, each bone turned as a frame (a least turn flips), bowed away from her head, the marks spaced round a halo; her own idle, gaze (the stare), strike, slash; locomotion from `Recorded` with the arms held out and swinging) |
| `pipeline.py` | the build from animal module to exported asset; a model's `PROPS = {bone: label}` (the Oni's kanabō) become toggles in the viewer, which shrinks a put-away prop's bone to nothing; `unflatten_uv` gives a triangle that lies flat in UV its own unwrap before the islands are scaled (one sliver otherwise fills the sheet) |
| `gltfmend.py` | mends exported glTF files in place: a zero-length tangent (a decimated triangle with no UV area) becomes one square to its normal, or three.js shades NaN and the glow spreads black squares; the pipeline runs it on every export, and `python3 kit/menagerie/gltfmend.py <files>` on old ones |
| `checks.py`, `../check.py`, `../ground.py`, `../overlap.py`, `../snap.py`, `../regress.py` | clearance and ground checks at rest; lowest point during every motion; muscles cutting through the body; bones that jump between two frames (a flash in the viewer); regression against a saved baseline |
| `../strip.py`, `../tune.py` | a contact sheet of one motion (many small frames on one image); the ground and overlap checks for several trial settings of a motion at once |
| `../compare.py` | the animal rendered exactly as its concept sheet's orthographic views are drawn (direction, scale, ground line), beside the sheet and outlined over it, with both silhouettes measured (from `SHEET` in the animal's file) |
| `../lod_check.py` | the exported LODs against LOD0: triangles, materials, new holes, silhouette overlap from the front and the side, one sheet (`renders/lod_check.png`); part of the QA gate (`docs/QA.md`) |
| `../retarget.py` | copies recorded motions from another humanoid rig (a GLB) onto an animal; writes `scripts/motions.json` |
| `../../gi_j01/tools/` | the gi's own tools: `drape.py` (draft and drape a garment for a host, or for the `HOST` a dressed model names, with the host's `PANTS` / `JACKET` / `FABRIC` settings), `motion_check.py` (`name=`: which dressed model; clips through the cloth, stretch, at rest), `proxy_check.py` |
| `../viewer/` | the shared web viewer |

## Parts (`parts.py`)

Every part returns an `MD` with metric UVs. `q` is the tessellation scale (1.0 = LOD0).

| Part | Call | Notes |
|---|---|---|
| Braided actuator | `actuator(a, b, r, q, bow, belly, rod_min)` | fusiform sleeve between steel ferrules, rod ends and eyelets; `bow` curves it sideways; `rod_min` is the thinnest a rod end may be (2.6 mm for animals, 0.9 mm on the hand) |
| Joint module | `joint(center, axis, r, half, q, plain, mat)` | dark housing, bright bearing face and centre bolt; `plain=True` is a simple barrel |
| Link plate | `link(a, b, n, ra, rb, waist, thick, q, slot)` | dog-bone between two joints in the plane with normal `n`; optional lightening slot |
| Flat bar | `ribbon(path, w, t, up, mat, ch, cap)` | chamfered rectangle swept along a path; `w` and `t` may vary along it |
| Perforated shell | `shell_plate(outer, lift, thick, holes, grid, mat)` | double-sided plate with through holes on any surface |
| Ring | `ring(center, axis, r, half, q, mat, bore)` | collar around a bar or barrel |
| Claw / fang | `spike(base, tip, bow, r, q)` | curved, tapering |
| Pill | `capsule3(a, b, r0, r1, q, mat)` | toe and finger bones |
| Outlines | `blob(...)`, `spline2(points)` | rounded cut-outs; smooth closed outlines |
| Lifts | `surf_lift(fn)`, `plane_lift(a, b, n)` | put a 2D outline onto a surface or a plane |

## Assemblies (`anatomy.py`)

`u` is the animal's size factor (1.0 = feline). `seg` is the `{segment: MD}` dict being filled.

| Assembly | Call | What the animal supplies |
|---|---|---|
| Trunk section | `Trunk(spine, stations, half_width, belly)` | keel line and the cage section at about ten stations |
| Dorsal keel | `keel(seg, trunk, ranges, q, u)` | one `(segment, y0, y1)` per rigid piece of the spine |
| Rib hoops | `rib_hoops(seg, trunk, ys, q, seg_at, u)` | hoop stations |
| Sternum | `sternum(seg, trunk, ranges, q, u)` | ranges |
| Trunk muscle rows | `torso_rows(seg, trunk, rows, q, seg_at, bone_at, u)` | per row: degrees from the top, radius, spans |
| Muscle set | `muscles(seg, table, q, limbs)` | `(start, end, radius, bow, (bone, bone))` per muscle |
| Joint frame | `FrameSurface(...)`, `a_frames(surf, outline, hole, joint, q, thick, r_boss)` | outline and opening in (y, arc length); `remap()` reuses another animal's outline |
| Head | `Skull(Y, A, B, C, TM)`, `head(H, q)`, `head_matrices(...)` | station tables, cut-outs, optic, teeth, hinge, mandible path |
| Paw | `paw(knuckle, q, k, toes, fan, claws, claw)` | digitigrade: toes with claws, knuckle axle, pad; `claw` scales the claws (0.5 = a dog's blunt nails) |
| Hand | `hand(wrist, q, k, palm_t)` | knuckle-walking fist: palm, four folded fingers, thumb; `palm_t` thins the palm block |
| Foot | `foot(ankle, q, k, length, width, toes, claws)` | plantigrade: sole, instep wedge, five toes, optional claws |
| Open hand | `open_hand(wrist, q, k)` | hanging hand: palm plate, four curled fingers, thumb |
| Digits | `digit(...)`, `finger(points, radii, q)` | straight toe; finger through explicit joints |
| Tail | `tail(seg, points, n_seg, n_groups, q, r0, r1)`, `tail_stations(...)` | centre line; one bone per barrel |
| Column | `Column(line, half_width, half_depth)`, `column_hoops`, `column_bar`, `column_rows` | a curved volume with hoops square to its own axis: the horse's neck, the humanoid's torso. `column_bar(collars=True)` threads vertebra collars onto the bar (the horse's neck crest), `column_bar(mirror=True)` gives side stringers and `column_rows(bowed=True)` curves the muscles to the wall: together a lattice shell (the spider's abdomen) |
| Hoof | `hoof(fetlock, q, k)` | fetlock joint, pastern, slanted hoof with a steel shoe |
| Carapace | `carapace(trunk, y0, y1, half_deg, q, along, across)` | perforated dorsal shell over a stretch of trunk; several short ones in a row make segmented back plates (pass `grid` for narrow plates) |
| Tongue | `tongue(root, direction, q)` | forked, built extended; a motion slides its bone back |
| Blade | `blade(path, w, t, up, q, teeth)` | flat blade with a row of teeth on one edge |
| Compound eye | `compound_eye(center, axis, r, q)` | large lit dome on a steel bezel |
| Antenna | `antenna(base, tip, bow, q, r, whorls, hair)` | long whip on a ball joint; `whorls` sets rings of bristles along it (the mosquito's) |
| Insect leg | `insect_leg(seg, names, hip, knee, ankle, toe, q, k, acts)` | hip module, slim femur plate with one or two actuators, knee, tapering tibia, clawed foot; the mantis's walking legs and all six of the mosquito's |
| Wing | `wing(root, span, chord, length, width, q, k, veins, profile)` | open-framed insect wing: leading-edge spar, trailing rim, two long veins, fan veins and cross-veins, a hinge at the root. No membrane: the texture set is opaque |
| Proboscis | `proboscis(base, tip, q, k, sheath)` | telescopic piercing mouthpart: returns the stepped sheath (fixed to the head) and the needle with its lit sensor ring (its own bone, which a motion slides) |
| Insect foot | `tarsus(ankle, direction, q, k)` | two pads and a hooked claw |
| Shield | `shield(center, normal, up, rx, ry, q, bulge, windows, standoff)` | a broad domed perforated carbon plate on steel posts, in the manner of the horse's shoulder frame: armour in the kit's own language |
| Dragon parts | `dragon_foot(joint, toe_y, q, k, splay, toes, fan, claw, dew)`, `lit_joint(md, c, axis, r, half, q)`, `dorsal_spike(md, base, up, back, L, r, q, out)` | the dragons' clawed foot at any size (k = 3.1 is the Kami Dragon's; it is also the wyvern's hand), a joint with a lit ring, a crest spike leaning back |
| Caged limb | `cage_limb(a, b, r0, r1, q, up, rails, acts)` | one limb segment as an open cage: carbon longerons bowed round actuators lying side by side, collars at the ends, a hoop at the middle (the crawler's) |
| Foot tip | `probe_foot(ankle, toe, q, r)` | a walking machine's end-effector: ankle joint, tapering nose, a lit strip down each side, a traction pad |
| Grasping hand | `claw_hand(wrist, direction, q, k, fan)` | a theropod's: a short palm and fingers fanned by the angles given, each two bones curling under to a hooked claw |
| Sickle toe | `sickle_toe(knuckle, q, k)` | a dromaeosaur's raised second toe and its sickle claw, built retracted; give it its own bone at the knuckle and turn it to slash |
| Pad foot | `pad_foot(joint, direction, toes, q, k, pad)` | an amphibian's splayed hand or foot: a short palm, digits fanned by the angles and lengths given, each two slim bones with a tendon actuator, ending in a round compliant pad |
| Camera eye | `camera_eye(center, axis, r, q, length)` | dark barrel, steel bezel, lit lens set back in a hood |
| Muscle link | `muscle_link(md, a, b, n, q, k, r, ra, outer)` | one arthropod limb segment: a link plate with a fat actuator on each face, optionally a third along one edge |
| Pincer | `chela(wrist, tip, out, q, k)` | hand swollen with four actuators, fixed finger and movable finger as serrated blades; returns hand, finger and hinge |
| Snake module | `serpent_segment(a, b, r, q, twist, bars)` | one body module between two joints: hinge, core, four actuators, and a cage of two collars joined by bars winding both ways |
| Ornament | `curl(base, direction, normal, stem, r, q, turns, size, hand, flat)`, `lock(a, b, r, q, ...)`, `mane(center, back, rows, q)` | a bar that winds into a spiral (round, or flat like one cut from plate); an actuator ending in a curl; rings of such locks round a neck |
| Arm module | `tentacle_segment(a, b, r0, r1, q, down, cups, tip)`, `suction_cup(center, axis, r, q)` | one tapering module of an octopus arm: hinge, core, three actuators, a collar, lit suction cups on a strip underneath; `tip=True` is the solid curling end |
| Bundle tail | `bundle_tail(seg, points, n_seg, n_groups, q, r0, r1, sting)` | fat segments, each four actuators round a core inside two hoops, with a hinge between; the last one is the sting (bulb, lit collar, curved point). Same bones and layout as `tail` |
| Arachnid leg | `arachnid_leg(seg, names, hip, knee, ankle, toe, q, k, power)` | femur and tibia each a link plate between two fat actuators, tapering metatarsus, two-claw foot; `power` adds a third actuator (jumping legs) |
| Ring frame | `oval(cy, cs, ry, rs)` | elliptical outline or opening for `a_frames` |
| Helpers | `lateral(a, b)`, `plate_normal(a, b, ref)`, `tagged(md, a, b, bone, bone)` | link-plate normals; skin tag |

### The head spec

`head(H, q)` takes a dict in head-local metres (nose towards −Y); `M_HEAD` scales and places it.

| Key | Value |
|---|---|
| `skull` | `Skull` tables: stations, half width, crown height, depth below, lower rim angle |
| `cuts`, `mids` | paired cut-outs `(y', degrees, half length, half width, rotation)`; midline ones `(y', half length, half width)` |
| `nose` | block origin, normal, width, height, depth, chamfer, base |
| `barrel`, `occiput` | sensor barrel inside the skull; neck joint disc |
| `optic` | `(y', degrees, inset, aim vector, scale)` |
| `fang`, `teeth` | upper canine `(y', length, radius)`; small teeth `(stations, length, radius)` |
| `hinge` | jaw joint `(x, y, z, radius, half width)` |
| `mandible` | path, height at hinge, height at chin, thickness |
| `lower_fang`, `lower_teeth`, `chin`, `closer` | lower teeth, chin bar, jaw-closing actuator |

## The hand (`manus.py`)

`Hand(k, digits, thumb, prefix)` holds one hand in its own frame: origin at the wrist joint, +Z along the fingers, +Y out
of the back of the hand, the thumb on the −X side (a left hand; mirror it for the right). `k = 1` is life size for a
two-metre figure. The default tables are four fingers (x, knuckle height, three bone lengths, width) and a thumb (base,
lean, three lengths, width); other tables give a broader or shorter hand.

| Call | Gives |
|---|---|
| `hand.pivots(M)`, `hand.parents(root)` | bone positions and parents to hand to `rigging.Rig`: `palm`, `<finger>_1..3`, `thumb_1..3` |
| `hand.build(seg, q, M)` | the palm and every finger bone into `seg`, placed by `M` |
| `hand.stub(q, root, M)` | the forearm coupling a study hand stands on, with the six actuators that cross the wrist |
| `hand.tip(digit)`, `hand.pad(digit)`, `hand.back(i, digit)` | contact points as (bone, point, normal): the soft end of a fingertip, its flat, the back of a finger bone |
| `hand.envelope(M)` | a capsule per finger bone and a box for the palm, for collision estimates |
| `Hand(k, manus.APE_DIGITS, manus.APE_THUMB, guards=True)` | a gorilla's hand: broad palm, thick even fingers, short low thumb, knuckle guards on the middle bones |
| `Hand(k, manus.SLENDER_DIGITS, manus.SLENDER_THUMB, claws=True)` | the Medusa's: fingers a sixth longer and a quarter thinner, each tip a hooked claw cap (a sleeve, a steel ring, a carbon cap and a steel point curving under) |
| `Hand(k, claws=1.8)` | the Great Devil's: the claw caps on MANUS-01's own fingers, the hooks 1.8 times as thick and a quarter longer, to read at a large size (a number in place of True) |
| `Hand(..., palm="forepaw", q=0.8)` | a hand to mount on a model: the wrist bone keeps the model's own name so its motions still drive it; `q` lightens it |

**Mounting a hand.** Give the hand a frame on the model (`manus._frame(wrist, lateral, dorsal, along)`: the hand's X,
Y and Z there), add its bones to the model's (`PIVOTS.update(HAND.pivots(M))`, parents from `HAND.parents("forearm")`
minus the wrist itself), list the finger bones among the mirrored limbs, and call `HAND.build(seg, q, M)` in `build`.
Wrap the motion set in `motions.WithHands(base, motions.Manus(RIG, HAND, M), {clip: (left, right)})`: each side is a
Manus pose by name ("relaxed", "fist", "knuckle", ...), a pose, or a function of time. The humanoid and G-02 are the
examples.

How the hand is driven, joint by joint: a pair of actuators lies along the back of each finger bone and each pulls a
steel tendon that runs over the next knuckle to an anchor bar on the next bone; an extensor and a flexor run from the
wrist block to each finger's root, again ending in a tendon over or under the knuckle; small spreaders sit in the gaps
between the palm bars; three fat actuators gather on the thumb's first bone; six cross the wrist. An actuator never
spans a joint itself: only tendons are skinned to two bones.

## The foot (`pes.py`)

`Foot(k, digits, thumb, palm="sole", h, heel)` is a `Hand` laid flat. Frame: origin at the ankle joint, +Z towards the
toes, +Y up, the great toe on the −X side (a left foot); the ground is at y = −h·k. The toes are the hand's fingers
(same bones, same back actuators and tendons); the great toe is its thumb, set back on the inner side. `Foot` overrides
three things: where a digit's axis sits (`digit_y`: on the ground), the body (`_palm`: ankle module, talus, tarsal
block, a sloping bar to each toe with an extensor above and a flexor under the arch, a cross bar and pads under the
ball, the heel on two struts, an outer-border actuator, the great toe's saddle and three actuators, an instep shell),
and the coupling (`stub`: a shin, with the heel tendon and two front actuators crossing the ankle).
`foot.contacts()` lists the points that can bear on the ground; `pes.standing(foot)` is the matrix that stands it on
a model's floor. Tables: `pes.TOES`, `pes.HALLUX` (the humanoid's), `pes.APE_TOES`, `pes.APE_HALLUX` (a gorilla's).

## The wing (`ala.py`)

`Wing(shoulder, rest, humerus, forearm, fingers, heel, k, bundles)` is one wing, the left, in the body's frame. The arm
(humerus, forearm) lies in one plane; the hand turns on the wrist and carries a fan of finger spars and a heel spar
lying back along the forearm. A pose is a **state**, a dict of degrees:

| Key | Meaning |
|---|---|
| `tilt`, `sweep`, `pitch` | how the arm's plane is turned on the body: from upright towards the side (90 = level), back about the vertical, the leading edge up |
| `arm`, `fore` | the humerus and the forearm in that plane, from its long axis towards the front |
| `flex`, `roll` | the wrist: the hand turned in the plane, and rolled about the forearm so the fan can hang outward |
| `fingers` | each finger in the hand, from the forearm's line towards the front (negative: behind) |

`body=` (on `Wing` and `FeatherWing`, a 3x3 rotation, identity by default) turns the frame the states are read in:
the Valkyrie V-02 mounts the eagle's wing with `Rx(-90)`, the bird's long axis down her spine, so the eagle's folded,
spread and beating states read on an upright body (folded down the back; spread sideways, feathers facing back) and,
when she lies forward, exactly as on the bird.

The wing is built in its `rest` state. `pose(state, side)` gives each bone's local turn (`wing_arm`, `wing_fore`,
`wing_hand`, `wing_f1..`) for any other state, the right wing mirrored; `blend(a, b, e)` mixes two states; `points(state)`
gives the shoulder, elbow, wrist, tips and heel, which is what a fit uses; `reach(target, pole, base)` is the state that
puts the wrist on a point with the elbow bent towards a pole (a wyvern stands on its wrists by it). `heel_on="fore"`
runs the last bay back along the forearm instead of a heel spar on the hand; `heel_on="shoulder"` runs it along the
forearm and the humerus to the body, its trailing edge meeting the flank `root` metres behind the shoulder (a bat's
wing, the wyvern's), skinned from the last finger to the forearm by angle and to the humerus past the elbow;
`thumb=False` leaves out the hooked thumb. Every vertex of a bay's sail and frame is
weighted by its angle between the two spars of the bay (through `MD.vw`), and each bundle by the angle of its axis, so
closing the fingers closes the sail like a fan. Build it spread: closing compresses the sail, opening a sail built
closed would stretch it and its texture. The Western Dragon's rest and folded states were fitted to the wrist and four
tips measured off its sheet (`docs/LESSONS.md`).

### The feathered wing (`ala.FeatherWing`)

The same arm, forearm and hand and the same state, with every flight feather a bone of its own: `prim` (on the hand),
`sec` (on the forearm) and `tert` (on the humerus) give each feather's angle in its bone's plane. Folding a wing is then
the bones' angles plus the feathers' (secondaries from -98 spread to -170 folded, primaries closing to -1..-7), as a
real wing's feathers turn on their roots. Primaries lie under the secondaries and tertials over them, so the folded wing
is a stack. The eagle's states (folded, raised, upstroke, glide) are in its own file.

## The talon (`talon.py`)

`Talon(k, toes, hallux, prefix, palm, h)` is the dexterous hand's digits laid out as a bird's foot: frame at the hub
(the end of the shank), +Z forward, +Y up, the inner side -X; the ground at y = -h. The palm bone keeps the leg's name
(`hindpaw`), so the leg solver still pitches the whole foot; each toe is `talon_<toe>_1..3`. `TalonMotion(talon, M)`
turns a pose into bone turns about each toe's own axis (a hand's poses turn about the hand's X, which splayed toes
cannot use). A standing foot cannot curl its toes without driving its claws into the ground: lift the claws instead
(`Aquiline.lifted_claws`), and curl only once the foot is clear.

For a study of its own (`talon_u01`, as the hand and the foot have): `standing(talon)` stands a left talon on the
ground facing -Y, `talon.stub(q, root, M)` is the eagle's shank cut short above the hub (a lit joint, a caged bone,
scutes, a yoke, extensors in front and the flexors' tendon behind), `talon.contacts()` the points that can bear on the
ground (pads and claw points), and `motions.TalonStudy(rig, talon, M)` the clips (Idle, Showcase, Step, Knead, Strike,
Grip), with `ground` keeping the lowest pad or claw on the floor (`Rig.fk` does the forward kinematics).

## Modules (`modules.py`)

| Call | Does |
|---|---|
| `Claw(k, root, prefix)`, `Pincer(k, root, prefix)`, `StingTail(k, path, n, prefix)`, `Tentacle(points, radii, bones, down)` | a part in its own frame; `root` can be a host's bone name, so the host's motions drive the joint it is mounted on. `Tentacle.standing(k)` is a straight one for its own card |
| `module.pivots(M)`, `module.parents(host bone)`, `module.build(seg, q, M)`, `module.bones` | the interface, the same as `Hand`'s |
| `frame(origin, along, dorsal)` | the matrix that puts a module at a point, running one way, its back another |
| `mount(PIVOTS, PARENT, LIMBS, module, M, host bone, paired)` | adds a module's bones to a host's tables, keeps parents before children, returns the new list of mirrored parts |
| `loadout(host, socket=default, ...)`, `equip(host, socket=module, ...)` | a host reads what it wears; a variant sets it before importing the host |
| `ClawMotion`, `PincerMotion`, `StingMotion`, `TentacleMotion` | named poses, `bones(pose, side)`, and the clips of the part's own card |
| `Equipped(base, [(motion, {clip: spec}, default, sides)])` | a host's motion set with its modules posed on top. A spec is a pose name, a pose, a function of time, or a pair of those for left and right |
| `coupling(q, root, child, k, M)` | what a study part stands on |

A host that takes modules needs three things in its file: a `LOADOUT`, a branch per socket that either builds its own
part or calls the module's `build`, and `mount` in place of writing the bones by hand. See `humanoid_h01` and
`mantis_s01`. `Rig(..., bone_parent={bone: tail bone})` lets a module hang from a tail bone.

## Structures (`structure.py`)

| Part | Call | Notes |
|---|---|---|
| Post | `bundle_post(a, b, n_seg, r, q, n_act, taper)` | a carbon core with a ring of actuators in each bay and a collar between bays |
| Box beam | `girder(half, bottom, top, half_width, q, windows, slant, act)` | along X, symmetric: two side plates with rounded windows, flanges, and a row of actuators inside; `bottom(x)` and `top(x)` shape it (an upswept lintel) |
| Plinth | `plinth(center, size, q)` | a stand for a statue: dark footing, carbon block, steel band, corner bolts, a lit strip |

A structure is built through the same pipeline as an animal: one segment (`pelvis`), a one-bone rig and a motion set
with a single still clip (see `torii_t01/scripts/torii.py`).

## Scenes

A scene models nothing. `scene_guardian_gate/` holds a layout (`viewer/scene.json`: which built model stands where and
which motion it plays), a page that reads the models' own viewer files, and a Blender script that loads their exports,
poses them, renders stills and writes one static GLB.

## Materials (`materials.py`)

`palette(u, px_per_cm, lens)` creates the six shared materials. The pipeline calls it after packing, so pattern pitch
follows the texel density the animal actually gets. `weathered(...)` is the underlying generator: base colour, roughness,
metal, then chips, rubbed edges, scratches, cavity dirt, dust, and optional braid or weave relief driven by the unpacked
`UVsrc` layer. `clay()` gives flat stand-ins for fast checks.

## The animal module

| Name | Meaning |
|---|---|
| `NAME`, `TITLE`, `SHORT` | file prefix, full title, viewer heading (`"GORILLA / G-01"`) |
| `U` | size factor |
| `build(q)` | returns `{segment: MD}` |
| `RIG` | `rigging.Rig(pivots, parent, limbs, legs, tail, u)` |
| `MOTIONS` | a motion set: `clips()`, `speeds()`, `PLAYLIST`; optionally `travel()` for clips that cover ground unevenly (a jump) and `headings()` for clips that travel some other way than straight ahead (degrees; 90 is to its left) |
| `DENSITY` | texel boost per segment, e.g. `{"head": 1.45}` |
| `FOCUS` | close-up cameras: `{"head" / "actuator" / "paw": (target, distance)}` |
| `FRAMES` | joint frames for the clearance check |
| `LENS`, `ACCENT`, `LOD_RATIO`, `SUBTITLE`, `VIEW_HEIGHT`, `CELL`, `START`, `SLEEP_CLIP`, `UV_MARGIN` | optional; `CELL` is the viewer's floor-grid square in metres, `START` the motion the viewer opens on; `UV_MARGIN` the packing margin (default 0.0016 of the sheet; a very large animal with thousands of small islands wants less) |

## Pipeline options

`blender -b --factory-startup -P <animal>/scripts/build.py -- [options]`

| Option | Effect |
|---|---|
| `4096` (any number) | bake resolution |
| `nobake` | reuse existing textures; only valid if the geometry has not changed |
| `render` | stills: left, top, front, hero, rear and the `FOCUS` views; also the landing-page card |
| `view=hero` | limit the stills (repeatable) |
| `anim` | check frames of every motion, side and wide view, into `renders/anim_frames/` |
| `clip=Walk` | limit the check frames (repeatable) |
| `clay` | flat colours, no bake, no exports: seconds instead of minutes |

Outputs: `textures/`, `export/` (static GLB, FBX, per-LOD glTF, rigged GLB and FBX, textures zip, `stats.json`),
`blend/`, `renders/`, and `viewer/` (page, `config.json`, `assets/`, download links).

## Checks

| Command | Answers |
|---|---|
| `blender -b --factory-startup -P kit/check.py -- <animal>` | do muscles touch a frame bar at rest; does anything go below the ground |
| `blender -b --factory-startup -P kit/ground.py -- <animal> [clip ...] [focus=forepaw,_3]` | lowest point of the posed animal during each motion, with the part and the time; `focus` looks at chosen parts only (the knuckles of a mounted hand) |
| `blender -b --factory-startup -P kit/overlap.py -- <animal> [clip ...] [pairs] [every=1]` | do limb muscles cut through the trunk, head or abdomen, at rest and during each motion; with `pairs` (always, for the hand) every part against every other that is not its parent or child; `every=1` tests every frame instead of every third (for something fast: a prop in the air). A clip lists only what is new against rest: pairs that touch at rest by design are left out unless they grow past 3x + 60 triangles (`all`, `grow=`, `slack=`) |
| `blender -b --factory-startup -P kit/compare.py -- <animal> [view=front] [clip=Idle t=0]` | is it built to the sheet: per orthographic view, sheet, render and outline over the sheet in `renders/compare/`, and the height, width, width at ten heights and the overlap of the two silhouettes. The animal declares `SHEET = {"image", "ppm", "ground", "hide", "views": {name: {box, centre, camera, blank, ppm, ground}}}` (see the tool's header; a view drawn at a scale of its own gives its own `ppm` and `ground`, and `camera="top"` compares a top view, its "ground" the sheet row of the centre line); leave `centre` out for a side view and the silhouettes are lined up; a model that runs off its box is flagged (the box hides it, so make it big enough) |
| `blender -b --factory-startup -P kit/snap.py -- <animal> [clip ...] [limit=20] [skip=index,thumb]` | does any bone jump between two frames: a turn of more than `limit` degrees in one frame and more than twice the frames either side (a flip, an angle wrapping, a blend through a degenerate pose; the loop seam too). Also prints each clip's fastest turn. Seconds: no mesh, forward kinematics only |
| `blender -b --factory-startup -P kit/strip.py -- <animal> clip=Strike view=left view=wide t=1.2:2.2 n=12` | what a motion looks like: a contact sheet in `renders/strips/`, about four seconds a sheet. `at=1.5,1.53` gives exact times, `p="tunable=value"` tries motion numbers without editing the file. Views: left, right, front, rear, top, wide, back34 |
| `blender -b --factory-startup -P kit/tune.py -- <animal> Roll "" "roll_tuck=0.05" "roll_tuck=0.03,roll_edge=0.09"` | lowest point and muscle overlaps of one clip for each quoted set of trial numbers (`""` is the setting as built) |
| `uv run --with playwright python kit/viewer_test.py <out folder> bear_u01:Stand@2.6 spider_j01` | does the viewer page load without errors and does every motion button play (headless Chromium, needs `./serve.sh`); `:Motion@seconds` saves a screenshot of that motion |
| `blender -b --factory-startup -P kit/regress.py -- <animal> save` | record the animal as it builds now |
| `blender -b --factory-startup -P kit/regress.py -- <animal>` | did a kit change alter its geometry, bones or motions |
