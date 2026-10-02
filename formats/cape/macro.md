# Macro files (`M000`, `M001`, ...)

The events of one action of [PROFILE.DAT](profile-dat.md): a recorded macro, a text, or a key combination. The entry of the action names the file and gives its size.

```
offset  size  field
0       1     4d ('M')
1       1     subtype: 00 macro, 01 key combination, 03 text
2       3     number of events, u24 BIG-endian
5       3     00 00 00
8       ...   the events: 2 bytes each, 4 for a long or a random delay
```

- iCUE's names for the subtypes: Macro 0, Shortcut 1, Media 2, Text 3, MouseKeyRemap 4. Only 0, 1 and 3 are used on this keyboard.
- **The number of events is big-endian** and counts events, not bytes: a file with a 4-byte event has fewer events than (size - 8) / 2. A text of 751 events starts `4d 03 00 02 ef 00 00 00`, and the firmware plays all 751. Byte 2 has always been 0; whether it belongs to the count is not known (it would take 65536 events).
- The firmware plays the events in order. Presses may overlap (B pressed before A is released). There is no end marker, and no key repeat while a key is held by the macro.

## Events

| Bytes | Event |
|---|---|
| `20 kk` | press of [key index](key-index.md) `kk` |
| `00 kk` | release of key index `kk` |
| `8h ll` | delay of `((h & 1f) << 8) \| ll` milliseconds: 13 bits, at most 8191 ms (first byte `80` to `9f`) |
| `c0 hh mm ll` | long delay: `(hh << 16) \| (mm << 8) \| ll` milliseconds, 24 bits big-endian, at most 16 777 215 ms; one event of 4 bytes |
| `e0 aa ab bb` | random delay between `min = (aa << 4) \| (ab >> 4)` and `max = ((ab & 0f) << 8) \| bb` milliseconds, two 12-bit values (at most 4095 each); one event of 4 bytes |

The first byte tells the kind of event: bit 7 clear, a key event (bit 5 set for a press, clear for a release); bit 7 set and bit 6 clear, a short delay (2 bytes); bits 7 and 6 set, a 4-byte event (bit 5 clear a long delay, set a random delay).

- iCUE writes a delay below 8192 ms in the short form and one of 8192 ms or more in the long form; it limits a long delay to `ff ff ff`. It never writes a short delay with a first byte above `9f`; what the firmware does with `a0` to `bf` is not known.
- Between two key events in a row iCUE puts a delay of 0 (`80 00`) in key combinations and texts.
- The firmware runs a random delay as a random time between the two bounds, the lower bound included: 0 to 500 ms gave 22 to 446 ms over 32 presses, 1234 to 3456 ms gave 1.42 to 2.94 s over 6. Consecutive values are not independent.
- How long the firmware's delays really are is an open question: measurements gave between 90 % and 99 % of the time written, and they were not settled.
- A mouse button inside a macro has not been seen: iCUE does not offer mouse events in a macro of a hardware profile, and it saves a macro of one mouse click as a [remap](profile-map.md).

## What iCUE writes

### Recorded macro (subtype 00)

Key presses, key releases and delays, as recorded or edited. iCUE records from the keyboard only, keeps at most **256 rows** (a delay is a row) and takes delays of **0 to 4095 ms** in a macro of a hardware profile, each a constant or a random delay. A right Alt recorded as AltGr is the right Alt (`43`) alone, and the firmware plays it as AltGr.

```
"a (145 ms) a released (437 ms) b (104 ms) b released": 7 events
4d 00 00 00 07 00 00 00  20 25  80 91  00 25  81 b5  20 36  80 68  00 36

5 pressed, a random delay of 0 to 500 ms, 5 released: 3 events in 8 bytes
4d 00 00 00 03 00 00 00  20 11  e0 00 01 f4  00 11

A pressed, a random delay of 1234 to 3456 ms, A released
e0 4d 2d 80                                     (the delay event)

Right Alt held around a key, as recorded
4d 00 00 00 07 00 00 00  20 43 80 7f 20 2e 80 67 00 2e 80 30 00 43
```

### Text (subtype 03)

iCUE turns the text into key events with a fixed **US layout** table, whatever the layout of the PC or of the keyboard:

- a character is press, delay 0, release (3 events); a character that needs Shift is left Shift press, the key, left Shift release, with delays of 0 between them (7 events);
- between two characters a delay event with the delay between characters (0 by default), none after the last;
- a space is `40`, a tab `18`, a line break one Enter (`52`);
- a character that is not in the table is dropped without a word.

iCUE takes at most **1024 characters** (a line break counts as one) and a delay between characters of **0 to 99999 ms**, written as a long delay from 8192 ms. So 1024 characters without Shift make 4095 events (3 per character and 1023 delays), a file of 8198 bytes; 1024 characters that all need Shift, the most, make 8191 events (7 per character and 1023 delays), a file of 16 390 bytes, or 18 436 bytes when the delay between characters is a long one *(inferred: worked out from the event sizes)*.

```
"ciao", no delay: 15 events
4d 03 00 00 0f 00 00 00  20 34 80 00 00 34 80 00  20 20 80 00 00 20 80 00  20 25 80 00 00 25 80 00  20 21 80 00 00 21

"ciao" with 150 ms between characters
4d 03 00 00 0f 00 00 00  20 34 80 00 00 34 80 96  20 20 80 00 00 20 80 96  20 25 80 00 00 25 80 96  20 21 80 00 00 21

"ab" with 10 000 ms between characters: the long delay where a short one would be
4d 03 00 00 07 00 00 00  20 25 80 00 00 25  c0 00 27 10  20 36 80 00 00 36

"Ab!@" with 150 ms between characters: Shift around each shifted character, "@" as Shift+2 (US table)
4d 03 00 00 1b 00 00 00  20 30 80 00 20 25 80 00 00 25 80 00 00 30 80 96  20 36 80 00 00 36 80 96
                         20 30 80 00 20 0d 80 00 00 0d 80 00 00 30 80 96  20 30 80 00 20 0e 80 00 00 0e 80 00 00 30

A space, a tab and a line break
4d 03 00 00 0b 00 00 00  20 40 80 00 00 40 80 00  20 18 80 00 00 18 80 00  20 52 80 00 00 52
```

### Key combination (subtype 01)

At most three modifiers among Windows, Ctrl, Alt and Shift and at most one other key. iCUE presses them in a fixed order whatever the order in which they were chosen, **Windows, Ctrl, Alt, Shift, then the key**, and releases them in the reverse order, with delays of 0. It writes the left modifiers only (`3d`, `3c`, `3e`, `30`).

```
Ctrl+Shift+V: 11 events
4d 01 00 00 0b 00 00 00  20 3c 80 00 20 30 80 00 20 35 80 00  00 35 80 00 00 30 80 00 00 3c

Ctrl+Alt+Shift+T
4d 01 00 00 0f 00 00 00  20 3c 80 00 20 3e 80 00 20 30 80 00 20 1d 80 00  00 1d 80 00 00 30 80 00 00 3e 80 00 00 3c

Ctrl+Shift+Windows+B: Windows first
4d 01 00 00 0f 00 00 00  20 3d 80 00 20 3c 80 00 20 30 80 00 20 36 80 00  00 36 80 00 00 30 80 00 00 3c 80 00 00 3d
```

### Imitate holding key

A key remap with iCUE's option "Imitate holding key" is not a [remap](profile-map.md): it is a key combination file of three events, **press, delay, release** of the destination key, with an entry in [PROFILE.DAT](profile-dat.md). The option has two modes:

- On press, with "Terminate in (sec)", 0.1 to 99999 s with one decimal: the delay is that time, the entry `00 01 01`. On the keyboard the destination key is held for the time. A time of 8192 ms or more is a long delay; iCUE writes 16 777.2 s as `c0 ff ff f0` and every longer time as `c0 ff ff ff`, the largest long delay.
- Toggle: delay 0, the entry `00 84 00`: an autofire of the key until it is pressed again ([PROFILE.DAT](profile-dat.md#start-condition-and-run-type)).

iCUE offers the option for key remaps only, not for mouse buttons, language keys, key combinations or media keys. Reading a file back, the entry and the delay tell the kinds apart: `00 01 01` with a delay of 0 is a key combination of one key, `00 01 01` with a delay of 100 ms or more is "Imitate holding key" on press, `00 84 00` with a delay of 0 is its toggle.

```
Remap to the grave accent key, on press, 0.1 s (entry 00 01 01)
4d 01 00 00 03 00 00 00  20 0c 80 64 00 0c

The same, 1.0 s
4d 01 00 00 03 00 00 00  20 0c 83 e8 00 0c

The same, toggle (entry 00 84 00)
4d 01 00 00 03 00 00 00  20 0c 80 00 00 0c

Remap to P, on press, 40.0 s: 3 events in 8 bytes, a long delay
4d 01 00 00 03 00 00 00  20 22  c0 00 9c 40  00 22
```

## Limits

iCUE puts no limit of its own on the number of actions or on the bytes of the macro files of a slot (20 actions and 22 macro files in one slot have been seen); the limit is the free space of the flash ([space](README.md#space)). A macro file is written and read like any other file, in more bursts.
