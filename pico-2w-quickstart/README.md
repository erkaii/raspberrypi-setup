# Pico 2W Setup Quickstart
For official documentation, see [Getting started with Raspberry Pi Pico-series Microcontrollers](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf).

## Requirements
* Raspberry Pi Pico 2 W
* Ubuntu machine
* USB cable

## Step 1. Flash MicroPython Firmware
1. Download the latest official MicroPython ```.uf2``` file for Pico 2 W at this 
[link](https://micropython.org/download/RPI_PICO2_W/)
2. Put Pico 2 W into BOOTSEL mode:
	- Hold down **BOOTSEL** button
	- Connect the Pico 2 W with the Ubuntu machine using the USB cable
	- A drive called ```RP2350``` should appear at ```/media/$USER/RP2350```
3. Copy the downloaded firmware (a ```.uf2``` file) into the ```RP2350``` folder,
the drive should automatically unmount, and Pico 2 W should reboot.

## Step 2. Open MicroPython REPL
1. Connect to REPL using minicom:
```
sudo apt install minicom
sudo minicom -b 115200 -o -D /dev/ttyACM0
```
2. Should see:
```
MicroPython v1.xx on YYYY-MM-DD; Raspberry Pi Pico2 W with RP2350
>>>
```

## Step 3. Blink the Onboard LED (Optional)
Paste the following into REPL and press enter:
```
import machine, utime

led = machine.Pin("LED", machine.Pin.OUT)

while True:
    led.toggle()
    utime.sleep(0.5)
```
To stop, press ctrl+c.

## Step 4. Store the LED Blink as ```main.py``` (Optional)
To make the LED always blink after boot, store the code as ```main.py```.
1. Paste the following into REPL:
```
code = """
import machine, utime

led = machine.Pin("LED", machine.Pin.OUT)

while True:
    led.toggle()
    utime.sleep(0.5)
"""
```
2. Paste the following and hit enter:
```
with open("main.py", "w") as f:
    f.write(code)
```

## Step 5. Exit REPL and Unplug Safely
1. To exit REPL, in minicom, press:
```
ctrl-A Q
Enter
```
2. Wait a second. Then unplug the Pico 2 W.
