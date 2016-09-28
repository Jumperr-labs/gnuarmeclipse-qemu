# GNU ARM Eclipse QEMU

The [GNU ARM Eclipse QEMU](http://gnuarmeclipse.github.io/qemu) subproject is a fork of [QEMU](http://wiki.qemu.org/Main_Page) (an open source machine emulator), intended to provide support for Cortex-M emulation in GNU ARM Eclipse. The source code is part of the **GNU ARM Eclipse** project, and is available from [GitHub](https://github.com/gnuarmeclipse/qemu). Binary packages are available from [GitHub Releases](https://github.com/gnuarmeclipse/qemu/releases).

## How to use

* [Overview](http://gnuarmeclipse.github.io/qemu/) (read me first!)
* [QEMU Install](http://gnuarmeclipse.github.io/qemu/install)
* Eclipse plug-in
* [Support](https://github.com/gnuarmeclipse/qemu/issues/1) (using the GitHub Issues)

## How to build

Follow original "How to build" from QEMU with some **minor changes**
* [How to build](http://gnuarmeclipse.github.io/qemu/build-procedure) (using Docker containers)
* [Change log](http://gnuarmeclipse.github.io/qemu/change-log) ([2014](http://gnuarmeclipse.github.io/qemu/change-log/2014))

**minor changes:**

curl -L https://github.com/Jumperr-labs/build-scripts/raw/master/scripts/build-qemu.sh -o ~/Downloads/build-qemu.sh

## How to build with debug-symbols

After building:
```bash
#Remove all executables:

find ~/Work/qemu/ -name "qemu-system-gnuarmeclipse" | xargs -I{} rm "{}"

bash ~/Downloads/build-qemu.sh --all --no-strip
```

## How run GNU ARM Eclipse QEMU with debugging output ?

Key part is `-d unimp,guest_errors,cpu` and `-D /tmp/qemu.log`. `cpu` parameter
will show registers state before Translation Block, those information will be
placed in `/tmp/qemu.log`.

``` 
~/Work/qemu/install/debian64/qemu/bin/qemu-system-gnuarmeclipse --verbose \
--verbose --board STM32F4-Discovery -d unimp,guest_errors,cpu \
--semihosting-config enable=on,target=native --image STM32F4-Discovery.bin \
-D /tmp/qemu.log
```

## How to enable cross-debugging of executed binary

First install cross-debugger (best would be to use one from your toolchain):

```
sudo apt-get install gdb-arm-none-eabi
```

Then run:

```
~/Work/qemu/install/debian64/qemu/bin/qemu-system-gnuarmeclipse --verbose \
--verbose --board STM32F4-Discovery -d unimp,guest_errors,cpu \
--semihosting-config enable=on,target=native --image STM32F4-Discovery.bin \
-D /tmp/qemu.log -s -S
```

* `-s` expose gdb server on `localhost:1234`
* `-S` freez CPU until continue command will be run in gdb

To attach to device:

```
$ arm-none-eabi-gdb STM32F4-Discovery.elf
(...)
Reading symbols from STM32F4-Discovery.elf...done.
(gdb) target remote :1234
Remote debugging using :1234
Reset_Handler () at /home/parallels/Downloads/STM32Cube_FW_F4_V1.13.0/Projects/STM32F4-Discovery/Examples/GPIO/GPIO_EXTI/SW4STM32/startup_stm32f407xx.s:80
80      /home/parallels/Downloads/STM32Cube_FW_F4_V1.13.0/Projects/STM32F4-Discovery/Examples/GPIO/GPIO_EXTI/SW4STM32/startup_stm32f407xx.s: No such file or directory.
(gdb) break main
Breakpoint 1 at 0x800022c: file /home/parallels/Downloads/STM32Cube_FW_F4_V1.13.0/Projects/STM32F4-Discovery/Examples/GPIO/GPIO_EXTI/Src/main.c, line 67.
(gdb) c
Continuing.

Breakpoint 1, main () at /home/parallels/Downloads/STM32Cube_FW_F4_V1.13.0/Projects/STM32F4-Discovery/Examples/GPIO/GPIO_EXTI/Src/main.c:67
warning: Source file is more recent than executable.
67      {
(gdb)
```

In above case not all source files where connected and that's why we getting
`No such file or directory`. If you have source but not in path used in binary
you can do automatic substitution by:

```
(gdb) set substitute-path /path/in/binary /path/in/my/system
```

## Releases & binaries

See the [releases](http://gnuarmeclipse.github.io/qemu/releases) page.
Binaries for most platforms can be downloaded from [GitHub Releases](https://github.com/gnuarmeclipse/qemu/releases).
