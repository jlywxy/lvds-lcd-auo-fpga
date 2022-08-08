# Altera-Based FPGA LVDS LCD Driver
This project is driving LCDs with LVDS signal input(AUO G070VW01 V0) using Altera Cyclone IV EP4CE10F7C8N

## what could it do now
Display full-screen switching color animation.
<img src="demo.png" width=250>

## Important knowledge bases of signal timing
1. It's necessary to use PLL to generate clock within LCD specification requirements.
2. ALTLVDS_TX IP is used for LVDS transmitting, with settings of: 
* serialization factor is 7, <br>
* transmitting speed is 7 times of input clock, <br>
* output clock duty cycle is 57.<br>
However, this setting will generate 3 bits of data ahead of the output clock(compare to JEIDA 28bit format): DE bit(in the front of ALTLVDS IP) should be at position 21 but now using 18. <b>This is still not fixed</b>, because Questa couldn't simulate correct ALTLVDS waveforms therefore not able to find what's wrong with ALTLVDS configurations.

## Alerts of Connections and tramsmissions
1. LVDS signal lines should be twisted-pairs. When connecting twisted-pair lines to 2.54 pin headers of FPGA board, soldering of line contacts are needed to reduce conduction problem. Using separated 2.54 pin connector is not recommended, except dual-row 10-pin continuous pin connector shown in the demo pic.
2. Although using True LVDS transceiver in 2.5v banks is faithful, it's not very lucky to find those pin enough on your FPGA development board. Therefore, it may be ok to use pins of Emulated LVDS transceiver(LVDS_E_3R) WITHOUT resistor network (even bank with 3.3v VCCIO) to drive the screen. 
* Pins below was tested and works well similar to those True LVDS transceivers:<br>
`T14-T15, R13-T13, R12-T12, R11-T11, R10-T10 (Bank4)`<br>
`B13-A13, B12-A12, B11-A11, B10-A10, D9-C9 (Bank7)`,<br>
which means this FPGA could drive a 2-lane LVDS screen up to 1920*1200 resolution at 60Hz, with even one more 1-lane LVDS screen, even being able to add a LCD with RGB signal input.<br>
* Pins above tested are the most 10 impedent pins on my FPGA board, you could test pins on your board which are impedent as well.<br>
* There are still a bit difference between Emulated LVDS and True LVDS of Altera.<br>
True LVDS pins are paired and use true transmitting buffer, which means electric level of the pin is already satisfied with most LVDS specifications of LCDs. Emulated LVDS pins only uses two single-end pins without true buffers, that means electric level of two pins are LVTTL/LVCMOS(not confirmed) and opposite with each other, but still slightly satisfied some LCD LVDS signal requirement.<br>
For those LCDS with harsh requirements to signal specifications, using Emulated LVDS without 2.5v VCCIO and resistor network may not works every time.