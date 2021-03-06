## Flashing the Arduino Dock {#flash-arduino-dock-wirelessly}

The Arduino Dock allows the Omega and the ATmega328P microcontroller interact with each other. Having an OS with the connectivity power of the Omega communicate easily with a microcontroller can be very powerful used effectively. We can do amazing things with the Omega communicating with the ATmega328P chip such as flashing the microcontroller wirelessly.

> Programming and flashing a microcontroller mean the same thing, you are taking compiled code and uploading it to a microcontroller. The terms are often used interchangeably.

### Prerequisites

You'll need to first make sure that your Omega has connected to internet.

Then you'll want to ssh into the Omega's terminal in order to install the arduino dock package.


> We've written a [guide to connecting to your Omega's terminal via SSH](#connecting-to-the-omega-terminal-ssh) in case you don't know how!

To install this package you'll need to use `opkg`. Enter the following commands on the command-line:

```
opkg update
```
```
opkg install arduino-dock-2
```

#### Accessing the Omega
<!-- // 1) your computer must be able to connect to the Omega by it's `Omega-ABCD` name
// can recycle content from: https://wiki.onion.io/Tutorials/Arduino-Dock/Initial-Setup#computer-setup_accessing-the-omega-s-url

// highlight that on windows you need the bonjour printer services -->

<!-- Computer Config -->
<!-- ```{r child = '../../Get-Started/First-Time-Components/First-Time-Component-01-computer-config.md'}
``` -->


The Omega must be accessible via its URL `http://omega-ABCD.local` where `ABCD` is your Omega's unique code.

The requirements vary depending on your Operating System:

| Operating System | Actions Required                                            |
|------------------|-------------------------------------------------------------|
| Windows          | Install Apple's Bonjour Service                             |
| OS X             | Nothing, good to go                                         |
| Linux            | Zeroconf services should already be installed, should be ok |


#### Arduino IDE

<!-- // arduino ide must be installed on your computer
// can reference https://wiki.onion.io/Tutorials/Arduino-Dock/Initial-Setup#computer-setup_arduino-ide -->

Install the latest Arduino IDE from the good folks over at [Arduino](//www.arduino.cc/en/Main/Software). We did all of our testing using Version 1.8.0.

#### Modification of `boards.txt`

<!-- // need to modify arduino's boards.txt file to allow ssh sketch uploads
// recycle content from https://wiki.onion.io/Tutorials/Arduino-Dock/Initial-Setup#computer-setup_arduino-ide_modification-of-boards-txt

// TODO: LAZAR to update this messy solution -->

The regular Arduino Uno has no way of communicating with your computer via WiFi, but the Omega provides the Arduino Dock with a means of wireless communication, so we'll have to let the Arduino software know!
This requires a one line addition to the `boards.txt` file in your Arduino IDE installation.

Open the boards.txt file in any text editor. The following table outlines where the `boards.txt` file can be found:

| Operating System | Location of `boards.txt`                                                  |
|------------------|---------------------------------------------------------------------------|
| OS X             | `/Applications/Arduino.app/Contents/Java/hardware/arduino/avr/boards.txt` |
| Windows          | `C:\Program Files (x86)\Arduino\hardware\arduino\avr\boards.txt`          |

Once it's open, add the following line:
```
uno.upload.via_ssh=true
```
Doesn't matter where you add it, you can nestle it with the other `uno.` settings if you like.





### Doing the Actual Flashing

<!-- // two methods to flash the mcu
//  1. using the arduino IDE
//  2. copying the compiled file to the omega and flashing manually from the command line

// make sure to mention that the Arduino Dock 2 comes ready to flash the microcontroller out of the bux -->

Now we get to the fun part, flashing sketches to the ATmega chip!

There are two methods for flashing the ATmega328P chip using the Omega. You can flash it using the Arduino IDE wirelessly **OR** you can compile the file on your computer, copy it to the Omega, and then flash the chip from the command line.

We **strongly** recommend using the Arduino IDE, and only recommend flashing the ATmega328P from the command-line as a backup plan.

### Wireless Flashing with the Arduino IDE

Thanks to the setup you did on your computer and the Arduino Dock, you can actually use the Arduino IDE on your computer to wirelessly flash Sketches to the Arduino Dock, so long as your computer and the Omega on your Arduino Dock are on the same WiFi network.

In the Arduino Tools menu, select "Arduino/Guinuino Uno" for the Board, and your Omega-ABCD hostname as the Port:
![Arduino IDE Tools->Port menu](//i.imgur.com/1xAEBmT.png)

If your Omega does not show up in the Port menu as a network port, restart the Arduino and wait for 30 seconds:

When your sketch is ready, hit the Upload button. Once the sketch is compiled, it will require your Omega password to upload:
![Arduino IDE Uploading Sketch](//i.imgur.com/UDXIDVL.png)

The IDE actually creates an SSH connection with the Omega to transfer the compiled hex file, and the Omega with then flash the ATmega chip via I2C!
(If your previous sketch did not include the Onion Arduino Library you will have to press the MCU_RESET button on the Arduino Dock right after entering your password in order to flash successfully)

Once the upload completes, the info screen will show something along the lines of:
![Arduino IDE Upload Done](//i.imgur.com/oPOB4Vl.png)

The ATmega chip is now running your sketch, enjoy!


**Note:** An orange message saying `ash: merge-sketch-with-bootloader.lua: not found` may appear in the info screen. You can **safely ignore this message**, it does not affect the sketch upload.




<!-- // can borrow heavily from https://wiki.onion.io/Tutorials/Arduino-Dock/Using-the-Arduino-Dock#using-the-arduino-dock_flashing-sketches
// just get rid of the stuff about the onion library and onion object, the new arduino dock doesn't need that!
// take new screenshots please -->

<!-- TODO: Replace screenshots with newer screenshots -->

### Manually Flashing on the Command line
<!--
// mention that this should be the back-up way in case the above method doesn't work
// can borrow heavily from  https://wiki.onion.io/Tutorials/Arduino-Dock/Using-the-Arduino-Dock#using-the-arduino-dock_flashing-sketches_using-the-omega
// for the part where we copy over the file, link to the transferring files article -->

Like we mentioned before this method should only be used as a backup to using the Arduino IDE. This is handy if the Arduino IDE cannot detect your Omega as a Network Port due to any connection/setup issues.

First, enable verbose output during compilation in the Arduino IDE Preferences:
![Arduino IDE Preferences](//i.imgur.com/A6uXT6Y.png)

Hit the verify button to compile the sketch, once it's complete you will have to scroll to the right to find the path to the compiled hex file:
![Arduino IDE Compiled Hex file](//i.imgur.com/QEiDwu8.png)

Copy this path and then transfer the file to your Omega.

> For more information on transferring files to your Omega from your computer you can check out our [extensive guide to transferring files to your Omega](#transferring-files)

Now that the hex file is on your Omega, you can flash it to the ATmega chip from the Omega's terminal:

```
sh /usr/bin/arduino-dock flash <hex file>
```

For example:

```
# sh /usr/bin/arduino-dock flash /root/blink2.hex
> Flashing application '/root/blink2.hex' ...
device         : /dev/i2c-0       (address: 0x29)
version        : TWIBOOTm328pv2.1??x (sig: 0x1e 0x95 0x0f => AVR Mega 32p)
flash size     : 0x7800 / 30720   (0x80 bytes/page)
eeprom size    : 0x0400 /  1024
writing flash  : [**************************************************#?] (3210)
verifying flash: [**************************************************#?] (3210)
> Done
```

The sketch has been flashing and is running on the Arduino Dock, enjoy!

<!-- TODO: Replace screenshots with newer screenshots -->
