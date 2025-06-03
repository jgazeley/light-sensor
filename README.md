# light-sensor

**Wheatstone-bridge LDR -> LM358 op-amp -> ADC0804 -> SN74LS283/SN74LS86 8-bit adder/subtractor**  
Manual B-input via DIP switches, Add/Subtract mode, producing a 0–5 V (0–255) digital result with carry flag.

---
![PXL_20250603_034309282](https://github.com/user-attachments/assets/8954b8d3-f28e-432a-94f7-435bc8c79dbe)

---

## Overview

- **Light to Voltage (Input A):**  
  - LDR in a Wheatstone bridge (along with three 22 kΩ resistors).  
  - **LM358** buffers and scales the bridge output so **0 V = dark** and **5 V = bright**.  
  - A **10 kΩ trim pot** plus an optional **100 Ω “pull-down”** ensures exact 0 V floor and 5 V ceiling.  

- **Analog to Digital:**  
  - **ADC0804** (8-bit) reads the LM358 analog output (0–5 V) and outputs an 8-bit value (0…255).  
  - Three 100 pF capacitors (150 pF total) and a 10k resistor set the ADC clock.  
    _See typical applications on page 11 of [ADC0804 datasheet](https://www.ti.com/lit/ds/symlink/adc0804-n.pdf?HQS=dis-dk-null-digikeymode-dsf-pf-null-wwe&ts=1748971369363&ref_url=https%253A%252F%252Fwww.ti.com%252Fgeneral%252Fdocs%252Fsuppproductinfo.tsp%253FdistId%253D10%2526gotoUrl%253Dhttps%253A%252F%252Fwww.ti.com%252Flit%252Fgpn%252Fadc0804-n) (“Self-Clocking in Free-Running Mode”)._  

- **Input B:**  
  - Eight SPDT DIP switches let you choose any number between 0 and 255.  
  - A Mode switch flips between **Add** and **Subtract**.  

- **Adder/Subtractor Logic:**  
  - Two **SN74LS283N** (4-bit full adders) combined for 8 bits.  
  - Two **SN74LS86AN** (quad XOR gates) invert B-bits when subtracting and feed the initial carry-in.  
  - A Carry/Borrow LED indicates overflow (addition) or borrow (subtraction).  

- **Output Display:**  
  - One 10-segment LED bar-graph (only 8 segments used) shows bits 7…0 (MSB on the left).  
  - Carry/Borrow LED shows if there’s overflow/borrow.  

- **Power:**  
  - Board requires **9 V** (for the bridge + LM358) and **5 V** (for ADC + logic + LEDs).  
  - I used two small adjustable buck converters: one set to 9 V, the other set to 5 V (both fed from a 12 V source).  

---

## Parts

### ICs
- **2 × SN74LS283N** — 4-bit Binary Full Adder (16-DIP)  
  [SN74LS283N datasheet (TI)](https://www.ti.com/lit/ds/symlink/sn74ls283.pdf?HQS=dis-dk-null-digikeymode-dsf-pf-null-wwe&ts=1748971357410&ref_url=https%253A%252F%252Fwww.ti.com%252Fgeneral%252Fdocs%252Fsuppproductinfo.tsp%253FdistId%253D10%2526gotoUrl%253Dhttps%253A%252F%252Fwww.ti.com%252Flit%252Fgpn%252Fsn74ls283)  
- **2 × SN74LS86AN** — Quad 2-Input XOR Gate (14-DIP)  
  [SN74LS86AN datasheet](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/2380/SN54%2C74%28LS%2CS%2986%28A%29.pdf)  
- **1 × ADC0804LCWM/NOPB** — 8-bit Analog-to-Digital Converter (20-SOIC)  
  [ADC0804 datasheet (TI)](https://www.ti.com/lit/ds/symlink/adc0804-n.pdf?HQS=dis-dk-null-digikeymode-dsf-pf-null-wwe&ts=1748971369363&ref_url=https%253A%252F%252Fwww.ti.com%252Fgeneral%252Fdocs%252Fsuppproductinfo.tsp%253FdistId%253D10%2526gotoUrl%253Dhttps%253A%252F%252Fwww.ti.com%252Flit%252Fgpn%252Fadc0804-n)  
- **1 × LM358N/NOPB** — Dual General-Purpose Op Amp (8-DIP)  
  [LM158/LM358 datasheet (TI)](https://www.ti.com/lit/ds/symlink/lm158-n.pdf?HQS=dis-dk-null-digikeymode-dsf-pf-null-wwe&ts=1748971372414&ref_url=https%253A%252F%252Fwww.ti.com%252Fgeneral%252Fdocs%252Fsuppproductinfo.tsp%253FdistId%253D10%2526gotoUrl%253Dhttps%253A%252F%252Fwww.ti.com%252Flit%252Fgpn%252Flm158-n)

### Resistors & Pots
- **3 × 22 kΩ** (Wheatstone bridge)  
- **1 × 10 kΩ pot** (LM358 feedback adjustment, to pinpoint 5V high)  
- **1 × 100 Ω** pull-down on LM358 output (hacky shit to force < 10 mV output)  
- **4 × 100 kΩ & 2 × 10 kΩ** (op-amp inputs, feedback, ADC clock)  
- **~26 × 330 Ω** (LED current-limiting resistors)

### Capacitors
- **3 × 100 pF** (ADC clock timing; combined = 150 pF)  
- **2 × 1 µF / 50 V** electrolytic (9 V & 5 V rails)

### Switches & LEDs
- **1 × 8-way SPDT DIP switch** (B-input bits)  
- **1 × SPDT toggle** (Add/Subtract mode)  
- **1 × Momentary pushbutton** (ADC reset)  
- **1 × LDR** (~22 Ω bright, ~20 kΩ dark)  
- **2 × Single LEDs** (Analog-Mid (ADC bit 7) + Carry/Borrow)  
- **3 × 10-LED bar-graph modules** (only 8 LEDs used per graph)  
  _Example: [Adafruit 10-Segment Light Bar Graph](https://www.adafruit.com/search?q=10%20Segment%20Light%20Bar%20Graph)_

### Power Modules
- **2 × Buck converter (adjustable)**
  _Example: [eBoot MP1584EN DC-DC Buck Converter Kit (Amazon)](https://www.amazon.com/dp/B07RVG34WR)_  

---

## PCB & Documentation

- **KiCad 3D Render:**  
  ![KiCad 3D Render](https://github.com/user-attachments/assets/5812d331-8268-4aba-a099-03524ee9046b)  
  *Figure: PCB 3D render in KiCad.*

- **KiCad PCB Screenshot:**  
  ![KiCad PCB View](https://github.com/user-attachments/assets/87d3d092-a69e-43b2-a4ed-35de53e7abc1)  
  *Figure: PCB layout in KiCad.*

- **CircuitMaker Schematic (Low Output):**  
  ![CircuitMaker Low Output](https://github.com/user-attachments/assets/82ca30f8-f663-48f8-b2c2-20621bd4e6a9)  
  *Figure: CircuitMaker schematic showing low output (~0 V).*

- **CircuitMaker Schematic (High Output):**  
  ![CircuitMaker High Output](https://github.com/user-attachments/assets/b648398c-ac91-49b3-8f65-481031cb5297)  
  *Figure: CircuitMaker schematic showing high output (5 V when LDR is ~22 Ω).*

- **Development Board:** 
  ![Development Board (Removable ICs)](https://github.com/user-attachments/assets/edcb489d-9dd9-4c5c-89e0-a1d4a312f3ea)  
*Figure: Development board with removable IC sockets.*

- **Soldered ICs:**  
  ![Final Board](https://github.com/user-attachments/assets/53a51b97-8dab-4410-94ed-47d69e763be2)  
  *Figure: Partially assembled board with ICs soldered directly.*

---

## Usage Notes

1. **Calibrating the LM358:**  
   - If the LM358 cannot swing fully to 0 V, install the **100 Ω pull-down** on its output.  
   - In bright light, adjust the **10 kΩ feedback pot** until LM358 output = 5.000 V exactly.  

2. **Verifying the ADC0804:**  
   - With LM358 output = 0 V, ADC should output all zeros.  
   - With LM358 output = 5 V, ADC should output decimal 255 (all ones).  

3. **Adder/Subtractor Testing:**  
   - Set **B = 0** (all DIP switches OFF), **MODE = Add**: the bar graph should match the ADC reading (0…255).  
   - Set **B** to a known pattern (e.g., 00001111 = 15), **MODE = Add**: output = A + 15 (watch for carry LED).  
   - Switch to **Subtract** (MODE = 1): output = A – B (two’s complement). If A < B, the Borrow LED lights; the bar graph shows (A – B mod 256).  

4. **Power Port Pinout (5-pin header, top to bottom)**  
   1. **9V** – Powers Wheatstone bridge & LM358  
   2. **5V** – Powers ADC0804, SN74LS logic, and LEDs  
   3. **NC** – No Connection  
   4. **NC** – No Connection  
   5. **GND** – Ground reference for both 9V and 5V rails  

---
