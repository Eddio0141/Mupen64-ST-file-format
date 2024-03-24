# Save state file (.st file) format for the mupen 64 emulator


### ST files are all compressed using the gzip compression - https://en.wikipedia.org/wiki/Gzip
### All raw data is stored as a 4 byte aligned little endian


hex offset | length (bytes) | name / desc
---------- | -------------- | -----------
0 | 32 | Current ROM's MD5 Hash
20 | 40 | RDRAM registers
48 | 36 | MI registers
6c | 52 | PI registers
A0 | 52 | SP registers
D4 | 8 | RSP register
DC | 16 | SI registers
EC | 60 | VI registers
128 | 32 | RI registers
148 | 40 | AI registers
170 | 48 | DPC registers
1A0 | 16 | DPS registers
1B0 | 8388608 | RDRAM - Always 8MB since there's no option to disable expansion slot
8001B0 | 4096 | SP DMEM
8011B0 | 4096 | SP IMEM
8021B0 | 64 | PIF RAM
​
Flashram info
8021F0 | 4 | Uses flashram?
8021F4 | 4 | Mode
8021F8 | 8 | Status
802200 | 4 | Erase Offset
802204 | 4 | Write Pointer
​
802208 | 1048576 | TLB LUT R
902208 | 1048576 | TLB LUT W
A02208 | 4 | LLBit register
A0220C | 256 | CPU registers
A0230C | 256 | MMU registers
A0240C | 8 | lo register
A02414 | 8 | hi register
A0241C | 256 | FPU registers
A0251C | 4 | FCR0 register
A02520 | 4 | FCR31 register (FPU status)
A02524 | 1664 | TLB E
A02BA4 | 4 | PC register
A02BA8 | 4 | next interrupt
A02BAC | 4 | next VI
A02BB0 | 4 | VI field
​
EventQueue info
A02BB4 | - | -
| | | It will be 4 bytes aligned and if you hit the value "0xFFFFFFFF" then this section will end
| | | It's usually 36 bytes but make sure you check when you hit 0xFFFFFFFF
| | | If the first 4 bytes isn't starting with 0xFFFFFFFF then it will be in a pattern -> interupt queue type, interupt queue count until it hits 0xFFFFFFFF
​
A02BB4 + EventQueue Length | 4 | If this ST file is for a movie
​
Those last part applies if this ST file is for a movie
A02BB8 + EventQueue Length | 4 | movie freeze size - Length of the "movie freeze buffer" section
​
Movie freeze buffer
A02BBC + EventQueue Length | 4 | Movie's "UID"
A02BC0 + EventQueue Length | 4 | Input frame count when this ST was made
A02BC4 + EventQueue Length | 4 | VI frame count when this ST was made
A02BC8 + EventQueue Length | 4 | length samples - Movie's length sample count when this ST file was made
A02BCC + EventQueue Length | 4 * (length samples + 1) | Movie input data up till point of ST creation
A02BCC + EventQueue Length + 4 * (length samples + 1) | 4 | Video section identifier - `0x53435200` (st file ends here on savestates without this section)
A02BCC + EventQueue Length + 4 * (length samples + 1) + 4 | 4 | Video buffer width
A02BCC + EventQueue Length + 4 * (length samples + 1) + 8 | 4 | Video buffer height
A02BCC + EventQueue Length + 4 * (length samples + 1) + 12 | width * height * 3 | Video buffer data

