# Reuse plan

How one kit of parts serves nine animals. Every model in the menagerie is the same machine drawn at a different size and
proportion: a bare frame, joint modules, and braided actuators pulling across the joints. So the kit is organised by how much
an element changes from animal to animal.

## The five levels of reuse

| Level | What it is | Changes between animals | Where it lives |
|---|---|---|---|
| 1. Parts | actuator, joint module, link plate, flat bar, perforated shell, claw, pill | only the numbers passed in | `kit/menagerie/parts.py` |
| 2. Assemblies | keel, rib hoops, sternum, muscle rows, A-frame, skull + mandible, digits, paw, tail | tables and counts | `kit/menagerie/anatomy.py` |
| 3. Body plan | which assemblies exist and how the limbs chain | one per family (below) | the animal's own file |
| 4. Rig and motions | bone names, leg solver, gaits, resting motions | tuning numbers; a few new clips per family | `rigging.py`, `motions.py` |
| 5. Pipeline and viewer | unwrap, bake, LODs, exports, check renders, web viewer | nothing | `pipeline.py`, `kit/viewer/` |

Levels 1, 4 and 5 are finished code that the next animal uses as is. Level 2 grows by one or two assemblies per new family.
Level 3 is the only part written fresh each time, and it is mostly tables.

## Scale: reuse by number, not by stretching

A part is never scaled as a mesh. Scaling a mesh scales wall thickness, ferrule size and texture detail with it, and a bear
then looks like an enlarged cat. Instead every animal declares one **size factor `u`** (1.0 = the feline, 0.52 m at the
shoulder) and the kit applies it where it belongs:

| Quantity | Rule | Why |
|---|---|---|
| Lengths and positions | written directly in metres by the animal | proportions are the animal's identity |
| Frame cross-sections (keel, hoops, sternum) | × `u` | structure grows with load |
| Plate thickness, bevel and edge-wear width | × `u^0.7` in the materials | plates thicken more slowly than the body grows |
| Actuator radius | given per muscle | muscle mass is a design choice |
| Ferrules, rods, eyelets | proportional to the actuator radius | already automatic |
| Wear noise (chips, scratches, grime) | frequency × `u^-0.5` | marks stay object-sized, not animal-sized |
| Braid and weave pitch | at least 6.5 texels | below that the bake aliases |
| Motion distances (stride, lift, body drop) | × `u` | gaits scale with leg length |
| Lights, cameras, floor, viewer stage | from the bounding box | no per-animal staging |

One exception, by request: a variant that is the same animal larger may enlarge the whole build (the Komodo's `size`
socket, used by the Giant Crocodile at 4.1), accepting that its rods, rings and joints grow with it.

Texture budget is the one thing that does not scale for free. One 4K set gives the feline 15 px/cm. A 2.4 m bear has about
five times the surface, which is about 7 px/cm. For animals above roughly `u = 2`, plan two 4K sets (front and rear halves) or
one 8K set; the pipeline packs one set today, so this is the first kit change the bear or horse will need.

## Body-plan families

| Family | Animals | Limb chain (front / hind) | New for the kit |
|---|---|---|---|
| Digitigrade quadruped | 01 Feline, 03 Malinois (built), 04 Shisa (built) | 3 joints + paw / 4 joints + paw | the Malinois is the feline's file at 1.34 times the size with a dog's numbers: no new assembly, a `claw` length on the paw, and the `Canine` motions. The Shisa (built) is the same file again, wide and heavy, with ornament: `curl`, `lock` and `mane`; its spiral tail is the scorpion's `bundle_tail` |
| Knuckle-walker | 02 Gorilla (G-02 built on the biped plan; G-01, the first pass, is kept in `gorilla_g01/`) | 3 joints + hand / 3 joints + foot | G-02: the humanoid's column torso and outside muscles, leaned 52 degrees until the knuckles reach the ground; `Silverback` motions. Was: hand, plantigrade foot. First pass used the quadruped plan and reads as a beast, not an ape; redo with the biped family |
| Plantigrade quadruped | 07 Bear (built) | 3 joints + foot / 3 joints + foot | no new assembly: gorilla's foot (with claws added), Komodo's carapace, horse's ears; `Ursine` motions add standing upright |
| Unguligrade quadruped | 05 Horse (built) | 4 joints + hoof / 4 joints + hoof | added `hoof`, `Column` neck, ring frames, leg `lean`, the `Equine` gaits |
| Sprawling quadruped | 08 Komodo (built), 16 Toad (built) | limbs stick out sideways | Komodo: added `Rig.solve3` (3D legs), the `Sprawler` gait, `carapace`, `tongue`; see the Komodo study. Toad: the Komodo's solver and tongue, the scorpion's `muscle_link` limbs, the mantis's eyes, the spider's on-the-spot leap; adds `pad_foot`, a four-bone hind leg whose foot rolls onto its toes, and the `Anuran` motions |
| Theropod | 14 Velociraptor (built) | 2 digitigrade legs (four joints, the feline's hind leg) under a level trunk; arms posed | the feline's trunk, hind leg, paw and tail, the horse's `Column` neck, the Komodo's skull tables and `carapace`; adds `claw_hand`, `sickle_toe` (its own bone), a gait `follow` option and the `Theropod` motions (walk, run, slash, an on-the-spot pounce) |
| Piloted frame | 18 MK-07B Valkyr (built) | the humanoid's plan at 5.16 times the size | second build, in the kit's own language: the crawler's `cage_limb` for every limb bone, a lattice torso, the gorilla-table `manus.Hand`, the biped motions at half speed (`Frame`); adds `shield` (perforated carbon armour on posts), a cockpit tub and seat sized for H-01, and a `hatch` bone. Next for it: shields of more than one shape, H-01 seated in it, loadouts, a second texture set |
| Biped | 06 Humanoid (built), 23 Valkyrie (built); 02 Gorilla to be redone here | arms hang, legs carry | the torso is the horse's `Column` stood upright; added `open_hand` and the `Biped` motions. The Valkyrie is H-01's plan and hands at 2.38 m with H-01's recorded locomotion scaled to her legs; her armour is the series' shields, her winged helmet and skirt the eagle's feathers; adds `arma` (spear, round shield) and the `Spearman` motions (with `flight`: a prop tossed from one grip to another). The Valkyrie V-02 Winged is V-01 with `shield=False, wings=True`: the eagle's `FeatherWing` at 0.7, mounted along her spine with the wing's new `body=` frame, and the `WingedSpearman` motions. H-02 is H-01 with the lats on the thorax and PES-01 feet (`foot="pes"`, the toes kept on the ground by `GroundedToes`). The Minotaur (24) is the same plan made massive at 3.2 m, its proportions fitted to the sheet's views with `kit/compare.py`: the hands at twice their size as gauntlets, the lizards' skull tables as a bull's head, new `an.horn` and `an.meander_band`, a hoof block on a three-joint leg, and the `Taurus` motions (the biped's solver: walk, charge, roar, stomp, smash). The Medusa (26) is the Valkyrie's plan at 2.48 m on high heels (`calceus.Boot`, the toe boxes kept on the ground by `GroundedToes`) with H-01's recorded walk, jog and run unchanged; her hair is eighteen small Snake S-01s (`crinis`), her hands MANUS-01 made slender with claws, her face a smooth mask under a hood of the skull tables, the Minotaur's meander as an oval belt and down her panels (`meander_strip`), and the `Gorgon` motions (`Spearman`'s arm setting split out as `Reaching`). Her boots are the kit's `calceus.Boot`; the Valkyrie wears them with `foot="heel"` (everything above the ankle lifted onto the heels, her recorded clips `Settled` onto the floor) |
| Long dragon | 19 Kami Dragon (built), 19B Kami-Ryu (built: a variant, the same chain drawn out to 56 modules, 13.7 m) | the Komodo's sprawling legs (solved in 3D) under one chain of 26 body modules | no new assembly: the snake's `serpent_segment` for neck, body and tail, the crawler's `cage_limb`, the Valkyr's `shield`, the Komodo's foot and skull tables, `spike` antlers. Adds the `Dragon` motions, which pose the chain by how steeply each module points, so the same code walks, flies and lies down |
| Cervid | 25 Stag (built) | four four-joint legs, a long neck column, a trunk with a keel | the horse's plan (trunk, neck column, skull tables, legs, `Equine` gaits) taller and finer; adds `antler` (the crown), `an.cloven_hoof`, the `C_brass` material for rivets, titanium bands laid on the trunk's surface, and the `Cervine` motions; fitted with `kit/compare.py` |
| Bird | 22 Eagle (built) | two four-joint legs (the raptor's, solved in the side plane) under a `Column` body; two feathered wings | the humanoid's `Column` as a lattice body, the raptor's legs and gait, the lizards' skull tables with a hooked beak, the crawler's `cage_limb`; the dragons' wing (`ala.Wing`) becomes `FeatherWing`, the dexterous hand (`manus.Hand`) becomes the `Talon`. Adds `pluma` (feathers, plumage) and the `Aquiline` motions. The Valkyrie's wing-pack and the feathered skirt are the next use of `pluma` |
| Winged dragon | 20 Western Dragon (built), 21 Wyvern (built), 21B Rokuro-Wyvern (built) | dragon: four legs solved in 3D under the Komodo's trunk, two bat's wings on the shoulders; wyvern: the wings are the fore legs | the Komodo's keel, hoops, sternum, rows and carapace; the Kami Dragon's chain of `serpent_segment`s for the neck and the tail, its feet (with a dew claw, now `dragon_foot`) and `Dragon` motions; the crawler's `cage_limb` and the Valkyr's `shield` on every leg. Adds `ala.Wing` (arm, hand, fan of spars, sail and bundles, scalloped frame, `reach`) and the `WingedDragon` and `WingWalker` motions. The wyvern was all reuse but for its arms: built spread, reached to its hands, the hand a bone of its own. The Rokuro-Wyvern is a variant by number only: the Wyvern with a 9 m neck and a 17.5 m tail |
| Serpentine | 09 Snake (built) | none | `serpent_segment` repeated 22 times; the head and tongue are the Komodo's tables at half size; `Serpent` motions follow a ground path |
| Insectoid | 11 Praying mantis (built), 15 Giant mosquito (built) | mantis: 2 raptorial arms (posed) / 4 thin walking legs (solved in 3D); mosquito: 6 thin legs and 4 wings | mantis: serrated `blade`, `compound_eye`, `antenna`, `tarsus`; the abdomen is a `tail`, the raised prothorax a `Column`. Mosquito: the mantis's leg moved into the kit as `insect_leg`, its eyes and antennae (now with bristles), the Shisa's `bundle_tail` as the abdomen, a `carapace` as the thorax shield; adds `wing`, `proboscis` and the `Culicid` motions (flight) |
| Arachnid | 12 Jumping spider (built), 10 Scorpion (built) | 8 walking legs on one rigid body | spider: `arachnid_leg`, `camera_eye`, lattice options on the `Column` (abdomen), the `Salticid` motions and jump `travel`. Scorpion: reuses the leg, the eight-leg walk (`Arachnid`), the eye, the mantis's `blade` (pincer fingers) and the Komodo's `carapace` (back plates); adds `chela`, `muscle_link`, `bundle_tail` with its sting, and the `Scorpion` motions |
| Radial machine | 17 X6-Crawler (built) | 6 identical limbs round a hub, three mirrored pairs | the spider's solver and tripod phases with six legs, its `camera_eye`; adds `cage_limb`, `probe_foot`, the `Hexapod` motions (walking in any direction, turning on the spot) and a clip heading for the viewer's floor |
| Cephalopod | 13 Octopus (built) | 8 arms; three bones of each are solved as a leg to stand and crawl | `tentacle_segment` with `suction_cup`; the mantle is the spider's lattice abdomen, the eyes its `camera_eye`, the crawl its eight-leg walk; `Octopus` motions add arms aimed directly |

## What each animal reuses

`=` reused as is, `~` reused with new numbers, `+` new assembly needed, `–` not present.

| Module | 01 Feline | 02 Gorilla | 03 Malinois | 04 Shisa | 05 Horse | 06 Humanoid | 07 Bear | 08 Komodo | 09 Snake |
|---|---|---|---|---|---|---|---|---|---|
| Actuator, joint, link, bar, shell | = | = | = | = | = | = | = | = | = |
| Keel + vertebra collars | = | ~ | ~ | ~ | ~ | ~ | ~ | ~ | ~ |
| Rib hoops + sternum | = | ~ | ~ | ~ | ~ | ~ | ~ | ~ | + segment ring |
| Muscle rows in the trunk | = | ~ | ~ | ~ | ~ | ~ | ~ | ~ | ~ |
| A-frame over shoulder / hip | = | ~ | ~ | ~ | ~ | ~ | ~ | ~ | – |
| Skull shell + mandible + optics | = | ~ | ~ | ~ + mane | ~ | + visor head | ~ | ~ | ~ |
| Paw (toes + claws) | = | – | ~ | ~ | – | – | ~ | ~ long claws | – |
| Hand | – | + | – | – | – | = | – | – | – |
| Plantigrade foot | – | + | – | – | – | ~ | = | – | – |
| Hoof | – | – | – | – | + | – | – | – | – |
| Tail (barrels, one bone each) | = | – | ~ | + spiral | ~ | – | ~ short | ~ long | = as body |
| Materials palette | = | = | = | = | = | = | = | = | = |
| Rig names + planar solver | = | = | = | = | ~ 4-joint front | + biped | = | + sprawl | + chain |
| Stock motions (idle, walk, run, sit, sleep, roll) | = | ~ + upright | = | = | ~ | + | ~ | + | + |
| Pipeline, viewer, export menu | = | = | = | = | = | = | = | = | = |

## Build order and what each one adds

1. **02 Gorilla** (built). Proved the kit at `u = 2.4`. Its file is tables plus a muscle list; it added `hand`, `foot`,
   `finger` and the `Ape` motion set to the kit, and reused the feline's frame outlines through `remap`. Idle, Sit, Sleep
   and Roll ran on it unchanged apart from tuning numbers.
2. **03 Malinois** (built). The feline's file written at the feline's size and multiplied by 1.34: same assemblies, same muscle
   list, a dog's landmarks, section tables and head numbers. The test held: no new geometry code.
3. **07 Bear** (built). The first animal assembled entirely from existing parts; only its motion set is new. Built with
   one 4K set like the others, which at 26 m² of surface gives 5.8 px/cm: the texture decision is now overdue.
4. **05 Horse** (built, out of order at the user's request). The four-joint front leg needed no solver change; the kit
   gained `Column`, `hoof`, `oval` frames and the `Equine` motion set. The `Column` is also the planned basis for the snake.
5. **04 Shisa** (built). Feline body plan plus ornament: curls on the head, body and legs, a mane of actuator locks, and a spiral
   tail made from the scorpion's bundles. The open question of ornament as geometry or as a decal was settled as geometry.
6. **08 Komodo** (built, after a separate study of proportions and gait). As predicted, the first solver change that is
   not just numbers: legs are solved in 3D and the girdles turn in place. `solve3` is general, so the snake's body wave
   and any future sideways motion (a crab-step, a turn on the spot) can use it.
7. **06 Humanoid** (built). The upright trunk needed no new assembly: the `Column` written for the horse's neck is the torso.
   Only the legs are solved; arms are posed directly. Added `open_hand` and the `Biped` motion set. The gorilla can now be
   redone on this plan: a sloped `Column` torso, long arms reaching the ground, `Biped` motions plus knuckle-walking.
8. **09 Snake** (built). One body module repeated 22 times and a path-following motion set; the head is the Komodo's with new numbers.

## To do

| | Model | Note |
|---|---|---|
| 15 | Giant mosquito: wing membranes | built 2026-11-14 with open-framed wings. A membrane needs a second, see-through material: the pipeline bakes one opaque set and the web GLB is exported without materials, so it means a separate skinned mesh for the membranes and a second material in the viewer |
| – | Feet and paws | hands are built and worn (MANUS-01 on the humanoid, MANUS-G1 on G-02); feet are built as studies (2026-11-14: Prehensile Foot PES-01, Gorilla Foot PES-G1, `kit/menagerie/pes.py`). Next: mount PES-01 on the humanoid (k about 1.06 for its 9 cm ankle) and PES-G1 on G-02 (k = 1.47), with the feet's rise onto the toes and heel strike driven by the leg solver's foot pitch; then a paw (four short digits with claws, no thumb) and the toad's pad foot from the same assembly |
| – | Variants | from the feedback notes: a humanoid with more and thinner back and chest muscles (kept beside H-01), a king cobra from the snake, more helmets; the Red Witness figure's back muscles thinner and more varied in size |
| – | Limb muscles against the rib cage | `kit/overlap.py` reports the feline, Malinois and Shisa alike: about ten muscle triangles of each upper arm, and a few of each thigh, graze the chest parts at rest and when walking. Inherited from the feline's muscle list |
| – | Floor contact in the older animals | `kit/ground.py` (written for the spider) shows motions of the feline, gorilla, horse, bear, Komodo and humanoid going below the floor: worst are the rolls (feline 302 mm, gorilla 353 mm, bear 252 mm), the bear's sit (174 mm) and walk (102 mm), and the lying poses. Motion numbers only: no rebake needed. First finding with `kit/tune.py` (2026-11-14): in the feline's walk the claws dig in during the swing because the hind toes droop 22 degrees and the fore paw pitches 30 while the foot is only 5 cm up; `H={'toe': 6}` clears the hind feet (37 mm under to none) and leaves the fore paws 23 mm under, so the fore `swing` pitch is next |
| – | Texture budget for large animals | second texture set or 8K for the bear, gorilla and horse |

## Modules: parts that fit more than one body

A module is a part with an interface, not a shape. It is written in its own frame (origin at its root joint, +Z along
it, +Y its back, the kit's left side), takes one size number `k`, hands a host its bones (`pivots(M)`,
`parents(host bone)`), builds itself wherever a matrix puts it (`build(seg, q, M)`), and has a motion object with named
poses that is laid over the host's own clips. The hand and the foot were the first; `kit/menagerie/modules.py` holds
the interface and four parts taken out of the animals.

### Built (2026-11-14)

| Module | Taken from | Bones | Poses | Card |
|---|---|---|---|---|
| `Claw` | the mantis's forelimb from the elbow down | root (femur), `blade` | ready, open, half, shut | Raptorial Claw MOD-C1 |
| `Pincer` | the scorpion's `chela` | root (hand), `finger` | rest, open, shut | Pincer MOD-P1 |
| `StingTail` | the scorpion's `bundle_tail` | `sting_01`…`sting_06` | rest, wind, lash, arch; `strike_pose(t)` | Sting Tail MOD-S1 |
| `Tentacle` | the octopus's arm, along any path | one a module, any of them the host's own | straight, hang, coil, grip, lift; `ripple_pose(t)` | Tentacle MOD-T1 |
| `manus.Hand`, `pes.Foot` | new | palm or sole, digits | see their own cards | MANUS-01, MANUS-G1, PES-01, PES-G1 |
| `calceus.Boot` | new, after the Medusa's sheet (2026-11-18) | the foot's own bone and `toe` | through the host's clips (`GroundedToes`, `Settled`) | Heeled Boot CALCEUS-01 |

### Hosts and sockets

A host's file names its sockets and their defaults with `modules.loadout`; a variant is a four-line file that calls
`modules.equip(host, socket=module)` and then imports the host (`mod_humanoid_raptor/scripts/humanoid_raptor.py`).
Nothing is modelled in a variant, and the host built with its defaults is unchanged, to the vertex.

| Host | Socket | Default | Can wear | Built |
|---|---|---|---|---|
| Humanoid H-01 | `wrist` | `hand` (MANUS-01) | `claw`, `pincer` | claws: Humanoid · Raptorial Claws. Pincers are one word away: `equip("HumanoidH01", wrist="pincer")` |
| Humanoid H-01 | `arm` (from the shoulder) | `arm` | `tentacle` | Humanoid · Tentacle Arms |
| Mantis S-01 | `wrist` (the femur's end) | `blade` (its own tibia) | `pincer` | Mantis · Pincers and Sting |
| Mantis S-01 | `tail` (the end of the abdomen) | nothing | `sting` | the same model |
| Valkyrie V-01 | `foot` | `plain` (her flat foot) | `heel` (`calceus.Boot`: she stands 12 cm higher; everything above the ankle is lifted) | ready, no variant built yet: `equip("ValkyrieV01", foot="heel")` |
| Valkyrie V-01 | `shield`, `wings` | shield, no wings | no shield, the eagle's wings | Valkyrie V-02 Winged |
| Humanoid H-01 | `foot` | `plain` | `pes` (PES-01) | Humanoid H-02 |
| Komodo K-01 | `size`, `head`, `armour`, `tongue`, `grounded` | 1, its own skull, the carapace, a tongue, off | 4.1, a crocodile's skull tables, keeled scutes, none, feet and tail kept out of the floor | Giant Crocodile CR-01 |
| Oni ON-01 | `foot` | `ape` (PES-G1) | any of `feet.NAMES`: `hoof_block`, `dragon`, `cloven`, `hoof`, `plain`, `pes`, `heel`, `paw` | Oni ON-01B Hoofed (`hoof_block`) |

How each host makes its modules move: the humanoid's boxing clip throws a claw open with each punch and clamps it as
the arm comes back; its tentacle arms run through the shoulder, elbow and wrist, so three of each tentacle's seven
modules keep the arm's bone names and follow every recorded motion, and the other four carry a slow wave. The mantis
snaps its pincers shut on its strike while the tail winds back and lashes over its back, and draws the tail right
back in the threat display to clear the raised wing case.

### The extremities as studies (2026-11-18)

The parts that are not modules yet (a builder in `anatomy.py`, called at a joint with a size `k`) are shown on their
own by `kit/menagerie/studies.py`: the part under a stub of its limb, on a `foot` bone (and `toes` across a plane, and
rigid extras), moved by `motions.ExtremityStudy`. Cards: Horse Hoof UNGULA-01 (`an.hoof`), Cloven Hoof UNGULA-02
(`an.cloven_hoof`), Hoof Block UNGULA-03 (`an.hoof_block`, out of the Minotaur), Webbed Foot PES-T1 (`an.pad_foot`),
Feline Paw PES-F1 (`an.paw`), Dragon Foot PES-D1 (`an.dragon_foot`), Raptor Foot PES-V1 (`an.paw` and
`an.sickle_toe`), Heeled Boot CALCEUS-01, and Clawed Hand MANUS-C1 (`manus.Hand(claws=True)`). A study shows what can
be taken; to wear one on another animal, call its builder at that animal's joint with its own `k` (as the dog, the
shisa and the Komodo take the cat's paw), or give it the module interface as the boot has.

### Feet by name (2026-11-18)

`kit/menagerie/feet.py` puts every foot behind one interface, so swapping feet is a word, not a rebuild of the leg. A
`Footing` knows the ankle height it needs (`h`), builds itself and its bones under an ankle, and wraps the host's
motion set so it meets the ground (`fit`: `GroundedToes` for toes, `Planted` for a rigid foot that would dip,
`Settled` for a clip that floats). The host moves its ankle to `h` (the shin reaches down or up; the figure keeps its
height) or, to stand taller on it, lifts everything above the ankle (a heel). The Oni was the first host: his dragon
feet became gorilla feet and hooves without touching his legs, and both stood on the floor within 6 mm on the first
build. H-01's `pes` and the Valkyrie's `heel` sockets predate it and could be moved onto it.

### What a host has to give, beyond a socket

- *Room.* A pincer cannot fold flat against the femur as a blade does, or come right to the mouth: the mantis's
  motions take two numbers for that (`tibia_k`, `groom_k`). A tentacle thicker than an arm brushes the hip until it
  is hung a little outward.
- *A bone to hang from.* The sting tail hangs from the abdomen's last bone, which is a tail bone; `Rig` takes
  `bone_parent` for that case.
- *Its own action.* A strike or a sting only reads if the body takes part: the mantis already lunges.

### Not done yet

| | |
|---|---|
| Humanoid `tail` socket | a sting tail or a tentacle from the lower back. The recorded breakfalls roll over it, so it needs those clips reworked or left out |
| Mantis `wrist` = `tentacle`, mantis `back` = wings | the mosquito's `wing` and `Culicid.beat` as a module; the mantis has only the wing case |
| Tentacle arms at the shoulder | the chest's own muscles still run to the upper arm and sink into the tentacle's first two modules |
| Sockets as data | each host still writes its frames by hand; a `SOCKETS` table (name, frame, parent bone, joint radius) would let any module be offered to any host and give `k` from the two radii |
| More modules | wing pair, tail of barrels, jumping leg, paw, sensor heads (see the candidates in the git history of this page) |
| Module textures | an equipped body bakes one texture set for everything it wears; a module has no texture of its own to carry from host to host |

## Rules that keep reuse honest

- **An animal file holds numbers and a muscle list.** If it needs a loop or geometry code, that code belongs in the kit.
- **Same bone names everywhere.** `pelvis, spine, chest, neck, head, jaw`, then per side `upper_arm, forearm, forepaw`
  and `thigh, shin, (hindfoot,) hindpaw`, then `tail_01…`. Motions are written against names, so they carry over.
- **Regression before refactor.** When kit code is generalised, an existing animal must still build to the same vertices
  (the feline is checked to 0.0 difference). That also keeps its baked textures valid.
- **A new assembly is written for two animals.** Before adding one, name the second animal that will use it.

## Open decisions

- Two texture sets or 8K for the large animals (bear, horse, gorilla). The gorilla was built with one 4K set and lands
  at 6.6 px/cm over 18.9 m² of surface, against the feline's 15 px/cm. Fine at a distance, soft in close-ups.
- Whether the scapula should become its own bone so shoulder frames can slide in fast gaits.
- Frame section size is now a separate factor from body size (the horse uses 2.0 for a 3.08 body) so large animals can stay light.
