# PCB Design for Hardware Control of Confocal.nl AION in Bypass Mode
## Problem
The AION microscope is controlled by Confocal.nl's own controller. The Confocal.nl controller can also be controlled by the Inscoper Device Controller. The Inscoper Device Controller can control other devices as well. For this reason, the AION can be set into an _optical bypass mode_, which allows accessing the cameras through the AION without making use of the ReScan, to instead perform other kinds of microscopy. However, the cameras and the laser lines (analog and digital) are normally wired to the Confocal.nl controller. This limits the way in which the cameras and the laser lines can be controlled at the level of the Inscoper Device Controller for use in other applications.
## Solution
For two controllers to send signals to the same device, multiplexing is necessary. In the case of the camera trigger (digital) signal, it is not possible to simply use a T-connector because if one controller is high and the other is low a large short-circuit current would flow directly between the two controllers' outputs as they fight each other, potentially damaging the controllers, while the shared node would sit at an undefined voltage that the device could not reliably interpret as high or low.
## Requirements
**⚠️ Those are for my particular case, please check the documentation of your devices accordingly.**
### Devices To Multiplex
#### Oxxius L6Cc With 4 Laser Lines
- 4 digital lines
- 4 analog lines
- Voltage Range (analog and digital): 0-5V
- Input Impedance (analog and digital): 500Ω
#### 2x Kinetix (10Mpix Version)
- 2 digital lines (Trigger In) / 1 per camera
- Voltage Range: 0-5V
- Input Impedance: Unknown
  - For reference, a similar ORCA-Fusion BT has an impedance of 10kΩ
### Selected ICs
#### Digital Multiplexing
`SN74HC157N` is a 4 x 2:1 digital multiplexer, with a single `SELECT` bit. Two will be used for a total of 8 (2:1) digital channels. Although only 6 are required.
#### Analog Multiplexing
`CD74HC4053E` is 3 x 2:1 analog multiplexer, with three `SELECT` bits (1 per channel). Two will be used for a total of 6 (2:1) analog channels. Although only 4 are required.
### PCB Design Rationale
Assuming up to two lasers are operated simultaneously, the current drawn from 2 digital and 2 analog lines is 40mA. On top of that, the two cameras only contribute 1mA (assuming 10kΩ). Multiplying by a safety factor of 1.2 that is about 50mA. The power will be supplied to the board via the Inscoper Device Controller directly.

All the `SELECT` bits are common and a single `SELECT` signal is also taken from the Inscoper Device Controller. This signal effectively switches between AION control and Inscoper control (for the cameras and lasers).

The `SELECT` line is pulled down by a 10kΩ resistor, defaulting to LOW, which corresponds to AION (for my use case).

100nF capacitors are used on each IC for decoupling.

Vertical through-hole SMA connectors will be soldered to the PCB. The PCB will be mounted in a case and a front panel will have SMA (inside facing) to SMB (outside facing) connectors.
