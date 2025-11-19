## Goal

Create a single-board Thunderbolt 4 dock with:

* PCIe x4 GPU slot
* M.2 NVMe slot
* USB hub (4 USB-C + 4 USB-A ports)
* 60–100 W Power Delivery (PD)
* Single voltage input

---

## Design Constraints / Features

### Wanted Features

* **Thunderbolt 4 (TB4) connection**

  * One TB4 port dedicated to PCIe tunneling for GPU
  * One TB4 port dedicated to M.2 NVMe + USB hub
* **PCIe 4.0 x4 slot**

  * Maximum lanes available over TB4 (for GPU)
* **M.2 NVMe slot**

  * PCIe Gen3 x4 recommended
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
  * TB4 Port 2: x4 lanes → M.2 NVMe + USB hub
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
| USB Hub                         | 2x CYUSB3304-68LTXC                   |
| ~~PD Controller~~                   | ~~TI TPS65987DDJ (or equivalent)~~        |
| Voltage Regulators              | Switching / Linear as required        |
| Optional PCIe Switch / Fallback | TBD (for lane allocation flexibility) |

[https://files.slop.gay/pub/docs/Asmedia/ASM2464PD-X/](https://files.slop.gay/pub/docs/Asmedia/ASM2464PD-X/)

---

## Block Diagrams

```
                 Host Device
                       |
                  +----+----+
                  |         |
             TB4 Port 1   TB4 Port 2 (Dual-Port Mode Only)
                  |         |
                  |         |
             +--------+     |
             | ASM1   |     |
             | (GPU)  |     |
             +--------+     |
                  |         |
              PCIe x4       |
                  |         |
              GPU Slot      |
                  |         |
           TB4 Port Out     |
                  |         |
             (Single-Port Mode Only)
                  +---------+
                       |
                  TB4 Port In
                       |
                  +--------+
                  | ASM2   |
                  | (NVMe  |
                  | + USB) |
                  +--------+
                   /      \
                  /        \
             PCIe x4    USB-C Out
                 |          |
            NVMe Slot       |
                            |
                      PCIe → USB Hub
                            |
                     3x USB-C + 4x USB-A
```
Legend:
-------
Mode A: Dual TB4 Ports - 
    TB4 Port 1 → ASM1 (GPU)
    TB4 Port 2 → ASM2 (NVMe + USB Hub)

Mode B: Single TB4 Port - 
    TB4 Port 1 → ASM1 (GPU) → ASM2 (NVMe + USB Hub) [Daisy Chain]

---

## Electrical / Power Planning

* Input Voltage: 12–20 V (regulated for all components)
* Power Budget:

  * GPU: external connector
  * NVMe: 3.3 V / 5 V
  * USB hub: 5 V, max 900 mA per USB-A port, PD for USB-C ports
* PD Output: 60–100 W to host laptop
* Overcurrent / overvoltage protection per rail

---

## Mechanical / PCB Planning

* PCB: Minimum 6 layers (signal, power, ground)
* High-speed routing: controlled impedance, length matching
* Port placement: USB-C, USB-A, TB4 accessible externally
* 3D-printed enclosure: clearance for GPU, NVMe, airflow channels
* Cooling: TB4 heatsink, optional fan headers, NVMe thermal pad

---

## Notes / To-Do

* Final PCIe lane allocation plan
* USB hub layout (mix of USB-C / USB-A)
* 3D case design / airflow
* PCB stackup, layer allocation
* Component sourcing & cost analysis
* Firmware testing (hot-plug, soft-reset, PD negotiation)

---

## References

* ASM2464PD datasheet
* CYUSB3304 datasheet
* TPS65987DDJ datasheet
* PCIe / Thunderbolt routing guides
* Voltage regulator datasheets

---
