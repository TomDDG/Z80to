# Z80to
Simple utility to convert Z80 or SNA snapshots to tape, cartridge or disk format so they can be loaded on real hardware with the appropriate device. Works with Tape, Sinclair Microdrive, Opus Discovery, MGT Plus D and +3 Disks. It supports either 48k or 128k snapshots.

The utility eliminates screen corruption, which a lot of the other conversion tools suffer from, by using compression and a custom 4 stage loader. Once memory is restored the only difference between this and the original snapshot will be a few bytes under the stack.

This brings all my Z80on utilities into one code base, ensuring any updates are now made across all formats. As per the other utilties you can either create a single snapshot per tape/cartridge/disk or one with multiple snapshots included and a menu to load them. You can also replace the loading screen with a custom one, as long as there is space.

I've also made some further advancements in this version such as ability to autodetect 48k snapshots incorrect saved as 128k and the ability to not store blank 128k memory pages to save space. The menu code is also now unified across all formats.

I originally created this utility to accompany my ZXPicoMD device (https://github.com/TomDDG/ZXPicoMD) as very few Microdrive cartridges where released in the 1980s.

I highly recommend using Skoolkit (https://skoolkit.ca/skoolkit/) to create "perfect" snapshots to use with this utility. Skoolkit will create the snapshot as soon as the game has loaded which has the advantage, in most cases, of removing the need to add a loading screen back in.

```
Z80to v1.0 ©Tom Dalby 2025
Usage: z80to [Output Format] <Menu> "InputFile" <InputFile Options>

Output Format:
  -t               Create Cassette [.TAP] Tape Output
  -m               Create Microdrive [.MDR] Cartridge Output
  -o               Create Opus Discovery [.OPD] Disk Output (SSSD Disk)
  -p               Create Plus D [.MGT] Disk Output (DSDD Disk)
  -d               Create +3DOS (DSK) Disk Output (SSSD Disk) *48k Only

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
v1.0 - initial release
v1.0a - fixed 3DOS naming convention
v1.1 - 3DOS now works with 128k snapshots
