# Z80to
Simple utility to convert Z80 or SNA snapshots to tape, cartridge or disk format so they can be loaded on real hardware with the appropriate device. Works with Tape, Sinclair Microdrive, Opus Discovery, MGT Plus D and +3 Disks. It supports either 48k or 128k snapshots.

The utility eliminates screen corruption, which a lot of the other conversion tools suffer from, by using compression and a custom 4 stage loader. Once memory is restored the only difference between this and the original snapshot will be a few bytes under the stack.

This brings all my Z80on utilities into one code base, ensuring any updates are now made across all formats. As per the other utilties you can either create a single snapshot per tape/cartridge/disk or one with multiple snapshots included and a menu to load them. You can also replace the loading screen with a custom one, as long as there is space.

I've also made some further advancements in this version such as ability to autodetect 48k snapshots incorrect saved as 128k and the ability to not store blank 128k memory pages to save space. The menu code is also now unified across all formats.

I originally created this utility to accompany my ZXPicoMD device (https://github.com/TomDDG/ZXPicoMD) as very few Microdrive cartridges where released in the 1980s.

I highly recommend using Skoolkit (https://skoolkit.ca/skoolkit/) to create "perfect" snapshots to use with this utility. Skoolkit will create the snapshot as soon as the game has loaded which has the advantage, in most cases, of removing the need to add a loading screen back in.

```
Z80to v1.2 ©Tom Dalby 2025
Usage: z80to [Output Format] <Menu> "InputFile" <InputFile Options>

Output Format:
  -t               Create Cassette [.TAP] Tape Output
  -m               Create Microdrive [.MDR] Cartridge Output
  -o               Create Opus Discovery [.OPD] Disk Output (SSSD Disk)
  -p               Create Plus D [.MGT] Disk Output (DSDD Disk)
  -d               Create +3DOS (DSK) Disk Output (SSSD Disk)
  -r               Create TR-DOS Disk Output (SSDD Disk)

<optional> Menu:
  -n "Name"        Add menu, titled "Name" (max 10chars). Up to 16 snapshots

"InputFile"        .Z80 or .SNA snapshot. 48k & 128k supported

<optional> InputFile Options:
  -l screen.scr    Use Alternate Loading Screen (must by 6912byte SCR)
  -f               Force 48k removing any 128k memory banks & disabling paging
  -k               Keep 128k fully intact (-f overrides)

Examples:
  z80to -m mminer.z80
    Creates Microdrive Cartridge with single snapshot
  z80to -o mminer.z80 jsw.z80
    Creates OPD disk with two snapshots
  z80to -p -n "MM+JSW" mminer.z80 jsw.z80 -l jsw.scr
    Creates MGT disk with two snapshots & menu titled "MM+JSW". jsw has an alternate loading screen
```
## Versions
- v1.0 - initial release
- v1.0a - fixed 3DOS naming convention
- v1.1 - 3DOS now works with 128k snapshots
- v1.2 - TR-DOS disks format added SSDD only

## 3DOS
Getting 128k snapshots to work on a +3 was a challenge. Unlike the other formats 3DOS makes extensive use of memory bank 7, even after disabling the RAM drive and disk cache. As a result, and if a snapshot contains data in bank 7, the launcher becomes a lot more complicated. This is why the original release of this utility only supported 48k snapshots as these don't require restoration of bank 7. 

In my original Z80onDSK utility I simply used the screen for the launcher and storing bank 7, but this causes a lot of corruption which looks a mess. As this is a unified utility I wanted 3DOS to match the others with zero corruption unless absolutely necessary. In order to do this I had to split bank 7 into 2 parts. Part 1 contains the memory areas around the area 3DOS uses, and this is loaded as per the other memory banks. The 2nd part is then added to the main compression and decompressed only after 3DOS has finished. This is the only time it is safe to overwrite the 3DOS area of bank 7. If there isn't enough room, then the screen is used as the last resort.

The only other change from the other formats is the handling of the alternate loading screens. For the other formats these are added to the main compression if there is enough memory. If there isn't enough memory then the final screen is loaded at the end, basically as a normal SCREEN$. Due to the bank 7 decompression 3DOS always puts the final screen at the end and never in the main compression.

### Motor timeout
On a +3 machine running 3DOS the 48k ROM contains an additional motor timeout routine which will run on an interrupt. This ROM routine uses memory location 0xe600 in bank 7 and over time will decrease the value held at this location to zero, upon which the motor is switched off. If the snapshot requires this memory location to be anything other than zero and the standard ROM interrupt is on then it may not work as the ROM routine will continually decrease this value on each interrupt.
