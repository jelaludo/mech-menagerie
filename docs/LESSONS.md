# Lessons

Things that went wrong once, and the rule that came out of each. Newest at the bottom of each section.

## Modelling

- **Proportions before parts.** The first feline read as a rat: snout too long, jaw too thin, paws too small. Check a flat
  "clay" render against the concept's side view before adding any detail.
- **Concept sheets contradict themselves.** The feline sheet says 1,280 mm long but draws 1.75 m at its stated shoulder
  height. Follow the drawing, and write the conflict down. The Minotaur's "1.45 m" width arrow spans 124 pixels where
  its shoulders, drawn at the height line's scale, span 133; its front and rear views differ by 0.4 m at the knees.
- **Check the silhouette against the sheet, not by eye.** `kit/compare.py` renders the views the sheet draws, at its
  scale, and measures both outlines. The Minotaur's first pass overlapped the front view 0.86 and the side 0.48; the
  numbers named each fault (horns twice as wide as drawn, a torso standing upright where the sheet's leans forward, back
  muscles 0.2 m too far out, the ankles half as deep), and four passes brought it to 0.90, 0.88 and 0.68 (the side view
  is lower because the sheet's arms and head differ in pose). Run on the Valkyrie, it found legs much thinner than her
  sheet's, a shorter skirt and her A-pose.
- **Fit the silhouette's shape before chasing its bands.** On the stag the widths at ten heights named the crown's
  faults (narrow at the pedicles where the sheet's beams leave the skull almost level, too wide at the top, a comb from
  the side), and the overall overlap went from 0.71 to 0.85 in two passes; after that a band is one row of pixels and
  swings with every tine, and the sheet's own views disagree about the crown (its top view sweeps the tines back where
  its side view keeps them over the head). Stop when the overlap stops moving and judge the rest in a render.
- **A check cannot see past its own frame.** The comparison drew only what fell inside each view's box; the stag's box
  stopped at 2.78 m, so the model "matched" the sheet's height while its antlers rose to 2.97 m. The build's own
  measurement disagreed, which is how it was found. The tool now flags a silhouette touching its box's edge.
- **A sheet's views crowd each other.** A fist or a hoof of the front view reaches into the side view's box; paint it
  out (`blank`) or the silhouette's width is wrong. A side view has no centre line to measure: line the silhouettes up
  by their middles instead. A side view labelled "right" may be drawn facing left (the Minotaur's).
- **Eighteen small copies cost more than one big one.** The Medusa's hair built from the Snake S-01's module at its
  own detail came to 187,000 triangles, more than the rest of her. A lightened module (a band, a braided core and a back
  plate where the Snake has four actuators in a cage) and the heads at lower detail brought it to 80,000; at eight
  centimetres the cage's bars only read as white stitches anyway.
- **A small render misleads.** The Minotaur's head looked nose-up in a 300-pixel panel; its matrix put the muzzle exactly
  where the sheet draws it. Measure the point before changing the number.
- **Thin plates need interior points only where they curve.** Flat link plates triangulate from their outline; curved
  shells (skull, frames) get a grid of about 4% of their span.
- **Faces must wind outward.** A flat bar swept with the wrong section order shades inside-out. The regression check is
  the signed volume, or simply a clay render.
- **Frames must stand clear of muscles.** Muscles under a rigid frame move when the limb moves. Make frames open
  A-frames set outside the muscle envelope, and check the gap numerically at rest.
- **Imported custom normals do not survive mirroring or triangulation.** Remove them and rebuild sharp edges from the face
  angle (Red Witness hands and feet).

- **Long limbs that are nearly straight at rest cannot reach forward.** The gorilla's arms are at 96% of full length in
  its rest pose, so a stride centred on the rest position lifts the hands off the ground. Shift the stride back
  (`front_y`) and lower the body a little (`walk_crouch`) instead of changing the pose.
- **Check the size after the first clay build, not before.** The gorilla's landmarks came out at 1.68 × 1.29 m against
  the sheet's 1,650 × 1,250 mm without tuning, because they were read off one consistent scale.
- **A frame set clear of big muscles stands far from the body.** At gorilla size the shoulder A-frame reads as a wing.
  Keep the gap to about 2 cm and trim the muscle bow rather than pushing the frame out.

- **Decide the body plan from how the animal reads, not from the sheet's label.** The gorilla sheet says
  "quadrupedal (knuckle-walking)", so it was built on the quadruped plan with a horizontal trunk. The result registers as
  a four-legged beast, not an ape: a gorilla's identity is its near-upright torso, broad chest and arms that hang. It
  belongs with the biped family (sloped or vertical trunk, arms as arms) and should be revisited when the humanoid is built.

- **A long straight leg has to lean as a whole.** With the cannon bone kept vertical, a horse's leg cannot reach a
  planted hoof 15 cm behind the hip. Letting the lower leg tilt with the limb (`lean`) fixed stance; folding the knee,
  hock and fetlock hard in the swing is what makes the gait read as a horse.
- **Cross tubes show when the chest is open.** The shoulder cross tube that hides inside the feline's chest sticks out in
  front of the horse's like a handlebar. Shorten it to the trunk width or drop it when the frames are rings.
- **Ring frames set outside the muscles read as handles** from the front. Same cause as the gorilla's wings.

- **A general assembly pays off in a role it was not written for.** The `Column` made for the horse's neck became the
  humanoid's whole torso with no changes, because it was defined by a centre line and a section, not by "neck".
- **A straight-legged rest pose leaves the solver no reach.** The humanoid's legs are 99.5% extended standing, so the walk
  has to lower the hips about 5 cm, which reads as a bent-kneed walk. A real stride gets its reach from pelvis rotation
  and tilt, which the planar solver does not have.
- **Per-part settings keyed by segment name must handle mirrored parts** (`forepaw` is two objects, `.L` and `.R`).

## Materials and baking

- **Read the texel density the build prints.** The Medusa's first bake came out at 1.7 px/cm with 1.9% of the sheet
  used: a few slivers along the edge of her face mask (three outline points in a near line) lay flat in UV but spanned
  surface where the mask curves back; averaging island scales blew them up to fill the sheet, and the packer put the
  rest of her in a corner. The pipeline now gives any triangle that is flat in UV its own unwrap (`unflatten_uv`):
  32.6% and 6.9 px/cm.
- **Edge-wear masks treat thin parts as all edge.** An inside-AO distance larger than half the wall thickness turns a
  6 mm strap into one big worn patch. Keep edge distances well under the thinnest wall.
- **Hide the originals while baking a joined copy.** Coincident geometry crushes the AO map (mean 0.17 instead of 0.5).
- **Patterns must be sized in texels.** A 6 mm braid at 8 px/cm is a moiré. Keep any repeating pattern at 6.5 texels or more.
- **Bake colour through an emission tap, not through lighting.** Each material exposes named taps
  (`CH_color`, `CH_rm`, `CH_emit`); the baker routes one at a time to an emission shader.
- **Albedo that looks right in a node preview is too bright under studio light.** Carbon wants about 0.025 linear,
  the sleeve about 0.25.
- **`nobake` is only valid while the geometry is unchanged.** UV packing is deterministic, so the same vertices give the
  same layout and old textures still fit. Any geometry change needs a rebake.

## Blender specifics (5.3)

- `Image.save()` on an untouched image copies the source file whatever `file_format` says. Touch a pixel first, then set
  the format, then save.
- Join objects only after collecting everything you need from them; joining invalidates the Python references.
- NLA tracks are the reliable way to get one glTF animation per clip (`export_animation_mode="NLA_TRACKS"`).
- The first Cycles render after an upgrade compiles the Metal kernels and takes about three minutes.
- Output from a redirected Blender run is buffered: wait for the process, do not poll the log.

## Rigging and motion

- **Axis-aligned bones make procedural motion trivial.** With every bone's rest orientation equal to world space, a pose
  is plain Euler angles and no per-bone frame maths is needed.
- **Weight muscles to both ends.** A sleeve rigidly bound to one bone detaches at the other. Blending from the bone at
  one end to the bone at the other makes it stretch like an actuator. Keep the ferrules rigid (first and last 12%).
- **Solve legs, do not animate them.** Two-link IK in the side plane with a planted-then-lifted foot path keeps paws on
  the ground for free; body motion can then be added without foot sliding.
- **Every clip must start and end in the same pose** so it loops and cross-fades cleanly.
- **I cannot watch motion.** Checks are stills: six to ten frames per clip from the side and from a wide angle.

- **Recorded motion beats procedural motion for a human.** My formula walk was bent-kneed and planar; the same rig
  driven by retargeted takes walks upright with hip sway and arm swing. Use formulas for animals nobody has captured,
  and retarget when good takes exist.
- **Retargeting is easy when rest poses agree.** With world-aligned bones and a matching rest pose, copying world
  rotation deltas is the whole method. Line the limbs up with the source limb directions first, and fix height by
  matching the lowest point to the source's, not by scaling the hips alone.
- **Find the source's floor from its motion, not its rest pose.** The showcase rig rests with the pelvis at the origin
  but animates standing on z = 0.

- **Study the animal before the solver.** Two facts from the reading changed the Komodo's design: the girdles are the
  still points of the body's wave (so they turn in place, they do not slide), and a running monitor holds its body
  and tail stiff. Both are the opposite of what I would have animated by guess.
- **Check a sprawler from above.** Side frames show almost nothing of a lizard's gait; the top view shows the bend,
  the foot placement and the tail.

- **Frame the check camera for the tallest pose, not the rest pose.** The bear's standing clips were cropped at the
  waist by cameras sized for it on all fours; the viewer needed the same headroom (`VIEW_HEIGHT`).
- **By the sixth animal a new one can be all reuse.** The bear needed no new geometry code, only numbers and motions.

- **Evenly sampled check frames miss fast actions.** The mantis strike lasts a tenth of a second; six frames over a
  3.8 s clip showed none of it. Render fast actions at named times (rest, wind-up, peak, contact, hold).
- **A new body plan can be mostly old assemblies in new roles.** The mantis's abdomen is a `tail`, its wing case a
  `carapace`, its raised prothorax a `Column`, and its legs use the lizard's 3D solver.

- **Check the ground during motions, not only at rest.** The spider stood clear of the floor at rest, but in the jump
  its abdomen went 69 mm under it while coiling and its fangs 56 mm under it on landing. Stills did not show either.
  `kit/ground.py` poses every other frame of every clip and reports the lowest point, the part and the time.
- **Check for parts passing through each other with a tool, at rest and in motion.** The spider's front legs went
  through the corners of its face at rest and I reported the model without seeing it; the user did, in one look.
  `kit/overlap.py` counts muscle triangles that cut through the body: 28 at rest before the fix, none after, and it
  also caught leg IV entering the abdomen during the idle turns. Run it before reporting.
- **A spider's jump is a snap, not a wind-up.** The legs are straightened by fluid pressure, so the launch takes two
  frames and the flight is long and flat (10 m in 0.6 s). The first version, a 0.1 s push and a 3 m lob, read as a hop.
- **Aim a chain by direction, not by joint angle.** The scorpion's first strike was written as extra curl per tail
  bone and the sting came down on its own back. Writing the pose as the direction each bone should point
  (`Scorpion.tail_to`) and summing the segments on paper put the tip 22 cm in front of the head on the second try.
- **The third animal of a family is nearly free.** The scorpion took the spider's legs, walk and eye, the mantis's
  blade and the Komodo's shell; it built and animated on the first run, and the two motion checks were clean after
  three small adjustments.
- **A snake follows a path; it does not wag.** Driving the joints with a sine wave makes a body that wriggles on the
  spot. Putting every joint on one fixed ground curve and feeding the body along it gives real lateral undulation:
  each part passes where the part ahead has been, and the speed falls out of the curve (0.74 m/s for the slither).
- **Pitch and yaw do not stack along a chain.** Lifting the snake's neck and then turning it 150 degrees round the coil
  rolled the head and put the jaw 18 mm under the floor. Keep raised poses in one plane, or solve them in world space.
- **Aim each bone in the body's frame.** After the snake's rolled head, the octopus's arms are posed by giving every
  bone an elevation and a heading relative to the body and deriving the local rotation from its parent's aim
  (`Octopus.arm`). Raised, spiralled and bundled arms all came out level the first time.
- **A limb can be a leg some of the time.** The octopus stands and crawls by handing three of each arm's seven bones
  to the leg solver, and swims, reaches and rests with the same bones aimed directly; `blend` joins the two.
- **A straight radial star reads as a spider.** The first octopus had eight straight arms and looked like one.
  Curving each arm in plan, alternating the direction, and hooking the tips changed the read more than any detail.
- **Do not name a clip `Rest`.** The viewer already has a Rest button for the bind pose; the octopus's resting clip
  collided with it and gave two buttons with one name. It is `Sleep`, like the other animals' resting clips.
- **A new check should be run on the old work.** `kit/ground.py` was written for the spider; run on the six earlier
  legged animals while the Malinois baked, it found motions well under the floor in every one (a 302 mm tail in the
  feline's roll, 174 mm in the bear's sit). They had only ever been looked at in stills.
- **Re-parameterising is real work in the numbers, not the code.** The Malinois needed no new geometry, but its first
  build was a stocky wolf: the feline's fat lower-leg muscles, deep chest and long claws. Lean lower legs, a higher
  belly line and blunt nails made it a dog.
- **An animal that carries its head high cannot roll like a cat.** The dog's head went 268 mm under the floor on its
  back. The neck is laid along the ground when it lies down, the feet are drawn in as it goes over, and the axis it
  rolls about is raised to half its lying height.
- **Read the dimension lines, not only the spec table.** The Shisa's table says 1,000 mm at the shoulder; its drawing
  puts 1,000 mm on the whole height and 1,800 mm on the whole length. Built to the table it came out 2.5 m long. Built
  to the drawing, length, height and stance width all matched at one scale factor.
- **Ornament attached to a limb must be checked through that limb's motions.** A curl behind the Shisa's elbow was
  fine standing and went 87 mm into the floor when the forearm lay flat. It moved to the upper arm, which turns far less.
- **A mythical animal is carried by a few signs.** For the Shisa: a head too big for the body, a short broad muzzle,
  an open snarl, curls on the crown, a ruff, a tail that is one upright spiral, blunt heavy paws, and the two poses
  people know it by (the gate sit and the lion-dance bow).
- **Author a leaning body upright, then lean it.** The second gorilla's torso is written as an upright ape's (z up the
  spine) and passed through one rotation about the hips (`tp`); limb muscles are placed with `on(a, b, f, front, side)`,
  which measures from the bone itself. Changing the lean from 46 to 52 degrees was one number.
- **An ape reads from its slope and its arms.** G-01's back was level (shoulders 0.96 m, hips 0.86 m) and it read as
  a beast. G-02 has shoulders at 1.2 m over hips at 0.7 m, arms longer than its legs and no shoulder frames.
- **A scene is a layout, not a model.** The Guardian Gate stores positions and motion names only; the page loads each
  model's existing viewer files, so two Shisa cost one download and a fix to the Shisa shows up in the scene.
- **Give a pose its own looping clip.** The Shisa's `Guard` sits down, opens its mouth and stands again. A statue needs
  to stay seated, so `SitOpen` and `SitShut` hold the seat and only breathe.
- **Blender's glTF importer adds a helper sphere** (`Icosphere`, the bone shape). It is not hidden from scripts: it
  made the scene 2 m taller and would have been exported with it.
- **A flat plate needs its mesh density set.** The plinths' first build had 950,000 triangles: `core.box` meshes its
  faces on a 4 mm grid unless told otherwise.
- **A jump on the spot needs the floor to move.** The clip keeps the body over the origin and carries only the rise
  and fall; the distance is a table (`travel()`) that the viewer applies to the floor. Use real gravity for the arc:
  a 0.6 s flight rises 0.44 m, and guessed timings look floaty. A fast clip needs wide floor squares (`CELL`), or the
  grid strobes.
- **A foot that leaves the ground should start from where it stood.** Compute the riding offset from the body pose at
  lift-off and at touch-down (`Salticid.rel`), so the switch between planted and airborne has no jump in it.
- **A concept drawn at 22 mm needs a stated scale.** The spider is built at about 50 times the sheet: 1.13 m of body.

- **Look at a strip of frames before anything else.** `kit/strip.py` draws twelve or more frames of a motion on one
  sheet in four seconds, from the side, the front and three-quarters. The first sheets showed in one look what two
  days of six-frame checks had not: the bear standing with its forearms folded back into its own rib cage, the
  scorpion's sting landing on its own face, half a metre short of its claws.
- **A planar leg solver folds an upright animal's elbows into its chest.** Elbows fold backwards; stand the animal up
  and backwards is inwards. Turn the solved arm about the line from shoulder to paw (`elbows_out`): the paw stays,
  the elbow goes out to the side. The same turn is what makes a chest beat read as one.
- **Measure a reach before animating it.** The scorpion's tail is 1.40 m and its claw tips are 1.5 m from the tail's
  root: no tail pose could land the sting in front of them. The body had to supply the rest (abdomen flexed up 18
  degrees, a 7 cm lunge, the catch held closer). Twenty lines of arithmetic found the pose; guessing had not.
- **Try numbers in batches.** `kit/tune.py` runs the ground and overlap checks for several settings of a motion in
  one go. The dog's roll went from 271 overlapping muscle triangles to 54 in two runs.
- **Speed at 30 frames a second is distance per frame.** The snake's strike already took two frames. Faster meant
  one frame, a longer throw, and above all a quick recoil: a strike that holds its end pose for half a second reads
  as slow however fast it got there.
- **An ornament or a frame must be seen to join the structure.** The Shisa's side frames stopped 7 to 11 cm short of
  the spine with nothing between: correct for a moving shoulder blade, but it read as loose trim. Two yokes across
  the back, standing on the keel, made them part of the machine.
- **A part that must move needs its own bone, even late.** The mantis's wing case was part of the body, so the
  abdomen curled up through it in the threat display. Splitting it off cost one rebake and gave the display its best
  feature: the case thrown up.
- **A wing that is built folded is posed by three turns that do not commute:** swing it out, lay it level and flap,
  twist about its own span. Compose them as matrices and convert once; adding Euler angles gives a wing that sweeps
  when it should feather.
- **Do not loop over quoted pairs in zsh.** `for job in "a 1" "b 2"; do set -- $job` does not split the words; every
  build in the queue "ran" in a second with a wrong path and exit code 0. Write a script with a function and one
  line per job, and read the log's first lines before trusting an exit code.

- **An actuator should not be skinned across a joint; its tendon should.** The hand's first knuckle actuators were
  blended between two bones along their whole length, like the animals' muscles. On a limb that bends 30 degrees
  that reads as a stretch; on a finger that bends 90 it threw long hoops of sleeve out over every knuckle. Each
  actuator is now rigid on its own bone and pulls a thin steel tendon over the knuckle, and only the tendon is
  skinned to both bones. It is also the truer mechanism.
- **Find out where the reference makes contact.** The pinch was first solved pad centre to pad centre and could not
  get the faces closer than 45 degrees to opposed: with this thumb it is out of reach. AUREL-01's pinch is
  calibrated at the soft end of the fingertip. With that contact point the same solver gave 1.5 mm and exactly
  opposed for three fingers.
- **Test reach by brute force before blaming the solver.** Sixty thousand random poses took a second and showed
  that the pad-to-pad pinch was a limit of the joints, not a bad search.
- **Give a search many starts and a seed.** A downhill simplex from one guess walked the fist's thumb out to the
  side to avoid the fingers and stayed there. Five thousand seeded random poses first, then the simplex from the
  best three, finds the wrapped thumb every time and always returns the same pose.
- **Order a sequence so nothing has to pass through anything.** The thumb closes only over fingers that are already
  curled and leaves before they open; the showcase goes fist, open, point rather than fist, point, because between
  those two the index would have to swing through the thumb.
- **The kit has absolute sizes in it.** Rod ends were never thinner than 2.6 mm, which is right on a cat and as
  thick as the actuator itself on a finger. Small work needs those floors passed in (`rod_min`), and a size factor
  chosen for the texture detail (the hand uses 0.06) rather than for its length.

- **A part made for one model should be built in its own frame and carried.** The hand was written standing up
  with its turns as world angles. Mounting it hanging from a wrist, and mirrored for the right side, needed every
  turn made in the hand's frame and carried by its placement (`P R P⁻¹`); after that the same poses worked on the
  study, the humanoid and the gorilla unchanged.
- **Draw the palm for one table and stretch it.** The palm's shell, pad and muscle anchors were written in numbers
  for the human hand. Stretching them by the width and length of whatever finger table is given made the gorilla's
  hand a table of numbers, with no new geometry code.
- **Size a mounted part against its neighbours, not only its function.** At the gorilla's size the first gorilla hand
  worked (knuckle stance on the floor, no clashes) and looked like a child's hand on that forearm. Widening the
  fingers by a third fixed the look without changing its reach, because widths do not change the stance's height.

- **Ask what the joints can do before deciding what the grip is.** The foot's first grasp put the great toe's pad
  opposite the pads under the other toes, across a bar, as the user's AUREL-02 does. With this great toe (based beside
  the toes, not behind them) the solver's best was faces at right angles. Seven candidate grips tried in one run
  showed what it can do exactly: clamp a bar against the side of the second toe, which is also how an ape's foot
  holds a branch.
- **Let the ground place the foot.** The foot's clips never say where the foot is. They give joint angles, and one
  function moves the shin so the lowest point rests on the floor over its own spot. Tiptoe, heel strike and the
  roll between them then need no numbers of their own, and the floor check reads 0 to 0.4 mm in every clip.
- **A plate's face must be square to its run.** `parts.link` lays a plate in the plane of the normal it is given; a
  bar that slopes needs that normal tilted with it, or the bar comes out level at its starting height.
- **Proportion is two numbers, not one.** The gorilla's fingers were widened by a third to suit its forearm and
  came out 23 mm long and 42 mm wide at the tip. Lengthening the finger bones and shortening the palm by the same
  amount kept the knuckle stance at its height and also closed its fist properly (thumb 8 mm off, from 16).
- **Do not edit the kit while a queued build has yet to start.** A build reads the kit when it starts; two builds
  queued one after the other would have read different kits. They were started together instead.

- **Take a part out of an animal by its frame.** The claw, the pincer, the sting tail and the tentacle were each
  written at one animal's coordinates. Rebuilding each in its own frame, with a matrix to place it, took an hour for
  all four, and each then stood on its own card and fitted a second body without another change.
- **A module can borrow the host's bones.** The tentacle arm runs through the humanoid's shoulder, elbow and wrist,
  and its modules at those three points keep the arm's bone names. Every recorded motion then drives it with no
  retargeting; only the four modules in between needed poses of their own.
- **A variant should be a sentence, not a copy.** An equipped body is a file that says what its host wears and
  imports the host. The host built with nothing changed is checked identical to its baseline, so the sockets cost
  the original nothing.
- **The bare pose of a rigged model is not its rest pose once clips are baked.** `overlap.py` reported a tentacle
  inside the chest "at rest": with no action chosen, Blender plays every baked clip's first frame together, and the
  boxing clip starts with the hands at the chest. The per-clip rows are the ones to trust.
- **A new part needs the old motions adjusted, not rewritten.** The mantis's strike, threat and groom all worked
  with pincers after two numbers were added (how far the tibia folds, how near the limb comes to the mouth).

- **Put shoulders and hips outside the cage.** The toad's first hips were 2 cm inside its own rib cage, copied from
  a taller animal's proportions; the overlap check counted 37 muscle triangles a side in the pelvis at rest. Moving
  the joints out to the cage's widest line cleared every one.
- **A leap is a line and a reach.** The toad's push is written as: drive the body along a launch line until the
  hind legs are 97% straight, with the foot rolling onto its toes to supply the last of the length. Distance, angle
  and flight time are then the only numbers, and hop and jump are the same code.
- **A long foot that pitches in the swing drives its toes into the ground.** The Komodo's swing pitch on the toad's
  ten-centimetre hind foot put the toe pads 26 mm under the floor. The toes go up in the swing, not down.

- **A leg that is nearly straight at rest has no stride (again).** The crawler's sheet draws low, flat limbs. With
  the knee at the sheet's height the legs were 92% extended standing and a 24 cm stride overreached. Raising the knee
  7 cm gave 85% and an 18 cm stride; the price is a silhouette a little more like a spider.
- **A machine with no front needs the floor to move its way.** Walking sideways with the viewer's floor running
  backwards reads as slipping. A clip can now carry a heading.

- **Long toes need their own rule in the swing.** The gait pitched the toes with the foot at lift-off, which is
  unnoticeable on a cat's paw and drove the raptor's 30 cm toes 13 cm into the ground. The gait now has `follow`.
- **Every pose that lowers the body needs the arms and head checked against the floor.** The raptor's first crouch
  put its hands 37 cm under the ground and its first rest put its jaw 19 cm under. Both were one ground check.
- **Run the ground and overlap checks before looking at anything else on a new animal.** On the raptor they listed
  seven faults in one pass (toes, claws, hands, jaw, hips inside the cage, shoulders, hip bar), each a number to change.

- **Write a big model at a small one's size and multiply.** The Valkyr's proportions are the humanoid's, in the
  humanoid's units, times one constant; the biped motions, which scale with leg length, ran on it unchanged. Half
  speed did the rest of the work of making it heavy.
- **A foot longer than the motion expects digs in.** The biped's foot rolls about a point 14 cm ahead of the ankle
  (at 1.9 m); an armoured foot reaching 21 cm ahead put its toe 16 cm under the floor. The rolling point is a tunable.

- **A new model must be made of the series, and measured from its sheet.** The first Valkyr scaled the humanoid up
  and covered it in flat pale slabs: a new material, a new primitive, and the humanoid's proportions on a concept
  whose legs are 58% of its height. It was rejected in one look. The second took its limbs, torso, armour and hands
  from assemblies already in the kit and its landmarks from fractions measured off the sheet, and needed one new
  assembly (`shield`). Start there next time.

- **Prove a fit with the body, not with numbers.** The cockpit was sized from H-01's joints, then H-01 itself was
  posed in the seat and every one of its parts tested against the pod. The first run found feet through the
  footrest, hands through the grips and hips on the pan; the same test, run on the pod inside the Valkyr, found the
  shoulder bar, the back muscles, the neck joint and the hatch's hinge in the way. Three iterations each.

- **Check a new pose from the front as well as the side.** The dragon's flight looked right in side view; from the
  front its fore legs were raised beside its head like wings, because the leg solver keeps the elbow's rest bend
  plane and the tucked target swung that plane upward. Trailing the feet down and back fixed it. The overlap check
  found it first (mane against upper arm).

- **Fit a pose to points measured off the sheet; do not guess its angles.** The Western Dragon's wing has seven angles
  for its plane and wrist. Guessed, the standing wings crossed over each other in an X with their tips 17 m up. A fit
  of the wrist and the four finger tips to points read off the sheet (random starts, then a simplex, with a pull
  towards sensible angles) gave the sheet's raised wing at the first try, and a folded state fitted the same way lay
  close enough to it to blend smoothly.
- **A fan closes by angle, not along a line.** A tag blends weight along one line; a sail between two finger spars
  needs every vertex weighted by its angle between them. `MD.vw` carries such weights through merging and mirroring,
  and the sail then closes like a fan. Build it open: closing compresses it, opening a sail built closed stretches it.
- **A large animal's girdles go further out.** The dragon's hips and shoulders were set at the cage's wall, as the
  Komodo's are; at this size the leg cages are a metre thick and the overlap check counted 28 muscle triangles in the
  trunk in the run. Moving the joints 0.5 to 0.75 m out took it to 12.
- **Beat shallower near the ground.** The first downstroke of the take-off put a wing tip 0.55 m under the floor while
  the body was still low; the beat now grows with the height.

- **A wing that is also a leg is still built spread.** Standing, the wyvern's fan lies close against its forearm;
  built that way its inner bay would stretch more than three times in flight. Built spread, the leg solver would have
  bent each arm sideways (it keeps the rest bend), so the arm is reached to its hand by the wing's own `reach`, with
  the elbow bent towards a pole, and the hand that stands on the ground is a bone of its own.
- **An elbow above a hand placed far forward goes forward or out.** Square to the line from shoulder to hand, "up" is
  up and forward; the sheet's high elbows are out to the side, and the pole says so.
- **Lift a hand off the ground before it becomes a wing.** Blending the standing arm and the flying arm bone by bone
  swung the hand a metre through the floor; raising the hand's target first, then blending, kept it clear.
- **A sail tied to the shoulder joint is a sliver.** The shoulder lies on the wing's leading edge, so an inner trailing
  edge from the last finger to it, bowed inward as a bat's is, ran along the humerus: from above there was no inner
  wing. Meeting the body 2 m behind the shoulder, on the axis the wing beats about, gave the whole inner wing and keeps
  still in flight.
- **What is per bone adds up over a longer chain.** The Kami Dragon's flight lifts each tail bone a little more towards
  the tip; on the Kami-Ryu's tail, twice as long, that curled it up like a scorpion's. Lengthening a chain is a change
  of numbers only if every per-bone amount is checked again.
- **A variant can change proportions too.** A host that reads its neck, tail or body line through `modules.loadout`
  takes a longer one from a four-line file, and built with its own numbers stays identical to its baseline.
- **A bird's foot is a hand.** Three fingers splayed forward and the thumb turned back as the hallux gave the eagle
  its talons with every actuator, tendon and knuckle of the dexterous hand; what was new was splay, a claw and turning
  each toe about its own axis.
- **A standing foot cannot curl its toes.** The claws rest on the ground, so any curl drives them in (14 to 20 cm in the
  first idle and walk). Lift the claws to knead, and curl only once the foot is clear of the ground.
- **Check the sign of a new bone's turn against the rule.** "Positive lowers whatever points forward" lifts a tail,
  which points back; the eagle's tail went 25 cm into the floor whenever it reared until the sign was fixed.
- **A feather turns on its root as the wing folds.** With the secondaries fixed to the forearm, the folded wing's
  feathers would stand out sideways or up; a bone each, at an angle that changes with the fold, lays them back.
- **Aim an arm by where its parts point, not by its joint angles.** A shield that must face forward and a spear that
  must lie level are each a direction and a roll; the Valkyrie's arms are written as the directions of the upper arm,
  forearm and hand, the shield's face and the spear's line, and the turns are derived (`Spearman.arm`). Every pose read
  right on the first render; the moves between them did not (next).
- **A pose that reads right can still flip on the way to it.** The guard blended the hand's two directions (along the
  fingers, across the palm) separately; from the carry they turn in opposite senses, pass through parallel, and the
  hand turned over in one frame (180 degrees at 0.68 s, the flash the user saw). Blend a hand's frame as a whole, the
  short way round, and run `kit/snap.py` on every clip: it flags a bone that turns much further in one frame than in
  the frames either side, which a fast sweep never does.
- **A wave must close on its clip.** The Medusa's snakes writhed at their own frequencies and every loop seam popped 41
  to 50 degrees; rounding each wave to a whole number of turns in its clip (and integrating a rate that rises when the
  snakes are roused, so the phase never jumps) took the fastest turn in the idle to 7 degrees a frame.
- **Things side by side should move in step.** Snakes given scattered phases moved against their neighbours and passed
  through them (up to 214 triangles a pair); neighbours nearly in step sway together like hair, a wave running round
  the crown, and the worst pair in the idle fell to 49. Reared at one point they crowded again (254): each needs its own
  mark, spaced round a halo.
- **The least turn flips.** Aimed by the shortest rotation from where each bone pointed, a snake that had to turn nearly
  end for end flipped 143 degrees in a frame. Turn each bone as a frame (its direction and a second axis carried down
  the chain without twisting) and nothing near-opposite is ever undefined.
- **Check the numbers before chasing a pose.** In a three-quarter strip one arm looked tucked against her side while
  the other spread; forward kinematics put both hands at 0.655 m out, mirror images. The near arm pointed at the camera.
- **A recorded clip carries moves, not heights.** H-01's walk retargeted to the Medusa, whose ankles stand 26 cm up on
  heels, kept her within 7 mm of the floor unchanged: the pelvis is recorded as an offset from rest.
- **A borrowed step fits only the foot it was written for.** The Minotaur's walk rolls the foot about a hoof's toe
  0.37 m ahead of the ankle; on the Oni's dragon feet, whose claws reach 0.55 m, it drove them up to 202 mm through the
  floor, and moving the pivot out only halved it (the swing pitches the foot toes-down too). Measure the new foot's
  reach against the clip's, or plant the foot afterwards: `motions.Planted` turns it toes-up about the ankle just
  enough. Then make the foot a word: `kit/menagerie/feet.py` carries each foot's ankle height, reach and ground
  wrappers with it, and the Oni's gorilla feet and hooves stood on the floor on their first build.
- **Search a pose numerically when it collides.** The raised club went through the Oni's head in the roar (596
  triangles); scoring the club's clearance from the head at seventeen wrist angles found 95 degrees (1.04 m clear)
  in one run.
- **A rigid grip cannot change in the hand.** Carried with the thumb toward the tip, a spear held overhand by the head
  needs to lie end for end in the fist; there is no way from one to the other but letting go. The Valkyrie tosses it
  (`Spearman.flight`), and every clip starts and ends in the carry so the viewer's crossfade never has to turn the
  hand half over either.
- **A stance turns what the hand holds.** Arm directions live in the chest's frame: the thrower's stance turns the
  shoulders 14 degrees and the javelin swung 14 degrees off line, its butt behind her head. Write a held line turned
  back by the stance's twist.
- **A falling prop must arrive along its length.** A ballistic arc keeps its sideways speed to the catch, and an
  upright shaft moving sideways sweeps its lower half through the forearm behind the catching hand; finishing the turn
  early did not help while the drift went on, nor while the stance was still straightening. Drift and turn in the
  first 70% of the flight, then drop; have the hand waiting and the body still; swing the butt out past the arm, and
  let the empty hand drop in out of the way after it lets go.
- **Test every frame for anything fast.** Every third frame (the overlap check's default) missed a shaft crossing the
  arm for one or two frames; `overlap.py every=1` found it.
- **Recorded motion carries to a larger figure by its rotations.** Only the pelvis's travel and the speeds need
  scaling (by the legs' length, not the height); the feet then float about a centimetre, which is near enough.
- **What is held hangs from one side.** The spear's bone names `forepaw.R` as its parent; the rig and the pipeline take
  a mirrored bone's side by name.
- **The check tools take the first script in `scripts/`.** A fitting script beside the animal's file was imported in
  its place; helpers live in `scripts/fit/`.

- **Garments are built closed, not sewn.** In the gi spike, two flat panels sewn shut across 0.8 m never closed (gaps
  of 9 to 24 cm, 94% stretch); the same panels wrapped round the body with their seams shared hung cleanly at 1%
  stretch. Blender's sewing springs only close short gaps.
- **Slice the collider, do not sample it.** Sections taken from proxy vertices in a thin slab collapsed wherever a
  tube slanted through the slab (24 points, a ring 4 cm from the axis); cutting the proxy's edges with the plane is
  exact and cheap.
- **bmesh vertex indices are not set until `index_update()`.** A seam measurement read every pair as the same vertex
  and reported 0.0 mm gaps; the render showed panels 20 cm apart. Look at the picture before trusting a perfect
  number.
- **Adding objects after a cloth simulation invalidates its cache.** Frame-setting back to the end then shows a cloth
  that was never simulated. Render static copies of the states you kept.

- **Copy the construction, not just the look.** The first gi jacket drew its yoke from the robot's outline and set a
  round sleeve into a tight ring at the arm root, and came out a down jacket. Cutting it as a gi is cut (the shape
  measured from photographs, sleeves straight and set on along a long slit in the body's side) fixed the shoulders at
  once, with half the stretch.
- **Dress over an envelope, not over the garment.** Cloth draped over another draped garment snags on its folds and
  shreds; a smooth envelope round the garment underneath gives the second layer something to slide on, and makes it
  certain the first cannot show through.

- **Measure before guessing.** The "blade" through the gi's front survived four fixes aimed at the wrong part (the
  collar band, the belt, culling, a weight cap) because a contact sheet cannot say which vertex is wrong. Skinning
  the garment at that frame in numpy and listing the most stretched edges with their weights named the cause in one
  run, and then the next four causes in turn.
- **Nearest is not the same as allowed.** Copying skin weights from the nearest body points fails wherever another
  part of the body is nearer than the part the cloth belongs to (hands beside a skirt, an elbow beside the ribs).
  Look further for the nearest points that carry a bone the cloth may follow.

- **A robot's skin weights are not a person's.** Copying weights from H-01's rigid parts copied their narrow joint
  blends (thigh to shin over 2 to 3 cm), and the pants over a bending knee stretched 3.5 times in that band. Cloth
  needs its joints blended over a cloth-like width: widen the copied weights along the garment, but hold what was
  copied from a muscle or a lower layer, or the cloth lags what is under it.
- **Layers move together only if they share their influences, all of them.** The skirt copied the pants' weights and
  still passed through them, because the jacket's list of allowed bones lacked the shin that H-01's thigh actuators
  carry up to the hip. Allow every bone the lower layer uses.
- **Check the check.** The first motion test counted points near openings and at the armpit that were not visible
  (the nearest face switched from jacket to sleeve; a ray from the bone's axis ran along the sleeve). Tie each point
  to its own patch of cloth and require that nothing else covers it before calling it a clip. And count only what is
  drawn: removing hidden faces leaves their vertices in the mesh, and half the pants points the check followed were
  never on screen.
- **Closest is the wrong point when a layer stands off.** The jacket's double front flap stands 6 cm off the pants at
  the waist, where the pants' weights change fast with height; the closest pants point was 1 to 2 cm lower, so the
  flap followed the legs and the pants under it the pelvis, and the pants came through in the guard. Copy from the
  same height (weight height more in the distance) or along the normal.
- **Blender's glTF importer adds an object of its own.** It makes a hidden icosphere as the bones' display shape (in a
  `glTF_not_exported` collection). A check that moves the imported objects into view rendered it as a ball bigger than
  the model and scored every LOD's silhouette 1.000. Drop the importer's helpers, then look at the sheet.
- **Test the rest pose too.** The motion test followed body points that start under the cloth, so a pectoral tip
  already through the jacket at rest passed every clip and was found by eye. Count what shows at rest as well.
- **Compare against the last version with the same tool.** Running the new check on yesterday's commit turned "looks
  better" into numbers: pants through the jacket when running went from 128 mm to 0.
- **Leave room for the clothes.** The Red Witness's forearms hang 4 cm from its thigh muscles; a sleeve, a skirt and
  the pants need about 11 cm. No skinning fixes that: the jacket came out a sack and the pants pushed through the
  sleeves. Holding the arms 10 degrees out (at rest and so in every motion, as a heavy body in a thick gi does) took
  cloth inside the body from 488 sample points to 5. Measure the gap between the arms and the body before draping.
- **The nearest bone is not the one that moves a point.** The proxy took each body point for the nearest bone, so the
  Red Witness's forearms took its pelvis wings and the sleeves ballooned; taking the bone that moves it made the arm
  take the latissimus that runs to it. What worked: the nearest of the bones that move it.
- **Cloth bridges hollows; a proxy of bumps makes a lumpy drape.** Each proxy ring as the convex hull of its slab of
  the body gave the cloth something to span.
- **Build near the pose you drape in.** Built with the arms 25 degrees out and lowered, the jacket's shoulders buckled
  into a fold across the back; at 12 degrees, cloth inside the body went to none and the worst stretch from 35 to 11.
- **A small triangle's normal is not a direction.** The motion check measured depth along the face normal of a 2 cm
  patch; when its corners moved 2 cm apart the normal swung round and a point still behind two layers of cloth read
  as 10 cm through. Use the smoothed vertex normals.
- **An edge that lies on the other layer is not an opening.** The cull kept the body near every lapel edge, so the
  abdominals under the crossed fronts (two layers of cloth over them) were kept and came through in the rolls. Ask of
  each edge whether another layer lies behind it or over it.
- **But the body must still show where it is seen.** On a deep chest the lapels stand 11 to 13 cm off it: the chest
  in the V was culled (its one outward ray hit a lapel) and the V showed the inside of the jacket. A body point that
  sees a lapel or neck edge within 15 cm, with no cloth between, stays.
- **A sleeve never follows a leg.** The inside of the Red Witness's lower sleeves copied up to 24% thigh weight from
  the hip beside it; in the jog the sleeve stayed behind and bunched at the elbow. Limit the sleeves to the arm's
  bones.
- **Judge a colour close up, with the glow off.** The Red Witness's black belt and chest looked grey in the viewer;
  I blamed the UV packer and re-baked twice. It was the viewer's glow bleeding the white gi over parts a few pixels
  tall: zoomed in, glow off, they were black. Look at the thing itself before theorising about the pipeline.
- **A fix in motion can uncover a fault at rest.** Making the belt's tails follow the skirt stopped them sinking in
  the walk, and the close-up then showed the collar band coming through a tail in every frame. Look at the frame again
  after every fix.
- **When a garment keeps clipping on one part, question the part.** H-01's trapezius rose steeply to the skull through
  the gi's collar, and three iterations of weights and hiding only shrank the clip. Moving the actuators to the foot of
  the neck column (a change to the host, the user's call) emptied the collar in every clip at once, and redraping on the
  new body halved the layer problems lower down: a garment draped round a part carries that part's shape everywhere.
  Redrape after any change to the host, then measure both drapes.

## Film

- **Make an animatic before the render.** A quarter-size, 12-sample, every-sixth-frame pass of the whole film took one
  minute; the final took two hours. Every framing fault was visible in the animatic.
- **Have someone else read the storyboard.** A director agent given the sheets and the shot table found what I had
  passed over: at almost every signature move a pillar or a guardian stood directly behind the animal, five shots were
  the same low three-quarter medium, and the sun I had described as behind the gate was in front of it.
- **Stage the move where the background is clean.** Inside the opening, far enough out to pan with, or from above.
- **Backlight a pale machine.** Front-lit, pale actuators on pale paving under a pale sky sat in the top third of the
  range. With the sun behind the gate the bodies are rim-lit and the shadows run at the camera.
- **Keep the sun out of frame or pay for it.** At 20 degrees off the axis it bloomed across half of several frames;
  at 40 degrees it stays outside most lenses and still back-lights.
- **Parallel Blender processes do not speed up one GPU.** Three or five at once gave the same 5.3 s a frame overall.
- **The first frame of a session can take minutes** (the render kernels are compiled once); time the second.
- **Blender encodes video without ffmpeg installed:** an image strip in the sequencer and an FFMPEG output.

## Web viewer

- **One NaN can black out the whole canvas.** Decimating a level of detail can leave a triangle with no UV area, and the
  glTF exporter writes a zero tangent for its corners; three.js normalises it into NaN, and the glow (bloom) spreads it
  into black squares that flicker over everything. Thirty published files had a few such vertices; the Wyvern's LOD1,
  which the public site opens on, showed it first. The pipeline now mends every export (`menagerie/gltfmend.py`) and
  the publisher refuses a file with a zero tangent.
- **Tell the browser to ask again.** The local server sent no cache headers, so a browser kept an old catalog and the
  Medusa's card did not appear for the user although the server had it. `hub/server.py` now sends
  `Cache-Control: no-cache` with every file.
- **Test the copy people see, at the level it opens on.** The local checks used LOD0; the public viewer starts at LOD1.

- Artifacts do not serve `.glb`; a local server does. The local viewer loads GLB directly.
- A missing `<meta charset>` turns `·` into `Â·`.
- A long control bar must be allowed to wrap, or its last buttons fall off a phone screen.
- Skinned meshes need `frustumCulled = false`; their bounds are those of the rest pose.

## Working method

- **Keep a fast loop.** `clay` builds in seconds with flat colours and no exports; use it for shape and motion, and bake
  only when both are right.
- **Verify the thing itself.** Re-import the export, open the page in a headless browser, press the buttons.
- **Say what was not checked.** Stills are not motion; a GLB that imports is not an asset tested in an engine.
