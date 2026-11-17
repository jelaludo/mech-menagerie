# Rig standard

One skeleton convention for every animal, so skin weights, the leg solver and motions are shared.

## Bones

| Bone | Sits at | Parent |
|---|---|---|
| `root` | the ground under the animal | – |
| `pelvis` | between the hips | `root` |
| `spine` | where the loin meets the pelvis | `pelvis` |
| `chest` | middle of the back | `spine` |
| `neck` | base of the neck | `chest` |
| `head` | skull joint | `neck` |
| `jaw` | jaw hinge | `head` |
| `upper_arm.L/R` | shoulder | `chest` |
| `forearm.L/R` | elbow | `upper_arm` |
| `forepaw.L/R` | wrist (carries the whole paw or hand) | `forearm` |
| `thigh.L/R` | hip | `pelvis` |
| `shin.L/R` | knee | `thigh` |
| `hindfoot.L/R` | hock (digitigrade animals only) | `shin` |
| `hindpaw.L/R` | toe knuckles, or the ankle of a plantigrade foot | `hindfoot` or `shin` |
| `tail_01 … tail_NN` | start of each tail barrel | `pelvis`, then the previous one |

The feline has 39 bones (18 in the tail). A tail-less plantigrade animal has 19. The horse has 40: it adds `neck_2`
(upper neck, child of `neck`) and `forehoof.L/R` (front pastern and hoof, child of `forepaw`, which is its cannon bone),
so both its legs are four-joint chains.

## Axes

Every bone is axis-aligned at rest: bone space equals world space (the bone points along +Y, roll 0). A pose is
therefore plain Euler XYZ angles:

- **X: pitch** in the side plane. Positive lowers whatever points forward (head down, forward leg swings back).
- **Z: yaw.** Positive turns the nose to the animal's left.
- **Y: roll.**

`pelvis` and `root` may also be moved. Only `Roll` moves `root`.

## Skin

- Rigid parts get weight 1 to their segment's bone.
- A muscle that spans a joint is tagged with the bone at each end. Its vertices blend linearly from one to the other
  along its length; the first and last 12% (ferrule and rod) stay rigid.
- Each tail barrel is weighted to its own bone.
- No vertex has more than two influences.

## Leg solver

`Rig.solve(trunk, feet)` works in the side plane.

- `trunk`: pelvis offset `dy`, `dz` and pitches `P` (pelvis), `s` (spine), `c` (chest).
- `feet`: for each leg, an offset of the foot from its rest position plus absolute pitches.
  - three-joint leg (`shoulder, elbow, wrist` or `hip, knee, ankle`): `(dy, dz, end pitch)`
  - four-joint leg (`hip, knee, hock, knuckle`): `(dy, dz, foot pitch, toe pitch)`
- Two-link IK finds the upper and lower bone angles; `back` says which way the middle joint folds.
- The result is local rotations for every leg bone, already compensated for the trunk's pitch, so the body can bob and
  flex while the feet stay put.

Limits: this solver is planar. Trunk yaw and roll move the shoulders slightly sideways and the feet follow (about 1 cm in
the walk).

### The 3D solver

`Rig.solve3(rot, loc, feet)` is for animals whose legs leave the side plane (the Komodo). Body bones are posed freely
with local rotations; each foot is a world position for the wrist or ankle plus a pitch and yaw for the foot. A leg is a
two-bone chain bent in a plane that turns with its girdle, so the elbow or knee keeps pointing the way it does at rest.
A foot can also `ride` with its parent segment (the forelegs when the animal rears). Three-joint legs only.

## Motions

A clip is a function `t → (pose, pelvis location)`, where `pose` maps bone names to `(rx, ry, rz)`. Clips are sampled at
30 fps into one Blender action each, with the last frame equal to the first, and exported as separate glTF animations.

For a biped only the legs are given to the solver (`legs={"H": ...}`); arms are posed with plain shoulder and elbow
rotations. Negative X swings a hanging arm forward or bends the elbow; Y raises it sideways (positive for the right arm).
`motions.Biped` provides Idle, Walk, Run, Jump, Squat, Box and Wave, with a heel-strike / toe-off foot path.

**Recorded motions.** `kit/retarget.py` reads a GLB with a humanoid rig and animations and writes per-frame bone
rotations for a kit rig. Because every kit bone is world-aligned at rest, a bone's pose is simply the world rotation
its source bone has made since the source's rest pose, expressed relative to its parent. `motions.Recorded` plays the
file and `motions.Combined` mixes it with procedural clips. The bone map is at the top of `retarget.py`.

`motions.Salticid` (jumping spider) solves eight legs with `solve3` (leg kinds `L1`–`L4`, front to back) from one rigid
body: `pelvis` is the cephalothorax and there is no spine, chest, neck or head. Extra bones: `abdomen`,
`chelicera.L/R` (the mandibles), `palp.L/R` and `palp_tip.L/R`, and `legN_upper / legN_lower / legN_foot` for each leg.
The walk alternates two sets of four legs. The jump is played on the spot: the body rises and falls on a true
ballistic arc, feet leave the ground and return to it with `ride` targets (`Salticid.rel` gives the offset that puts a
riding foot exactly on its ground spot), and `travel()` returns `{clip: [(seconds, metres)]}`, the ground covered over
the clip. The pipeline writes it to `stats.json` as `travel_m` and the viewer moves the floor by it.

`motions.Canine` (dog) is the quadruped set with a `Trot` (diagonal pairs with a moment in the air) in place of the
stalk and a `Bark` in place of the roar. The tail hangs at rest, so `tail_to(out, angles, e, pitch)` points its bones
along given directions and takes the pelvis's own tilt out: that is how it is laid flat on the ground when the dog
sits, sleeps or rolls. `wag` adds the wag. The roll has three tunables for long-legged animals: `roll_tuck` draws the
feet in as it goes over on a side, `roll_edge` lifts the body as it pivots on its down-side edge, and `roll_h` is the
height of the axis it turns about, which must be at least half the height of the animal lying down.

`motions.Silverback` (the gorilla on the biped plan) is `Ape` plus the `Upright` mixin the bear also uses. At rest the
trunk already slopes at about 50 degrees and all four limbs are solved, so it knuckle-walks and charges like the first
gorilla; `Stand`, `BipedWalk` and `ChestBeat` pitch the trunk the rest of the way up over planted feet and let the
hands `hang` from the shoulders. `Upright` reads three tunables: `up` (pelvis, spine and chest pitch), `hang` (where a
hand hangs below its shoulder) and `level` (the neck and head pitch that bring the head level again).

`motions.Shisa` also has `SitOpen` and `SitShut`: seated the whole time, breathing, mouth open or closed. They loop, so a
pair of them can sit either side of a gate (the Guardian Gate scene).

`motions.Shisa` extends `Canine` for the guardian lion-dog: a rolling `Walk`, `Roar`, `Guard` (the upright sit of a gate
statue, mouth opening wide) and `Bow` (the lion dance: chest down, rump up, head tossing, jaws snapping). In the bow
the fore feet are reached far enough forward that the forearms lie along the ground; the reach was found by solving
the leg for the elbow's height. Its tail is built as an upright spiral, so `tail_curl_up(out, tighten, sway, upright)`
tightens the curl, leans it, and keeps it standing when the rump drops.

`motions.Octopus` poses an arm in one of two ways. Each arm is seven bones, `armN_01`…`armN_07` (arm 1 is the front
pair), hanging from `pelvis` (the hub and head); `mantle` is one more bone on top. Standing and crawling, the solver
treats `_01`, `_03` and `_05` as a leg's upper, lower and foot (legs `A1`–`A4`; `_02` and `_04` stay straight, the last two
are the free tip) and the eight-leg walk is reused. `solve3` passes through bones that hang off a solved limb bone.
Swimming, reaching, displaying and resting, `Octopus.arm(key, elevations, yaw)` points every bone directly: each is
aimed in the body's frame and the local rotation is taken from its parent's aim, so lifting and turning do not
compound along the chain. `blend` moves from a solved pose to an aimed one. The swim has a negative speed: the
animal travels mantle first, away from the way it faces.

`motions.Serpent` (snake) has no legs to solve. The skeleton is one chain both ways from a root a third of the way
down the body: `pelvis`, then `spine_01`…`spine_08` towards the head, `head`, `jaw`, `tongue`, and `tail_01`…`tail_13`
behind. Locomotion is path following: `Serpent.lay(swing, wave, s0)` puts every joint exactly on one fixed curve on the
ground (heading swinging `swing` degrees either side over `wave` metres of body) and the clip feeds the body along it,
so no part slides sideways. The speed is the ground one wave covers divided by the period, and the floor moves at
that speed, so the bends stay over the same floor squares. Raised poses (`Rear`, `Strike`) add pitch to the front
bones with `lift`; `Coil` sets each joint's turn from a spiral whose radius shrinks towards the tail.

`motions.Scorpion` shares the eight-leg walk with the spider (both extend `motions.Arachnid`). `pelvis` is the front
part of the body, carrying the legs and the pincer arms; `abdomen` is the back part and the parent of the tail
(`Rig(..., tail_parent="abdomen")`). The pincer arms are `upper_arm / forearm / forepaw` plus `claw` for the movable
finger, posed directly: mostly yaw, since they lie flat. The tail is `tail_01`…`tail_06`, the last bone being the
sting. `Scorpion.tail_to(angles)` turns a list of directions (0 back, 90 up, 180 forward, 270 down, one per bone)
into bone rotations, which is how the strike is aimed: the wind-up and the lash are two such lists.

`motions.Mantid` (mantis) solves four walking legs with `solve3` (leg kinds `M` and `H`) and poses the raptorial
forelimbs directly. Fast actions use `keys(t, [(time, value), ...])`: the strike goes from drawn back to full reach in
0.11 s. Extra bones: `mid_thigh / mid_shin / mid_foot` (middle legs) and `antenna.L/R`.

`motions.Culicid` (mosquito) solves six legs with `solve3` (leg kinds `F`, `M`, `H`; bones `fore_*`, `mid_*` and
`thigh / shin / hindpaw`) on one rigid body: `pelvis` is the thorax, the abdomen is `tail_01`…`tail_07`, and `head`
carries `antenna.L/R` and `proboscis`, a bone that a motion slides along its own line (`needle`; `reach` gives the
slide that puts the point on the surface for the body's lean). The wings are `forewing.L/R` and `hindwing.L/R`, one
bone each at the hinge. They are built folded back, so a wing's pose is three turns in this order: `fold` degrees of
yaw to swing it out square to the body, a pitch that lays it level and then flaps it, and a twist about its own span;
`beat` composes them as matrices and converts to Euler angles, because the three do not commute. The beat is 7.5 Hz:
four frames to a stroke, the fastest 30 frames a second can show as up, level, down, level. In the air the body is
really a metre up (the pelvis is moved) and the feet `ride` with the body, each offset from where it stands
(`dangle` hovering, `trail` cruising); `Flight` blends from planted to riding as it lifts off and back as it lands.

`Upright.elbows_out(out, trunk, left, right)` turns a solved fore limb about the line from its shoulder to its paw:
the elbow swings out sideways and the paw stays where the solver put it. The planar solver can only fold an elbow
backwards, which for an animal standing upright means into its own chest; the bear's `Stand`, `BipedWalk` and `Swipe`
and the gorilla's `ChestBeat` use it. `Arachnid.feel(key, e, aim, reach, height, pitch)` holds a leg out from its hip
like a feeler (degrees round from straight ahead, metres out and up); the spider's `Sense` and `Wave` are written with it.

`motions.Manus` (the hand) has no solver for legs; it has one for the thumb. A pose is a dict of joint values in
degrees, named as on the user's AUREL-01: `wrist_flex`, `wrist_dev`, `<finger>_spread / _mcp / _pip / _dip`,
`thumb_opp`, `thumb_abd`, `thumb_roll`, `thumb_mcp`, `thumb_ip`; `bones(pose)` turns it into bone rotations (the
thumb's first bone takes three turns composed as matrices; its other two bend about the thumb's own side axis, which
is not a world axis). `reach(base, target, ...)` finds the thumb's five values, and for a pinch the finger's four,
that bring the thumb's fingertip face to face with a point on another bone a set gap away, while `clash` keeps the
thumb's last two bones out of the fingers and the palm (capsules and a box from `Hand.envelope`). The search is five
thousand seeded random poses, then a downhill simplex from the best three, so it always returns the same pose; it
takes about half a second a pose. `move(a, b, e)` goes between two poses with the fingers first when the thumb is
closing over them and the thumb first when it is leaving. `report()` prints what each solved pose achieves.

A mounted hand's turns are written in the hand's own frame and carried into the model's (`P R P⁻¹`, with P the hand's
placement), so the same pose drives a hand hanging from the humanoid's wrist or folded under G-02's. The right hand
is the mirror image of the left: its Euler angles are (x, −y, −z). `motions.ApeManus` adds the knuckle stance (finger
roots straight, middle joints square, end joints tucked, the thumb laid along the index) and the hook grip.
`motions.WithHands` takes any motion set and poses the fingers on top of each clip, leaving the clip's own wrist.

`motions.Pes` (the foot) is `Manus` with the ground under it. Pose keys are the hand's for the toes and the great
toe (`<toe>_spread / _mcp / _pip / _dip`, `thumb_opp / _abd / _roll / _mcp / _ip / _ext`) plus `ankle_pitch`
(positive points the toes down), `ankle_roll` and `lift` (metres off the ground). A negative `_mcp` raises a toe.
`ground(rot, lift)` returns where the shin (the root bone) must be so that the lowest contact point rests on the
floor and stays over the spot it stands on at rest; the points nearly as low share the weight, so the foot rolls from
heel to ball without a jump. So a clip never places the foot: `ankle_pitch = 40` with every toe's `_mcp = −40` is
"up on its toes", and the heel strike of `Step` is just a negative pitch. `move` keeps everything that bears on the
ground in step. The grips are solved with `reach`: `grasp` clamps a bar between the great toe's pad and the side of
the second toe, `nip` brings their tips together.

`motions.Anuran` (toad) solves four splayed legs with `solve3`. The hind leg has four bones: `thigh`, `shin`,
`hindfoot` (the long foot, the solver's end bone) and `hindpaw` (the toes, a child the clip poses). A foot's
orientation can be given to `solve3` as a rotation matrix instead of (pitch, yaw), which the leap needs: during the
push each hind foot turns about an axis of its own, through the toe joint, so the ankle rises on an arc while the toes
are counter-turned to stay flat. `plan(name)` works out, once, how far along the launch line the body can go before
the legs are spent (a bisection on the hip-to-toe distance), the foot's turn that reaches it, and the height of the
flight. In the air the feet `ride` with the body, as the spider's do; `travel()` gives the distance.

`motions.Hexapod` (the X6-Crawler) is `Arachnid` with three leg kinds: the phases come out as two alternating
tripods. `stepping(phase, heading)` walks in any direction, since a radial machine has no front; `headings()` tells
the pipeline which clips travel sideways, and the viewer runs its floor that way. `Turn` gives each tripod its own
angle about the hub and steps them round in turn while the hub follows; each foot's yaw is its tripod's angle. The
stalk is `neck`, `neck_2` and `head` (lean, bend, tilt, pan).

`motions.Theropod` (the raptor) gives the planar solver only hind legs (`legs={"H": four joints}`), like `Biped`
but digitigrade. Arms, neck (`neck`, `neck_2`, `head`), tail and the sickle claws (`sickle.L/R`, a child of each
`hindpaw`) are posed directly. The gait takes `follow=0` so the long toes stay level when the foot pitches in the
swing. In the pounce the feet are given the body's own rise plus an offset while it is in the air, since the planar
solver's feet are world targets; `travel()` gives the distance.

`motions.Ursine` (bear) adds an amble and three upright clips to the quadruped set. Upright, the trunk is pitched to
about 80 degrees with the hind feet planted and the fore feet placed below the shoulders with `hang`.

`motions.Sprawler` (lizards) provides Idle, Walk, Run, Threat, TailWhip, Tripod and Bask on `solve3`. A motion can also
slide a bone: `pose["_loc"] = {bone: (x, y, z)}` (the tongue).

`motions.Quadruped` provides Idle, Walk, Stalk, Run, Roar, Sit, Sleep and Roll. All distances are multiplied by the
animal's size factor. An animal tunes them with keyword numbers (`stride`, `lie_drop`, `sit_P`, …) or subclasses it to
add clips.

| Building block | Use |
|---|---|
| `rig.gait(phase, phases, duty, stride, lift)` | feet for a walking or running cycle; `phases` sets the footfall order |
| `rig.stand()` | feet at rest |
| `rig.tail_wave`, `rig.tail_curl` | travelling wave down the tail; tail curled and lifted |
| `ramp(t, a, b)`, `mix(a, b, e)` | smooth blend in and out of a held pose |
| `pose["root"]`, `pose["_root_loc"]` | rotate or move the whole animal (used by Roll) |

Footfall orders in use: lateral-sequence walk (LH, LF, RH, RF at quarter phases) and rotary gallop (LH, RH, RF, LF).
The `Equine` set adds the trot (diagonal pairs together), a three-beat canter and a four-beat transverse gallop, and the
per-leg gait options `lean` (the lower leg tilts with the whole limb in stance), `swing` and `toe` (how far the knee or
hock and the fetlock fold in the air).
