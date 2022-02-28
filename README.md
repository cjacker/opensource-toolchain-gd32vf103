# Opensource toolchain for gd32vf103

**GD32VF103 SoC** is the first general RISC-V MCU from GigaDevice Semiconductor which is based on Nuclei RISC-V Process Core.

If you want to learn more about it, please click https://www.gigadevice.com/products/microcontrollers/gd32/risc-v/

The GD32VF103 device is a 32-bit general-purpose micro controller based on the RISC-V core with best ratio in terms of processing power, reduced power consumption and peripheral set.

The RISC-V processor core is tightly coupled with an Enhancement Core-Local Interrupt Controller(ECLIC), SysTick timer and advanced debug support.

The GD32VF103 device incorporates the RISC-V 32-bit processor core operating at 108MHz frequency with Flash accesses zero wait states to obtain maximum efficiency.

It provides up to 128KB on-chip Flash memory and 32KB SRAM memory.

An extensive range of enhanced I/Os and peripherals connect to two APB buses.

The devices offer up to two 12-bit ADCs, up to two 12-bit DACs, up to four general 16-bit timers, two basic timers plus a PWM advanced timer, as well as standard and advanced communication interfaces: up to three SPIs, two I2Cs, three USARTs, two UARTs, two I2Ss, two CANs, an USBFS.

The SoC diagram can be checked as below GD32VF103 SoC Diagram:

<p align="center">
<img src="https://user-images.githubusercontent.com/1625340/155824560-96b510b6-1613-4c9f-9c5b-7228ff2a6d80.png" width="70%"/>
</p>


**Longan Nano** is a GD32VF103CBT6 based minimal development board based on GigaDevice's latest RISC-V 32-bit core microcontroller. Convenient for students, engineers, geeks and enthusiasts to access the latest generation of RISC-V processors.

In the heart of Longan Nano is a GigaDevice's GD32VF103CBT6, based on Nucleisys Bumblebee kernel (support RV32IMAC instruction sets and ECLIC rapid interrupt). You can download instruction set documents here: http://dl.sipeed.com/LONGAN/Nano/DOC/.

The chip has built-in 128KB Flash, 32KB SRAM, and peripherals listes below:

    4 x universal 16-bit timer
    2 x basic 16-bit timer
    1 x advanced 16-bit timer
    Watchdog timer
    RTC
    Systick
    3 x USART
    2 x I2C
    3 x SPI
    2 x I2S
    2 x CAN
    1 x USBFS(OTG)
    2 x ADC(10 channel)
    2 x DAC

Longan Nano development board is breadboard friendly. It has onboard 8M passive crystal, 32.768KHz RTC low-speed crystal, mini TF card slot, and use the latest Type-C USB interface.

<p align="center">
<img src="https://user-images.githubusercontent.com/1625340/155824632-bb1fb2d2-301c-434b-b41c-b466d4aee71d.png" width="70%"/>
</p>

# Hardware requirements
* a development board with GD32VF103, such as Longan Nano
* a USB/JTAG adapter

# RISC-V GNU Toolchain for gd32vf103
gd32vf103 soc and longan nano board is well supported by [riscv-gnu-toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain) and Rust crate [gd32vf103xx-hal](https://crates.io/crates/gd32vf103xx-hal) and [longan-nano](https://crates.io/crates/longan-nano). for Rust toolchain, please refer to [riscv-rust/longan-nano](https://github.com/riscv-rust/longan-nano).

the RISC-V GNU toolchain, which contains compilers and linkers like gcc and g++ as well as helper programs like objcopy and size is available from [riscv-gnu-toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain). There are also some prebuilt release provided by nuclei or other teams, such as 'xpack', so you can either build it yourself or download a prebuilt release.

## Building from source
If you want to use a prebuilt release, just ignore this section.

Building a cross compile gnu toolchain was difficult long time ago, you need to understand and use configuration options carefully. [riscv-gnu-toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain) provided a simpler way to help us building a workable toolchain. It supports two build modes: a generic ELF/Newlib toolchain and a more sophisticated Linux-ELF/glibc toolchain. we only need the 'generic ELF/Newlib toolchain' for gd32vf103.

```
git clone https://github.com/riscv-collab/riscv-gnu-toolchain.git
cd riscv-gnu-toolchain
mkdir build
cd build
../configure --prefix=/opt/riscv-gnu-toolchain --disable-linux --with-abi=ilp32 --with-arch=rv32imac
make -j<nprocs>
make install
```

It will be installed to "/opt/riscv-gnu-toolchain" dir, and the [target triplet](https://wiki.osdev.org/Target_Triplet) of gcc should be 'riscv32-unknown-elf-'.

Since the prefix is not set to standard dir, you need add '/opt/riscv-gnu-toolchain/bin' to PATH env. for example, for bash, add it to ~/.bashrc:
```
export PATH=/opt/riscv-gnu-toolchain/bin:$PATH
```

## Use prebuilt toolchain
There are a lot of prebuilt riscv toolchains you can download and use directly if it support the arch 'rv32imac'. Here are two choices with well support.

*   Nuclei official toolchain

Nucleisys provide prebuilt toolchain, you can download it from https://nucleisys.com/download.php, up to this tutorial written, the lastest version is "nuclei_riscv_newlibc_prebuilt_linux64_2022.01.tar.bz2". 

after download finished, extract it to somewhere and modify the PATH env, for example:

```
sudo mkdir -p /opt/nuclei-riscv-toolchain
sudo tar xf nuclei_riscv_newlibc_prebuilt_linux64_2022.01.tar.bz2 -C /opt/nuclei-riscv-toolchain --strip-components=1
```

And add `/opt/nuclei-riscv-toolchain/bin` to PATH env according to your shell.

**NOTE**, the target triplet of nuclei riscv toolchain is **`riscv-nuclei-elf`**.

*   Xpack riscv toolchain

[xpack-dev-tools](https://github.com/xpack-dev-tools/riscv-none-embed-gcc-xpack) provde a prebuilt toolchain for riscv. you can download it from https://github.com/xpack-dev-tools/riscv-none-embed-gcc-xpack/releases/tag/v10.2.0-1.2. up to this tutorial written, the lastest version is '10.2.0', after download finished, extract it and add path to PATH env, for example:

```
sudo mkdir -p /opt/xpack-riscv-toolchain
sudo tar xf xpack-riscv-none-embed-gcc-10.2.0-1.2-linux-x64.tar.gz -C /opt/xpack-riscv-toolchain --strip-components=1
```

and add `/opt/xpack-riscv-toolchain/bin` to PATH env according to your shell.

**NOTE**, the target triplet of xpack riscv toolchain is **`riscv-none-embed`**.

# SDK
There are several SDKs you can use with gd32vf103/longan nano.

## Minimum baremetal SDK
[GD32VF103_templates](https://github.com/WRansohoff/GD32VF103_templates) provide minimum baremetal SDK and some project templates for demo.

```
git clone https://github.com/WRansohoff/GD32VF103_templates.git
```
Note, you need modify 'Makefile' of every example to match your toolchain triplet, the default triplet of GD32VF103_templates is set to:

```
CC = riscv32-unknown-elf-gcc
OC = riscv32-unknown-elf-objcopy
OS = riscv32-unknown-elf-size
```

If you use xpack riscv toolchain, it should be changed to:
```
CC = riscv-none-embed-gcc
OC = riscv-none-embed-objcopy
OS = riscv-none-embed-size
```

If you use nuclei official toolchain, it should be changed to:
```
CC = riscv-nuclei-elf-gcc
OC = riscv-nuclei-elf-objcopy
OS = riscv-nuclei-elf-size
```

then try build a project:
```
cd hello_led
make
```

'main.elf' and 'main.bin' will be generated. but you can do nothing with them up to now, since it need to 'transfer' to target development board.


## Nuclei official SDK
[Nuclei RISC-V Software Development Kit](https://github.com/Nuclei-Software/nuclei-sdk) is provided by nucleisys.

```
git clone https://github.com/Nuclei-Software/nuclei-sdk.git
cd application/baremetal
make SOC=gd32vf103 BOARD=gd32vf103c_longan_nano COMPILE_PREFIX=riscv-none-embed-
```

'COMPILE_PREFIX' should set to your toolchain's triplet, above example is for xpack risv toolchain, you can also modify the 'Build/Makefile.conf'.

Change it from:

```
COMPILE_PREFIX ?= riscv-nuclei-elf-
```
to
```
COMPILE_PREFIX ?= riscv-none-embed-
```

by default, it will generate the ELF file, run below command to generate bin file:

```
make SOC=gd32vf103 BOARD=gd32vf103c_longan_nano COMPILE_PREFIX=riscv-none-embed- bin
```
or 
```
<your riscv triplet>-objcopy helloworld.elf -O binary helloworld.bin

```


# Programming(Flashing) and Debugging
After toolchain installed and demo examples built, you need to 'transfer' the result binary to development board. there are 4 way to do this job.

## OpenOCD for Programming and Debugging
The Open On-Chip Debugger (OpenOCD) aims to provide debugging, in-system programming and boundary-scan testing for embedded target devices. Generally, you can think OpenOCD as a bridge to connect host to target board via SWD/JTAG adapter, to provide a channel for devices programming and remote debugging.

Upstream OpenOCD already support RISC-V, but lack of [GD32VF103 flash driver](https://review.openocd.org/c/openocd/+/6763) support. there is also some patches to [stm32f1x flash driver](https://review.openocd.org/c/openocd/+/6704) had been done and submitted for review.

You can choose either [official OpenOCD](https://github.com/openocd-org/openocd) with a patch or [riscv-openocd](https://github.com/riscv/riscv-openocd) fork with gd32vf103 flash driver.

Before building OpenOCD, you may need some development packages of libusb/libftdi/hidapi installed.

for Official OpenOCD:
```
git clone https://github.com/openocd-org/openocd.git
cd openocd
git submodule update --init --recursive --progress
wget https://raw.githubusercontent.com/cjacker/opensource-toolchain-gd32vf103/main/add-support-for-RISC-V-GigaDevice-GD32VF103.patch
cat add-support-for-RISC-V-GigaDevice-GD32VF103.patch|patch -p1 -d .
./configure --prefix=/opt/openocd --program-prefix=riscv-
make && make install
```

for RISC-V OpenOCD fork:
```
git clone https://github.com/riscv/riscv-openocd.git
cd riscv-openocd
git submodule update --init --recursive --progress
./configure --prefix=/opt/openocd --program-prefix=riscv-
make && make install
```

`riscv-openocd` command will be installed in `/opt/openocd/bin`, please add '/opt/openocd/bin' to PATH env according to your shell.

If you used openocd before, it's very easy to understand OpenOCD need a interface config file for USB/JTAG adapter and a target config file for target developement board. you should choose a interface config file according to USB/JTAG adapter you used, and use the ['target board config file'](https://raw.githubusercontent.com/cjacker/opensource-toolchain-gd32vf103/main/gd32vf103.cfg) provied within this repo or download it from [here](https://gist.githubusercontent.com/elfmimi/1deb9c94b0f0900ae8a9df740b62bcd6/raw/f89019b9dc3cec778aaf073a11523d6030c7137c/gd32vf103.cfg), it's the most complete configration for gd32vf103 target board and support OpenOCD 'reset run', any other config files, even the one provided by Nuclei SDK can not handle 'reset run' properly.

Before continue reading, please wire up the USB/JTAG adapter (Use 3.3v VCC pin) with Longan Nano board. Here I use [tigard with FT2232](https://github.com/tigard-tools/tigard) as USB/JTAG adapter.

* **Test OpenOCD connection**
```
riscv-openocd -f tigard-jag.cfg -f gd32vf103.cfg
```
the output looks like:
```
Info : starting gdb server for riscv.cpu on 3333
Info : Listening on port 3333 for gdb connections
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
```
* **Debugging**

Here use hello_riscv from Minimum baremetal SDK as example.

after launch OpenOCD with `riscv-openocd -f tigard-jag.cfg -f gd32vf103.cfg`, run `<your triplet>-gdb -q main.elf`:

```
(gdb) target extended-remote :3333
Remote debugging using :3333
0x080001a2 in main () at src/main.c:35
35	  memcpy( &_sdata, &_sidata, ( ( void* )&_edata - ( void* )&_sdata ) );
(gdb) load
Loading section .vector_table, size 0x15c lma 0x8000000
Loading section .text, size 0x298 lma 0x800015c
Start address 0x800015e, load size 1012
Transfer rate: 975 bytes/sec, 506 bytes/write.
(gdb) backtrace
#0  reset_handler () at src/main.c:13
(gdb) break main
Breakpoint 1 at 0x8000194: file src/main.c, line 35.
(gdb) continue
Continuing.
Note: automatically using hardware breakpoints for read-only addresses.

Breakpoint 1, main () at src/main.c:35
35	  memcpy( &_sdata, &_sidata, ( ( void* )&_edata - ( void* )&_sdata ) );
(gdb) print counter
$1 = 536903680
(gdb) next
37	  memset( &_sbss, 0x00, ( ( void* )&_ebss - ( void* )&_sbss ) );
(gdb) next
40	  volatile int counter = 0;
(gdb) print counter
$2 = 0
(gdb) next
42	    counter++;
(gdb) continue
Continuing.
^C
Program received signal SIGINT, Interrupt.
0x080001f4 in main () at src/main.c:42
42	    counter++;
(gdb) print counter
$3 = 3389557
(gdb)
```

* **Programming/Flashing**
```
# program elf file
riscv-openocd -f tigard-jtag.cfg -f gd32vf103.cfg -c "program main.elf verify reset exit"
# program bin file
riscv-openocd -f tigard-jtag.cfg -f gd32vf103.cfg -c "program main.bin 0x08000000 verify reset exit"
# reset and run
riscv-openocd -f tigard-jtag.cfg -f gd32vf103.cfg -c "init; reset run; exit"
```

you can combine various OpenOCD commands together, for example, programming and run.
```
riscv-openocd -f tigard-jtag.cfg -f gd32vf103.cfg -c "program main.bin 0x08000000 verify reset exit" -c "init; reset run; exit"
```

## dfu-util for programming (no debugging support)
The GD32VF103 contains a DFU compatible bootloader which allows to program the firmware of your longan-nano just using an ordinary USB-C cable without additional hardware like a JTAG adapter. You can use 'dfu-util' to flash the firmware.

**Keep the BOOT0 button pressed while power-up or while pressing and releaseing the reset button will enter DFU mode**

Device detection:
```
sudo dfu-util -l
```

Programming:
```
sudo dfu-util -a 0 -s 0x08000000:leave -D main.bin
```

## stm32flash for Flashing
With a USB/UART adapter, the board can be programmed by 'stm32flash' over serial connection, for example:

```
sudo stm32flash -g 0x08000000 -b 115200 -w main.bin /dev/ttyUSB0
```


## RV LINK for Flashing and Debugging

RV-LINK is a Chinese firmware similar to Black Magic Probe (BMP). It need another piece Longan Nano as a debugger adapter. please refer to https://gitee.com/zoomdy/RV-LINK for more information.





