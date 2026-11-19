# Knowledge base

What we have learned building these assets, written so the next animal starts from it.

| Page | What it answers |
|---|---|
| [Method](?doc=METHOD.md) | How a concept sheet becomes a rigged game asset, step by step, and how to add an animal |
| [Reuse plan](?doc=REUSE.md) | What is shared across the animals, how size is handled, and the build order |
| [Kit reference](?doc=KIT.md) | Every part and assembly in the shared kit, with its parameters |
| [Rig standard](?doc=RIG.md) | Bone names, axes, the leg solver and how motions are written |
| [Lessons](?doc=LESSONS.md) | Mistakes already made, and the rule each one left behind |
| [Retrospective](?doc=RETROSPECTIVE.md) | What went well and what did not over the first fifteen models, and where things stand |
| [Study: Komodo dragon](?doc=STUDY-KOMODO.md) | Proportions and gait of a sprawling reptile, and how they become numbers in the kit |
| [Study: jiu-jitsu gi](?doc=STUDY-GI.md) | How a gi is built, what Blender's cloth can do (with a spike), three hosts measured, and the plan for a tailored, reusable gi |

## The workspace

```
3d_assets/
  index.html, catalog.json     landing page and the list of models it shows
  serve.sh                     one local server for everything: http://localhost:8780/
  concepts/                    the concept sheets, numbered
  kit/menagerie/               the shared Python kit (parts, anatomy, materials, rigging, motions, pipeline)
  kit/viewer/                  the shared web viewer; each build copies it next to its own data
  docs/                        this knowledge base
  hub/server.py                the local server behind serve.sh: static files, plus the feedback notes
  hub/publish.py               writes the public, display-only copy into _site/ (LOD2-3, 2K, the terms in every page
                               and file); hub/publish_check.py opens every page of it, or of the live site
  LICENSE.md, NOTICES.md       the terms (all rights reserved; full-detail models kept for sale) and third-party notices
  feedback/                    notes written on the landing-page cards, one text file per model
  feline_c07/ ... shisa_s01/   one folder per animal, built on the kit (see catalog.json for the list)
  torii_t01/                   the shrine gate; scene_guardian_gate/ sets two Shisa before it
  film_gate_procession/        the one-minute film: shot list, render and encode scripts
  red_witness_helmet/          the first builds (helmet, then a figure); own scripts, not on the kit
  red_witness_rig/             the Red Witness figure on the kit's rig and motions (the host for its gi)
  red_witness_gi/              Red Witness in a Gi (kit/menagerie/dress.py, as humanoid_gi)
```

## Feedback notes

Every model's viewer has a FEEDBACK button beside EXPORT, and every card on the landing page has a FEEDBACK fold; both
show the same notes (`hub/feedback.js` and the landing page). A note typed in either is appended to `feedback/<id>-<name>.txt` with
the date, time and the commit the repository was at. The files are plain text and can be edited by hand.

```
=== 2026-11-14 08:49:24 +0900 · commit cb3892f ===
the note, as written
  -> 2026-11-15: what was changed in answer to it (added by Claude)
```

Before working on a model, read its file. When a note has been acted on, add a line under it starting with `  -> ` that
says what changed, so the note and its answer stay together. Notes are saved only when the page is opened through
`serve.sh`; a plain file server shows the cards without the fold.

Each animal folder has the same shape: `scripts/<animal>.py` (its numbers), `scripts/build.py` (three lines that call the
pipeline), `textures/`, `export/`, `blend/`, `renders/`, `viewer/`.

## Commands

```
./serve.sh                                                          # landing page + all viewers
blender -b --factory-startup -P <animal>/scripts/build.py -- 4096 render     # full build, about 6 minutes
blender -b --factory-startup -P <animal>/scripts/build.py -- clay render     # shape check in seconds, no exports
blender -b --factory-startup -P <animal>/scripts/build.py -- clay anim clip=Walk   # motion check frames
blender -b --factory-startup -P <animal>/scripts/build.py -- 4096 nobake     # re-rig and re-export, reuse textures
uv run --with pillow python hub/make_thumbs.py                      # refresh landing-page cards
```
