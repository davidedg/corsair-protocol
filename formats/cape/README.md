# On-board profile files ("CAPE" file system)

The K95 RGB Platinum keeps its three on-board profiles in a small file system in its flash memory, reached through the commands of field `0x17` ([`0e 17`](../../host-to-device/0e/17/) and [`07 17`](../../host-to-device/07/17/)). Each profile slot is a set of files, and in hardware mode the firmware runs the profile of the active slot from them. How the files are read and written is in [devices/k95p.md](../../devices/k95p.md).

Everything in these pages was observed on a K95 RGB Platinum with firmware 3.29 and bootloader 3.03, unless it is marked *(inferred)* or described as what iCUE writes. Byte values are hexadecimal; multi-byte integers are little-endian unless stated otherwise.

## The files of a slot

| File | Size | Content | Page |
|---|---|---|---|
| `PROFILE.I` | 268 | identity (GUID, revision cookie), name, indicator colours | [PROFILE.I](profile-i.md) |
| `PROFILE.MAP` | 4 + 3 per entry | key remaps | [PROFILE.MAP](profile-map.md) |
| `PROFILE.DAT` | 4 + 16 per entry | actions (macro, text, key combination) by key; Win Lock options | [PROFILE.DAT](profile-dat.md) |
| `M000`, `M001`, ... | 8 + the events | the events of one action | [macro files](macro.md) |
| `lghtcnt.cnt` | 4 | number of lighting layers (u32) | [lighting](lighting.md) |
| `lght_NN.d` | 13, 37 or 553 | descriptor of lighting layer NN | [lighting](lighting.md), [custom effects](custom-effects.md), [predefined effects](predefined-effects.md) |
| `lght_NN.k` | 4 + 1 per cell | the cells of the layer | [lighting](lighting.md) |
| `lght_NN.r` | 1024 | the colours of a static layer | [lighting](lighting.md) |
| `PROFILE.ZIP` | variable | iCUE's own copy of the profile | below |

File names are ASCII, at most 12 characters with the terminating zero, in 8.3 style.

The first byte of the binary files is a type letter, the second a version or subtype. iCUE's names for the letters: `P` (`50`) the action table (`PROFILE.DAT`), `A` (`41`) the remap table (`PROFILE.MAP`), `I` (`49`) the profile info (`PROFILE.I`), `M` (`4d`) a macro file; it also names `D` (RGB data) and `X` (effects), not seen in these files.

Two other tables use their own numbering: the files of keys and actions name keys by [key index](key-index.md), the lighting files by [cell](lighting-cells.md).

## An empty profile

A profile created in an empty slot by iCUE has `PROFILE.DAT` = `50 01 00 00`, `PROFILE.MAP` = `41 00 00 00`, `lghtcnt.cnt` = 0, no lighting or macro files, a `PROFILE.I` and a `PROFILE.ZIP`. iCUE names it `HW Profile N`, N being the slot number counted from 1.

## Space

The flash has room for **2038 sectors of 4 KiB** for the files of the three slots. A file takes ceil(size / 4096) sectors, at least one; `PROFILE.ZIP` counts like any other file. [`0e 17 0b`](../../host-to-device/0e/17/0b/0e170b.md) answers the free space. Clearing a slot frees every file of it.

How many files the file system can hold is not known: up to 27 files in one slot and 40 in all have been seen.

## `PROFILE.ZIP`

iCUE's own copy of the profile, for iCUE: a 4-byte **big-endian** uncompressed length followed by a zlib stream (Qt's `qCompress` format), holding iCUE's serialisation of the profile. **The firmware does not read it**: a slot without it works, but iCUE 5 shows that slot as empty. Its content is not described here.
