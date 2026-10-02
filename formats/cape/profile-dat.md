# `PROFILE.DAT`: actions and Win Lock options

The keys of the profile that run an action (a recorded macro, a text, a key combination), each with the [macro file](macro.md) that holds its events. The header also holds the Win Lock options of the profile.

```
offset  size   field
0       1      50 ('P')
1       1      Win Lock options (bit mask, below); 01 by default
2       2      number of entries (u16)
4       16*n   entries
```

## Win Lock options (byte 1)

What the Win Lock button disables, while Win Lock is on:

| Bit | Value | Disables | iCUE's option |
|---|---|---|---|
| 0 | `01` | the Windows key | Disable Win Key |
| 1 | `02` | Alt+Tab | Disable Alt+Tab |
| 2 | `04` | Alt+F4 | Disable Alt+F4 |
| 3 | `08` | Shift+Tab | Disable Shift+Tab |

iCUE's default is `01`. Bits 4 to 7 are always 0 in iCUE's files.

On the keyboard a disabled combination reaches the host as its modifier alone: the firmware leaves the other key out of the report (with `04`, Alt+F4 arrives as Alt). With bit 0 the Windows key itself is not sent. New options take effect as soon as they are written and the keyboard is back in hardware mode, without switching slot.

## Entry (16 bytes)

```
offset  size  field
0       1     key index of the key that runs the action
1       4     name of the macro file, ASCII, e.g. "M000"
5       3     size of the macro file in bytes (u24)
8       1     start condition: 00 on press, 11 on release
9       1     run type: 01 run once, 03 while pressed, 04 toggle, 84 (below)
10      1     repeat count: 01; 00 with run type 84
11      5     zeros
```

The key is a [key index](key-index.md). iCUE writes the macro files before `PROFILE.DAT`, and names them in the order of the entries (ascending key index) in lower-case hexadecimal: `M000` ... `M009`, `M00a`, ... The name is only the string this entry points to.

### Start condition and run type

| Bytes 8 9 10 | Meaning | iCUE's option for a macro |
|---|---|---|
| `00 01 01` | starts when the key is pressed, runs to the end | on press |
| `11 01 01` | starts when the key is released, runs to the end | on release |
| `00 03 01` | starts when the key is pressed and is stopped when it is released; it runs once, it does not repeat (a short press types only part of a text) | while pressed |
| `00 04 01` | toggle | toggle |
| `00 84 00` | autofire: the first press starts the macro over and over, until the key is pressed again | the toggle mode of "Imitate holding key" |

- iCUE offers the four options of the third column for a recorded macro, as alternatives. A text and a key combination always have `00 01 01`.
- Run type `84` with repeat `00` is the entry of a key remap with "Imitate holding key" in its toggle mode (its macro file: press, delay 0, release; see [macro files](macro.md#imitate-holding-key)). It is the only entry seen with bit 7 of byte 9 set; what that bit means on its own is not known. On the keyboard the press and release of the macro repeat about every 5 ms.
- What the firmware does exactly with run type `04` (toggle) was not measured.
- iCUE's names for the run types: Uninterrupted 1, Queued 2, WhilePressed 3, OnToggle 4, SmartStop 5; for the start conditions OnPress 0 and OnRelease `11`. iCUE does not write 2 or 5, a combination of start `11` with run type 3, or a repeat count other than 1 (except with `84`), for this keyboard.

## Examples

```
50 01 00 00                                                              no actions
50 01 01 00  7d 4d 30 30 30 86 00 00 11 01 01 00 00 00 00 00             G6 -> M000 (134 bytes), on release, run once
50 01 03 00  7b 4d 30 30 30 26 00 00 00 01 01 00 00 00 00 00             G4 -> M000 (38 bytes), on press
             7c 4d 30 30 31 46 00 00 11 01 01 00 00 00 00 00             G5 -> M001 (70 bytes), on release
             7d 4d 30 30 32 a6 00 00 00 03 01 00 00 00 00 00             G6 -> M002 (166 bytes), while pressed
```
