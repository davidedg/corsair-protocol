# Enumerations

iCUE's names for the values of the on-board profile files. The values are the ones written in the files.

| Enumeration | Values | Where |
|---|---|---|
| ContainerType | Profile `P`, RgbData `D`, Fx `X`, Action `A`, Macro `M`, Info `I`, Invalid 0 | byte 0 of the binary files ([files](README.md#the-files-of-a-slot)) |
| MacroSubtype | Macro 0, Shortcut 1, Media 2, Text 3, MouseKeyRemap 4 | byte 1 of a [macro file](macro.md) |
| StartCondition | OnPress 0, OnRelease `11` | byte 8 of a [PROFILE.DAT](profile-dat.md) entry |
| RunType | Uninterrupted 1, Queued 2, WhilePressed 3, OnToggle 4, SmartStop 5 | byte 9 of a [PROFILE.DAT](profile-dat.md) entry |
| EffectType | see below | byte 0 of `lght_NN.d` ([lighting](lighting.md)) |
| Speed | Undefined 0, Slow 1, Medium 2, Fast 3 | [predefined effects](predefined-effects.md) |
| Duration | Undefined 0, Short 1, Medium 2, Long 3 | [predefined effects](predefined-effects.md) |
| ColorType | Undefined 0, Random 1, Selected 2, Alternating 3 | [predefined effects](predefined-effects.md) |
| Direction | Undefined 0, Left 1, Right 2, Up 3, Down 4, Clockwise 5, CounterClockwise 6, FromCenter 7 | [predefined effects](predefined-effects.md) |
| WaveSpread | Undefined 0, OnlyBetweenFronts 1, WithAfterInnerFront 3, WithBeforeOuterFront 5, WithEveryDirection 7 | byte 4 of a [custom effect](custom-effects.md) (iCUE writes 1) |
| KeyboardInternalFunction | NoFunction 0, BrightnessToggle 1, Programming 2, ProfileUpRound 3, ProfileDownRound 4, ProfileUp 5, ProfileDown 6, FnMode 7, WinLock 8, MR 9, M1 to M5 10 to 14, OnboardKeyboardProgramMode 15, Test 29, Demo 30, Remap 31 | **not honoured** by this keyboard as the third byte of a [PROFILE.MAP](profile-map.md) entry |
| CommitFlag | None 0, Red 1, Green 2, Blue 4, All 7 | possibly the parameter of [`07 17 0e`](../../host-to-device/07/17/0e/07170e.md) *(inferred)*, always 0 |

## EffectType

| Value | Name | Kind |
|---|---|---|
| 0 | ColorShift | [predefined](predefined-effects.md) |
| 1 | ColorPulse | predefined |
| 2 | SpiralRainbow | predefined |
| 3 | RainbowWave | predefined |
| 4 | ColorWave | predefined |
| 5 | Visor | predefined |
| 6 | Rain | predefined |
| 7 | Static | [static colour](lighting.md#lght_nnd-of-a-static-layer) |
| 8 | TypeLightingGradient (TypeLighting, key mode) | predefined |
| 9 | TypeLightingRipple (TypeLighting, ripple mode) | predefined |
| 10 | AdvancedSolid | [custom](custom-effects.md) |
| 11 | AdvancedGradient | custom |
| 12 | AdvancedRipple | custom |
| 13 | AdvancedWave | custom |
| 14 | RecordedLighting | never seen |

In a macro file of this keyboard iCUE writes only key presses, key releases and the two kinds of delay, constant and random ([macro files](macro.md#events)).
