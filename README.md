# Illinois Tech Robotics - Robot Interface Board

This is a work-in-progress PCB design based around the existing goliath protoboards, but adapted to work with a Raspberry Pi Pico (W), but is also usable with other designs using solder bridge jumpers to support various configurations. 

The majority of the circuitry allows connecting 2 OSMC's (Open Source Motor Controller) v3.22 over RJ45. They are wired with pin 2 through 9, as CAT6 only has 8 conductors and pin 1 and 10 are the same as 2 and 9 respectively, in this case we don't need the increased current offered by the extra pins.

The motor encoders are also connected, intended to be used with the Pico's PIO.

There is a MCP3008 ADC which is used for battery voltage & current monitoring.

The 10 connections for the OSMCs are just PWM or digital outputs using MCT9001 Optocouplers, by default set up for 12V (Or whatever is supplied to VIN), but with a jumper it can be set to 5V or 3.3V logic.
