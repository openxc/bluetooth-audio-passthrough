Bluetooth Audio Accessory
=========================

This repository is a part of the [OpenXC][] project.

This repository contains the documentation and open source design files for an
example OpenXC hardware accessory, the Bluetooth Audio accessory, originally
created by Ford.

There is a [gallery of
photos](https://plus.google.com/photos/108408483770573977605/albums/5989364910333751905)
of the prototype accessory.

![Bluetooth Audio Accessory](/docs/BT_Audio_scaled.JPG "The Bluetooth Audio Accessory")

### Description

The Bluetooth Audio Passthrough is an audio-capable Bluetooth chipset, powered
and configurable over USB. The Passthrough uses a Bluegiga WT-32 Bluetooth
module, which is capable of running several different Bluetooth profiles in the
Bluetooth 2.1 standard (and even multiple profiles in parallel). By default, the
BT Passthrough will appear as an A2DP audio sink - an audio endpoint that can
wirelessly stream audio from a cellphone music player to a car Aux input.

### Features

* Supports many Bluetooth protocols out of the box: SPP, DUN, OBEX OPP, HFP
    (v.1.5) A2DP, AVRCP, DID and HID
* Configurable over USB using easy-to-read commands
* 2 output channels (stereo), one input channel (mono)
* Provides a 2.7v microphone bias
* Output headphone amplifier capable of driving up to 105mW per channel

Documents
---------

The schematics, board layout, BOM and testing results are in the `schematics`
directory of this repository.

The schematics were created with Eagle, and are also available as PNGs.

### Assembly Files

The assembly files are in the `fabrication` subfolder, including the board
layout DXF and and archive of all assembly files:

 * .top  - Top Copper Layer
 * .bot  - Bottom Copper Layer
 * .smt - Top Soldermask
 * .slk - Top Silkscreen
 * .tsp - Top Solderpaste
 * .smb - Bottom Soldermask
 * .bsk - Bottom Silkscreen
 * .bsp - Bottom Solderpaste
 * .oln - PCB Outline
 * .drd - Excellon Drill Data
 * .drl - Basic Drill tool information
 * .dri - Extended Drill tool information

Details
-------

### Block diagram

![Bluetooth Audio Block Diagram](/docs/BT%20iJoey%20Block.svg "Bluetooth Audio Block Diagram")

### Parts Layout

![Bluetooth Audio Headers](/docs/BT_Audio_header.JPG "Bluetooth Audio Headers")

### Inputs/Outputs

* JP1 - Serial RX and TX lines - if the USB-Serial interface is not working,
    the TX and RX serial lines can be accessed from this header.
* JP2 - Power - Access to the +3.3V and Ground power rails.
* JP3 - USB CTS and RTS - Access to the hardware flow control lines provided
    by the FT232RL USB to serial IC.
* JP4 - Bluetooth CTS and RTS - Access to the hardware flow control lines on
    the WT32 adapter. Can be used with JP3 to enable hardware flow control.
* J3 - SPI programming interface - If the firmware on the WT32 needs to be
    updated from iWrap 4, this interface can be used.
* J2 (the Headphone Jack) - This is a 4 pole TRRS connector, which can be used
    for 2 output channels and 1 input channel. A standard 3-pin TRS headphone
    cable can be used, and the microphone input will be unused.
    * Pin 1 (tip) - Out Left
    * Pin 2 (ring 1) - Out Right
    * Pin 3 (ring 2) - Ground
    * Pin 4 (shield) - Microphone Input

Usage:
------

### How to use

1. Plug the Bluetooth Audio Accessory into the Dock
1. Connect the short 4-pin 1/8" TRRS cable to the audio jack on the Accessory
1. Connect the other end of the TRRS cable into the dock
1. Plug a 1/8" headset or 1/8" extension cable into the dock
1. On a smartphone or tablet, search for Bluetooth devices
1.  A "WT-32" device should appear. Pair with it. No PIN code should be
    necessary, but "0000" can be used.
1.  Once pairing is complete, connect to the WT-32 device
1. The LED on the Bluetooth Audio Accessory should turn green and the Bluetooth
    host should now play audio through the device.

### How to program

Upon receiving a Bluetooth Audio Accessory from the factory, a quick
configuration step will be required:

1. Connect the Accessory directly into the USB port of a PC
1. The accessory should appear as a serial device (/dev/ttyUSBX in linux, COMX
   in windows, /dev/tty.usbserial-xxxxxxxx in OSX).
1. In a terminal application, open the serial port with baud 115200 and no
   hardware flow control.
1. Type ```RESET``` and press enter. The Accessory should respond with the
   iWrap firmware version.
1. Type the following commands, pressing enter after each line:

```
SET BT SSP 3 0
SET BT AUTH * 0000
SET PROFILE A2DP SINK
SET PROFILE AVRCP CONTROLLER
SET BT CLASS 240438
SET CONTROL GAIN D D
SET CONTROL INIT SET CONTROL GAIN D D
SET CONTROL CD 400 0
RESET
```

The device should now be ready for use as an A2DP sink. Here are a few other
popular protocols (repeat the above steps to apply):

#### A2DP Sink (Audio from Bluetooth to Speaker)

```
SET PROFILE A2DP SINK

SET PROFILE AVRCP CONTROLLER

SET BT CLASS 240438
```

#### A2DP Source (Audio from Microphone to Bluetooth)

```
SET PROFILE A2DP SOURCE

SET PROFILE AVRCP TARGET

SET BT CLASS 280428
```

#### HFP - Hands Free Protocol (Act like Bluetooth Headset)

```
SET BT SSP 3 0
SET BT AUTH * 0000
SET PROFILE HFP ON
SET BT CLASS 200404
```

### Known issues

#### v0.1

* Audio output noise
    * Power supply - The audio output is fairly sensitive to power supply
        noise. The +5V USB power supply is filtered, but very noisy USB busses
        can still produce audible pops and buzzes
    * Bluetooth - The headphone amplifier is somewhat sensitive to Bluetooth
        energy. If the connected tablet or cell phone is held very closely to
        the BT Passthrough, cellular and/or Bluetooth noise will be induced into the
        audio output. Try not to rest any cellular devices immediately against
        the Passthrough.
* Audio output level - The default gain on the WT32 chipset is fairly
    conservative. To increase the default gain, connect the BT Passthrough to a PC. It
    should appear as a serial device. Connect to that serial device at 115200
    baud, and send the following commands:

```
RESET

SET CONTROL GAIN D D

SET CONTROL INIT SET CONTROL GAIN D D

RESET
```

Try adjusting the 0xD parameter above (valid range is 0 - 17 in hexadecimal, see
the WT-32 manual). In testing, a gain of 0xD is the highest that a pair of 64
ohm headphones would tolerate before distortion.

Design details:
---------------

### Bluetooth Chipset

The WT-32 was selected as a prototype-friendly Bluetooth Audio chipset. It was
the only chipset available from distributors like SparkFun that had full, native
audio support. Advantages:

* Simple serial command protocol
* Many built-in Bluetooth protocols
* Built-in microphone pre-amplifier with software-adjustable gain
* software-adjustable output gain as well

The basic WT-32 design is very strongly based from its [Evaluation
Board](http://www.digikey.com/product-search/en/rf-if-and-rfid/rf-evaluation-and-development-kits-boards/3539644?k=WT32).
The WT32 documentation is available in the Documentation directory.

### Configuration Interface

The WT-32 is configured by a serial UART at 115200 baud. This is exposed via
USB using an industry-standard [FTDI
FT232RL](http://www.ftdichip.com/Support/Documents/DataSheets/ICs/DS_FT232R.pdf)
USB to serial converter IC. The chip requires few external components - each
power rail is decoupled with 0.1uF capacitors, TX and RX LEDs are included, and
the UART is protected from excess current through RX and TX resistors R1 and R2.
USB signals require high speed PCB routing to avoid signal integrity issues,
since the bitrates are high (11Mbps - 480Mbps). Unfortunately with a 2 layer
PCB, the trace widths required to obtain the ideal 90 ohm differential impedance
for a USB line would be unfeasibly wide. To compensate for this fact, the USB
connector was located extremely close (~300 mils) to the FTDI 232RL USB pins.

The WT-32 also has an ICSP header if future firmware updates are desired.
Current WT-32 modules will ship with either iWrap4 or iWrap5 firmware, both of
which contain the necessary configurations and protocols needed by the
accessory. Upgrading shouldn't be necessary, but is possible using the SPI bus
exposed by J3. See the WT32 documentation for firmware upgrade procedures.

### Power

A single Low Dropout linear regulator is used to step the +5V USB power down to
the 3.3V required by the Bluetooth module. IC5 preforms this task, providing up
to 1.6A of current (far more than is necessary). The power is filtered in two
places. Immediately after entering via the USB connector, power is passed
through inductor L3 with filtering capacitors C12 and C5. This is from the
FT232RL reference design. An additional filtering stage separates the normal
+3.3V rail from the +3.3V used by the analog audio circuitry. Analog power is
run through inductor L4 and decoupled by C16 in an attempt to remove additional
transients caused by the power draw of the WT32.

A DC midpoint is also required by the output amplifier. Since the amplifier is
a single-ended device (no negative power rail is available), a midpoint is
required to properly bias / center the audio signal before amplification. This
is generated using a rudimentary resistor divider of R6 and R7. Excess DC
midpoint current can unbalance the amplifier, but with the low input impedance
of the output amplifier, this shouldn't be an issue. If the DC midpoint becomes
unbalanced, or if more ground filtering is desired, consider buffering the
DC_MID signal with an opamp.

### Analog Microphone Input

The microphone input receives it's signal from pin 4 of the TRRS audio
connector. IC2 provides a 2.7V microphone bias for the microphone in a
connected headset. The WT32 contains a microphone bias output, but the
reference schematic recommends using an additional Low Dropout Regulator (IC2)
instead. The LDO provides additional power filtering for the microphone bias,
which is an extremely noise-sensitive signal for an electret microphone element.
This circuit assumes a microphone element with an impedance in the 1k - 3k range
(specifically we are matching 1K using resistor R3).

Inductors L1 and L2 and capacitors C9 and C10 attempt to filter excess high
frequency noise from the microphone. Capacitors C11 and C13 decouple the
microphone signal before connecting it to the WT32. Diodes D1 and D2 protect
the WT-32 built-in microphone preamplifier and ADC from damage due to voltage
transients by clamping the microphone signal to +3.3v and Ground. Note that
this circuit uses a shared ground for the microphone input and the headphone
output - if crosstalk between headphones and microphone becomes an issue,
consider spliting the 4-pin TRRS jack into two seperate 3-pin TRS jacks.

### Headphone / Line output

A [TI LM4808 Headphone Amplifier](http://www.ti.com/lit/ds/symlink/lm4808.pdf)
isolates the audio output from the WT32. The reference design was pulled
directly from the WT-32 evaluation board. The amplifier also converts the
differential audio output of the WT32 into a single-ended output used by most
headphones and consumer electronics. Note that output decoupling capacitors C23
and C28 are specified as Tantalum capacitors, since they lie directly in the
audio pathway and need to be robust to handle the voltage transients of plugging
and unplugging audio signals. Care must be taken to chose capacitors with a
good sonic signature, low ESR and a sufficient voltage rating.

## License

Copyright (c) 2014 Ford Motor Company

The electrical and mechanical designs in this repository are available under the
[Creative Commons Attribution 4.0
International](http://creativecommons.org/licenses/by/4.0/deed.en_US) license.

[OpenXC]: http://openxcplatform.com
