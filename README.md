# Save state file (.st file) format for the mupen 64 emulator


### ST files are all compressed using the gzip compression - https://en.wikipedia.org/wiki/Gzip
### All raw data is stored as a 4 byte aligned little endian


hex offset | length (bytes) | name / desc
0								32							Current ROM's MD5 Hash
20								40							rdram register
48								36							MI register
6c								52							pi register
A0								52							sp register
D4								8							rsp register
DC								16							si register
EC								60							vi register
128								32							ri register
148								40							ai register
170								48							dpc register
1A0								16							dps register
1B0								8388608						RDRAM - Always 8MB since there's no option to disable expansion slot
8001B0							4096						SP DMEM
8011B0							4096						SP IMEM
8021B0							64							PIF RAM

	- Flashram info
8021F0							4							Use flashram
8021F4							4							Mode
8021F8							8							Status
802200							4							Erase Offset
802204							4							Write Pointer

802208							1048576						tlb LUT r
902208							1048576						tlb LUT w
A02208							4							llbit
A0220C							256							reg
A0230C							256							reg cop0 - It's an unsigned int array with the length of 32
															it gives each index 8 bytes for old version compatibility
A0240C							8							lo
A02414							8							hi
A0241C							256							reg cop1 fgr 64
A0251C							4							FCR0
A02520							4							FCR31
A02524							1664						tlb e
A02BA4							4							If emu is not using a dynamic core and uses the interpcore then it stores the "interp address"
															Or else it stores the "PC address"
A02BA8							4							next interupt
A02BAC							4							next vi
A02BB0							4							vi field

	- EventQueue info
A02BB4							-							-
		- It will be 4 bytes aligned and if you hit the value "0xFFFFFFFF" then this section will end
		- It's usually 36 bytes but make sure you check when you hit 0xFFFFFFFF
		- If the first 4 bytes isn't starting with 0xFFFFFFFF then it will be in a pattern [interupt queue type, interupt queue count] until it hits 0xFFFFFFFF

A02BB4 + EventQueue Length		4							If this ST file is for a movie

	- Those last part applies if this ST file is for a movie
A02BB8 + EventQueue Length		4							movie freeze size - Length of the "movie freeze buffer" section
	
	- Movie freeze buffer
A02BBC + EventQueue Length		4							Movie's "UID"
A02BC0 + EventQueue Length		4							Input frame count when this ST was made
A02BC4 + EventQueue Length		4							VI frame count when this ST was made
A02BC8 + EventQueue Length		4							"length_samples" - Movie's length sample count when this ST file was made
A02BCC + EventQueue Length		4 * (length_samples + 1)	Movie inputs in an array					
