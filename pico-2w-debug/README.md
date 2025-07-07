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

include(pico_sdk_import.cmake)

project(blink_project)

pico_sdk_init()

add_executable(blink
    blink.c
)

target_link_libraries(blink pico_stdlib)

pico_enable_stdio_usb(blink 1)
pico_enable_stdio_uart(blink 0)

pico_add_extra_outputs(blink)
```
3. Create a simple source code ```blink.c``` file with the content below under the project directory (*~/pico/blink* in this example).
```
#include "pico/stdlib.h"

int main() {
    const uint LED_PIN = PICO_DEFAULT_LED_PIN;
    gpio_init(LED_PIN);
    gpio_set_dir(LED_PIN, GPIO_OUT);

    while (true) {
        gpio_put(LED_PIN, 1);
        sleep_ms(500);
        gpio_put(LED_PIN, 0);
        sleep_ms(500);
    }
}
```
4. Build the project. Again, do this under the project directory (*~/pico/blink* in this example).
```
mkdir build
cd build 
cmake ..
make
```
This should produce these files under the ```build``` folder:
```
blink.elf
blink.uf2
```

## Step 4. Flash Firmware
2. Put Pico 2 W into BOOTSEL mode:
	- Hold down **BOOTSEL** button
	- Connect the Pico 2 W with the Ubuntu machine using the USB cable
	- A drive called ```RP2350``` should appear at ```/media/$USER/RP2350```.
3. Copy the firmware (the ```.uf2``` file from the previous step) into the 
```RP2350``` folder, the drive should automatically unmount, and Pico 2 W should 
reboot.

