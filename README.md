[![Build Status](https://travis-ci.com/antmicro/litex-vexriscv-tensorflow-lite-demo.svg?branch=master)](https://travis-ci.com/antmicro/litex-vexriscv-tensorflow-lite-demo)

# Tensorflow lite demo running in Zephyr on Litex/VexRiscv SoC

This repository collects all the repositories required to build and run [Zephyr](https://www.zephyrproject.org/) [TensorFlow Lite Micro](https://www.tensorflow.org/lite/microcontrollers) demos on either:

* a [Digilent Arty A7](https://reference.digilentinc.com/reference/programmable-logic/arty-a7/start) board
* the open source [Renode simulation framework](http://renode.io/) (no hardware required)

The hardware version of the `magic wand` demo also requires a [PmodACL](https://store.digilentinc.com/pmod-acl-3-axis-accelerometer/) to be connected to the `JD` port of the Arty A7 board.

## Prerequisites

Clone the repository and submodules:
```bash
git clone https://github.com/fintros/swervolf-nexys-a7-tensorflow-lite-demo.git
cd swervolf-nexys-a7-tensorflow-lite-demo
DEMO_HOME=`pwd`
SWERVOLF_ROOT=`pwd`/swervolf
git submodule update --init --recursive
cd 
```

Install prerequisites (tested on Ubuntu 18.04):
```bash
sudo apt update
sudo apt install cmake ninja-build gperf ccache dfu-util device-tree-compiler wget python python3-pip python3-setuptools python3-tk python3-wheel xz-utils file make gcc gcc-multilib locales tar curl unzip xxd
```

Install Zephyr prerequisites:
```bash
# update cmake to required version
sudo pip3 install cmake virtualenv
# install Zephyr SDK
wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.11.2/zephyr-sdk-0.11.2-setup.run
chmod +x zephyr-sdk-0.11.2-setup.run
sudo ./zephyr-sdk-0.11.2-setup.run -- -d /opt/zephyr-sdk
```

Export Zephyr configuration:
```bash
export ZEPHYR_TOOLCHAIN_VARIANT=zephyr
export ZEPHYR_SDK_INSTALL_DIR=/opt/zephyr-sdk
```

## Building the demos

### Cleaning project

```bash
export swervolf-nexys-a7-tensorflow-lite-dem
make -f tensorflow/lite/micro/tools/make/Makefile TARGET=zephyr_swervolf BUILD_T
```

### Hello World demo

Build the `Hello World` demo with:
```bash
cd $DEMO_HOME
export SWERVOLF_ROOT=swervolf
cd $DEMO_HOME/tensorflow
make -f tensorflow/lite/micro/tools/make/Makefile TARGET=zephyr_swervolf BUILD_TYPE=debug hello_world_bin $*
```
The resulting binaries can be found in the `tensorflow/lite/micro/tools/make/gen/zephyr_vexriscv_x86_64/hello_world/build/zephyr` folder.

To run debug:
````bash
export PATH=$PATH:/home/tensorflow/alsp/riscv-fw-infrastructure/WD-Firmware/demo/build/toolchain/gcc/bin
riscv64-unknown-elf-gdb /home/tensorflow/alsp/swervolf-nexys-a7-tensorflow-lite-demo/tensorflow/tensorflow/lite/micro/tools/make/gen/zephyr_swervolf_x86_64/hello_world/build/
````

### Magic Wand demo

Build the `Magic Wand` demo with:
```bash
cd $DEMO_HOME
export SWERVOLF_ROOT=swervolf
cd $DEMO_HOME/tensorflow
make -f tensorflow/lite/micro/tools/make/Makefile TARGET=zephyr_swervolf BUILD_TYPE=debug magic_wand_bin $*
```
The resulting binaries can be found in the `tensorflow/lite/micro/tools/make/gen/zephyr_vexriscv_x86_64/magic_wand/build/zephyr` folder.

To run debug:
````bash
export PATH=$PATH:/home/tensorflow/alsp/riscv-fw-infrastructure/WD-Firmware/demo/build/toolchain/gcc/bin
riscv64-unknown-elf-gdb /home/tensorflow/alsp/swervolf-nexys-a7-tensorflow-lite-demo/tensorflow/tensorflow/lite/micro/tools/make/gen/zephyr_swervolf_x86_64/magic_wand/build/z
````

## Building the gateware

Download and unpack to $DEMO_HOME: https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.1.0-2019.01.0-x86_64-linux-ubuntu14.tar.gz
Install Vivado (example is for Vivado 2019.2)
````bash
export WORKSPACE=$(pwd)
export SWERVOLF_ROOT=$WORKSPACE/swervolf
source $VIVADO_PATH/Vivado/2019.2/.settings64-Vivado.sh
export PATH=$PATH:$PWD/riscv64-unknown-elf-gcc-8.1.0-2019.01.0-x86_64-linux-ubuntu14/bin/
````