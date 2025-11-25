## Goal

Create a single-board Thunderbolt 4 dock with:

* PCIe x4 GPU slot
* M.2 NVMe slot
* Lots of USB Ports
* Fast Ethernet
* 60–100 W Power Delivery (PD)
* Single voltage input

---

## Design Constraints / Features

### Wanted Features

* **Thunderbolt 4 (TB4) connection**

  * One TB4 port dedicated to PCIe tunneling for GPU
  * One TB4 port dedicated to M.2 NVMe, USB, and Ethernet
* **PCIe 4.0 x4 slot**

  * Maximum lanes available over TB4 (for GPU)
* **M.2 NVMe slot**

  * PCIe Gen4 x2
* **Power Delivery (PD)**

  * 60–100 W to laptop over TB4
* **USB Hub**

  * 4 USB-C ports
  * 4 USB-A 3.x ports
* **Single Voltage Input**

  * Powers all components: TB4 controller, USB hub, NVMe, optional GPU rails

---

## Notes / Design Considerations

* **PCIe Lane Allocation**

  * TB4 Port 1: x4 lanes → GPU
  * TB4 Port 2: x4 lanes → M.2 NVMe, USB, and Ethernet
* **Power Distribution**

  * Hub powered from 5 V regulated rail
  * NVMe powered from 3.3 V regulated rail
  * Optional GPU rails from main input if using external connector
* **High-Speed Signal Integrity**

  * Controlled impedance for PCIe and TB4 differential pairs
  * Length matching for PCIe lanes
* **Thermal Management**

  * Heatsink for ASM2464PD TB4 controllers
  * Airflow for GPU and NVMe
  * Optional fan headers
* **User QoL Features**

  * LED indicators: TB4 link, GPU detected, NVMe active, USB hub power/activity
  * Hot-plug detection and optional soft-reset for GPU/NVMe
  * Debug/test points for PCIe, USB lanes, and PD monitoring

---

## Component Selection

| Component                       | Model / Notes                         |
| ------------------------------- | ------------------------------------- |
| TB4 Controller                  | 2x ASM2464PD                          |
| PCIe Fanout Chip (USB+2.5G MAC) | PCI11414C                             |
| 2.5GbE Transceiver              | TBD                                   |
| USB Hub(S)                      | 1x USB7216C + 1x USB7206C             |
| PD+USB-C negotiation            | TBD                                   |
| Voltage Regulators              | Switching / Linear as required        |

<!--
[https://files.slop.gay/pub/docs/Asmedia/ASM2464PD-X/](https://files.slop.gay/pub/docs/Asmedia/ASM2464PD-X/)
-->

---

## Data Flow

![Data Flow Chart](/images/dataflow.png)

---
<!-- 
## Electrical / Power Planning

* Input Voltage: 12–20 V (regulated for all components)
* Power Budget:

  * GPU: external connector
  * NVMe: 3.3 V / 5 V
  * USB hub: 5 V, max 900 mA per USB-A port, PD for USB-C ports
* PD Output: 60–100 W to host laptop
* Overcurrent / overvoltage protection per rail

---
-->

## Mechanical / PCB Planning

#### Layer Stackup
| Layer | Type   | Primary Use                  | Key PI/SI Note                                                      |
|-------|--------|------------------------------|---------------------------------------------------------------------|
| L1    | Signal | HS (TB4, PCIe)               | Microstrip. Route shortest high-speed traces here.                  |
| L2    | GND    | Solid Ground                 | Unified Ground. Primary reference for L1 & L3.                      |
| L3    | Signal | HS (TB4, PCIe)               | Stripline. Route longest critical differential pairs. Ref: L2/L4.   |
| L4    | GND    | Solid Ground                 | Unified Ground. Shields top signals from the power core.            |
| L5    | Signal | HS (USB 3.2, 2.5GbE)         | Stripline. Secondary high-speed routing layer. Ref: L4/L6.          |
| L6    | PWR    | 12V GPU Rail                 | CRITICAL: Use 2 oz copper. Sandwiched by L5 & L7 GND for isolation. |
| L7    | GND    | Solid Ground                 | Unified Ground. Isolates 12V from 5V.                               |
| L8    | PWR    | 5V System Rail               | Sandwiched by L7 & L9 GND. Consider 2 oz copper if current is high. |
| L9    | GND    | Solid Ground                 | Unified Ground. Isolates 5V from 3.3V.                              |
| L10   | PWR    | 3.3V Logic Rail              | Sensitive. Ideal layer for small Analog Power Island split.         |
| L11   | GND    | Solid Ground                 | Unified Ground. Shields core from bottom signals.                   |
| L12   | Signal | LS (I2C, SPI)                | Stripline. Good for noisy low-speed digital signals.                |
| L13   | Signal | LS (GPIOs)                   | Stripline. General-purpose routing.                                 |
| L14   | Signal | LS (Audio)                   | Stripline. Keep sensitive audio traces here.                        |
| L15   | Signal | POL Islands / Spare          | Ideal for local low-voltage power islands (1.050V, 1.900V).         |
| L16   | Signal | Component Breakout           | Microstrip. Used for lower speed/density connections.               |


---
<!--
## Notes / To-Do

* Final PCIe lane allocation plan
* USB hub layout (mix of USB-C / USB-A)
* 3D case design / airflow
* PCB stackup, layer allocation
* Component sourcing & cost analysis
* Firmware testing (hot-plug, soft-reset, PD negotiation)

---
-->

<!--
## References

* ASM2464PD datasheet
* CYUSB3304 datasheet
* TPS65987DDJ datasheet
* PCIe / Thunderbolt routing guides
* Voltage regulator datasheets

---
-->
