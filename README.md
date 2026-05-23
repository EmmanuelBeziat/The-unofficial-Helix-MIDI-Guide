# The “Unofficial” Helix MIDI Guide

***Version 3.1***

> This is based on the excellent work from [The Unofficial Helix MIDI Guide](https://www.dropbox.com/scl/fi/a0lc12mbwonz9i1erglwx/The-Unofficial-Helix-MIDI-Guide.pdf?rlkey=3qnc90qlrrixrwcwbew5vz0mn&e=1&dl=0) created by Stoopalini. I found the PDF version a bit of a hassle to navigate, search, crawl and feed it to LLMs so I took it upon myself to convert it to markdown format, and make some edits for clarity and formatting.

# Table of Contents

- [1 - Basic information](#1---basic-information)
	- [1.1 - Quick facts on Helix](#11---quick-facts-on-helix)
	- [1.2 - Helpful Resources](#12---helpful-resources)
- [2 - Global Settings](#2---global-settings)
- [3 - Incoming MIDI Messages](#3---incoming-midi-messages)
	- [3.1 - Predefined MIDI Commands](#31---predefined-midi-commands)
		- [3.1.1 - Setlist, Preset and Snapshot Recall](#311---setlist-preset-and-snapshot-recall)
		- [3.1.2 - Emulating footswitches and Expression pedals](#312---emulating-footswitches-and-expression-pedals)
		- [3.1.3 - Looper Controls](#313---looper-controls)
		- [3.1.4 - MIDI clock (receive)](#314-midi-clock-receive)
	- [3.2 - Custom MIDI Command Assignments](#32---custom-midi-command-assignments)
		- [3.2.1 - Controlling Block Bypass States](#321---controlling-block-bypass-states)
		- [3.2.2 - Controlling Block Parameters](#322---controlling-block-parameters)
- [4 - Outgoing MIDI Messages](#4---outgoing-midi-messages)
	- [4.1 - Default MIDI Messages When Recalling Presets](#41---default-midi-messages-when-recalling-presets)
	- [4.2 - Command Center (not available on HX Stomp)](#42---command-center-not-available-on-hx-stomp)
		- [4.2.1 - Overview](#421---overview)
		- [4.2.2 - Screen Layout](#422---screen-layout)
		- [4.2.3 - Instant Commands](#423---instant-commands)
		- [4.2.4 - Footswitch Commands](#424---footswitch-commands)
		- [4.2.5 - Expression Pedals](#425---expression-pedals)
		- [4.2.6 - Variax knobs](#426---variax-knobs)
		- [4.2.7 - Expression Pedal toe switch](#427---expression-pedal-toe-switch)
	- [4.3 - MIDI clock (send)](#43-midi-clock-send)
	- [4.4 - MIDI Thru](#44-midi-thru)
- [5 - Other Key Details](#5---other-key-details)
	- [5.1 - Checking Firmware Version](#51---checking-firmware-version)
	- [5.2 - Creating a MIDI Loopback Connection](#52---creating-a-midi-loopback-connection)
	- [5.3 - MIDI message order](#53---midi-message-order)
- [6 - Command Center Snapshot Assignment Issue](#6---command-center-snapshot-assignment-issue)
- [7 - MIDI CC Quick Reference Charts](#7---midi-cc-quick-reference-charts)
	- [7.1 - Pedal and Footswitch](#71---pedal-and-footswitch)
	- [7.2 - Looper Control](#72---looper-control)
	- [7.3 - Additional Controls](#73---additional-controls)

## 1 - Basic information

MIDI is a means of communicating information between devices. It does not send or receive any audio, but only control messages. Think of MIDI like a network, where multiple devices are sending and receiving message to and from each other. Those messages can then be used to make things happen on the receiving devices.

Typical commands are Program Change (PC) messages, Control Change (CC) messages, and Note on/off messages. These commands would then have {values} associated to the message for further control.

MIDI Clock is another common type of MIDI message. MIDI Clock is a constant stream of tempo information, which can be used to synchronize multiple devices to the same BPM.

Some devices are very programmable, where the user can define what a MIDI message will cause to happen when received; As well as define what message will be sent when a button/switch/knob/expression pedal is operated. Other devices are more limited, where the MIDI messages are pre-set, in terms of what will happen when a particular message is received; As well as what message is sent when a button/switch/knob/expression pedal is operated. Helix devices have both pre-set commands, as well as user programmable commands.

A MIDI network uses a standardized 5-pin DIN connector and cables, or USB; and most recently, Bluetooth MIDI devices have started to become popular for wireless MIDI communication.

### 1.1 - Quick facts on Helix

* Helix MIDI channels are numbered 1-16. Some other devices may number channels 0-15.
* Helix MIDI values for messages (CC, PC, Note On) are numbered starting at 0, not 1
* There are three physical connectors to use for MIDI on the Helix:
	* USB
	* 5 Pin “MIDI In” DIN
	* 5 Pin “MIDI Out/Thru” DIN
* An iOS device (iPad/iPhone) can be connected to the Helix’s USB jack by using an [Apple Camera Connector Kit adapter](https://www.amazon.com/gp/product/B01F7KJDIM) to provide a MIDI interface between the iOS device and the Helix. This also enables an 8x8 USB audio interface between the iOS device and the Helix.
* By default, the Helix cannot send MIDI commands to itself. Outgoing MIDI message from the Helix are not treated as incoming MIDI messages, and therefore you cannot use the Helix to send a MIDI message to itself. Some folks have used a 5-pin MIDI DIN cable to physically connect the “MIDI Out/Thru” jack on the back of the Helix to the “MIDI In” jack, to enable the Helix to send MIDI commands to itself. See the “Creating a MIDI Loopback Connection” portion of this document for more info

### 1.2 - Helpful Resources

* [Helix User’s Manuals](https://line6.com/support/manuals/#helix)
* [Software & Firmware Downloads](https://line6.com/software/index.html)
* [General MIDI Information](https://cdn.roland.com/assets/media/pdf/MIDIBasics.pdf)
* Troubleshooting Tools:
	* The [iOS “MIDI Wrench” app](https://apps.apple.com/us/app/midi-wrench/id589243566), running on an iOS device which is connected to the Helix using the [Apple Camera Connector Kit adapter](https://www.amazon.com/gp/product/B01F7KJDIM), can be very helpful when troubleshooting MIDI issues, as it allows you to see all MIDI messages coming in and out of the iOS device.
	* Equally good for troubleshooting is the [“MIDI-OX” tool](http://www.midiox.com/jsoft.htm#MIDI-OX32) running on a PC. This also enables you to see all MIDI messages being sent by the Helix, but also provides a control panel to send MIDI messages back to the Helix. This can be very helpful for testing configurations.

## 2 - Global Settings

The “Global Settings” – “Preferences” screen has a setting called “Snapshot Edits”. This setting is important to understand when setting up “Command Center” MIDI assignments for snapshots.

* If this is set to “Discard”, the preset must be saved by pressing the `Save` button twice, when making adjustments to snapshot controller assignments before changing to another snapshot.
* If this is set to “Recall”, changes made to “Command Center” values will be stored immediately on the snapshot, without needing to press `Save`. The “Global Settings” – “MIDI/Tempo” screen is where the remaining MIDI settings are found
* “MIDI Base Channel”
	* This is where you set the MIDI channel for the Helix to use.
	* Within other Helix menu screens, sometimes “Base” is an option for a “Channel” parameter. This “Base” term is referring to whatever is set within the “MIDI Base Channel” parameter.
	* Commands sent to the Helix, on this channel, will be picked up and processed by the Helix.
	* If this is set to “Omni”, the Helix will process commands on all 16 MIDI channels.
* “MIDI Thru”
	* If this is set to “On”, the Helix will pass incoming MIDI messages back out to
downstream devices on the network.
	* This is especially useful when daisy chaining MIDI devices together.
* “Receive MIDI Clock”
	* If set to something other than “Off”, the Helix will allow an external device to control the Helix’s BPM/Tempo through MIDI Clock messages.
* “Send MIDI Clock”
	* If set to something other than “Off”, the Helix will send BPM/Tempo over MIDI Clock
messages to other devices connected to the MIDI network.
* “MIDI over USB”
	* Determines if MIDI messages should be received and sent over the USB connector.
* “MIDI PC Receive”
	* If set to something other than “No”, the Helix will respond to incoming PC messages and recall the preset corresponding to the PC value received.
* “MIDI PC Send”
	* If set to anything other than “Off”, the Helix will send default MIDI messages whenever a Preset is recalled.
* “Duplicate PC Send”
	* This setting controls how PC message assignments function in the Command Center.
	* If a PC message is assigned to a function in Control center, and the value set for the PC message is the same between multiple snapshots, then:
		* The PC command will be sent each time the snapshots are loaded, but only if “Duplicate PC Send” is turned “On”.
		* If “Duplicate PC Send” is turned “Off”, then the PC message will only be sent when the PC value assigned to the command is different between snapshots.

## 3 - Incoming MIDI Messages

The Helix can receive MIDI messages over a USB connection, or from a 5-pin DIN cable plugged into the “MIDI In” jack. The Helix uses predefined functions as well as custom assignments for incoming MIDI messages.

Incoming messages must be sent to the Helix’s “MIDI Base Channel”, for the Helix to process the commands. This is done by configuring the MIDI command on the sending device.

For example: If the Helix is set for “MIDI Base Channel 7”, then the sending device should be configured to send messages on Channel 7 for the Helix to process. This is not the same as the sending device’s MIDI base channel.

Each device on the MIDI network should have its own unique channel assigned, unless you want more than one device to respond to the same messages.

### 3.1 - Predefined MIDI Commands

#### 3.1.1 - Setlist, Preset and Snapshot Recall

“MIDI PC Receive” in Global Settings must not be not turned off to recall presets with PC messages. Snapshots can still be recalled with CC messages though, even when “MIDI PC Receive” is turned off.

You can recall a specific preset, and snapshot within that preset, by sending three MIDI commands to the Helix. One is for setlist selection, one is for preset recall, and the 3rd is for snapshot recall. It’s important to send the messages in that order, as they are processed as they are received. For example: If you send a snapshot message 1st, followed by a preset message … the Helix will recall the snapshot on the current preset, then recall the new preset afterward. This will cause the new preset to be loaded with whatever snapshot was selected when the new preset was last saved.

All three of these commands can also be used independently as well. For example: If you just want to change snapshots of the current preset, you can just send the snapshot MIDI CC command without a setlist or a preset command.

As another point to consider: If you don’t send the snapshot command, the preset will load with whatever snapshot was active when that preset was last saved. So if your presets already have the starting snapshot you prefer, then there is no need to send the snapshot CC command in addition to the preset PC command.

* Send a CC32 command for Setlist selection
	* An incoming CC32{value} command will cause the Helix to select a setlist. A value of 0 will change to setlist 1 and a value of 7 will change to setlist 8.
	* This is typically used prior to sending a preset recall message, to ensure the correct preset within the correct setlist is recalled.
	* On some DAWs, CC32 is reserved for Patch Change message configuration. This is because the CC32 command is defined as the LSB (Least Significant Bit) portion of a structured MIDI patch/bank change message. If your DAW does not allow you to configure a CC32 message, then look for a “Sub-Bank” setting in the Preset/Bank change configuration screen. This will typically be the place to configure the CC32 value.
* Send a PC command for Preset selection
	* An incoming PC{value} message will recall a preset, in the selected setlist. The preset relating to the value will be recalled. A value of 0 will recall the 1st preset in the setlist, a value of 127 will recall the 128th preset in the setlist.
* Send a CC69 command for snapshot selection
	* An incoming CC69{value} message will recall a snapshot within the currently loaded preset. A value of 0 will recall snapshot 1, while a value of 7 will recall snapshot 8.

To identify the CC32, PC and CC69 values for recalling a particular preset, press down on the [Preset] knob, then navigate to the desired preset. After the desired preset is highlighted, you will see the CC32{value} the PC{value} and the CC69{value} displayed on the screen, above encoder knob #2. Sending these three commands to the Helix will recall this preset & snapshot.

#### 3.1.2 - Emulating footswitches and Expression pedals

There are predefined MIDI commands which emulate the controls on the Helix, allowing external MIDI devices to remotely operate the Helix’s switches and pedals.

This can be beneficial if you want to automate activation of the Helix footswitches and EXP pedals from a computer, an iOS device, or a MIDI controller.

**Expression Pedal Emulation**
* A CC1{value} command will emulate expression pedal #1
* A CC2{value} command will emulate expression pedal #2
* A CC3{value} command will emulate expression pedal #3

In all three expression pedal messages, the value sent relates to the position of the pedal. Where a value of 0 relates to 100% heel down, and a value of 127 relates to 100% toe down.

**Footswitch Emulation**
* Footswitch emulation commands only function against the “Stomp” footswitches. If the Helix is in “Snapshot” or “Preset” mode, sending these CC commands will toggle the “Stomp” footswitch even though the stomps are not being displayed on the Helix.
* CC49{value} though CC53{value} commands will toggle the state of the Helix’s 1-5 stomp footswitches.
	* CC49 will control footswitch 1 …. CC53 controls footswitch 5
* CC54{value} through CC58{value} commands will toggle the state of the Helix’s 7-11 stomp footswitches
	* CC54 will control footswitch 7 …. CC58 controls footswitch 11
* The value here isn’t important, as any value will simply toggle the footswitch’s state; just like stepping on the footswitch with your foot.
* There is no discrete MIDI command function for a footswitch’s on/off state; this means you cannot send a footswitch “on” or “off” command, as any command sent on these CC numbers will always toggle the footswitch to the alternate state.

**Expression Pedal Footswitch Emulation**
* A CC59{value} command will emulate the expression pedal’s toe switch.
	* Just like the footswitch emulation, there are no discrete MIDI commands for on/off, and a CC59 command sent with any value between 0 – 127 will simply toggle the EXP pedal toe switch on/off.

**Tuner toggle**
* A CC68{value} command, sent with any value between 0 – 127, will toggle the Helix tuner on/off

**Tap Tempo**
* A CC64{value} command, sent with any value between 0 – 127 will emulate the TAP footswitch being pressed a single time.

#### 3.1.3 - Looper Controls

The Helix looper can be controlled with MIDI commands as well. This works by triggering the Looper’s controls with specific MIDI CC commands. These commands work for both the “1 Switch Looper” and the “6 Switch Looper”.

The Looper mode does not need to be entered on the Helix to control the Looper functions with MIDI. So an external MIDI controller can be connected, and MIDI commands configured on it, to enable Helix Looper control without ever accessing the Looper mode on the Helix directly.

This means the Helix footswitches can continue to display Presets, Snapshots, and/or Stomps while the Looper is being controlled though MIDI.

**The following functions are available for Looper MIDI control:**
* **Record/Overdub switch (FS8)**
	* A CC60{value} command will trigger the “Record” function, when the value being sent is between 64 – 127
	* A CC60{value} command will trigger the “Overdub” function, when the value being sent is between 0 – 63
* **Play/Stop switch (FS9)**
	* A CC61{value} command will trigger the “Stop” function, when the value being sent is between 0 – 63
	* A CC61{value} command will trigger the “Play” function, when the value being sent is between 64 – 127
* **Play Once switch (FS3)**
	* A CC62{value} command will trigger the “Play Once” function, when the value being sent is between 64 – 127
* **Undo switch (FS2)**
	* A CC63{value} command will trigger the “Undo” function, when the value being sent is between 64 – 127
* **Forward/Reverse switch (FS11)**
	* A CC65{value} command will trigger the “Forward” function, when the value being sent is between 0 – 63
	* A CC65{value} command will trigger the “Reverse” function, when the value being sent is between 64 – 127
* **Full/Half Speed switch (FS10)**
	* A CC66{value} command will trigger the “Full Speed” function, when the value being sent is between 0 – 63
	* A CC66{value} command will trigger the “Half Speed” function, when the value being sent is between 64 – 127
* **Looper Mode on/off (This just changes the Helix to display the Looper controls)**
	* A CC67{value} command will trigger the “Looper Mode On” function, when the value being sent is between 64 – 127
	* A CC67{value} command will trigger the “Looper Mode Off” function, when the value being sent is between 0 – 63

#### 3.1.4 - MIDI clock (receive)

MIDI clock is a way for devices on a MIDI network to all synchronize to the same tempo/BPM. This is not a PC or a CC message, but is a stream of constant information being sent across the network.

The Helix BPM can be controlled by external MIDI clock messages, or by the TAP Tempo functionality. The Helix cannot receive a CC or a PC message to set tempo to a specified value.

In global setting, the “Receive MIDI Clock” setting can be set to Off, USB, MIDI, or Auto. When set to something other than “Off”, and a MIDI Clock signal is detected on that connection, the Helix will change its tempo to match the signal being received.

**When the Helix tempo is being controlled by an external MIDI clock:**
* The TAP LED on the Helix will flash blue instead of red
* The BPM no longer displays in the Tempo screen’s “BPM” parameter, but instead will show “External”. You can access the Tempo screen by quickly touching the TAP/Tuner footswitch. ▪ You cannot tap in an alternate BPM by tapping on the TAP footswitch, as the BPM will be locked to the external MIDI clock signal

### 3.2 - Custom MIDI Command Assignments

In addition to predefined MIDI commands being used to control the Helix functions, custom MIDI controller assignments can also be setup to control signal path blocks and parameters.

> NOTE: You cannot assign CC numbers which are used in the predefined commands, to be used as custom commands. For example: Because CC64 is used to emulate the TAP footswitch, CC64 is not available for custom assignments to other functions.

#### 3.2.1 - Controlling Block Bypass States

* MIDI commands can control a block’s bypass state. This is configured by selecting a block, pressing the [Menu] button (sometimes called the ‘hamburger’ button due to the three lines on it), and then pressing in on encoder knob #1. The “Bypass Assign” screen will display, and encoder knob #4 can be used to assign a MIDI CC# to the bypass function of the block.
* This CC assignment enables discrete bypass/activate functionality:
	* The CC# with a value between 0 - 63 will bypass the block.
	* The CC# with a value between 64 - 127 will activate the block.

#### 3.2.2 - Controlling Block Parameters

* A MIDI command can also be used to control individual block parameters. To configure this, enter the “Controller Assign” screen, then:
	1. Use encoder knob #1 to select the parameter to be controlled with MIDI
	2. Use encoder knob #2 to select “MIDI CC” as the “Controller”
	3. Use encoder knob #3 to select the CC# to use for controlling the parameter
	4. Use encoder knob #4 to set the “Min” value for the parameter’s sweep
	5. Use encoder knob #5 to set the “Max” value for the parameter’s sweep
* The “Min” and “Max” parameters of the controller assignment are used to specify the range of the parameter’s value when being controlled with MIDI CC.
	* As an example: If an Amp block’s “Drive” parameter was set for CC4 control, with the “Min” set to 4 and the “Max” set to 8, Then:
		* CC4 with a value of {0} would set “Drive” to 4
		* CC4 with a value of {127} would set “Drive” to 8
		* CC4 with a value of {64} would set “Drive” to 6
		* All other CC4{values} between 0 - 127 are valid, and would set the “Drive” parameter according to the range specified by the Min and Max values.

> QUICK TIP: To access “Controller Assign”, select a block then press & hold the encoder knob for the parameter you wish to control.

## 4 - Outgoing MIDI Messages

The Helix can send MIDI messages over a USB connection, or through a 5-pin DIN cable plugged into the “MIDI Out/Thru” jack on the back of the unit.

Outgoing messages are configured with a MIDI channel for the message to be sent across. If the outgoing message is configured for channel “Base”, it means the message will be sent on the MIDI channel currently selected in “Global Settings” for the “MIDI Base Channel” parameter.

Each device on the MIDI network should have its own unique channel assigned, unless you want more than one device to respond to messages being sent. So when configuring Helix outgoing MIDI messages, you should choose the MIDI channel of the receiving device.

### 4.1 - Default MIDI Messages When Recalling Presets

Whenever a preset is recalled, the Helix can send out default MIDI messages. This default MIDI messages consist of a CC32{value} message (where the value corresponds to the setlist the preset is within) as well as a PC{value} message (where the value corresponds to the position of the preset within the setlist).

Recalling a preset that doesn’t have a Looper block in the signal path, will also cause a CC61{0} message to be sent, which is the Looper’s stop command.

These default messages are sent out on whatever channel is set for the Helix’s “MIDI Base Channel” in “Global Settings”, and cannot be changed to send on a different channel.

These default messages are only sent when the “MIDI PC Send” setting within Global Settings is set to something other than “Off”. It can be set to USB, MIDI, or Both. When set to MIDI, these default messages are sent over the 5-pin DIN connector only. When set to USB, these default messages are sent over the USB only, and when set to both, these default messages are sent over both the USB and the 5- pin DIN connector.

Note that these default MIDI message are the only place in the Helix where you can configure a MIDI message to be sent on only USB or only the 5-pin MIDI DIN connector. In all other cases where MIDI is sent out of the Helix, it will always go over both connection types, assuming “MIDI Over USB” is turned on in “Global Settings”.

This is important to know if you have one MIDI device connected VIA USB, and a 2nd MIDI device connected VIA the 5-pin DIN connector, and you only want PC messages sent to one of those devices upon preset recall. These default messages are the only way to accomplish doing this.

**The default MIDI messages are:**
* CC32{value} – representing the Setlist the preset is within, where value is between 0 – 7
* PC{value} – representing the preset location within the setlist, where value is between 0 – 127
* CC61{0} – This is the Looper’s stop command, and is only sent when the preset being recalled doesn’t have a looper block in the routing path.

### 4.2 - Command Center (not available on HX Stomp)

> NOTE: The “Command Center” Is not available on the HX Stomp device.

#### 4.2.1 - Overview

The Command Center is used to configure MIDI commands to be sent out by the Helix when specific actions occur. All MIDI commands sent out from the Command Center will go over both the 5-pin MIDI DIN connector, as well as the USB connection, so long as “MIDI Over USB” is turned on in Global Settings. There is no way to specify a Command Center command to only use USB or to only use the 5-pin DIN connection.

When viewing the Command Center screen, a small “Triangle” icon shown above any of the items indicates a command assignment has been made on that item.

The Command Center is entered by pressing the [Menu] button on the Helix, then pressing down on the 3nd encoder knob, labeled “Command Center”.

#### 4.2.2 - Screen Layout

Once entered, the Command Center screen will show various items to which commands can be assigned. The top row shows 6 lightning bolts, which represent “Instant Commands”.

Below the Instant Commands are two rows of 6 footswitches. These represent the 12 footswitches on the Helix.

To the right of the Instant Commands, is another footswitch. This represents the integrated expression pedal’s toe switch, which is activated by pressing hard on the toe end of the integrated expression pedal.

Also on the screen are three expression pedals. They represent expression pedals 1, 2 and 3 (from left to right).

Lastly, there are two knobs above expression pedals 2 and 3. These knobs represent Variax control knobs found on Variax guitars, and can only be utilized if you have a Variax guitar connected VIA the “Variax Input” jack on the back of the Helix.

#### 4.2.3 - Instant Commands

Instant Commands are MIDI messages saved on the individual Preset, and are sent out of the Helix when Presets and Snapshots are recalled.

**Instant Commands are sent under the following circumstances:**
* When Presets are recalled, all assigned Instant Commands will be sent.
* When Snapshots are recalled, all, some, or none of the Instant Commands may be sent; depending on the configuration of the commands.
* Reselecting the currently active Preset will retrigger the Instant Commands to be sent, just as when the preset is initially recalled.

From a timing perspective, the Helix sends Instant Commands in order from left to right. So if the order of commands is important to the receiving device, be sure to assign them in Command Center accordingly.

To setup an Instant Command, access the Command Center screen and use the Helix joystick to select one of the lightning bolt icons, then use the encoder knobs to configure the MIDI command to be assigned.

**Available messages for Instant Commands are:**
* MIDI CC
	* Upon preset recall, the configured CC MIDI message will always be sent
	* Upon snapshot recall, the configured CC MIDI message will only be sent if the snapshot being recalled has a different value than the currently active snapshot
	* The CC value can be changed across snapshots, but the CC number itself cannot
* Bank/Prog – (PC Messages)
	* Upon preset recall, the configured PC MIDI message will always be sent
	* Upon snapshot recall, the configured PC MIDI message will only be sent if the snapshot being recalled has a different value than the currently active snapshot. The exception to this is if the “Duplicate PC Send” option in Global Settings is turned “On”. In that case, the PC message will be sent when changing snapshots, even if the PC value isn’t different between the 2 snapshots.
	* Note this MIDI message has more than just a “Program” (or PC) parameter available. In addition to encoder #4 (where the PC’s {value} is set) it also shows two additional parameters of “Bank CC00” and “Bank CC32”. These are specific to Bank and Program change message which some MIDI devices require to successfully execute a bank or program change over MIDI. Without getting into too much detail, the CC00 command represents the MSB (Most Significant Bit) message for the bank change, and the CC32 command represents the LSB (Least Significant Bit) message for the bank change. In many cases, these two parameters can be left “Off”, but may need specific values depending on the receiving device’s requirements.
* MMC
	* An MMC message is a “MIDI Machine Control” message. It’s typically used to control playback of a DAW or external looper (ie: transport control).
	* It sends out a predefined “Transport Control” message for the command.
	* The available MMC messages are: Stop, Play, Forward, Rewind, and Pause
* Ext Amp
	* This command doesn’t actually send any MIDI messages out, but is used to control the signal on the “EXT AMP” connection on the back of the Helix.
	* The “EXT AMP” connection can be used to control amplifiers, or other devices, where a ¼” signal cable is used to trigger specific functions. For example, trigger the tremolo circuit on a tremolo equipped amplifier, or trigger a function on an external pedal that supports expansion foot switches over ¼”, etc …
* CV Out
	* Like the “Ext Amp” command, the “CV Out” command also does not send any MIDI messages out, but is used to control the “CV” connection on the back of the Helix.
	* The “CV” connection can be used to control devices which have a ¼” expression pedal input jack. It’s essentially a simulated expression pedal. The “CV Out” message type in Command Center is then used to define the simulated expression pedal’s position.
	* The “CV Value” parameter on the “CV Out” command relates to the position of the simulated expression pedal.

Note that MIDI command types: “CV toggle”, “Note on”, and “CC Toggle” are NOT available for assignment as Instant Commands, but ARE available for assignment to the footswitch icons.

How Instant Commands react with snapshot changes can be confusing. The simplest way to think of this is: A MIDI message can be assigned to an Instant Command within a preset, and the value of the message can be changed between the snapshots of that preset. But the MIDI command type itself cannot be changed across snapshots.

So if a MIDI CC command is assigned to Instant Command #1, and CC#4 is chosen … it must remain as a MIDI CC#4 command for all snapshots within the preset, although the value of the CC#4 command can be changed per snapshot.

When changing snapshots in a preset where Instant Commands have been configured, only the Instant Commands where the value is changing due to being set differently in both snapshots, will be sent out of the Helix when the snapshot is recalled. The only exception to this are PC messages, when the “Duplicate PC Send” option is turned on in Global Settings.

To configure different values between snapshots, simply change the value parameter in the MIDI message, and then be sure the snapshot is saved. There is no need to press down on the encoder knob to do this, and brackets [ ] will not appear around the value like they do when a parameter is assigned to a snapshot controller.

It’s important to know how the “Snapshot Edits” setting in Global Settings is set when configuring Instant Commands for snapshot control.

* If “Snapshot Edits” is set to “Recall”, then any changes made to Instant Command parameters will be automatically saved for the currently loaded snapshot.
* If “Snapshot Edits” is set to “Discard”, then the `Save` button on the helix must be pressed twice, to save the preset, and for the changes to be saved on the snapshot.

As a step-by-step for setting different Instant Command values on two Snapshots:
1. Select a snapshot within a preset
2. Access the “Command Center” screen
3. Create an Instant Command assignment, and adjust the values.
4. Remember to save the preset if “Snapshot Edits” is set to “Discard” in Global Settings before changing to another snapshot.
5. Change to a different snapshot within the same preset
6. Adjust the value (and only the value) of the same Instant Command assignment setup in step #3
7. Remember to save again if “Snapshot Edits” is set to “Discard”.
8. Now flip between the two snapshots, and the value will change to what was set for each.

To remove Instant Commands assignments, change the “Command” parameter to “none” and ensure the preset is saved. Things can quickly become confusing if you have different values across different snapshots. So if things start acting strange or unpredictable, clear the Instant Command by selecting “None”, then save the preset, and start over.

If an Instant Command is setup to change values between two snapshots, but not the other 6: Then when changing to a snapshot where a value has not been defined, the screen will continue to show the last value which was sent, and the MIDI Instant Command message will not be resent.

#### 4.2.4 - Footswitch Commands

Footswitch Commands are similar to Instant Commands, in that they are stored on the individual Presets, but they are not sent when the Preset or Snapshot is recalled. There are a few exceptions to this, where a MIDI command assigned to a Command Center Footswitch may be sent upon Preset or Snapshot recall, but in general, they are not. Instead, since they are assigned to a physical Helix footswitch, and are sent when that footswitch is activated.

It’s important to note that Footswitch Command assignments in Command Center will not be sent if the footswitch the command is assigned to is in Snapshot mode. So if a footswitch is displaying “Snapshot #”, and a command is assigned to that footswitch in Command Center, the command will not be sent when that footswitch is pressed.

Footswitch Commands can be assigned to all 12 footswitches, and will be sent when the footswitch is activated, in all cases, other than when the footswitch is in Snapshot mode. Even the “Mode”, “Tap”, “Bank ^” and “Bank v” footswitch can have MIDI commands assigned to them in the Command Center.

Another interesting point is if a footswitch is triggered remotely, through a MIDI command by an external device using the “Footswitch Emulation” MIDI feature of the Helix, any commands assigned to that footswitch in the Command Center will also be triggered.

Most other features of Footswitch Command Center assignments are the same as Instant Commands, in that the values of the commands can be customized per snapshot, and the “Snapshot Edits” setting within Global Settings impacts if the changes must be saved before changing to another Snapshot. Even the state of the footswitch itself can be saved on a per snapshot basis.

**Footswitch Commands are sent under the following circumstances:**
* When the physical footswitch is activated
* When the footswitch is triggered through a MIDI command
* When Presets are recalled, the following command types will automatically trigger, and send the appropriate toggle value corresponding to the state of the footswitch when the Preset was last saved:
	* CC Toggle
	* CV Toggle

> The “Snapshot Recall” function of CC Toggle has a bug in firmware 2.54, but is fixed in 2.60.

* When Snapshots are recalled, the following command types should be automatically sent, but only if the state of the footswitch to which the command is assigned is different on the current state versus the snapshot being recalled.
	* CC Toggle
	* CV Toggle
	* Note On (when in Latching mode)

To setup a Footswitch Command, access the Command Center screen and use the Helix joystick to select one of the 12 footswitches, then use the encoder knobs to configure the MIDI command to be assigned.

Available MIDI messages for Footswitch assignment are the same as Instant Commands, but also include three additional types:

* CC Toggle
	* A “CC Toggle” is used to toggle the value of a CC message back and forth with each press of the footswitch. The LED of the assigned footswitch will toggle as well. This cannot be used to toggle the CC number itself, but only the value assigned to the CC Toggle command.
	* Upon preset recall, the configured CC Toggle MIDI message will be sent
	* Upon Snapshot recall, the value of the CC Toggle command, which was saved with the snapshot, will be sent; but only if the current state of the footswitch does not match the saved state in the snapshot being recalled.
	* The CC value can be changed across snapshots, but the CC number itself cannot
	* *Note:* In some cases, the footswitch’s LED for a CC Toggle command can become out of sync when switching snapshots, where the Lit value is sent when the footswitch is changed to Dim, and the Dim value is sent when the footswitch changes to Lit.
* CV Toggle
	* Like the “CV Out” command, the “CV Toggle” command does not send any MIDI messages out, but is used to control the “CV” connection on the back of the Helix. The “CV Toggle” command type is used to toggle the value of a CV command back and forth, with each press of the footswitch.
	* The “CV” connection can be used to control devices which have a ¼” expression pedal input jack. It essentially simulates an expression pedal. The “CV Toggle” message type in Command Center can then used to define the simulated expression pedal’s position.
	* The “Dim Value” and “Lit Value” parameters on the “CV Toggle” command relate to the position of the simulated expression pedal when the assigned footswitch is Lit and when it is not Lit (or is Dim).
* Note On
	* The “Note On” command can be used to send MIDI note messages.
	* This is useful when controlling a software instrument on a remote device.

#### 4.2.5 - Expression Pedals

The three expression pedals in the Command Center relate to EXP 1, EXP 2, and EXP 3. When assignments are made to these, the messages will be sent when the expression pedals are activated.

EXP 1 and EXP 2 can be controlled using the integrated expression pedal, and switching between the two is done by activating the expression pedal’s toe switch. As an alternative, an external pedal can be plugged into the EXP 2 jack on the back of the Helix for controlling Expression Pedal 2.

EXP 3 can only be controlled by utilizing an external expression pedal plugged into the EXP 3 jack on the back of the Helix.

Unlike Instant Commands and Footswitch Commands, the values set to Expression Pedal Commands within the Command Center cannot be different between snapshots.

Available commands for the expressional pedals are:
* MIDI CC
	* Will send a range of MIDI CC messages when the expression pedal is operated
* CV Out
	* Will send a range of CV messages, through the CV connection on the back of the Helix, when the expression pedal is operated.

#### 4.2.6 - Variax knobs

There are two Variax Knob Controllers in the Command Center. One is the Variax volume knob, and the other is the Variax tone knob. These operate just like the expression pedals, and have the same commands available.

Like the Expression Pedal Controllers, the Variax Knob Controllers also cannot have different values for different snapshots.

#### 4.2.7 - Expression Pedal toe switch

The footswitch icon located above expression pedal 1 represents the toe switch of the integrated expression pedal. This operates just like the other 12 footswitch controllers, and has all the same command options and limitations as the other Footswitch Controllers.

### 4.3 - MIDI clock (send)

The Helix has the ability to send MIDI clock messages to other devices. This can be configured to send over the USB connection, the 5-pin DIN connection, or both. This option is controlled in “Global Settings”, in the “MIDI/Tempo” section. Turn encoder knob #4 to set this to “Off”, “MIDI”, “USB”, or “MIDI + USB”.

The Helix can send the tempo/BPM signal out regardless on how the Helix is setting BPM. Regardless if it’s set for global, or per preset, or per snapshot, or if it’s receiving Global Clock from an external device over MIDI …. the Helix can still pass MIDI Clock to other MIDI devices.

For example, the Helix could be connected to a computer or an iOS device over USB, and receive MIDI clock through this connection. Then the Helix could pass the MIDI Clock signal it is receiving over USB, to other devices over the 5-pin DIN connection.

### 4.4 - MIDI Thru

Since MIDI is a network of devices, sometimes devices will be daisy chained together. This means not all messages being sent over the network are intended for every device on the network. The Helix has the ability to pick up and process the messages intended for it, but also pass all MIDI traffic through to other devices.

For example: If the Helix is connected to a computer running a DAW, over USB, and that DAW needs to send messages to the Helix and a keyboard; the keyboard could be connected to the Helix’s MIDI “Out/Thru” 5-pin DIN connector, and the “MIDI Thru” option switched on.

Even though the Helix and the keyboard would have different MIDI channels assigned to them, the DAW on the computer could send messages to the keyboard, by having them pass through the Helix.

This option is found in “Global Settings”, the “MIDI/Tempo” section.

## 5 - Other Key Details

Details which do not fit within other categories of this document

### 5.1 - Checking Firmware Version

Within this document, there have been several mentions of various firmware revisions. To check the firmware version of a Helix unit, enter Global Settings by pressing the [MENU] button, then pressing the “Global Settings” encoder knob. The top right corner of the screen will display the firmware version currently loaded on the Helix.

### 5.2 - Creating a MIDI Loopback Connection

By default, the Helix cannot send MIDI commands to itself. Outgoing MIDI message from the Helix are not treated as incoming MIDI messages. You can use a 5-pin MIDI DIN cable to physically connect the “MIDI Out/Thru” jack on the back of the Helix to the “MIDI In” jack, to enable the Helix to send MIDI commands to itself.

When creating this loopback type connection, it is important to configure Global Settings in such a way where an endless loop of MIDI commands does not happen. If this does happen, the Helix will appear to freeze up, and none of the footswitches, knobs or buttons will function; although sound will continue to pass through.

If this endless loop does occur, simply unplugging one end of the MIDI cable from the back of the Helix will resolve the frozen condition.

When using a MIDI loopback setup, The Global Settings should be configured as follows to prevent the endless loop scenario:

* “MIDI Thru”
	* This should be switched “Off”
* “Receive MIDI Clock”
	* This should be switched “Off”, or set to “USB”, but not to “MIDI” or “Auto”.
Send MIDI Clock”
	* This should be switched “Off”, or set to “USB”, but not to “MIDI” or “MIDI+USB”.
* “MIDI PC Receive”
	* This should be switched “Off”, or set to “USB”, but not to “MIDI” or “MIDI+USB”.
* “MIDI PC Send”
	* This should be switched “Off”, or set to “USB”, but not to “MIDI” or “MIDI+USB”.

### 5.3 - MIDI message order

When various MIDI messages are configured to be sent from the Helix, and a preset is recalled, the messages are sent in a particular order. This is very important if you are configuring the Helix to control an external device, and want the external device’s bank selection to be in sync with the Helix preset selection. Note the version of firmware on the Helix has an impact on this message order.

When a preset is recalled, MIDI messages are sent in the following sequence:

***Firmware 2.54 and Previous***

1. Command center commands are sent 1st
	1. Instant Commands are sent 1st, in order from left to right
	2. Footswitch toggle commands are sent 2nd, in order of footswitch 1 – 12
2. The CC61(0) looper stop command is sent next, but only if a looper block doesn’t exist within the preset being recalled
3. The default “Bank Selection” messages are sent last (if “MIDI PC Send” is not turned “Off”)
	1. 1st is the bank MSB message which is CC0(0)
	2. 2nd is the bank LSB message which is the setlist selection message of CC32{value}
	3. Last is the bank PC message which is the preset location message of PC{value}

So if you are relying on the default PC messages to change an external device’s bank, and then also have Command Center commands established in your presets; Those Command Center commands will not be sent to the bank being recalled on the external device, but will be sent prior to the bank being recalled. To work around this, turn the “MIDI PC Send” option off in Global Settings, and then use the very 1st Instant Command to send the PC “Bank Change” message to the external device.

***Firmware 2.60 and Later***

1. The default “Bank Selection” messages are sent first (if “MIDI PC Send” is not turned “Off”)
	1. 1st is the bank MSB message which is CC0(0)
	2. 2nd is the bank LSB message which is the setlist selection message of CC32{value}
	3. Last is the bank PC message which is the preset location message of PC{value}
2. Command center commands are sent 2nd
	1. Instant Commands are sent 1st, in order from left to right
	2. Footswitch toggle commands are sent 2nd, in order of footswitch 1 – 12
3. The CC61(0) looper stop command is sent next, but only if a looper block doesn’t exist within the preset being recalled

Firmware 2.60 changed the order of MIDI messages to be more logical, where the default MIDI messages which are sent when a Preset is recalled, are now sent out 1st, followed by any Command Center messages second. This solves synchronization issues when trying to align Helix Presets with an external device’s bank selection with MIDI commands.

## 6 - Command Center Snapshot Assignment Issue

> This issue has been fixed in firmware 2.60

Within the Command Center, on firmware 2.54 (and maybe previous), there is an issue where Instant Command assignments, which have varying values across snapshots, will all change to the same value under a particular circumstance. This happens when a new Instant Command is selected for the same command which has already been setup on a different Instant Command

For example: If you setup Instant Command #1 with a CC10 command, and assign value of 10 for snapshot 1, a value of 20 for snapshot 2, and a value of 30 for snapshot 3; and then save the preset. After doing this, a CC10 command will be sent by Instant Command #1 when you select snapshots 1, 2 or 3; and the value of the command will be 10, 20 or 30, depending on which snapshot is selected.

The issue arises if you then go to a new Instant Command, and simply scroll the encoder knob to select a command for CC10. Even if you don’t save the preset, but just scroll the encoder to display CC10, the issue occurs. What happens is the previously configured CC10 command for snapshots 1, 2, and 3 will all be changed to have the same value and will lose the snapshot values 10,20 and 30 which were assigned to them.

To fix this, the user must go back and set the snapshot assignments again for the Instant Command.

## 7 - MIDI CC Quick Reference Charts

### 7.1 - Pedal and Footswitch assignments

| Midi CC# | Value | Function | Helix Rack | Helix Floor | Helix LT | HX Effects | HX Stomp |
| -- | -- | -- |-- | -- | -- | -- | -- |
| **1** | 0-127 | Emulate EXP 1 Pedal | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **2** | 0-127 | Emulate EXP 2 Pedal | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **3** | 0-127 | Emulate EXP 3 Pedal | ✔️ | ✔️ |  |  |  |
| **49** | 0-127 | Emulate FS1 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **50** | 0-127 | Emulate FS2 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **51** | 0-127 | Emulate FS3 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **52** | 0-127 | Emulate FS4 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **53** | 0-127 | Emulate FS5 | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **54** | 0-127 | Emulate FS6 | | | | ✔️ | |
| **55** | 0-127 | Emulate FS7 | ✔️ | ✔️ | ✔️ | | |
| **56** | 0-127 | Emulate FS8 | ✔️ | ✔️ | ✔️ | | |
| **57** | 0-127 | Emulate FS9 | ✔️ | ✔️ | ✔️ | | |
| **58** | 0-127 | Emulate FS10 | ✔️ | ✔️ | ✔️ | | |

### 7.2 - Looper Controls

| Midi CC# | Value | Function | Helix Rack | Helix Floor | Helix LT | HX Effects | HX Stomp |
| -- | -- | -- |-- | -- | -- | -- | -- |
| **60** | 0-63 | Overdub | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **60** | 64-127 | Record | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **61** | 0-63 | Stop | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **61** | 64-127 | Play | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **62** | 64-127 | Play Once | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **63** | 64-127 | Undo | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **65** | 0-63 | Forward | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **65** | 64-127 | Reverse | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **66** | 0-63 | Full speed | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **66** | 64-127 | Half speed | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **67** | 0-63 | Exit Looper FS Mode | ✔️ | ✔️ | ✔️ | ✔️ | |
| **67** | 64-127 | Enter Looper FS Mode | ✔️ | ✔️ | ✔️ | ✔️ | |

### 7.3 - Additional Controls

| Midi CC# | Value | Function | Helix Rack | Helix Floor | Helix LT | HX Effects | HX Stomp |
| -- | -- | -- |-- | -- | -- | -- | -- |
| **0** | N/A | MSB - Reserved | | | | | |
| **32** | 0-7 | LSB - Setlist Selection | ✔️ | ✔️ | ✔️ | | |
| **64** | 64-127 | Tap Footswitch Emulation | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **68** | 0-127 | Toggle Tuner Screen | ✔️ | ✔️ | ✔️ | ✔️ | ✔️ |
| **69** | 0-7 | Snapshot Selection | ✔️ | ✔️ | ✔️ | 0-3 only | 0-2 only |
| **69** | 8 | Next Snapshot | | | | | ✔️ |
| **69** | 9 | Previous Snapshot | | | | | ✔️ |
| **70** | 0-63 | Bypass On | | | | ✔️ | ✔️ |
| **70** | 64-127 | Bypass Off | | | | ✔️ | ✔️ |
| **71** | 0 | FS Mode: Stomp | | | | | ✔️ |
| **71** | 1 | FS Mode: Scroll | | | | | ✔️ |
| **71** | 2 | FS Mode: Preset | | | | | ✔️ |
| **71** | 3 | FS Mode: Snapshot Select | | | | | ✔️ |
| **71** | 4 | Next FS Mode | | | | | ✔️ |
| **71** | 5 | Previous FS Mode | | | | | ✔️ |