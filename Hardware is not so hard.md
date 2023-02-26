# Hardware is not so hard

## Information
**Category** | **Points** 
--- | --- 
Hardware | 100 

## Description

I have captured communication between a SD card and an embedded device. Could you extract the content of the SD Card? It's in SPI mode.

## Solution

Actually i got this by lucky as i have limited knowledge about hardware :)).

Looking at the source given, i notice at these lines :
```
Device to SD Card : 510000000055
SD Card to Device : 00
SD Card to Device : fffffffffffffffffffeffd8ffe000104a46494600010101004800480000ffdb0043002c1e2127211c2c272427322f2c35426f48423d3d42886166506fa18da9a69e8d9b98b1c7ffd8b1bcf1bf989bdeffe0f1ffffffffffacd5ffffffffffffffffffffdb0043012f3232423a4282484882ffb79bb7ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffc20011080355028003011100021101031101ffc400190001010101010100000000000000000000000102030405ffc4001801010101010100000000000000000000000001020304ffda000c03010002100310000001f1800000140000000052800a529a346e29a29a280000000002804000000000000001c4f15000080000a53a1a2c5294a0a5294a500a0800000001400000000014100000000000000064f9d40002000029e98ee50014000000005040000002800000a40000000000000000000014f9b590002000029ee8d8050000000000504000000280000000000000000000000000014f09c68002000029f4600a000000000500004000050000000002900000000000000000283ca796801000003a9ee8000000000000a000400140000000280002000000000000000a00389e1a02000000f447ac0000000000050000400a00000000280002000000000000000a00327cda00400000f6477000000000001411dc
```

All the hex messages after in 'SD Card to Devices' also begin with 'ff....fe', and i also noticed JPG header 'ffd8ff' in the first hex message, so i tried to remove all 'ff...fe' and concatenated all the hex message to get a jpg image.

Unfortunately, it didn't give me the image. I made another guess, that each hex message is correspond to one hex number '5100..' above it, so i sort them in ascending order and tried again.

This time, my guess was right, but it's blurred, so i have to guess the flag and once again, by luck, i got the flag correctly (I got 10 minutes left when i solved this chall).

> Flag is: ACSC{1tW@sE@syW@snt1t}
