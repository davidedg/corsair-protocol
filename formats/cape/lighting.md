# Lighting files

The lighting of a slot is a list of **layers**. `lghtcnt.cnt` holds their number (u32: `00 00 00 00` for none, `01 00 00 00` for one layer) and each layer `NN` (`00`, `01`, ...) has its own files: a descriptor `lght_NN.d`, and for some kinds the cells it covers `lght_NN.k` and their colours `lght_NN.r`.

| Kind of layer | Byte 0 of `.d` (EffectType) | `.d` | `.k` | `.r` | Page |
|---|---|---|---|---|---|
| predefined effect | 0 to 6, 8, 9 | 13 bytes | no | no | [predefined effects](predefined-effects.md) |
| static colour | 7 | 37 bytes | yes | yes | below |
| custom effect | 10 to 13 | 553 bytes | yes | no | [custom effects](custom-effects.md) |

Type 14 (RecordedLighting) has never been seen. Byte 0 of every descriptor is the type; the rest of the layout depends on the kind. The names of the types are in [enumerations](enums.md#effecttype).

## Limits and order

- **A profile has at most 5 layers of the custom kind (static colours and custom effects, mixed), or 1 predefined effect alone**: iCUE refuses more. A predefined effect is always the only layer of its profile.
- **The files follow iCUE's list of layers from the bottom**: `lght_00` is the layer at the bottom of the list, the last file the layer at the top. It is not the order in which the layers were created.
- iCUE writes the files of a layer in the order `.d`, `.k`, `.r` (where they exist), layer 0 first, and `lghtcnt.cnt` last.

## `lght_NN.d` of a static layer

37 bytes, the same for every static layer whatever its colour, opacity or keys: `07` at byte 0, `01` at byte 18, the rest zero.

```
07 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 01 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
```

Bytes 17 to 20 are the four start and stop flags of the [custom effects](custom-effects.md#layout-of-lght_nnd) (start on key press, start with the profile, stop on key press, stop on key release): a static layer starts with the profile and never stops.

## `lght_NN.k`: the cells of a layer

A u32 count, followed by that many [cells](lighting-cells.md), one byte each: the keys and LEDs the layer covers. iCUE lists them in its canonical order ([lighting cells](lighting-cells.md#the-cells-in-icues-order)), whatever the order in which they were selected. A layer whose selection is empty has a `.k` of count 0 (4 bytes). The profile, brightness and Win Lock buttons are never in a layer: their colours are in [PROFILE.I](profile-i.md).

## `lght_NN.r`: the colours of a static layer

1024 bytes, 256 entries of 4 bytes R G B A. **Entry *i* is the colour of the *i*-th cell of the layer's `.k`**: a compact list from entry 0, not an array indexed by cell or key. The unused entries are zero.

- **A static layer has one colour**: all its entries are the same. Different colours need one layer each, so a hardware profile holds at most five colours of static lighting.
- **The alpha byte of every entry is the opacity of the layer**: `ff` at 100 %, `80` at 50 %, `40` at 25 % (round(255 * opacity)). The firmware honours it.
- Three layers of one key each (the keys 1, 2 and 3, cells 9, 17 and 25) in white have byte-identical `.r` files, with one entry `ff ff ff ff` at index 0: the entries follow the `.k`, not the cells.
- The layer of all keys that iCUE makes has 138 entries for its 135 cells: iCUE repeats the colour once per LED of the layer, which includes the three buttons that are not cells *(inferred)*.

## How the firmware paints the layers

- **The firmware paints the layers in file order, `lght_00` first, each over the ones before it.** At 100 % opacity a later layer covers an earlier one on the keys they share; with a lower opacity it is blended over them ("over" compositing, the opacity of the upper layer as its alpha): red under 50 % blue shows purple, 50 % blue over nothing a dark blue. The blending was observed by eye; the firmware's exact arithmetic is not known.
- **The same key can be in several layers.** iCUE does not flatten them: it writes the shared keys in the `.k` of every layer involved. In iCUE's own preview the layer higher in the list wins, which is the same rule, since the top of the list is the last file.
- **A key in no layer is dark, and so is a black layer**: a black layer is written like any other (`00 00 00 ff`). Both look the same on the keyboard; a black layer matters only over another layer.
- A reader that builds the picture of a slot must composite the layers over one another in file order. One that skips the cells already coloured gets overlapping layers backwards, and still passes every test of disjoint layers.
- How the custom effects and a predefined effect combine with static layers on the keyboard has not been examined.
