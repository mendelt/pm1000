# PM1000 PSU

KiCad project for the PM1000 power supply.

## Purpose

This power supply is intended to provide the DC rails for two PM1000 preamp modules and an additional phantom-power board. It accepts a transformer secondary voltage of 36 Vrms total, whether implemented as 2 × 18 V or 1 × 36 V, rectifies that AC to raw DC, and then creates the regulated rails needed by the modules. The circuit produces two separate regulated rails from the same rectified supply: the main 44 V rail that powers the PM1000 boards, and the phantom rail that feeds the microphone accessory board.

Each PM1000 board nominally draws about 20 mA, with a theoretical maximum of about twice that value, so the 44 power rail must be able to supply around 80mA.

The phantom-power section is intended to feed a standard 48 V microphone arrangement for both channels, so it must be capable of providing enough current for two connected microphones. The phantom board will need a maximum current of roughly 15 mA, plus a little margin for the board itself.

The total current drawn from the input will be around 50 mA nominally. With a maximum of 100 mA, or around twice that.

## Schematic operation

This schematic is a small linear power supply intended to generate a regulated DC output and a phantom-feed output from an AC input. It is not a transformer-isolated switch-mode converter; it is a rectifier followed by LM317 linear regulators, with the output voltages determined by the resistor ratios in the regulator networks. The design is organized into three main sections: AC rectification, regulation, and status/indicator wiring.

### 1) AC to DC conversion

The input connector J2 is fed by a 36 Vrms transformer secondary, whether that is implemented as 2 × 18 V or 1 × 36 V AC. The peak voltage is therefore 36 × √2 ≈ 50.9 V. The bridge rectifier formed by D1 through D6 drops roughly two silicon junctions, or about 2 × 0.7 V = 1.4 V, so the capacitor charges to roughly 49.5 V at light load. The schematic uses a single 2200 µF reservoir capacitor on the raw DC bus. The remaining ripple is approximately I / (2 f C), so at 100 mA with a 100 Hz full-wave ripple the ripple is about 0.23 Vpp: 0.1 / (2 × 100 × 2.2e-3) ≈ 0.23 V. C3 and C4 are additional 100 nF decoupling capacitors placed close to the regulator inputs to help suppress switching noise and short transients.

The resulting raw rail is labeled as unregulated DC in the schematic and is the source for the regulated outputs, sitting at roughly 49 V DC with a maximum of 230 mV of ripple before the LM317 regulators.

### 2) 44 V regulated outputs

There are two independent 44 V regulator channels in this schematic. U1 and U2 are separate LM317 adjustable regulators, each configured around its own resistor network and local filtering components to generate a regulated 44 V output. The resistor networks around the output/adjust nodes are the key setting: in the 44 V channels the LM317 uses an 8.2 kΩ resistor from output to adjust and a 240 Ω resistor from adjust to ground. The formula is Vout = 1.25 × (1 + Rtop / Rbottom), so for 8.2 kΩ and 240 Ω we get Vout = 1.25 × (1 + 8200 / 240) ≈ 44.0 V. This is the actual voltage set by the schematic, and it is the same ratio used for each of the two 44 V rails.

The phantom regulator U3 is a separate LM317 channel using the same 8.2 kΩ / 240 Ω feedback ratio, so it also regulates to approximately 44.0 V from the same raw DC supply. The two 44 V rails are brought out separately to J3 and J4, which are the two 44 V output connectors on the board, while the phantom rail remains isolated on its own LM317-derived output. At the maximum main load, each 44 V regulator must drop roughly 49.5 V raw DC down to 44 V and supply about 40 mA, so the dissipation in each channel is approximately (49.5 - 44) × 0.04 ≈ 0.22 W. That is still moderate for a TO-220 package.

### 3) Phantom power rail

U2 is a second LM317 regulator used for the phantom-feed section. The resistor values around R1, R2, and R3 set the output to the phantom voltage, and the network around C7, C8, C9, and C10 filters the rail and provides the local reservoir needed for a clean phantom output. The output is brought to J4, which is labeled `Phantom` and is connected to the `+Phantom` net.

This rail is used as a higher-voltage auxiliary supply intended for phantom-feed service. In the actual schematic, the phantom regulator has the same 8.2 kΩ / 240 Ω LM317 feedback ratio as the two 44 V rails, so its actual output is also approximately 44.0 V. That leaves about 5.5 V of headroom above the regulated output while the raw rectified bus sits at roughly 49.5 V, which is enough for a linear LM317 to remain in regulation under normal loading. With a 44 V phantom rail and a 15 mA maximum load, the phantom LM317 dissipates roughly (49.5 - 44) × 0.015 ≈ 0.083 W. The three regulators therefore dissipate roughly 0.22 W each at the 40 mA 44 V channel load and about 0.083 W in the phantom channel at the maximum phantom current.

### 4) LED indicators

D3 and D4 are the visible indicator LEDs in the schematic. They are fed through the resistor network R1 to R4, which are the current-limiting resistors for the LED branches rather than the LM317 feedback resistors. The exact resistor values in the schematic are 22 kΩ, and that network is used to set the indicator current in a way that is consistent with the low-current LED drive requirements. At a 44 V input rail and assuming a red LED forward drop of about 2 V, each LED branch current is approximately I = (44 - 2) / 22 000 ≈ 1.9 mA. With both indicator LEDs lit at the same time, the total LED load is roughly 2 × 1.9 mA ≈ 3.8 mA. In practice, the LED branches are intended only to provide visual status indication and do not carry the main regulator load current.

### 5) Protection

D7, D8, and D10 are diode elements placed around the rails to steer or clamp current and protect the regulators from reverse or transient conditions. They ensure that the regulated outputs are not exposed to damaging reverse-current or transient conditions while the raw rectified bus is present.
