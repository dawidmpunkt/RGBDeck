# RGBDeck

**RGB Lighting for the Valve Steam Deck** - 8 bit Microcontroller version (work in progress)

Forked from WUBBSY - see here for the original Adafruit Trinket M0 version: https://github.com/WUBBSY/RGBDeck.

This Project uses a small microcontroller and some **WS2812B** LEDs (aka. NeoPixels or ARGB) to add RGB lighting to the back of the Steam Deck. Best effect when using a translucent back.  

>## Huge thanks to [Adam Honse](https://github.com/CalcProgrammer1) for finding the I2C interface of the Steam Deck and implementing it into OpenRGB. 
> You can find the I2C sketch for the Adafruit Trinket M0 on his [Github](https://gitlab.com/CalcProgrammer1/Arduino_I2C_NeoPixel_Controller/-/tree/adafruit_trinket_m0).  
You can then use the Flatpack version from the Discover Store on the Steam Deck to control your LEDs.  
You'll have to enable I2C on the Steam Deck first.  

>## Disclaimer:  
> **This Project includes modification of the Steam Deck hardware and soldering of thin wires and small pads. Please be careful as to not damage your hardware.**  
This Mod is finicky and should only be done if you're competent in soldering and working with electronics.  
The Steam Deck contains a Lithium-Polymer battery cell which can be very dangerous if handled improbable.  
**I don't take any responsibility or liability for any damages to you, your hardware or anything else.**  
This mod may also interfere with your WiFi or Bluetooth. I haven't encountered any problems so far, but your mileage may vary. 

## Table of contents
- [RGBDeck](#rgbdeck)
  - [Table of contents](#table-of-contents)
  - [Introduction](#introduction-and-power-supply-discussion)
  - [What is needed](#what-is-needed)
  - [Choice of the Microcontroller](#choice-of-the-microcontroller)
  - [Connecting the Microcontroller to your Steam Deck](#connecting-the-microcontroller-to-your-steam-deck)
  - [Adding the addressable LEDs](#adding-the-addressable-leds)

## Introduction and power supply discussion

The Steam Deck provides an accessible I2C interface, which can be used to attach and control additional devices (Such as Haptic Motor Drivers, [Link](https://github.com/dawidmpunkt/rumble-for-steamdeck)).

There have been attempts to attach RGB controllers to the steam Deck in order to control the RGB LED via Software from the Steam Deck ([Link](https://www.reddit.com/r/SteamDeck/comments/10uzoj6/openrgbdeck_lives_rgbdeck_mod_with_jsaux_cover/)). This project failed due to issues with the power supply, which either damaged the board ([LINK](https://old.reddit.com/r/SteamDeck/comments/110ca10/warning_about_the_rgbdeck_mod_from_last_weekavoid/)) or caused the Deck not to boot ([LINK](https://github.com/WUBBSY/RGBDeck/issues/1#issuecomment-1653933577)). The prior issue was due to the attempt to connect the RGB LEDs to the 5V rail, used to provide power to the controller boards. The latter issue was due to the fact, that the external device was directly connected to the battery, thus (most likely) causing issues with the charging controller, which caused the Deck not to start up. 

The 5V rail on the Steam Decks controller board is not designed to power additional high current devices (like RGB LEDs), thus resulting in components failing. 

Since it is (a) possible to either power the LEDs via an external power source (Battery) and (b) to attach the RGB controller to the Vsys rail, the issues above can be considered as solved (as demonstrated in the rumble-for-steamdeck project). 

In case of the Vsys rail, power can be drawn from close to the Vsys output of the MAX77961 Battery Charging IC. According to the Datasheet of the charging IC ([LINK](https://www.analog.com/en/products/max77961.html)), it can output up to 10 A of current (if proper cooling is provided). 

Vsys on the Steam Deck is designed to be around 8.7 volts. The Steam Deck APU (if not modified in software) draws around 15 watts of power (around 2 amps of current at 8.7 volts) (according to gamescope data). The display draws around 5-6 Watts (< 1 amp). This should give enough headroom to power additional devices.  

## What is needed

- A suitable microcontroller.
- WS2812B LEDs (aka. NeoPixels or ARGB). A LED Strip is suitable since in can be mounted easily.
- Buck converter or an external battery. 
- thin wire best would be enamelled wire
- Tools to open the Steam Deck (Philips Driver size 0 and a plastic spudger)
- Soldering iron. Preferibly with a small tip since the pads are very small

## Choice of the microcontroller 
(to interface the WS2812 LEDs via I2C)

Since the WS2812 LEDs cannot be addressed via I2C directly, we need an additional device as an „I2C-bridge“. In the projects mentioned above, microcontrollers were used for this job. For this project, a microcontroller was chosen according to the following criteria:
* Widely available
* Cheap
* Small size (to be integrated into the shell of the Steam Deck
* Small power draw
* Easy to program

In this project, 8-bit microcontrollers were chosen, due to being easier to program (for beginners) than their 32-bit counterparts.

At this moment, the STM8S001S was chosen. further documentation is TODO

### Failed attempts
The Attiny85 was tested but the configuration was complicated and the Attiny performed unreliably. This is most likely due to the Attiny not having dedicated I2C Hardware, but I2C being bitbanged via software. The attiny could receive commands via i2c (tested by letting a LED, connected to an output pin, turn on/off on command), when the clock frequency was set to 16 MHz (it failed at 8 MHz), but there were difficulties in receiving the ACK signal from the attiny. 

## Connecting the Microcontroller to your Steam Deck

We fist must remove the back cover of the Steam Deck. A handy guide can be found on [iFixit](https://www.ifixit.com/Guide/Steam+Deck+Back+Cover+Replacement/148893)  
It is then advisable to disconnect the battery to minimize the risk of damage to the components. (Small [guide](https://www.ifixit.com/Guide/Steam+Deck+Battery+Replacement/149070) follow till step 7)  

## Adding the addressable LEDs
NeoPixels or ARGB LEDs contain a WS2812B chip which enables them to be individually controlled.  
These LEDs have 4 contacts _+5V_, _Din_, _Dout_, _GND_.  
_+5V_ and _GND_ are your power connections and need to be connected to the _Gnd_ and _Bat_ or _5V_ connections on your micro.  
_Din_ gets connected to Pin 4 or _D4_ on your micro.  
_Dout_ is the output of the last LED in array and you can connect more LEDs with _Din_ onto it.  

After connecting all your LEDs, you mount them in the back half of your Steam Deck.  

**Be careful when attaching the LEDs to the Steam Deck to not short out any connections of the pcbs or the LEDs!**  

After that put back the case, careful to use the correct screws and you're done!
