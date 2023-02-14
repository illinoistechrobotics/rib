# Illinois Tech Robotics GOLIATH PCB

This is a work-in-progress PCB design based around the existing goliath protoboards, but adapted to work with a Raspberry Pi Pico, and eventually will be suitable for our other robots too. 

The majority of the circuitry allows connecting 2 OSMC's (Open Source Motor Controller) v3.22 over RJ45. They are wired with pin 2 through 9, as CAT6 only has 8 conductors and pin 1 and 10 are the same as 2 and 9 respectively, in this case we don't need the increased current offered by the extra pins.

The motor encoders are also connected, intended to be used with the Pico's PIO.

The 10 connections for the OSMCs are just PWM or digital outputs using MCT9001 Optocouplers, by default set up for 12V, but with a jumper it can be set to 5V logic.
