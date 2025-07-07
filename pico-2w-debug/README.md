# Pico 2W Setup with Debugging Enabled
For official documentation, see [Getting started with Raspberry Pi Pico-series Microcontrollers](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf).

## Requirements
* Raspberry Pi Pico 2 W
* Ubuntu machine
* USB cable

## Step 1. Install Required Tools
On the Ubuntu machine
```
sudo apt update
sudo apt install -y git cmake build-essential gcc-arm-none-eabi libnewlib-arm-none-eabi \
    openocd
```

## Step 2. Get the Pico SDK and Examples
Note that an environment variable ```PICO_SDK_PATH``` is set for **only the current shell session**. This environment variable is used in later steps!
```
mkdir -p ~/pico
cd ~/pico
git clone -b master https://github.com/raspberrypi/pico-sdk.git
cd pico-sdk
git submodule update --init
export PICO_SDK_PATH=$(pwd)
```

## Step 3. Create and Build a Project
1. Create a project folder, an example to blink the LED is used here.
```
cd ~/pico
mkdir blink
cd blink
```
2. Create a simple ```CMakeLists.txt file``` with the following content:
```
cmake_minimum_required(VERSION 3.13)

include(${PICO_SDK_PATH}/external/pico_sdk_import.cmake)

project(blink_project)

pico_sdk_init()

add_executable(blink
    blink.c
)

target_link_libraries(blink pico_stdlib)

if (PICO_CYW43_SUPPORTED)
    target_link_libraries(blink pico_cyw43_arch_none)
endif()

pico_add_extra_outputs(blink)
```
3. Create an empty source code ```blink.c``` file under the project directory (*~/pico/blink* in this example). Then copy the code from [this file](blink.c) into it. Note that the source code used here is simply copied from the official [example](https://github.com/raspberrypi/pico-examples/blob/master/blink/blink.c) repo.

4. Build the project. Again, do this under the project directory (*~/pico/blink* in this example).
```
mkdir build
cd build 
cmake .. \
  -DPICO_BOARD=pico2_w \
  -DPICO_PLATFORM=rp2350 \
  -DPICO_SDK_PATH=$PICO_SDK_PATH \
  -DCMAKE_C_COMPILER=/usr/bin/arm-none-eabi-gcc \
  -DCMAKE_CXX_COMPILER=/usr/bin/arm-none-eabi-g++ \
  -DCMAKE_ASM_COMPILER=/usr/bin/arm-none-eabi-gcc \
  -DDEBUG_ON_PICO=ON
 
make -j$(nproc)
```
This should produce these files under the ```build``` folder:
```
blink.elf
blink.uf2
```

## Step 4. Flash Firmware
1. Put Pico 2 W into BOOTSEL mode:
	- Hold down **BOOTSEL** button
	- Connect the Pico 2 W with the Ubuntu machine using the USB cable
	- A drive called ```RP2350``` should appear at ```/media/$USER/RP2350```
2. Copy the firmware (the ```.uf2``` file from the previous step) into the 
```RP2350``` folder, the drive should automatically unmount, and Pico 2 W should 
reboot.

