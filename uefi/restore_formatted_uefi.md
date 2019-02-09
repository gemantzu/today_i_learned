# Restore formatted UEFI (when you by mistake delete whole disk)
- Boot Windows Installation with USB or disk
- Select Language (US) and press Repair
- Troubleshoot
- Command Prompt

# Commands to restore boot

## Assign Letter to Disk
- diskpart
- list disk
- select disk x (Windows)
- list vol
- Note down windows regular partition and boot partition (must be fat32)
- select vol x (boot)
- assign letter=I (some non registered letter)
- exit

## Rebuild boot
- (Optional - If not fat32) format I: /FS=FAT32
- bcdboot D:\Windows /s I: /f UEFI
- exit
