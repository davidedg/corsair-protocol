# `PROFILE.MAP`: key remaps

The keys of the profile that send another key or a mouse button. A key that has an action in [PROFILE.DAT](profile-dat.md) is not in this file.

```
offset  size  field
0       2     header 41 00
2       2     number of entries (u16)
4       3*n   entries: [source key index] [destination] [80]
```

- The source and the destination are [key indices](key-index.md). Any key can be a source, the profile, brightness and Win Lock buttons, the media keys and the volume wheel included; any key index can be a destination, media keys included.
- A destination `c8` to `cc` is a mouse button, 1 (left) to 5 (forward): see [key indices](key-index.md#mouse-buttons). In hardware mode the keyboard sends it as its mouse report [`05`](../../device-to-host/05/05.md).
- The third byte is `80` in every file iCUE writes. Entries with bit 7 of it clear (`03`, `05`, `08`, `0a`, `0b` were tried, with destination `00`) are ignored by the firmware.

## Examples

```
41 00 00 00                                  no remaps
41 00 01 00 78 2c 80                         G1 -> K
41 00 03 00 78 22 80 79 c8 80 7a 61 80       G1 -> P, G2 -> mouse left, G3 -> mute
41 00 0c 00 01 65 80 47 cb 80 60 cc 80 61 1b 80 79 c9 80 7a ca 80 7b 82 80 7c 61 80 7d 62 80 82 19 80 83 1a 80 46 63 80
   F1 -> next, brightness -> mouse 4, Win Lock -> mouse 5, mute -> E, G2 -> mouse 2, G3 -> mouse 3, G4 -> volume up,
   G5 -> mute, G6 -> stop, volume wheel up -> Q, volume wheel down -> W, profile button -> previous track
```

## What iCUE writes

iCUE offers these kinds of remap for a hardware profile: to a key (from a US ANSI 104-key picker, whatever the layout of the keyboard), to a media key, to a language key, to a mouse button (1 to 5), and to a key combination. A key combination, and a key remap with the option "Imitate holding key", are not remaps of this file: they are actions in [PROFILE.DAT](profile-dat.md) with a [macro file](macro.md).

- The `\` of iCUE's key picker is written `50` (the ANSI backslash). The keyboard sends the USB usage of the ANSI backslash for it, and with an Italian layout on the PC that key types `ù`.
- Of iCUE's 21 language keys, 9 are written as the index of a real key: Lang1 `41`, Lang2 `3f`, International1 `53`, International2 `42`, International3 `55`, International4 `91`, International5 `90`, NonUsTilde `51` and NonUsBackslash `31`. **The other 12 (Lang3 to Lang9, International6 to International9, KeypadComma) are written as `00`, the index of Esc, and the keyboard sends Esc for them.**
- A macro made of one click of the left mouse button is saved by iCUE as the remap to mouse 1 (`c8`), not as a macro.
