# `PROFILE.I`: profile info

The identity and the name of the profile, and the colours of the three buttons that are not [lighting cells](lighting-cells.md#buttons-that-are-not-cells). 268 bytes.

```
offset  size  field
0       2     header 49 00
2       16    GUID of the profile (the same 16 bytes as in its slot table entry)
18      4     revision cookie (u32; iCUE keeps it within 0 to 0xfffffe)
22      ~64   name, UTF-16LE, zero terminated (iCUE: "HW Profile 1", ...)
...     ...   zeros
253     3     profile indicator colour, RGB (default ff 00 00)
256     3     brightness indicator colour, RGB (default ff ff ff)
259     3     Win Lock on colour, RGB (default 00 ff ff)
262     3     Win Lock off colour, RGB (default ff 00 00)
265     3     00 00 00
```

- The GUID and the cookie are the ones of the slot table ([`0e 17 04`](../../host-to-device/0e/17/04/0e1704.md), [`07 15 SS 00`](../../host-to-device/07/15/SS/00/0715SS00.md)), where the cookie has 24 bits. iCUE raises the cookie at every save of the profile and wraps it from `0xfffffe` to 0.
- The profile button shows the profile colour, the brightness button the brightness colour, the Win Lock button the on or off colour. The firmware shows the colours as soon as they are written and the keyboard is back in hardware mode, without switching slot.
- **There is one brightness colour**, the one of the 100 % level: the firmware shows it dimmed at the lower levels, down to off at 0 %. Nothing else in the 268 bytes varies between iCUE's files but the GUID, the cookie, the name and these colours.
- The Win Lock on and off colours are there from firmware 3.1, according to iCUE.
- The colour that iCUE's interface lets one give to a profile is not in this file (nor in any other file of the slot).
- A writer that changes the name, the cookie or the colours should patch the file it read rather than build a new one: the bytes after the name may hold the rest of a longer name.

## Example

`HW Profile 3`, cookie 8, default colours:

```
49 00 f2 dd 5a 20 fd 76 4a d4 94 d8 aa a5 f3 db 85 da 08 00 00 00 48 00 57 00 20 00 50 00 72 00
6f 00 66 00 69 00 6c 00 65 00 20 00 33 00 00 00 ... 00 ff 00 00 ff ff ff 00 ff ff ff 00 00 00 00 00
```
