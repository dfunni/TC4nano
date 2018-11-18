# TC4nano
The TC4nano is an Arduino Nano shield for termperature logging (up to 4 thermocouples) and PID control of a resistive heating element for use in home coffee roasting (based off of the TC4 Arduino Uno shield design copyright 2014 MLG Properties LLC and bvwelch.com CC-BY). The TC4nano includes one onboard SSR for heater control via slow PWM. Forced air is recommended for cooling the SSR in configurations drawing 10A or above.

### CONTENTS:
#### Configuring Artiasn
#### Updating TC4 Firmware
#### Troubleshooting

The TC4nano is pre-configured for the Artisan roaster-scope software using the aArtisanQ_PID_6_2_3 by Jim Gallt and Brad
Collins provided under BSD Liscense.

The latest version of the TC4 firmware can be found at:
https://github.com/greencardigan/TC4-shield

The latest version of Artisan can be found at:	
https://github.com/artisan-roaster-scope

Artisan installation instructions:
https://github.com/artisan-roaster-scope/artisan/blob/master/wiki/Installation.md

## CONFIGURING ARTISAN

Leave defaults unless specified below. Text input is listed in " ".

### General Config
The configuration below assumes use with Poppery I roaster and no desired fan control through Artisan. Fan control can be added by connecting a DC motor driver (for Poppery II or other DC fan) or second SSR (for AC fan) to the IO3 jumper. If fan control is desired see setup described in the following video: https://www.youtube.com/watch?v=0-Co-pXF2NM. 

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

Device 2:
- Device - ArduinoTC4 78
- Label1 - "SV" 
- Label2 - "AT"

Note: after clicking "OK" in the Device menu the Port menu will automatically pop up. Prior to each roast session ensure the port reflects the correct Arduino.

#### Config > Sampling Interval 
- set to "1.0" seconds

#### Config > Curves... > RoR tab
- check the boxes for curves and LCDs you want to view and check projection


#### Config > Events

Config tab:
- Annotations - check
- 1 - uncheck - Fan
- 2 - check - Heater
- 3 - uncheck - Damper
- 4 - uncheck - Burner
- Auto CHARGE/Auto Drop/Mark MET/Show Time Guide - uncheck

Buttons tab:
- add one button
- Label - "Heater Off"
- Type - Heater
- Value - 0
- Action - Serial Command
- Documentation - "OT1;0"
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

#### Config > Temperature
- Select Fahrenheit or Celcius mode


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



## UPDATING TC4 FIRMWARE

The latest version of the TC4 firmware can be found on github. Version 
6.6 zip file can be found at:
https://github.com/greencardigan/TC4-shield/tree/master/applications/Artisan/aArtisan_PID/tags/REL_aArtisanQ_PID_6_6

Once downloaded open aArtisanQ_PID.ino in the Arduino IDE 
(https://www.arduino.cc). This will open all of the other files from the
aArtisanQ_PID folder as seperate tabs in the IDE.

Select the user.h tab and to choose the options you wish to enable by 
uncommenting/commenting the options in this file.

- Under "Base configuration" ensure CONFIG_PWM is selected as the 
TC4nano does not include a ZCD.

- Under "Tempreature Unit" comment out CELCIUS if Fahrenhiet is desired.

When all appropriate changes are made, connect the Arduino to the computer and
select the following settings from the menu bar:

- Tools > Board > Arduino Nano
- Tools > Processor > ATMega328P (Old Bootloader)
- Tools > Port > /dev/cu.YOUR_ARDUINO

Finally, flash the Adduino Nano by clicking the upload (forward arrow) button 
in the Arduino IDE. A display of the progress will appear in the command prompt
at the bottom of the IDE and "Done Uploading" will display when it completes 
sucessfully.



## TROUBLESHOOTING:


### Generic Arduino Not Recognized (macOS)

Copy and paste these commands into terminal one by one 
(to open terminal press command+space and type 'terminal', then hit enter):

First check to see if homebrew is installed


_______________________________________________________________________________
brew --version
_______________________________________________________________________________

If it is not installed (ie you get an output that is something other than
the version of homebrew) run the following commmand


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
Alternately, you can enter the following in the terminal and look for an output
that is not an error.

_______________________________________________________________________________
ls /dev/cu.usb* /dev/cu.wch*
_______________________________________________________________________________
