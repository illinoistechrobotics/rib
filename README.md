# Illinois Tech Robotics - Robot Interface Board (RIB)

This is a PCB design based around the existing Goliath protoboards, but adapted to work with a Raspberry Pi Pico (W), but is also usable with other robot designs using solder bridge jumpers to support various configurations. 

# Features

## Output
The majority of the circuitry allows connecting 2 OSMC's (Open Source Motor Controller) v3.22 over RJ45. They are wired with pin 2 through 9, as CAT6 only has 8 conductors and pin 1 and 10 are the same as 2 and 9 respectively, in this case we don't need the increased current offered by the extra pins.

The 10 connections for the OSMCs are just PWM or digital outputs using MCT9001 Optocouplers, by default set up for 12V (Or whatever is supplied to VIN), but with a jumper it can be set to 5V or 3.3V logic to interface with a variety of motor controllers.

There is a MAX6959 36-channel LED driver chip, that independently controls the 2-digit 7-segment display, the status LEDs of the ethernet ports, and the 3 RGB LEDs present.

## Input
2 rotary quadrature encoders can be connected with RJ45, intended to be used with the Pico's PIO. The RJ45 connections for the encoders have breakouts for Z inputs (for absolute positioning) or 2 more encoders with relative positioning only. These inputs are not connected to the Pico by default due to the limited number of GPIO pins.

There is a MCP3008 ADC which is used for battery voltage & current monitoring.

## I/O

There are 2 PCF8574 I/O expanders which are mostly ununsed, allowing for the connection of low bandwith devices (e.g. enable pins, end stops / limit switches, basic on/off logic) as their speed is limited by the I2C bus. That said, the interrupt pins are broken out and can be optionally used for a decent connection for a rotary encoder.

The first I/O expander is connected to 2 N-channel MOSFETs that are connected to the board's input voltage for VIN @ 2A (total) controllable high-power output.

There are 3 extra GPIO pins exposed, which can be used for connecting e.g. the extra encoder pins, the I/O expander interrupts, or I2C communication to other RIB(s) in order to create a megaRIB for connecting more than 2 OSMCs.

Finally, there is a place for a Digi xBee to sit and communicate with the robot over 2.4Ghz / 900 MHz radio.

## POWER 

Additionally, the PCB pulls power in from the OSMCs over the RJ45 connections or the top left terminal block. The RIB can take in from 8 to 60V of power, or you can provide 5V via headers directly. If it's necessary, the RIB can be powered by a 2S Lithium battery, the power supply can still function at around 7V in.

The onboard 5V switching power supply can provide ~1.8A and the 3v3 regulator, which pulls from the 5V line, can use up to the same 1.8A limit. The 3v3 regulator exists to  provide extra power to the xBee and 3v3 logic without having to put strain on the Pico.

The power inputs on the OSMC RJ45's can also be changed to *output* power and will match the logic voltage 5V/3.3V.


# Programming

The Pi Pico W can be programming with [Arduino-Pico](https://arduino-pico.readthedocs.io/en/latest/), the Pico C++ SDK, Micro/CircuitPython, or Embassy Rust framework. Code examples below are written based on the Arduino-Pico framework. *Note: we discovered a bug in Arduino-Pico's setXX functions affecting the RIB that is only fixed in v3.6.0 or higher.*

Things to know for programming the hardware on the RIB:

The xBee is connected to UART0 / Serial1 on the Pico via pins 16 & 17 for TX & RX respectively. 

	Serial1.setRX(17);
  	Serial1.setTX(16);

The I2C bus for the LED driver and I/O expanders is connected to I2C1 / Wire, pins 20 & 21 for SDA and SCL respectively. 

    Wire.setSDA(20);
    Wire.setSCL(21);

The LED driver has address 0x38. There is no library for the MAX6959 available online, we wrote one in [rib-test-code](https://git.myitr.org/adeck/rib-test-code). Look for lines like `Wire.beginTransmission(0x38)` and functions like `set_digit()` for example.

The I/O expanders have addresses 0x20 for the top one (connected to MOSFETs) and 0x21 for the bottom one.

The library `xreef/PCF8574` has the ability to set pins as well, so the code you would use is

    PCF8574 ioex1(0x20, 20, 21);
    PCF8574 ioex2(0x21, 20, 21);