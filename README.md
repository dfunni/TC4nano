# TC4nano
The configuration specified in this document is for the TC4nano which uses
one SSR for heater control (no fan control) and logs BT only.

### CONTENTS:
#### Configuring Artiasn
- General Config
- PID Config
- Creating a Profile
- Loading a Background Profile
#### Updating TC4 Firmware
#### Troubleshooting
- Generic Arduino Nano Not Recognized

The TC4nano is configured for the Artisan roaster-scope software. The latest 
version can be found at:	
https://artisan-roasterscope.blogspot.com

Artisan installation instructions:
https://github.com/artisan-roaster-scope/artisan/blob/master/wiki/Installation.md

The TC4nano comes preloaded with aArtisanQ_PID_6_2_3 by Jim Gallt and Brad
Collins and is provided under BSD Liscense. 

The provided firmware has been tested with Artisan versions up to 1.5. 
If you wish to update the firmware the instructions are provided below.



## CONFIGURING ARTISAN

Leave defaults unless specified below. Text input is listed in " ".

### General Config


#### Config > Device...

ET/BT tab:
- select TC4 radio button
- ET Channel - 1
- BT Channel - 2
- AT Channel - None
- PID Firmware - unchecked
- Control - checked
- Curves: ET - unchecked BT - checked
- LCDs: ET - unchecked BT - checked Swap - unchecked

Extra Devices tab:
- Add two devices

Device 1: 
- Device - ArduinoTC4 56 
- Label1 - "Heater" 
- Label2 - "Fan"

Device 2:
- Device - ArduinoTC4 78
- Label1 - "SV" 
- Label2 - "AT"
- OK

Note: after clicking "OK" in the Device menu the Port menu will automatically pop up. Leave this as default for now. When roasting ensure the port reflects the TC4's Arduino.
- OK

#### Config > Sampling Interval 
- set to "1.0" seconds
- OK

#### Config > Curves... > RoR tab
- check the boxes for curves and LCDs you want to view and check projection
- OK


#### Config > Events

Config tab:
- Annotations - check
- 1 - check - "Fan"
- 2 - check - "Heater"
- 3 - uncheck
- 4 - uncheck
- Auto CHARGE/Auto Drop/Mark MET/Show Time Guide - uncheck

Buttons tab:
- add one button
- Label - "Fan Off"
- Type - Fan
- Value - 0
- Action - Serial Command
- Documentation - "IO3;0"
- Visibility - ON

Sliders tab:
- In the heater row set the following to:
- Heater - check
- Action - Serial Command
- Command - "OT1;{}"
- Offset - 0.0
- Factor - 1
- Min - 0
- Max - 100
- Fan - uncheck
- Damper - uncheck
- Burner - uncheck
- OK

#### Config > Temperature
- Select Fahrenheit or Celcius mode
- OK


### PID Config

Click the blue CONTROL button (ensure Control is checked under Config > Device)

Recommended starting PID parameters:

|            | kp  | ki   | kd  |
|------------|-----|------|-----|
| Poppery I  | 2.2 | 0.08 | 20  |
| Poppery II | 1.3 | 0.06 | 4.5 |




- Source - BT

Target:
- Positive - Heater
- Negative - None

Set Value:
- Mode - Background (ensure background profile is loaded prior to roasting)
- Lookahead - 10s (adjust as necessary)

- Start PID on CHARGE - check
- OK


### Creating a Profile

#### Tools > Designer

Adjust the circles to the desired locations, add or delete points as necessary.

When profile is coplete go to Tools > Designer (uncheck) and proile will be
loaded onto the graph.

Hit the RESET button, then save the profile to your Profiles directory.


### Loading a Background Profile

#### Roast > Background

- Show - check
- BT - check
- All others - uncheck
- Align - CHARGE
- Load - select a saved profile from your profile directory
- OK



## UPDATING TC4 FIRMWARE

The latest version of the TC4 firmware can be found on github. Version 
6.6 zip file can be found at:
https://github.com/greencardigan/TC4-shield/tree/master/applications/Artisan/aArtisan_PID/tags/REL_aArtisanQ_PID_6_6

Once downloaded open aArtisanQ_PID.ino in the Arduino IDE 
(https://www.arduino.cc). This will open all of the other files from the
aArtisanQ_PID folder as seperate tabs in the IDE.

Select the user.h tab and to choose the options you wish to enable by 
uncommenting/commenting the options in this file.

- Under "Base configuration" ensure CONFIG_PWM is selected since the 
TC4nano does not include a ZCD.

- Under "Tempreature Unit" comment out CELCIUS if Fahrenhiet is desired.

- Leave all 4 "TC_TYPEx typeK" enabled under "Thermocouple Input Options"
even though only one thermocouple will be used.

- Ensure Baud rate of BAUD 115200

When all appropriate changes are made, connect the arduino to the computer and
select the following options from the menu bar:

Tools > Board > Arduino Nano
Tools > Processor > ATMega328P (Old Bootloader)
Tools > Port > /dev/cu.YOUR_ARDUINO

Finally, flash the Adduino Nano by clicking the upload (forward arrow) button 
in the Arduino IDE. A display of the progress will appear in the command prompt
at the bottom of the IDE and "Done Uploading" will display when it completes 
sucessfully.



## TROUBLESHOOTING:


### Generic Arduino Not Recognized

Copy and paste these commands into terminal one by one 
(to open terminal press command+space and type terminal, then hit enter):

First check to see if homebrew is installed


_______________________________________________________________________________
brew -v
_______________________________________________________________________________

If it is not installed (ie you get an output that is something other than
the version of homebrew) run the following commmand... this takes a while


_______________________________________________________________________________
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
_______________________________________________________________________________

Then run the following two commands to install the CH34x drivers needed
to use the generic arduino nano


_______________________________________________________________________________
brew tap mengbo/ch340g-ch34g-ch34x-mac-os-x-driver https://github.com/mengbo/ch340g-ch34g-ch34x-mac-os-x-driver
_______________________________________________________________________________

_______________________________________________________________________________
brew cask install wch-ch34x-usb-serial-driver
_______________________________________________________________________________

Now restart your computer, and see if Artisan recognizes the arduino.
Or you can enter the following in the terminal and look for an output
that is not an error.


_______________________________________________________________________________
ls /dev/cu.usb* /dev/cu.wch*
_______________________________________________________________________________
