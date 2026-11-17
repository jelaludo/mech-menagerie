# Study: Komodo dragon

The first reptile in the menagerie. Every earlier animal carries its legs under its body and swings them in the side
plane; a monitor lizard does neither. This page records what the animal looks like and how it moves, where that came
from, and what each fact turns into in the model.

## Proportions

| Feature | What the references say | In the model (2.8 m sheet length) |
|---|---|---|
| Tail | about as long as head and body together; slightly longer in the young, less with age | 1.12 m of 3.00 m (37%), following the concept sheet, which draws it shorter than life |
| Trunk | flatter and more massive than smaller monitors | section 0.48 m wide by 0.29 m deep |
| Head | broad, flattened, long snout, strongly hinged jaw | 0.47 m long, 0.25 m wide, 0.13 m deep; many small teeth, no fangs |
| Limbs | carried far out from the body; hind limbs stouter than fore; long recurved claws on every toe | wrists and ankles 0.31 m either side of the centre line; 0.93 m across the claws (the sheet says 760 mm); five clawed toes splayed outward |
| Height | sheet: 600 mm at the shoulder | back at 0.54 m, belly 0.23 m off the ground when walking |

The upper limb bones (humerus, femur) point outward and only slightly down; the lower ones drop to the ground. Elbows
point back and up, knees forward and up.

## How it moves

- **Walking.** The body, head and tail swing from side to side. Speed about 4.8 km/h (1.3 m/s).
- **The bend is a standing wave** with its still points on the two limb girdles. The girdles do not slide sideways; they
  turn about a vertical axis, in opposite directions, and the trunk bows between them. (Measured on the lizard
  *Sceloporus*; assumed here to hold for a monitor.)
- **Footfalls** go in diagonal pairs, a walking trot: each hind foot lands just before the opposite fore foot.
- **Girdle and foot timing:** the hip on the side of the landing hind foot is forward as it lands. Pelvic rotation leads
  the foot slightly. Hind-limb yaw in *Varanus* often exceeds 25 degrees.
- **Running.** Up to 14–18.5 km/h in short bursts. The body and tail are held fairly rigid, the tail lifts off the
  ground, and the hind feet swing in a wide lateral arc.
- **Threat display:** gaping mouth, inflated throat, arched back, hissing, tail lashing or quivering.
- **Other behaviour used here** (from general knowledge, not from the sources below): constant tongue flicking while it
  moves and looks about; rearing onto the hind legs with the tail as a prop; basking flat on the belly with the limbs
  splayed.

## What this needs from the kit

| Need | Why the existing kit could not do it | What was added |
|---|---|---|
| Legs solved in 3D | the leg solver works in the side plane; these legs work mostly in the ground plane | `Rig.solve3`: body bones posed freely, each leg a two-bone chain bent in a plane that turns with its girdle |
| Girdles that turn in place | earlier gaits only pitch the trunk | `Sprawler.bend`: hip and shoulder yaw, with the trunk bowed so the shoulders stay on the centre line |
| Feet placed in the world | earlier feet are offsets in the side plane | foot targets are world positions; the swing foot arcs outward |
| A tongue that slides | only the pelvis and root could be moved | any bone can be moved by a motion (`_loc`); `tongue` assembly |
| Dorsal armour | no part covered the back | `carapace`: a perforated shell over a stretch of trunk |

Reused unchanged: parts, keel, hoops, sternum, muscle rows, the skull and jaw assembly, the paw (five toes, splayed),
the tail, materials, pipeline and viewer.

## Motions built from this

| Motion | Basis |
|---|---|
| `Walk` | diagonal pairs, hips ±17° and shoulders ±13° in opposite directions, tail wave, one tongue flick every other stride |
| `Run` | same footfalls, faster; girdle rotation cut to ±7° and ±5°, body raised, tail held up and nearly still, hind feet swung wide |
| `Idle` | head scanning side to side, three tongue flicks, slow tail sway |
| `Threat` | stands tall, back arched, jaws gaping with a hiss tremor, tail lashing |
| `TailWhip` | body curled to one side, tail coiled then cracked across, base first |
| `Tripod` | rears on the hind legs with the tail on the ground, looks left and right |
| `Bask` | belly down, legs splayed, head laid aside, tail curled round; the eyes dim |

## Known gaps

- The model is 3.0 m long without the tongue (the sheet says 2.8 m): the head was enlarged after the first build to match
  the concept's heavy skull. The first build was also 1.27 m wide across the claws; the feet were shrunk and brought in.

- Model speeds are about 0.78 m/s walking and 2.5 m/s running: below the real 1.3 and 4–5 m/s. The stride is limited by
  how far the short legs can reach without the body rolling and the limb bones twisting about their own axes, neither
  of which is modelled.
- The trunk bends at two joints (three rigid pieces), so the bow is angular, not a smooth curve.
- No inflating throat. The tail's sideways wave is a formula, not measured.
- I found no source giving footfall or bending phases for *Varanus* itself; the timing above is the general lizard
  pattern.

## Sources

- San Diego Zoo Wildlife Alliance fact sheet, [Komodo dragon: behaviour](https://ielc.libguides.com/sdzg/factsheets/komododragon/behavior) and [physical characteristics](https://ielc.libguides.com/sdzg/factsheets/komododragon/characteristics) (gait description, speeds, threat display).
- Reilly and DeLancey 1997, [Sprawling locomotion in the lizard *Sceloporus clarkii*](https://www.originalwisdom.com/wp-content/uploads/bsk-pdf-manager/2019/10/Reilly-and-Delancy_1997_Sprawling-locomotion-in-the-lizard-Sceloporus-clarkii.pdf) (standing wave with nodes on the girdles; pelvic rotation leads the foot).
- SICB abstract, [Varanus, Alligator, and the diversity of non-parasagittal locomotion](https://sicb.org/?p=31724) (hind-limb yaw over 25 degrees).
- Journal of Experimental Biology, [Evolution of limb bone loading and body size in varanid lizards](https://cob.silverchair.com/jeb/article/214/18/3013/10372/Evolution-of-limb-bone-loading-and-body-size-in) (larger monitors raise duty factor).
- [Komodo dragon dimensions](https://www.dimensions.com/element/komodo-dragon-varanus-komodoensis) and the Wikipedia entry as quoted in search results (tail-to-body ratio, hind limbs stouter than fore).
