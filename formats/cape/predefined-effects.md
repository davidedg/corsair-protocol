# Predefined effects

Eight lighting effects of the firmware that take a few parameters and no keys: a profile with one of them has it as its only [lighting layer](lighting.md), `lght_00.d` of **13 bytes**, `lghtcnt.cnt` = 1, and no `.k` or `.r`. iCUE allows one per profile, alone.

```
offset  size  field
0       1     EffectType: 0 ColorShift, 1 ColorPulse, 2 SpiralRainbow, 3 RainbowWave, 4 ColorWave, 5 Visor, 6 Rain,
              8 TypeLighting in its "key" mode, 9 TypeLighting in its "ripple" mode
1       1     speed: 1 slow, 2 medium, 3 fast; 0 where the effect has no speed
2       1     colour type: 1 random, 3 alternating (two colours); 0 where the effect has no colour choice
3       1     direction: 1 left, 2 right, 3 up, 4 down, 5 clockwise, 6 counter-clockwise; 0 where the effect has none
4       1     duration: 1 short, 2 medium, 3 long; only TypeLighting in its key mode, 0 otherwise
5..8    4     colour 1, R G B A, when the colour type is 3; zero otherwise
9..12   4     colour 2, R G B A
```

The values are iCUE's enumerations ([enumerations](enums.md)). The file holds only what applies to the effect: iCUE keeps other parameters in its own copy, but a speed, a direction or colours that the effect does not show are 0 in the file.

| Effect | Speed | Colour type | Direction | Duration |
|---|---|---|---|---|
| ColorShift (0): changes colour almost at once | yes | random or alternating | | |
| ColorPulse (1): fades out and in | yes | random or alternating | | |
| SpiralRainbow (2) | yes | | clockwise or counter-clockwise | |
| RainbowWave (3) | yes | | left, right, up, down | |
| ColorWave (4) | yes | random or alternating | left, right, up, down | |
| Visor (5): goes back and forth | yes | random or alternating | | |
| Rain (6) | yes | random or alternating | | |
| TypeLighting, key mode (8): lights the pressed key | | random or alternating | | yes |
| TypeLighting, ripple mode (9): a ripple from the pressed key | yes | random or alternating | | |

## Examples

Colours `ff0000` and `0000ff` where the colour type is alternating.

| Setting | `lght_00.d` |
|---|---|
| ColorPulse, random, fast | `01 03 01 00 00 00 00 00 00 00 00 00 00` |
| ColorShift, alternating red and blue, slow | `00 01 03 00 00 ff 00 00 ff 00 00 ff ff` |
| ColorWave, alternating red and blue, slow, up | `04 01 03 03 00 ff 00 00 ff 00 00 ff ff` |
| Rain, random, fast | `06 03 01 00 00 00 00 00 00 00 00 00 00` |
| RainbowWave, slow, down | `03 01 00 04 00 00 00 00 00 00 00 00 00` |
| RainbowWave, slow, right | `03 01 00 02 00 00 00 00 00 00 00 00 00` |
| SpiralRainbow, fast, counter-clockwise | `02 03 00 06 00 00 00 00 00 00 00 00 00` |
| TypeLighting, key mode, random, duration medium | `08 00 01 00 02 00 00 00 00 00 00 00 00` |
| TypeLighting, ripple mode, random, speed medium | `09 02 01 00 00 00 00 00 00 00 00 00 00` |
| Visor, alternating red and blue, medium | `05 02 03 00 00 ff 00 00 ff 00 00 ff ff` |
| ColorWave, alternating `12ab34` and `fe7a05`, fast, left | `04 03 03 01 00 12 ab 34 ff fe 7a 05 ff` |
| SpiralRainbow, slow, clockwise | `02 01 00 05 00 00 00 00 00 00 00 00 00` |
| TypeLighting, key mode, alternating `12ab34` and `fe7a05`, duration long | `08 00 03 00 03 12 ab 34 ff fe 7a 05 ff` |

Not seen: duration short (1); the alternating colours of ColorPulse, Rain and TypeLighting in its ripple mode, which iCUE offers; colour type 2 ("selected"), which iCUE does not offer; direction 7 ("from centre").

[OpenRGB](https://gitlab.com/CalcProgrammer1/OpenRGB) sets the hardware effect of Corsair keyboards with the same descriptor and the same type numbers, written as `lght_00.d`.
