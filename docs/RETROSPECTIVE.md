# Retrospective

Written 2026-11-14, after two days of building: fifteen models, one scene and one film. It records what worked, what
did not, and where things stand, so a fresh session can start from here. The rules each mistake left behind are in
[Lessons](?doc=LESSONS.md); this page is the account.

## Where things stand

| | |
|---|---|
| Built on the kit | Feline, Gorilla (G-02, and the first pass G-01), Malinois, Shisa, Horse, Humanoid, Bear, Komodo, Snake, Scorpion, Mantis, Jumping spider, Octopus, Torii |
| Also built | Guardian Gate scene, Gate Procession film (59.5 s), Red Witness helmet and figure (before the kit) |
| Concept only | 14 Velociraptor (15 Giant Mosquito was built later the same day: see the end of this page) |
| Repository | github.com/jelaludo/mech-menagerie-source (named Jela-Mech-Menagerie until 2026-11-17), private. Last commit `cb3892f`. The film, the feedback notes feature and this page are not committed yet |
| To do | the table in [Reuse plan](?doc=REUSE.md): the two new animals, floor contact in six older models, limb muscles against the rib cage, texture budget for the large models |
| Feedback | notes written on the landing-page cards are saved in `feedback/<model>.txt`; read them before working on a model |

## What went well

**The shared kit.** After the feline, each animal was mostly a table of numbers. The Malinois and the Shisa are the
feline's file with a scale factor; the scorpion took the spider's legs, the mantis's blade and the Komodo's shell and
built on its first run. By the end a new body plan cost one or two new assemblies and a motion set.

**Regression baselines.** Every model's geometry, bones and motions are hashed. After each kit change every earlier
model was rebuilt and compared; all of them stayed identical through about forty kit changes, so no baked texture was
ever invalidated by accident.

**Checks written as tools.** `kit/ground.py` (lowest point through every motion) and `kit/overlap.py` (muscles cutting
through the body) found faults that stills had not: the spider's abdomen 69 mm under the floor, the dog's head 268 mm
under it in a roll, rear legs entering an abdomen during a turn. Once written, a new model went through them before
its bake.

**A fast loop before the slow step.** A clay build takes seconds and a 4K bake takes minutes, so shape and motion were
settled in clay and the bake ran in the background while the documentation was written. The film used the same idea:
a one-minute animatic before a two-hour render.

**Ideas that turned out to be the right ones.**
- The snake follows a fixed curve on the ground, so it travels instead of wriggling on the spot.
- A chain is aimed by the direction each bone should point (the scorpion's sting, the octopus's arms, a dog's tail).
- A jump is played on the spot with real gravity, and the distance is a table the viewer or a game applies.
- A leaning body (the second gorilla) is authored upright and passed through one rotation.
- A scene stores only a layout and loads the models' own files.

**A second pair of eyes.** The director agent, given the storyboard and the shot table, found what I had passed over:
a pillar or a guardian behind the animal at almost every signature move, five shots that were the same shot, and a
sun that was in front of the gate when I believed it was behind. The film changed more from that review than from
anything I did alone.

**Short, direct feedback from the user.** "Reads as a quadruped rather than a biped", "front legs clip through the
face", "the jump must be much further and much more sudden", "closer to a stocky dog than a lion": each was one line
and each moved the work more than my own checking. That is the reason for the feedback notes.

**Housekeeping.** One commit per model with the docs updated in the same change; the landing page, the catalogue and
the to-do list stayed true to what was built.

## What did not go well

**I never watched anything move.** Every motion was judged from still frames at chosen times. The viewer tests
confirmed that pages load and buttons work, not that a walk looks like a walk. Every report says so, and it is still
the largest gap: timing, foot slide, pops at loop points and blends in the film are all unverified.

**Likeness to the concept sheets.** Every model's "falls short" list is mostly this. Heads are smooth shells with
windows where the sheets are sculpted. Legs are link plates with fat actuators where the sheets show armour and
shaped limbs. Named cases: the Malinois reads as a heavy wolf, the Shisa's legs are a crouching cat's and its curls
stand up like antennae, the octopus's mantle is a cage, the snake's cage hides its muscles, the first gorilla was the
wrong body plan and the second still has block hands.

**Faults found late, and some still open.**
- The six older legged models go through the floor in several motions (rolls by 250 to 350 mm, the bear's sit by 174 mm). Found only when the ground check was run on them; not fixed.
- Upper-arm and thigh muscles graze the rib cage on the feline, Malinois and Shisa. Not fixed.
- The spider was reported with its front legs through its face. The user saw it in one look; I had no check for it yet.

**Sizes drifted from the sheets.** Several models came out longer or wider than drawn (Komodo, bear, Malinois, scorpion).
The Shisa was first built to the spec table's shoulder height and came out 2.5 m long before I read the dimension lines
on the drawing.

**Texture budget.** One 4K set per model is sharp on the small animals (17 px/cm on the snake) and soft on the large
ones (5.8 on the bear, 3.5 on the torii). Decided nowhere; listed as an open question since the gorilla.

**The film's weak points.** The mantis's strike does not read from the front, the sun still blows out the edge of two
shots, the guardians' bow is small in the frame, the world is an empty paved plain and there is no sound.

**Time lost to my own slips.**
- Shell mistakes: helper functions defined in one command and used in the next, unquoted patterns, loops over quoted pairs.
- A plinth meshed at 4 mm (950,000 triangles), an importer helper sphere left in a scene, a clip named `Rest` colliding with the viewer's button, a body-plan name reused.
- Running several Blender processes on one GPU to render faster: no gain, measured after the fact rather than before.
- Describing the film's lighting from the numbers instead of looking at where the shadows fell.

**What was never tested.** Nothing has been opened in a game engine. Exports were not checked one by one, though the
film and the scene did load thirteen rigged GLBs back into Blender and play their motions, which is some evidence the
files are sound.

**Long, unattended turns.** Several requests ran for hours with only short status lines. The reports at the end were
complete, but a wrong direction (the first film lighting, the first Shisa size) could run a long way before anyone
looked.

## What I would do differently

1. Record every motion as a short clip and look at it, or at least at a dense strip of frames, before reporting.
2. Run the ground and overlap checks on a model before its first bake, and on every model after a kit change to motions.
3. Read the dimension lines on a concept sheet first, and compare the clay build to the sheet's side view at the same scale.
4. For anything that renders for more than ten minutes, get a second reading of a cheap preview first.
5. Spend the next round on likeness: heads, limb shapes and the big silhouette, guided by the feedback notes.

## Later the same day: the first feedback round

Written 2026-11-14, after working through the notes in `feedback/`. The account above is left as it was written.

**What changed.** Fourteen notes were read; nine asked for a change and got one (snake, spider, scorpion, mantis,
Malinois, gorilla's chest beat, bear, Shisa, horse), two said no change was needed, and the rest ask for new work that
is now on the to-do list in the [Reuse plan](?doc=REUSE.md): hands and feet for the apes, a second humanoid, a king
cobra, the Red Witness figure's back, more helmets. Each note has its answer under it. The mosquito (15) was built as the first flyer.

**The largest gap above is smaller.** `kit/strip.py` draws a contact sheet of any motion in about four seconds, and
every change in this round was looked at that way before it was exported. It is still not watching: twelve stills
show a pose and a rhythm, not a pop at a loop point or a foot that slides. `kit/tune.py` tries motion numbers in
batches against the ground and overlap checks, and `kit/viewer_test.py` opens a rebuilt viewer and presses every
motion button.

**What the sheets showed at once.** The bear's forearms folded back into its own rib cage when it stood up; the
scorpion's sting came down on its own face, 54 cm short of its claws; the mantis's abdomen went through its wing
case. All three were in the user's notes, and none had been visible in the six-frame checks.

**What is still open.**
- The mosquito's wings are open frames. A membrane needs a second, see-through material through the pipeline and the viewer.
- Floor contact in the older animals: the cause in the walks is found (toes drooping while the foot is barely off the
  ground) but only tried on the feline, not applied.
- Upper-arm muscles against the chest at rest on the feline, Malinois, Shisa and bear.
- Nothing has been committed since `cb3892f`: the film, the feedback notes, this round and the mosquito are all
  uncommitted work in the folder.
- One slip repeated from the list above: a shell loop over quoted pairs ran the whole build queue as no-ops. It cost
  ten minutes and is now a rule in [Lessons](?doc=LESSONS.md).

## The first series, complete (2026-11-14, evening)

Seventeen concept sheets, seventeen models: the Velociraptor was the last. With the Toad and the X6-Crawler added the
same day the series now covers a digitigrade cat and dog, a guardian lion-dog, a horse, a bear, two apes' worth of
gorilla, a humanoid, a sprawling lizard, a snake, a toad, a theropod, three arthropod plans (mantis, spider and
scorpion, mosquito), an octopus and a six-legged machine. Beside them: two hands and two feet as studies, four modules
taken out of the animals, three bodies wearing them, a gate, a scene and a film.

What the last three models showed about the kit: each was built, checked and baked in well under an hour, each needed
two or three new assemblies and one motion set, and each time the ground and overlap checks, run first, listed the
faults before any picture was looked at. What they did not change: every "falls short" list still begins with the
frame, which is hoops and plain shells where the sheets draw sculpted lattice. That is the next thing worth a round of
its own, and it is one piece of work for the whole series, not seventeen.

Still open across the series: likeness of heads and frames to the sheets; fingers and toes that are single bones on
most animals; floor contact in the six oldest models; one 4K texture set on the large animals; the mosquito's wing
membrane; feet not yet worn by the humanoid and the gorilla; and nothing has yet been watched in motion or opened in
a game engine.

