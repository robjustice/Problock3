# Problock3
A SOS Driver for Prodos block mode cards. 

This SOS block driver interfaces with Apple cards that use the Prodos block mode compatible firmware. The idea came about after I purchased a Booti card and was looking to use this in my Apple ///. I had some discussions with the designers and they wondered about using the block mode interface. We thought the screenholes would be an issue. The Apple /// uses these for font loading and does not allow them for card firmware use.

## Details
This driver does the following to try and setup an environment for the card firmware to allow it to work without interfering with SOS.
Before calling the firmware on the card:
- saves the screenholes and loads in the card firmware ones (current slot and slot0)
- save zeropage $20-$4f
- if there is VBL interrupts enabled, then wait from 2 x vbl periods, before turning of font loading and changing the screenholes. This part i'm still not sure I have it working perfectly. I have tried quite a few varitaons and this seems ilke the best.
- turn off interrupts before we change anything.

On return from the firmware call, it restores these saved values.

If the driver slot is configured with $FF (default), then the driver scans the slots from 4 to 1 looking for the Prodos firmware signature. The first one it finds, it updates the DIBs with this slot and uses that until the next boot. If you want to use a specific slot, then if the driver slot is set to 1 to 4, then that specific slot will be used.

For a card to work with this driver, it needs to use Indirect Indexed addressing ( (zp),Y ) to copy the data to and from the buffer. This is required as SOS uses the A3 extended addressing for the buffer pointer in the driver call. Most cards firmware seem to use this.

## Compatibilty
This has been tested with the following cards

| Card | Comments |
| --- | --- |
| CFFA v1.3 | The CF Card needs to be formatted for Apple2 mode. This means 32mb partitions which are problematic with SOS. |
| CFFA v2 | Only tested with MAME emulation. The CF Card needs to be formatted for Apple2 mode. This means 32mb partitions which are problematic with SOS. . In MAME, if you use the option to mount a 16mb .2mg or .po image directly, then it works well. eg mame64 apple3 -flop1 bosboot_pb3.dsk -sl1 cffa2 -hard bos.po |
| Booti | Tested with the card set to block mode |
| CFFA3000 | Works ok booting Selector and sysutils, but will not work with BOS for some as yet undetermined reason |
| Focus | Only tested with the MAME emulation |

Note: the card setup using its own firmware to select harddisk images will need to be performed on an Apple2, or using the A2emulation on the Apple3. Once you have the images setup, then it should work ok in Apple3 mode.

## Build
The driver is built via the ca65 assembler and a3driverutil.

## Release
Disk images with the driver setup for slot autoscan included in the release section for the following:
  Apple/// System Utilities
  Selector boot (and selector HD image)
  BOS Boot (and cffa2 HD image for BOS from apple3rtr)
