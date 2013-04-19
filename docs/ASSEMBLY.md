Vehicle Interface Assembly Instructions
=======================================

Special Assembly Instructions
-----------------------------

*	Verify LED3 and LED4 have been installed  as right-angle LEDs - the LED
	package is ambiguous and can be mounted either way.

Assembly file output instructions
---------------------------------

*	Verify silkscreen and PCB Frame artwork reflects accurate revision
*	Update layer 125 text on top of each component should identify the part
	designator
*	Delete all content on layer (144) DrillLegend
*	run "drillegend.ulp": Use all default values, click OK.
*	Reselect layer (144) and move the newly created drill legend back onto the
	frame
*	Verify SSC-EAGLE-2Lyr_v1.0.0.3.dru is loaded as current design rules file
*	Turn on all copper and stopmask layers, turn off silkscreen layers (skip
	silkscreen error checking, silkscreen will be clipped by assembly house)
*	Preform DRC/ERC check
*	run "drillcfg.ulp": select "Inch" as output format.  Click OK.  Save.
*	run "excellon.cam" in the CAM processor, click "Process Job"
*	run "2LPlus-Sunstone.cam" in the CAM processor, click "Process Job"
*	Double check all gerber files using a gerber file viewer (like gerbv).
*	Deselect all layers, select only the following layers:
	(17)Pads, (18)Vias, (20)Dimension, (21)tPlace, (22)bPlace, (23)tOrigins,
	(24)bOrigins, (49)Reference, (51)tDocu, (52)bDocu, (125)tAssy
*	run "dxf.ulp": deselect "Fill Areas", make sure "mm" is selected under the
	"units" header.  Save the DXF file.
*	Deselect all layers, select only the following layers:
	(17)Pads, (18)Vias, (20)Dimension, (21)tPlace, (23)tOrigins, (48)Document,
	(49)Reference, (51)tDocu, (125)tAssy, (144)DrillLegend
*	Hit print (Ctrl+P), select "Print to File (PDF)" as the printer, select
	BT_Audio_iJoey_ASSY.pdf, paper size A4, scale factor 1.  Click OK.
*	Deselect all layers, select only the following layers:
	(1)Top, (16)Bottom, (17)Pads, (18)Vias, (20)Dimension, (21)tPlace,
	(22)bPlace, (25)tNames, (51)tDocu, (52)bDocu
*	Zoom in so that PCB is centered in the screen and fills the window as much
	as possible.
*	Select File->Export->Image.  Select BT_Audio_iJoey.brd.png.  Set
	"Resolution" to 600.  Set "Area" to "Window".
*	In the schematic window, hit print (Ctrl+P), select "Print to File (PDF)" as
	the printer, select BT_Audio_iJoey.sch.pdf, paper size A4, scale factor 1,
	page limit 1  Click OK.
*	In the schematic window, select File->Export->Partlist.  Select
	"BT_Audio_iJoey.parts.txt".  Click OK.
*	Manually compare "BT_Audio_iJoey.parts.txt" to "BT_Audio_iJoey.bom.xls".
	Make sure the two lists are consistent.
*	Open "CAN Translator Changelog + Testing.xls".  Update the file to reflect
	the new revision.
*	Create a zip archive with the following files (at least):
	*	BT_Audio_iJoey.sch.pdf
	*	BT_Audio_iJoey.brd.png
	*	BT_Audio_iJoey_ASSY.pdf
	*	BT_Audio_iJoey.tsp
	*	BT_Audio_iJoey.top
	*	BT_Audio_iJoey.smt
	*	BT_Audio_iJoey.smb
	*	BT_Audio_iJoey.slk
	*	BT_Audio_iJoey.oln
	*	BT_Audio_iJoey.gpi
	*	BT_Audio_iJoey.dxf
	*	BT_Audio_iJoey.drl
	*	BT_Audio_iJoey.dri
	*	BT_Audio_iJoey.drd
	*	BT_Audio_iJoey.bsp
	*	BT_Audio_iJoey.bsk
	*	BT_Audio_iJoey.bot
	*	BT_Audio_iJoey.bom.xls
	*	BT_Audio_iJoey.sch
	*	BT_Audio_iJoey.brd
	*	BT_Audio_Testing-Changelog.xls

Factory Testing Plan
--------------------

This testing plan will require a simple voltmeter as well as linux host of some
kind with USB, audio, and Bluetooth interfaces.  The voltmeter can be soldered
to a 2-pin 0.1" pitch male header, to be inserted into JP2 during testing.  Code
could be written using pyserial and pybluez to preform the test routine:

1.	Connect BT Audio Accessory to a USB power source
1.	Connect a meter to JP2, verify +3.3V rail is within range
1.	Check linux host (lsusb, /sys/bus/usb, /var/log/kern.log) to see if device
	has enumerated properly
1.	Preform [the WT32 configuration procedure in the Readme document](./README.html#how-to-program)
1.	Power-cycle the BT Audio Accessory
1.	Verify LED4 is lit RED
1.	Scan and pair with the BT Audio Accessory over Bluetooth
1.	Connect to the A2DP service
1.	Verify LED3 is lit GREEN and LED4 is not lit.
1.	Play audio through the A2DP Sink and record it using the Line IN of the
	linux host
1.	Preform audio analysis on the played verses recorded samples.
1.	Reconfigure the WT32 as an [A2DP Source using the Readme document](./README.html#a2dp-source-audio-from-microphone-to-bluetooth)
1.	Reconnect to the WT32 using bluetooth
1.	Play audio through the Line OUT of the linux host, and record using the A2DP
	Source
1.	Preform audio analysis on the played verses recorded samples.
1.	Reconfigure the WT32 as an [A2DP Sink using the Readme document](./README.html#a2dp-sink-audio-from-bluetooth-to-speaker)
