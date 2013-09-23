# GODUS unofficial file format documentation

##Overview
This document outlines the file format found in Godus, beta 1.4.1 . As this game is in beta, the file format is almost guaranteed to change with time. I will try to keep this updated as I can; please submit pull requests if you can contribute anything! 

Godus uses a binary file format that consists of a sequence of blocks. A checksum of the contents of the file is stored at the end of the file, so it isn't expected that we can modify the file at this time without knowing how to regenerate this checksum.

All values are unsigned small-endian unless otherwise specified.

##File format

The file format consists of a header, a series of blocks, and a footer.

    [# of blocks][block 0][block 1]...[block n-1][checksum block]

The # of blocks is stored as an 8 byte integer at the beginning of the file; all other data is stored in blocks. The current format appears to have 22 blocks.

There is no way to randomly access a block without visiting each preceding block and examining its length.

##Block format
Each block consists of a fixed length header, and a variable length data section. The header format is common to all block types, but the data section's format is different for each block type.

    [64 bytes- header] [?? bytes - data (length specified in header)]

###Block header format:

The header is a fixed 64 bytes. Most bytes are common to each header; there isn't much variation other than the name and length fields.

    [12 bytes- null terminated name. Unused bytes filled with junk]
    [48 bytes- ??. Most bytes identical in all 22 blocks]
    [4 bytes - data section length]

The length field is the length of the data section; the total block length is 64 + length.

###Block types:
**houses**: Details of each house.

This field has a series of 208 byte structures, each describing one house.

**terrain**

**landeffects**: Consists of mostly 0x80 repeated over and over, with only a few bytes different

**trees**: Describes each tree. Consists of an array of 32 byte structures:

* 4 bytes, all 0 except the first (which is 0xffffffff)
* 4 bytes, large numbers. A coordinate?
* 4 bytes, large numbers. A coordinate?
* 4 bytes, large numbers. A coordinate?
* 4 bytes, always below 14. An enum?
* 4 bytes, always 3?
* 4 bytes, large numbers. A coordinate?
* 4 bytes, large numbers. A coordinate?

**followers**: ??
First half of data is empty. Second half is an array of 96 byte structures.

**playerdata**: Contains names of known players living in your world, and some other stuff.

**missionprogress**: Contains a list of numbers showing the status of each mission at the Mount of the Gods.

* 0: unplayed
* 1: successfully completed?

Currently 64 elements.

**MiscV1**: contains the name you selected for yourself and your people, and probably more.

**Currency data**: This section is currently 7 numbers, each 8 bytes.

    1: ????
    2: ????
    3: ????
    4: ????
    5: ????
    6: Belief count
    7: Gem count

**Special checksum block:**

The special checksum block is a bit different than the other blocks in the file- the header is much more sparsely populated. It contains only a 10 byte checksum, and the header is all 0s except for the length field (= 10).

We don't yet know how the checksum is calculated.