# `7f NN SS 00` - Write multiple packet stream

When the protocol needs to send a message that is more than a 64-byte packet long, it sends a series of 7f packets containing the data, with an incrementing nonce in NN, and the data length in SS (no more than 60 bytes per packet). This is used in firmware update, for transmitting the firmware packets, and in updating the colours, because there are so many keys.

## K95 RGB Platinum

The data of a file of the on-board file system is written with these packets, after [`07 17 05`](../../../../07/17/05/00/07170500.md) has opened the file: bursts of up to five chunks of up to 60 bytes (300 bytes, the size of the transfer buffer), `NN` from 1 to 5 within each burst, each burst committed with [`07 17 09`](../../../../07/17/09/071709.md). iCUE sends the chunks of a burst and their `07 17 09` about 1 ms apart.

```
> 7f 01 14 00 50 01 01 00 02 4d 30 30 30 1e 00 00 00 01 01 00 00 00 00 00     a PROFILE.DAT of 20 bytes, one chunk
> 07 17 09
```
