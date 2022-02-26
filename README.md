# Opensource toolchain for gd32vf103

## 1. Overview 
### 1.1 GD32VF103 RISC-V MCU

GD32VF103 SoC is the first general RISC-V MCU from GigaDevice Semiconductor which is based on Nuclei RISC-V Process Core.

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

### 1.2 Longan Nano development board

Longan Nano is a GD32VF103CBT6 based minimal development board based on GigaDevice's latest RISC-V 32-bit core microcontroller. Convenient for students, engineers, geeks and enthusiasts to access the latest generation of RISC-V processors.

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

**NOTE**, you also need a USB/JTAG debugging adapter for debugging.


# 2. RISC-V GNU Toolchain for gd32vf103
gd32vf103 soc and longan nano board is well supported by ![riscv-gnu-toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain) and Rust ![gd32vf103xx-hal](https://crates.io/crates/gd32vf103xx-hal) ![longan-nano](https://crates.io/crates/longan-nano). for Rust toolchain, refer to HERE.

the RISC-V GNU toolchain, which contains compilers and linkers like gcc and g++ as well as helper programs like objcopy and size is available from ![riscv-gnu-toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain). There are also some prebuilt release provided by nuclei or other teams, such as 'xpack', so you can choose building it yourself or download a prebuilt release.

## 2.1 Building from source
If you want to use a prebuilt release, just ignore this section.

Building a cross compile gnu toolchain was difficult long time ago, you need to understand and use configuration options carefully. ![riscv-gnu-toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain) provided a simpler way to help us building a workable toolchain. It supports two build modes: a generic ELF/Newlib toolchain and a more sophisticated Linux-ELF/glibc toolchain. here, we only need the 'generic ELF/Newlib toolchain' for gd32vf103.

```
git clone https://github.com/riscv-collab/riscv-gnu-toolchain.git
cd riscv-gnu-toolchain
mkdir build
cd build
../configure --prefix=/opt/riscv-gnu-toolchain --disable-linux --with-abi=ilp32 --with-arch=rv32imac
make -j<nprocs>
make install
```

After installation, riscv toolchain will be installed to "/opt/riscv-gnu-toolchain" dir, and the ![target triplet](https://wiki.osdev.org/Target_Triplet) of gcc should be 'riscv32-unknown-elf-', that's to say, the riscv gcc command is 'riscv32-unknown-elf-gcc' and same for g++/gdb etc.

Since the installation is not to standard PATH dir, you need add '/opt/riscv-gnu-toolchain/bin' to PATH env according to the shell you use, then you can use these command everywhere. for example, for bash:
```
export PATH=/opt/riscv-gnu-toolchain/bin:$PATH
```
you can add it to `~/.bashrc`, launch terminal and try to run `riscv32-unknown-elf-gcc` in terminal to see it works or not.

### 2.2 Use prebuilt toolchain
There is a lot of prebuilt riscv toolchain you can download and use directly if they support the arch 'rv32imac'. Here is two choice with well supported.

*   Nuclei official toolchain

Nucleisys provide prebuilt toolchain, you can download it from https://nucleisys.com/download.php, up to this tutorial writing, the lastest version is "nuclei_riscv_newlibc_prebuilt_linux64_2022.01.tar.bz2". after download, just extract it to somewhere and modify the PATH env, for example:

```
sudo mkdir -p /opt/nuclei-riscv-toolchain
sudo tar xf nuclei_riscv_newlibc_prebuilt_linux64_2022.01.tar.bz2 -C /opt/nuclei-riscv-toolchain --strip-components=1
```

And add `/opt/nuclei-riscv-toolchain/bin` to PATH env according to your shell.

**NOTE**, the target triplet of nuclei riscv toolchain is `riscv-nuclei-elf`.

*   Xpack riscv toolchain

![xpack-dev-tools](https://github.com/xpack-dev-tools/riscv-none-embed-gcc-xpack) provde a prebuilt riscv toolchain for riscv. you can download it from https://github.com/xpack-dev-tools/riscv-none-embed-gcc-xpack/releases/tag/v10.2.0-1.2. up to this tutorial writing, the lastest version is '10.2.0', after download, same as nuclei toolchain, extract it and add path to PATH env, for example:

```
sudo mkdir -p /opt/xpack-riscv-toolchain
sudo tar xf xpack-riscv-none-embed-gcc-10.2.0-1.2-linux-x64.tar.gz -C /opt/xpack-riscv-toolchain --strip-components=1
```

and add `/opt/xpack-riscv-toolchain/bin` to PATH env according to your shell.

**NOTE**, the target triplet of xpack riscv toolchain is `riscv-none-embed`.

# 3. SDK
There are several SDK you can use with gd32vf103/longan nano, choose one as you like.

## 3.1 Minimum baremetal SDK
![GD32VF103_templates](https://github.com/WRansohoff/GD32VF103_templates) provide minimum baremetal SDK and some project templates for demo.

```
git clone https://github.com/WRansohoff/GD32VF103_templates.git
```
Note, you need modify 'Makefile' of evenry demo project to match your toolchain triplet, the default triplet used in GD32VF103_templates set to:

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

'main.elf' and 'main.bin' will be generated. but you can do nothing with them up to now, since it need to 'tranfer' to target board.


### 3.2 Nuclei official SDK
![Nuclei RISC-V Software Development Kit](https://github.com/Nuclei-Software/nuclei-sdk) is provided by nucleisys, the designer of nuclei RISC-V Process Core.

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

# 3. Flashing and Debugging
After toolchain installed and demo project built, you need to 'transfer' the result binary to development board. there are 4 way to do this job.

## 3.1 OpenOCD for Flashing and Debugging
The Open On-Chip Debugger (OpenOCD) aims to provide debugging, in-system programming and boundary-scan testing for embedded target devices. Generally, you can think OpenOCD as a bridge to connect host to target board via SWD/JTAG adapter, to provide a channel for devices programming and remote debugging.

Upstream OpenOCD already support RISC-V, but lack ![GD32VF103 flash driver](https://review.openocd.org/c/openocd/+/6763) support. there is also some patches to ![stm32f1x flash driver](https://review.openocd.org/c/openocd/+/6704) had been done and submitted for review.

You can choose either ![official OpenOCD](https://github.com/openocd-org/openocd) with a patch or ![riscv-openocd](https://github.com/riscv/riscv-openocd) fork with gd32vf103 flash driver.



## 3.2 dfu-util for Flashing
## 3.3 stm32flash for Flashing
## 3.4 RV LINK for Flashing and Debugging





