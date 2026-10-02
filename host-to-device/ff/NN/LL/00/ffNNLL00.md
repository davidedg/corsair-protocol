# `ff NN LL 00` - Read a chunk of the read buffer (K95 RGB Platinum)

Part of the on-board profile file system of the K95 RGB Platinum ([devices/k95p.md](../../../../../devices/k95p.md)). Asks for chunk `NN` (1 to 5) of `LL` bytes (at most 60, `3c`) of the 300-byte read buffer, which [`07 17 0a`](../../../../07/17/0a/07170a.md) has filled from the open file. The reply carries the data from byte 4:

```
> ff 01 3c 00        < 0e ff 01 3c <60 bytes of data>
> ff 02 3c 00        < 0e ff 02 3c <60 bytes of data>
...
> ff 05 1c 00        < 0e ff 05 1c <28 bytes of data>      (the last chunk of a file may be shorter)
```

The reply starts with `0e ff NN LL`. Without `07 17 0a` before each burst of chunks the replies hold stale data.
