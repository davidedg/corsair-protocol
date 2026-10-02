# Custom effects: Solid, Gradient, Ripple, Wave

Four kinds of [lighting layer](lighting.md) are animations that the firmware runs from a colour curve: EffectType 10 (iCUE's Solid), 11 (Gradient), 12 (Ripple) and 13 (Wave). A layer of these kinds has a `lght_NN.d` of **553 bytes** (41 bytes of header and 512 bytes of colour samples) and a `lght_NN.k` with its [cells](lighting-cells.md), but **no `.r`**: the colours are in the descriptor. They can be mixed with static layers, up to five layers in all.

## Layout of `lght_NN.d`

```
offset  size  field
0       1     EffectType: 10 Solid, 11 Gradient, 12 Ripple, 13 Wave
1       1     Wave and Ripple: tail length, in lights; 0 otherwise
2       1     Wave and Ripple: velocity, in lights per second; 0 otherwise
3       1     Wave: 01 two-sided, 00 one-sided; 0 otherwise
4       1     Wave and Ripple: 01 (iCUE's WaveSpread OnlyBetweenFronts); 0 otherwise
5..8    4     Wave: angle in degrees (u32); 0 otherwise
9..12   4     X of the origin of the animation (i32); 0 in Solid and Gradient
13..16  4     Y of the origin of the animation (i32); 0 in Solid and Gradient
17      1     start on key press (01 / 00)
18      1     start with the profile
19      1     stop on key press
20      1     stop on key release
21..22  2     duration, in tenths of a second (u16)
23..24  2     stop after this many times (u16); 0 if not used
25..36  12    zero
37      1     80: the number of colour samples (128)
38..40  3     zero
41..552 512   128 colour samples, 4 bytes each: R G B A
```

Every descriptor iCUE wrote agrees with this layout: the reserved bytes are zero, byte 37 is `80`.

- **Tail and velocity** are whole numbers in the file: iCUE's editor takes 0.1 to 99.9 with one decimal and rounds to the nearest integer, halves up (4.0 is `04`, 8.5 is `09`, 3.4 is `03`, 7.5 is `08`), so 0.1 to 0.4 become 0. iCUE's default is tail 4 and velocity 2.
- **Angle**: iCUE takes 0 to 359 degrees. The angle runs counter-clockwise, and 0 travels to the right.
- **Duration**: iCUE takes 0.1 to 99.9 s, so 1 to 999 in the file.
- **Start and stop**: iCUE offers to start "with profile" (byte 18) or "on key pressed" (byte 17), and to stop "on key press" (byte 19), "on key release" (byte 20), "after" a number of times, 1 to 99 (bytes 23 and 24), or "never" (none of them). A static layer has the flags `00 01 00 00` ([lighting](lighting.md)).
- **Origin** (bytes 9 to 16): the point the animation starts from, in iCUE's own units of the keyboard's layout, with (0, 0) about the top left LED of the light bar. The values are signed: an empty key list gives negative values. The point depends on the effect:

| Effect | Origin |
|---|---|
| Wave, two-sided | the centre of the box around the keys of the layer |
| Wave, one-sided | on the side of that box opposite to the direction of travel (0 degrees: the left side; 90: the bottom; 180: the right side; 270: the top), its middle at 0, 90 and 270 degrees; at the other angles, the corner opposite to the direction of travel (30 degrees: the bottom left corner) |
| Ripple | the centroid of the keys of the layer |
| Solid, Gradient | none (zero) |

- **The samples** are the colour curve of the layer over its duration, sampled 128 times: iCUE's editor sets the colours (with their opacity) at points in time, and the samples follow the curve between them. Before the first point and after the last a sample is `00 00 00 00`, transparent, and the keys are dark there. A Solid is a set of colour blocks, each made of two points of the same colour.

The exact rules iCUE uses to compute the samples and the origin from its editor are not documented here yet.

## Examples of header

| Layer | Bytes 1 to 4 | Bytes 17 to 20 (flags) |
|---|---|---|
| Solid, start on key press, stop on key release | `00 00 00 00` | `01 00 00 01` |
| Gradient, start with the profile, stop on key press | `00 00 00 00` | `00 01 01 00` |
| Ripple, start on key press, stop after 4 times (bytes 23 and 24: 4) | `04 02 00 01` | `01 00 00 00` |
| Wave, two-sided, start on key press, stop on key release | `04 02 01 01` | `01 00 00 01` |
| Wave, one-sided | `04 02 00 01` | |

## On the keyboard

- **Solid**: with start on key press and stop on key release, pressing and holding any key of the layer lights all the keys of the layer; releasing it turns them all off. The start and stop conditions act on the whole layer.
- **Gradient**: all the keys of the layer go together through the colours of the curve, in cycles of the duration, until the stop condition, which turns them off.
- **Ripple**: a wave of colour that starts from the origin and spreads outwards; the keys it has passed turn off, and the colour of a key changes as it goes.
- An effect runs again after the keyboard is handed back to hardware mode at the end of a software session.
