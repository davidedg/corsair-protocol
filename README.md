# The Corsair HID protocol

<sup>**_NOTE:_** This is a fork of [ckb-next/corsair-protocol](https://github.com/ckb-next/corsair-protocol). It adds a description of the on-board profiles of the K95 RGB Platinum, starting from [`devices/`](devices/README.md). These additions were written with AI assistance, and the upstream developers have a strict no-AI policy, hence the separate fork.</sup>

This repo is structured into folders by bytes, to facilitate fast lookup.

- [`devices/`](devices/README.md): the overall picture of a device, with links to the pages of its commands and formats.
- [`formats/`](formats/cape/README.md): file formats, such as the files of the on-board profiles of the K95 RGB Platinum.

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).

## What we know so far

All packets have a 64 byte payload, padded with zeroes.
The first four bytes of the command are echoed in the response packet.
The protocol is poll based - the mouse may reply to an `0e` command with an arbitrary amount of `01` events terminated with an `03` event before replying.
The protocol uses USB URB interrupts - URB control packets work on most devices, but not on the K95 Platinum.

- 01 - HID event from device to host.
- 03 - Corsair-specific event from device to host.
- 07 - Write command from host to device - does not get a reply from the board.
- 0e - Read command from host to device - gets a reply from the board.
- 7f - Multiple packet stream from host to device - used as a payload in firmware update and colour update
- ff - Read of a chunk of data from host to device - gets a reply from the board; used to read the files of the on-board profiles of the K95 RGB Platinum.
- 05 - Mouse report of the K95 RGB Platinum, from device to host.
