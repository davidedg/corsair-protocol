# Lighting cells

The lighting files of a profile ([lighting](lighting.md)) do not use [key indices](key-index.md): a layer lists its keys as **cells**, the positions of the keyboard's LED matrix. A K95 RGB Platinum has **135 cells**: 116 keys and the 19 LEDs of the light bar along the top edge.

## The cells, in iCUE's order

iCUE always lists the cells of a layer (in `lght_NN.k`) in one fixed order, whatever the order in which the keys were selected: the table below, a list of all 135 cells. Every `.k` that iCUE writes is a subsequence of it. Below cell 144 the order is the LED grid read row by row (the main block, then the media keys, the keypad and the G keys); the 19 cells of the light bar come last, in an order of their own. The LEDs of the light bar are numbered 1 to 19 as iCUE numbers them; their cells are not in the order of those numbers.

Two entries rest on the rule below rather than on a key seen alone: the cells of G2 to G6 (the six G keys were seen together, G1 alone), and the cell of light bar LED 10 (by elimination). A writer that wants the same bytes as iCUE sorts the cells of a layer by their position in this table. The firmware takes any order of the cells of a layer of one colour *(inferred: no other order was tried)*.

<!-- generated:cells -->
| Position | Cell | Key or LED | Key index |
|---|---|---|---|
| 0 | 0 | Esc | `00` |
| 1 | 8 | F1 | `01` |
| 2 | 16 | F2 | `02` |
| 3 | 24 | F3 | `03` |
| 4 | 32 | F4 | `04` |
| 5 | 40 | F5 | `05` |
| 6 | 48 | F6 | `06` |
| 7 | 56 | F7 | `07` |
| 8 | 64 | F8 | `08` |
| 9 | 72 | F9 | `09` |
| 10 | 80 | F10 | `0a` |
| 11 | 88 | F11 | `0b` |
| 12 | 1 | Grave accent and tilde | `0c` |
| 13 | 9 | 1 | `0d` |
| 14 | 17 | 2 | `0e` |
| 15 | 25 | 3 | `0f` |
| 16 | 33 | 4 | `10` |
| 17 | 41 | 5 | `11` |
| 18 | 49 | 6 | `12` |
| 19 | 57 | 7 | `13` |
| 20 | 65 | 8 | `14` |
| 21 | 73 | 9 | `15` |
| 22 | 81 | 0 | `16` |
| 23 | 89 | - and _ | `17` |
| 24 | 2 | Tab | `18` |
| 25 | 10 | Q | `19` |
| 26 | 18 | W | `1a` |
| 27 | 26 | E | `1b` |
| 28 | 34 | R | `1c` |
| 29 | 42 | T | `1d` |
| 30 | 50 | Y | `1e` |
| 31 | 58 | U | `1f` |
| 32 | 66 | I | `20` |
| 33 | 74 | O | `21` |
| 34 | 82 | P | `22` |
| 35 | 90 | [ and { | `23` |
| 36 | 3 | Caps Lock | `24` |
| 37 | 11 | A | `25` |
| 38 | 19 | S | `26` |
| 39 | 27 | D | `27` |
| 40 | 35 | F | `28` |
| 41 | 43 | G | `29` |
| 42 | 51 | H | `2a` |
| 43 | 59 | J | `2b` |
| 44 | 67 | K | `2c` |
| 45 | 75 | L | `2d` |
| 46 | 83 | ; and : | `2e` |
| 47 | 91 | ' and " | `2f` |
| 48 | 4 | Left Shift | `30` |
| 49 | 12 | ISO key right of Left Shift (Non-US \ and \|) | `31` |
| 50 | 20 | Z | `32` |
| 51 | 28 | X | `33` |
| 52 | 36 | C | `34` |
| 53 | 44 | V | `35` |
| 54 | 52 | B | `36` |
| 55 | 60 | N | `37` |
| 56 | 68 | M | `38` |
| 57 | 76 | , and < | `39` |
| 58 | 84 | . and > | `3a` |
| 59 | 92 | / and ? | `3b` |
| 60 | 5 | Left Ctrl | `3c` |
| 61 | 13 | Left Windows (GUI) | `3d` |
| 62 | 21 | Left Alt | `3e` |
| 63 | 37 | Space | `40` |
| 64 | 61 | Right Alt (AltGr) | `43` |
| 65 | 69 | Right Windows (GUI) | `44` |
| 66 | 77 | Menu (Application) | `45` |
| 67 | 6 | F12 | `48` |
| 68 | 14 | Print Screen | `49` |
| 69 | 22 | Scroll Lock | `4a` |
| 70 | 30 | Pause/Break | `4b` |
| 71 | 38 | Insert | `4c` |
| 72 | 46 | Home | `4d` |
| 73 | 54 | Page Up | `4e` |
| 74 | 62 | ] and } | `4f` |
| 75 | 78 | ISO key left of Enter (Non-US # and ~) | `51` |
| 76 | 86 | Enter | `52` |
| 77 | 7 | = and + | `54` |
| 78 | 23 | Backspace | `56` |
| 79 | 31 | Delete | `57` |
| 80 | 39 | End | `58` |
| 81 | 47 | Page Down | `59` |
| 82 | 55 | Right Shift | `5a` |
| 83 | 63 | Right Ctrl | `5b` |
| 84 | 71 | Up arrow | `5c` |
| 85 | 79 | Left arrow | `5d` |
| 86 | 87 | Down arrow | `5e` |
| 87 | 95 | Right arrow | `5f` |
| 88 | 104 | Mute | `61` |
| 89 | 112 | Stop | `62` |
| 90 | 120 | Previous track | `63` |
| 91 | 128 | Play/Pause | `64` |
| 92 | 136 | Next track | `65` |
| 93 | 100 | Num Lock | `66` |
| 94 | 108 | Keypad / | `67` |
| 95 | 116 | Keypad * | `68` |
| 96 | 124 | Keypad - | `69` |
| 97 | 132 | Keypad + | `6a` |
| 98 | 140 | Keypad Enter | `6b` |
| 99 | 97 | Keypad 7 | `6c` |
| 100 | 105 | Keypad 8 | `6d` |
| 101 | 113 | Keypad 9 | `6e` |
| 102 | 129 | Keypad 4 | `70` |
| 103 | 137 | Keypad 5 | `71` |
| 104 | 101 | Keypad 6 | `72` |
| 105 | 109 | Keypad 1 | `73` |
| 106 | 117 | Keypad 2 | `74` |
| 107 | 125 | Keypad 3 | `75` |
| 108 | 133 | Keypad 0 | `76` |
| 109 | 141 | Keypad . (Delete) | `77` |
| 110 | 98 | G1 | `78` |
| 111 | 106 | G2 | `79` |
| 112 | 114 | G3 | `7a` |
| 113 | 122 | G4 | `7b` |
| 114 | 130 | G5 | `7c` |
| 115 | 138 | G6 | `7d` |
| 116 | 144 | Light bar LED 1 | - |
| 117 | 145 | Light bar LED 2 | - |
| 118 | 146 | Light bar LED 3 | - |
| 119 | 158 | Light bar LED 4 | - |
| 120 | 160 | Light bar LED 5 | - |
| 121 | 147 | Light bar LED 6 | - |
| 122 | 148 | Light bar LED 7 | - |
| 123 | 149 | Light bar LED 8 | - |
| 124 | 150 | Light bar LED 9 | - |
| 125 | 151 | Light bar LED 10 | - |
| 126 | 152 | Light bar LED 11 | - |
| 127 | 153 | Light bar LED 12 | - |
| 128 | 154 | Light bar LED 13 | - |
| 129 | 155 | Light bar LED 14 | - |
| 130 | 159 | Light bar LED 15 | - |
| 131 | 162 | Light bar LED 16 | - |
| 132 | 161 | Light bar LED 17 | - |
| 133 | 156 | Light bar LED 18 | - |
| 134 | 157 | Light bar LED 19 | - |
<!-- /generated:cells -->

## Buttons that are not cells

Three buttons are lit but have no cell, and are never in a layer: their colours are the indicator colours of the profile.

<!-- generated:non-cells -->
| Button | Key index | Its colour |
|---|---|---|
| Profile button | `46` | profile indicator colour ([PROFILE.I](profile-i.md)) |
| Brightness button | `47` | brightness indicator colour ([PROFILE.I](profile-i.md)) |
| Win Lock button | `60` | Win Lock on and off colours ([PROFILE.I](profile-i.md)) |
<!-- /generated:non-cells -->

## The cell rule

The cells are the LED numbers of the K95 RGB Platinum (the second field of the K95 table of the ckb-next key map, here `led`) arranged in another way: the firmware's matrix has 8 rows where that numbering has 12. With `c = led / 12` and `r = led % 12` (integer division):

```
led >= 144 (light bar)     cell = led                                     -> 144..162
r < 8                      cell = 8*c + r                                 ->   0..95
r >= 8                     cell = 8*(12 + c%6) + 4*(c/6) + (r - 8)        ->  96..143
```

Rows 0 to 7 stay where they are; rows 8 to 11 (keypad, media, G and M keys) go to the columns 12 to 17 of the matrix. The rule gives every cell of the table above, and the cells it gives for the other keys of that numbering (the JIS and ANSI keys, the G7 to G18 and M keys of other models, and the three buttons above) are exactly the ones missing from the table. The table above is the reference: a writer must not emit a cell that is not in it.
