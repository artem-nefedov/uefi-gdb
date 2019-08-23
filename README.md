# UEFI OVMF symbol load script for GDB

GDB extension to automate loading of debug symbols for OVMF image.
Also works for custom drivers and applications, as long as symlinks to .efi and
.debug files are present in working directory.

Sourcing script from gdb adds new command "efi".
Usage can be displayed by printing help page:
```
(gdb) help efi
```

Requires OVMF image to built with [EDK II](https://github.com/tianocore/edk2)

Algorithm is based on the instruction:

[How to debug OVMF with QEMU using GDB](https://github.com/tianocore/tianocore.github.io/wiki/How-to-debug-OVMF-with-QEMU-using-GDB)

Steps:

1. Enter edk2 git directory.
2. Run qemu with debug console being redirected to file "debug.log":
```
$ qemu-system-i386 -s -bios Build/OvmfIa32/DEBUG_GCC5/FV/OVMF.fd -net none -debugcon file:debug.log -global isa-debugcon.iobase=0x402
```
3. (optional) If debugging a custom app/driver, run or load it in QEMU, so the load address can be found in debug.log.
4. Run gdb and source "efi.py":
```
$ gdb -ex 'source efi.py'
```
5. Execute "efi" or "efi -r":
```
(gdb) efi
...
add symbol table from file "Build/OvmfIa32/DEBUG_GCC5/IA32/Ps2KeyboardDxe.debug" at
        .text_addr = 0x6c37220
        .data_addr = 0x6c3e480
add symbol table from file "Build/OvmfIa32/DEBUG_GCC5/IA32/VirtioPciDeviceDxe.debug" at
        .text_addr = 0x7ca0220
        .data_addr = 0x7ca4160
Restoring pagination
(gdb)
```
