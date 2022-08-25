# Documentation for racking up Yamaha PM1000 channel strips

Requirements;
- Two channel strips in one 2U 19inch rack enclosure
- Internal power supply that provides 44V to two strips
  - We're assuming 100mA nominal power draw per channel strip 200mA peak (for now)
  - 44V (or 48V if possible) switchable phantom power
  - Uses 1x 44V or 2x 22V transformer (VA rating tbd)
- Phantom power is supplied via two 6k8 resistors and not via the center tap of the input transformer
- We're using the original Tamura input and output transformers where possible.
- Power switch on the front panel
- Power led on the front panel
- Phantom power switchable per channel
- Phantom power led per channel
- TS unbalanced instrument input

This project will include
- Instructions on how to prepare the channel strips
- PSU PCB design
- Front panel designs
