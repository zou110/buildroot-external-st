# STM32MP1 Buildroot external tree

This repository is a Buildroot `BR2_EXTERNAL` tree dedicated to
supporting the [STMicroelectronics](https://www.st.com)
[STM32MP1](https://www.st.com/en/microcontrollers-microprocessors/stm32mp1-series.html)
platforms. Using this project is not strictly necessary as Buildroot
itself has support for STM32MP1, but this `BR2_EXTERNAL` tree provide
example configurations demonstrating how to use the different features
of the STM32MP1 platforms.

## Available configurations

This `BR2_EXTERNAL` tree provides four example Buildroot
configurations:

1. `st_stm32mp157a_dk1_defconfig`, which is a minimal configuration to
   support the [STM32MP1 Discovery Kit
   1](https://www.st.com/en/evaluation-tools/stm32mp157d-dk1.html)
   board. It builds the TF-A firmware, U-Boot bootloader, Linux kernel
   and a minimal user-space composed of just Busybox.

2. `st_stm32mp157c_dk2_defconfig`, which is a minimal configuration to
   support the [STM32MP1 Discovery Kit
   2](https://www.st.com/en/evaluation-tools/stm32mp157f-dk2.html)
   board. It builds the TF-A firmware, U-Boot bootloader, Linux kernel
   and a minimal user-space composed of just Busybox.

3. `st_stm32mp157a_dk1_demo_defconfig`, which is a more feature-ful
   configuration to support the [STM32MP1 Discovery Kit
   1](https://www.st.com/en/evaluation-tools/stm32mp157d-dk1.html)
   board. It has support for OpenGL and Qt5 for graphics, for OP-TEE
   as a Trusted Execution Environment. It also shows how Device Tree
   files generated by CubeMX can be integrated in a Buildroot
   configuration.

4. `st_stm32mp157c_dk2_demo_defconfig`, which is a more feature-ful
   configuration to support the [STM32MP1 Discovery Kit
   2](https://www.st.com/en/evaluation-tools/stm32mp157f-dk2.html)
   board. It has support for OpenGL and Qt5 for graphics, for OP-TEE
   as a Trusted Execution Environment. It also shows how Device Tree
   files generated by CubeMX can be integrated in a Buildroot
   configuration.

| Feature | st_stm32mp157a_dk1 | st_stm32mp157c_dk2 | st_stm32mp157a_dk1_demo | st_stm32mp157c_dk2_demo |
| ------- | ------------------ | ------------------ | ----------------------- | ----------------------- |
| TF-A    | v2.4-stm32mp-r1 | v2.4-stm32mp-r1 | v2.4-stm32mp-r1 | v2.4-stm32mp-r1 |
| U-Boot  | v2020.10-stm32mp-r1.1 | v2020.10-stm32mp-r1.1 | v2020.10-stm32mp-r1.1 | v2020.10-stm32mp-r1.1 |
| Linux   | v5.10-stm32mp-r1 | v5.10-stm32mp-r1 | v5.10-stm32mp-r1 | v5.10-stm32mp-r1 |
| OP-TEE  | N/A | N/A | 3.12.0-stm32mp-r1 | 3.12.0-stm32mp-r1 |
| Qt5     | N/A | N/A | Yes | Yes |
| OpenGL  | N/A | N/A | Yes | Yes |
| WiFi    | N/A | N/A | Yes | Yes |
| Bluetooth | N/A | N/A | Yes | Yes |
| Audio    | N/A | N/A | Yes | Yes |
| CubeMX Device Tree | No | No | Yes | Yes |
| Cortex M4 Firmware examples | No | No | No | Yes |

Note that upstream Buildroot also contains pre-defined configurations
for STM32MP1 platforms, but they use the upstream versions of TF-A,
U-Boot and Linux, while the configurations in this `BR2_EXTERNAL` tree
use the versions provided and supported by ST.

## Getting started

### Getting the code

This `BR2_EXTERNAL` tree is designed to work with the latest LTS
version of Buildroot, `2021.02.x`. However, we needed a few changes on
top of upstream Buildroot, so you need to use our own Buildroot fork
together with this `BR2_EXTERNAL` tree, and more precisely its
`st/2021.02` branch.

```bash
$ git clone -b st/2021.02 https://github.com/bootlin/buildroot.git
```

See our documentation on [internal details](docs/internals.md) for more
information about the changes we have compared to upstream Buildroot.

Now, clone the matching branch of the `BR2_EXTENAL` tree:

```bash
$ git clone -b st/2021.02 https://github.com/bootlin/buildroot-external-st.git
```

You now have side-by-side a `buildroot` directory and a
`buildroot-external-st` directory.

### Configure and build

Go to the Buildroot directory:

```bash
$ cd buildroot/
```

And then, configure the system you want to build by using one of the 4
*defconfigs* provided in this `BR2_EXTERNAL` tree. For example:

```bash
buildroot/ $ make BR2_EXTERNAL=../buildroot-external-st st_stm32mp157c_dk2_defconfig
```

We are passing two informations to `make`:

1. The path to `BR2_EXTERNAL` tree, which we have cloned side-by-side
to the Buildroot repository

2. The name of the Buildroot configuration we want to build.

If you want to further customize the Buildroot configuration, you can
now run `make menuconfig`, but for your first build, we recommend you
to keep the configuration unchanged so that you can verify that
everything is working for you.

Start the build:

```bash
buildroot/ $ make
```

This will automaticaly download and build the entire Linux system for
your STM32MP1 platform: cross-compilation toolchain, firmware,
bootloader, Linux kernel, root filesystem. It might take between 30
and 60 minutes depending on the configuration you have chosen and how
powerful your machine is.

## Flashing and booting the system

The Buildroot configurations generate a ready-to-use SD card image,
available at `output/images/sdcard.img`. Flash this image on a SD
card:

```bash
buildroot/ $ dd if=output/images/sdcard.img of=/dev/sdX bs=1M
```

(Note: this assumes your SD card appears as `/dev/sdX` on your system.)

Then:

1. Insert the microSD card in connector CN15

2. Plug a micro-USB cable in connector CN11 and run your serial
communication program on /dev/ttyACM0.

3. Configure the SW1 switch to boot on SD card

4. Plug a USB-C cable in CN6 to power-up the board.

5. The system will start, with the console on UART. You can log-in as
`root` with no password (a password can of course be added by changing
the Buildroot configuration)

# Going further

* [Using the STM32 Cube Programmer](docs/stm32cubeprogrammer.md)
* [Using Device Tree files generated by STM32 CubeMX](docs/stm32cubemx.md)
* [Using the Firmware examples for the CM4](docs/cm4.md), only for the *Discovery Kit 2*
* [Testing display support](docs/display.md)
* [Using WiFi](docs/wifi.md), only for the *Discovery Kit 2*
* [Using Bluetooth](docs/bluetooth.md), only for the *Discovery Kit 2*
* [Using Audio](docs/audio.md)
* [Using Qt5 demos](docs/qt5.md)
* [Using OP-TEE](docs/optee.md)
* [Internal details](docs/internals.md) on this `BR2_EXTERNAL` tree

# References

* [Buildroot](https://buildroot.org/)
* [Buildroot reference manual](https://buildroot.org/downloads/manual/manual.html)
* [Buildroot system development training
  course](https://bootlin.com/training/buildroot/), with freely
  available training materials

# Support

You can contact Bootlin at info@bootlin.com for commercial support on
using Buildroot on STM32MP1 platforms.
