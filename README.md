<img align="right" src="arduino_cbus_logo.png"  width="150" height="75">

# CAN_Wrapper

An Arduino program that provides a MERG CBUS interface that can interface with the users
application.  The program is derived from CANmINnOUT, which was originally developed as
a technology demonstrator. CANmINnOUT thus serves as an exmaple of how to use this program
and can be found here:

https://github.com/MartinDaCosta53/CANmINnOUT

Key Features:
- MERG CBUS interface
- Modular construction to ease adaptation to your application.

## Overview

The program does not allow for the inclusion of a CBUS setup switch or LEDs. Consequently,
the following library versions are required:
- CBUS Version 1.1.14 or later
- CBUSConfig 1.1.10 or later
Notwithstanding the fact that they are not used, CBUSSwitch and CBUSLED libraries must be 
available for access by the CBUS and CBUSConfig libraries for reason of backwards library
compatibility.

It is possible that the CBUS setup switch could be reinstated for purposes of requesting a
Node Number.  The LEDs remain superfluous as SLiM is not supported and the module is always
in FLiM.

## Using CAN_Wrapper

The MCP2515 interface requires five Arduino pins to be allocated. Three of these are fixed
in the architecture of the Arduino processor. One pin must be connected to an interrupt
capable Arduino pin. Thus, the total number of pins available for input or output is:
- UNO  13 pins
- NANO 15 pins (Note that two of these, A6 & A7, are analogue inputs only.
- MEGA 63 pins

**It is the users responsibility that the total current that the Arduino is asked to supply 
**stays within the capacity of the on board regulator.  Failure to do this will result in 
**terminal damage to your Arduino.

### Library Dependancies

The following third party libraries are required:
Library | Purpose
---------------|-----------------
Streaming.h  |*C++ stream style output, v5, (http://arduiniana.org/libraries/streaming/)*
ACAN2515.h   |*library to support the MCP2515/25625 CAN controller IC*
CBUS2515.h   |*CAN controller and CBUS class
CBUSconfig.h |*module configuration*
CBUS.h       |*CBUS Class*
cbusdefs.h   |*Definition of CBUS codes*
CBUSParams.h   |*Manage CBUS parameters*
CBUSSwitch.h   |*library compatibility*
CBUSLED.h      |*library compatibility*

### Application Configuration

The module can be configured to the users specific configuration in a section of code 
starting at line c.93 with the title DEFINE MODULE. The following parameters can be changed 
as necessary:
```
#define DEBUG 0       // set to 0 for no serial debug
```
This define at circa line 73 allows various output reports to be made to the serial monitor 
for use in debugging.  To enable these, change the value of DEBUG from 0 to 1.

```
// module name
unsigned char mname[7] = { 'C', 'A', 'N', 'w', 'r', 'a', 'p' };
```
This can be adjusted as required to reflect the module configuration.  Only one character is allowed between 
each pair of ' ' and the total number of characters must not exceed seven.

```
const unsigned long CAN_OSC_FREQ = 8000000;     // Oscillator frequency on the CAN2515 board
```
If the oscillator frequency on your CAN2515 board is not 8MHz, change the number to match. The 
module will not work if this number does not match the oscillator frequency.



### CBUS Op Codes

The following Op codes are supported:
OP_CODE | HEX | Function
----------|---------|---------
 OPC_ACON | 0x90 | On event
 OPC_ACOF | 0x91 | Off event
 OPC_ASON | 0x98 | Short event on
 OPC_ASOF | 0x99 | Short event off

Other op codes and responses can be added to the case list that starts around line 247.
 
## Set Up and the Serial Monitor

Without a CBUS switch, it is necessary to have another means of registering the module on 
the CBUS and acquiring a node number.  This is accomplished by sending a single character to 
the Arduino using the serial send facility in the serial monitor.

#### 'r'
This character will cause the module to renegotiate its CBUS status by requesting a node number.
The FCU will respond as it would for any other unrecognised module.

#### 'z'
This character needs to be sent twice within 2 seconds so that its action is confirmed.
This will reset the module and clear the EEPROM.  It should thus be used with care.

Other information is available from the serial monitor using other commands:

#### 'n'
This character will return the node configuration.

#### 'e'
This character will return the learned event table in the EEPROM.

#### 'v'
This character will return the node variables.

#### 'c'
This character will return the CAN bus status.

#### 'h'
This character will return the event hash table.

#### 'y'
This character will reset the CAN bus and CBUS message processing.

#### '\*'
This character will reboot the module.

#### 'm'
This character will return the amount of free memory. 
 
