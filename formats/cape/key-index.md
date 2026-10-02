# Key indices

The files of an on-board profile ([PROFILE.MAP](profile-map.md), [PROFILE.DAT](profile-dat.md) and the [macro files](macro.md)) name keys by their **key index**: the firmware's own number of the key, the same number the `07 40` key input packets use ([`07 40 NN 00`](../../host-to-device/07/40/NN/00/0740NN00.md)). It is not a USB HID usage.

As a destination (a remap, a key event of a macro) an index makes the keyboard send the USB HID usage of that key, also for indices of keys that the keyboard itself does not have (the language keys, for instance).

The names in the third column are the short names of the [ckb-next](https://github.com/ckb-next/ckb-next) key map, where the position of a key in its table is this index. The last column marks the 121 keys of the K95 RGB Platinum the tables come from, an ISO unit: an ANSI unit has `bslash` (`50`) in place of `hash` and `bslash_iso` *(inferred)*.

<!-- generated:key-index -->
| Index | Key | Name in ckb-next | On the K95 RGB Platinum |
|---|---|---|---|
| `00` | Esc | `esc` | yes |
| `01` | F1 | `f1` | yes |
| `02` | F2 | `f2` | yes |
| `03` | F3 | `f3` | yes |
| `04` | F4 | `f4` | yes |
| `05` | F5 | `f5` | yes |
| `06` | F6 | `f6` | yes |
| `07` | F7 | `f7` | yes |
| `08` | F8 | `f8` | yes |
| `09` | F9 | `f9` | yes |
| `0a` | F10 | `f10` | yes |
| `0b` | F11 | `f11` | yes |
| `0c` | Grave accent and tilde | `grave` | yes |
| `0d` | 1 | `1` | yes |
| `0e` | 2 | `2` | yes |
| `0f` | 3 | `3` | yes |
| `10` | 4 | `4` | yes |
| `11` | 5 | `5` | yes |
| `12` | 6 | `6` | yes |
| `13` | 7 | `7` | yes |
| `14` | 8 | `8` | yes |
| `15` | 9 | `9` | yes |
| `16` | 0 | `0` | yes |
| `17` | - and _ | `minus` | yes |
| `18` | Tab | `tab` | yes |
| `19` | Q | `q` | yes |
| `1a` | W | `w` | yes |
| `1b` | E | `e` | yes |
| `1c` | R | `r` | yes |
| `1d` | T | `t` | yes |
| `1e` | Y | `y` | yes |
| `1f` | U | `u` | yes |
| `20` | I | `i` | yes |
| `21` | O | `o` | yes |
| `22` | P | `p` | yes |
| `23` | [ and { | `lbrace` | yes |
| `24` | Caps Lock | `caps` | yes |
| `25` | A | `a` | yes |
| `26` | S | `s` | yes |
| `27` | D | `d` | yes |
| `28` | F | `f` | yes |
| `29` | G | `g` | yes |
| `2a` | H | `h` | yes |
| `2b` | J | `j` | yes |
| `2c` | K | `k` | yes |
| `2d` | L | `l` | yes |
| `2e` | ; and : | `colon` | yes |
| `2f` | ' and " | `quote` | yes |
| `30` | Left Shift | `lshift` | yes |
| `31` | ISO key right of Left Shift (Non-US \ and \|) | `bslash_iso` | yes |
| `32` | Z | `z` | yes |
| `33` | X | `x` | yes |
| `34` | C | `c` | yes |
| `35` | V | `v` | yes |
| `36` | B | `b` | yes |
| `37` | N | `n` | yes |
| `38` | M | `m` | yes |
| `39` | , and < | `comma` | yes |
| `3a` | . and > | `dot` | yes |
| `3b` | / and ? | `slash` | yes |
| `3c` | Left Ctrl | `lctrl` | yes |
| `3d` | Left Windows (GUI) | `lwin` | yes |
| `3e` | Left Alt | `lalt` | yes |
| `3f` | Lang2 (Hanja) | `hanja` |  |
| `40` | Space | `space` | yes |
| `41` | Lang1 (Hangul/English) | `hangul` |  |
| `42` | International2 (Katakana/Hiragana) | `katahira` |  |
| `43` | Right Alt (AltGr) | `ralt` | yes |
| `44` | Right Windows (GUI) | `rwin` | yes |
| `45` | Menu (Application) | `rmenu` | yes |
| `46` | Profile button | `profswitch` | yes |
| `47` | Brightness button | `light` | yes |
| `48` | F12 | `f12` | yes |
| `49` | Print Screen | `prtscn` | yes |
| `4a` | Scroll Lock | `scroll` | yes |
| `4b` | Pause/Break | `pause` | yes |
| `4c` | Insert | `ins` | yes |
| `4d` | Home | `home` | yes |
| `4e` | Page Up | `pgup` | yes |
| `4f` | ] and } | `rbrace` | yes |
| `50` | ANSI \ and \| (above Enter) | `bslash` |  |
| `51` | ISO key left of Enter (Non-US # and ~) | `hash` | yes |
| `52` | Enter | `enter` | yes |
| `53` | International1 (JIS Ro) | `ro` |  |
| `54` | = and + | `equal` | yes |
| `55` | International3 (JIS Yen) | `yen` |  |
| `56` | Backspace | `bspace` | yes |
| `57` | Delete | `del` | yes |
| `58` | End | `end` | yes |
| `59` | Page Down | `pgdn` | yes |
| `5a` | Right Shift | `rshift` | yes |
| `5b` | Right Ctrl | `rctrl` | yes |
| `5c` | Up arrow | `up` | yes |
| `5d` | Left arrow | `left` | yes |
| `5e` | Down arrow | `down` | yes |
| `5f` | Right arrow | `right` | yes |
| `60` | Win Lock button | `lock` | yes |
| `61` | Mute | `mute` | yes |
| `62` | Stop | `stop` | yes |
| `63` | Previous track | `prev` | yes |
| `64` | Play/Pause | `play` | yes |
| `65` | Next track | `next` | yes |
| `66` | Num Lock | `numlock` | yes |
| `67` | Keypad / | `numslash` | yes |
| `68` | Keypad * | `numstar` | yes |
| `69` | Keypad - | `numminus` | yes |
| `6a` | Keypad + | `numplus` | yes |
| `6b` | Keypad Enter | `numenter` | yes |
| `6c` | Keypad 7 | `num7` | yes |
| `6d` | Keypad 8 | `num8` | yes |
| `6e` | Keypad 9 | `num9` | yes |
| `70` | Keypad 4 | `num4` | yes |
| `71` | Keypad 5 | `num5` | yes |
| `72` | Keypad 6 | `num6` | yes |
| `73` | Keypad 1 | `num1` | yes |
| `74` | Keypad 2 | `num2` | yes |
| `75` | Keypad 3 | `num3` | yes |
| `76` | Keypad 0 | `num0` | yes |
| `77` | Keypad . (Delete) | `numdot` | yes |
| `78` | G1 | `g1` | yes |
| `79` | G2 | `g2` | yes |
| `7a` | G3 | `g3` | yes |
| `7b` | G4 | `g4` | yes |
| `7c` | G5 | `g5` | yes |
| `7d` | G6 | `g6` | yes |
| `7e` | G7 | `g7` |  |
| `7f` | G8 | `g8` |  |
| `80` | G9 | `g9` |  |
| `81` | G10 | `g10` |  |
| `82` | Volume wheel, up | `volup` | yes |
| `83` | Volume wheel, down | `voldn` | yes |
| `84` | MR (macro record) | `mr` |  |
| `85` | M1 | `m1` |  |
| `86` | M2 | `m2` |  |
| `87` | M3 | `m3` |  |
| `88` | G11 | `g11` |  |
| `89` | G12 | `g12` |  |
| `8a` | G13 | `g13` |  |
| `8b` | G14 | `g14` |  |
| `8c` | G15 | `g15` |  |
| `8d` | G16 | `g16` |  |
| `8e` | G17 | `g17` |  |
| `8f` | G18 | `g18` |  |
| `90` | International5 (Muhenkan) | `muhenkan` |  |
| `91` | International4 (Henkan) | `henkan` |  |
| `92` | Fn | `fn` |  |
<!-- /generated:key-index -->

Indices `6f` and `93` to `97` have no key.

## Mouse buttons

A remap can also send a mouse button. The firmware uses five codes outside the key indices for them:

<!-- generated:mouse-codes -->
| Code | Mouse button |
|---|---|
| `c8` | 1 (left) |
| `c9` | 2 (right) |
| `ca` | 3 (middle) |
| `cb` | 4 (back) |
| `cc` | 5 (forward) |
<!-- /generated:mouse-codes -->

In hardware mode the keyboard reports these buttons as a mouse, with its report [`05`](../../device-to-host/05/05.md).
