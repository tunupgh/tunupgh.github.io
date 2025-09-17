---
layout: post
title: 'Raspberry Pi Zero: Blink an LED using GPIO pins'
date: 2016-09-14 12:19
comments: true
categories:
- raspberry pi
- electronics
- gpio
---
Want to use the GPIO pins to blink an LED using a Raspberry Pi Zero? Here's a quick guide to get you started.

## Build the circuit

Do this is the Raspberry Pi off. You don't want to accidentally touch the wrong pin on the Pi and fry the Pi. 

Use http://pinout.xyz to check the pinouts of the Raspberry Pi.

Create a circuit that goes from GND to LED to resistor to GPIO pin (BCM).

<img src="https://www.raspberrypi.org/wp-content/uploads/2015/11/led-gpio17.png" width="500">

## Install the Python Library

We'll be using python to build our program so first download the python library. There is an api to interface with the GPIO pins which makes this a lot easier.

```
sudo apt-get install python3-gpiozero python-gpiozero
```

https://gpiozero.readthedocs.io/ for more information on this library.

## Create the Program


Now create a file called `blink.py` and put the following in it:

```python
from gpiozero import LED
from time import sleep

led = LED(17) # Choose the correct pin number

while True:
    led.on()
    sleep(1)
    led.off()
    sleep(1)
```

## Run the Program

Run the program with the following command:

```
python blink.py
```

If all went well you should now see your LED blinking!


## References

https://gpiozero.readthedocs.io/

https://www.raspberrypi.org/blog/gpio-zero-a-friendly-python-api-for-physical-computing/

http://pinout.xyz

https://hackr.io/tutorials/python