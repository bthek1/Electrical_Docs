# Electrical Switch Components

An electrical switchboard is a critical part of electrical systems that houses various components to control and protect electrical circuits. These components include protection devices, control devices, measuring instruments, and busbars, among others. In this guide, we’ll dive deeper into **protection devices**, which are responsible for safeguarding electrical circuits from faults such as short circuits, overloads, and ground faults.

Author

Benedict Thekkel

### **1. Key Components of an Electrical Switchboard**

| **Component** | **Description** |
|----|----|
| **Busbars** | Conductive bars that distribute electrical power to different circuits. |
| **Switches** | Devices to open or close electrical circuits. |
| **Fuses** | Overcurrent protection devices that disconnect the circuit in case of faults. |
| **Circuit Breakers** | Automatic switches that protect circuits from overloads and short circuits. |
| **Meters/Indicators** | Devices to monitor and indicate electrical parameters like current and voltage. |
| **Relays** | Control devices that open or close a circuit based on certain conditions. |
| **Protection Devices** | Devices that protect the electrical system from faults, primarily including fuses, circuit breakers, and relays. |

------------------------------------------------------------------------

### **2. Protection Devices: Purpose and Types**

Protection devices are essential to ensure the safe operation of electrical systems by detecting faults and disconnecting power when necessary to prevent damage to the equipment, fire, or injury to personnel.

#### **Main Functions of Protection Devices**:

- **Overcurrent Protection**: Protects the system from currents exceeding safe levels.
- **Short-Circuit Protection**: Prevents damage from high fault currents during a short circuit.
- **Ground Fault Protection**: Detects leakage currents or faults to the ground.
- **Overvoltage/Undervoltage Protection**: Protects against excessive or insufficient voltage.
- **Differential Protection**: Detects discrepancies in the flow of current between two points.

------------------------------------------------------------------------

### **3. Types of Protection Devices in Electrical Switchboards**

#### **3.1. Fuses**

- A **fuse** is a sacrificial protection device that disconnects the circuit when an overcurrent occurs, by melting the fuse element.

| **Type of Fuse** | **Description** | **Application** |
|----|----|----|
| **Cartridge Fuse** | A cylindrical fuse that is used for small and medium circuits. | Residential and commercial circuits. |
| **Strip Fuse** | Contains a metal strip that melts under high current. | Low-voltage systems. |
| **High-Voltage Fuse** | Used in high-voltage circuits, providing protection by interrupting the current flow during faults. | Power substations, industrial applications. |

#### **3.2. Circuit Breakers**

Circuit breakers are automatic protection devices that detect faults and break the circuit to prevent damage.

| **Type of Circuit Breaker** | **Description** | **Application** |
|----|----|----|
| **Miniature Circuit Breaker (MCB)** | Used for low current protection (up to 100A). | Residential and small commercial applications. |
| **Molded Case Circuit Breaker (MCCB)** | Protects circuits up to 2500A. Adjustable settings for overloads. | Industrial and larger commercial systems. |
| **Air Circuit Breaker (ACB)** | Suitable for high currents and has a faster response time than MCCBs. | Large industrial plants, substations. |
| **Vacuum Circuit Breaker (VCB)** | Used in medium and high-voltage applications, with contacts sealed in a vacuum. | Power plants and high-voltage systems. |

#### **3.3. Relays**

Relays are control devices that open or close a circuit based on specific conditions, such as overloads or faults.

| **Type of Relay** | **Description** | **Application** |
|----|----|----|
| **Overload Relay** | Opens the circuit when current exceeds the rated value for a specific time. | Motor protection and electrical devices. |
| **Earth Leakage Relay** | Detects ground faults and disconnects the circuit. | Protects from ground faults. |
| **Differential Relay** | Detects the difference in current between two points, such as the primary and secondary of a transformer. | Transformer protection, electrical substations. |

#### **3.4. Ground Fault Protection Devices**

Ground fault protection devices monitor the system for leakage currents and isolate the fault if necessary.

| **Device** | **Description** | **Application** |
|----|----|----|
| **Residual Current Device (RCD)** | Detects leakage currents to earth and disconnects the circuit. | Used in residential and industrial installations. |
| **Ground Fault Circuit Interrupter (GFCI)** | Protects against electric shock by disconnecting when ground faults are detected. | Used in wet areas like bathrooms and kitchens. |

------------------------------------------------------------------------

### **4. Protection Device Settings**

Protection devices often have adjustable settings to tailor their response to specific conditions:

| **Setting** | **Description** | **Adjustment Range** |
|----|----|----|
| **Overload Protection** | Prevents circuits from carrying excessive current. | 1.0 to 1.5 times rated current. |
| **Time-Delay Setting** | Introduces a delay before the breaker trips. | 0.1 seconds to several seconds. |
| **Current Sensitivity** | Adjusts the threshold for tripping during fault conditions. | 1-10x rated current. |
| **Voltage Sensitivity** | Determines the level at which the device will disconnect for undervoltage or overvoltage conditions. | 90% to 110% of rated voltage. |

------------------------------------------------------------------------

### **5. Protection Schemes**

Protection schemes are designed to ensure that faults are detected and isolated with minimal disruption to the system.

#### **5.1. Time-graded Protection**

This method uses time delays to coordinate protection devices, ensuring that the device closest to the fault operates first.

``` plaintext
Fault -----> Protection Device A (Time Delay) -----> Protection Device B (Time Delay)
```

#### **5.2. Differential Protection**

Used for high-value equipment (e.g., transformers), differential protection compares the current entering and leaving the protected zone. If the difference exceeds a set threshold, the fault is detected.

``` plaintext
Incoming Current -----> Protective Relay -----> Outgoing Current
                    If Difference > Threshold
                            Fault Detected
```

#### **5.3. Overcurrent Protection**

Monitors current flow and disconnects the circuit when current exceeds safe levels.

``` plaintext
Normal Current -------> No Action
High Current ---------> Breaker Trips
```

------------------------------------------------------------------------

### **6. Important Considerations for Protection Devices**

- **Coordination**: Protection devices should be coordinated in a way that minimizes downtime by isolating only the faulty part of the circuit.
- **Sensitivity**: Devices should be sensitive enough to detect faults but not trip under normal conditions.
- **Reliability**: Protection devices must be reliable to ensure quick action during faults and prevent further damage.

------------------------------------------------------------------------

### **7. Protection Device Maintenance**

Routine inspection and testing of protection devices are essential to ensure their reliability. Some of the key maintenance tasks include: - **Cleaning**: Dust and dirt can interfere with the operation of components. - **Testing**: Periodic testing of settings and operation to confirm correct functioning. - **Calibration**: Ensuring that the protection devices are calibrated to the correct settings.

------------------------------------------------------------------------

### **8. Summary of Protection Devices**

Protection devices in electrical switchboards are critical to ensuring safety and system reliability. They prevent damage from overloads, short circuits, and faults. Here’s a recap of the most common protection devices:

| **Protection Device** | **Purpose** | **Example** |
|----|----|----|
| **Fuses** | Protects circuits from overcurrent. | Cartridge Fuses, High Voltage Fuses |
| **Circuit Breakers** | Automatically trips in case of faults (overload, short). | MCBs, MCCBs, ACBs |
| **Relays** | Control devices to activate breakers under fault conditions. | Overload Relays, Differential Relays |
| **Ground Fault Protection** | Detects leakage currents to the ground and disconnects power. | RCDs, GFCIs |

## Contactor and Relay

Here’s a clear comparison in a single table:

| Aspect | Relay 🟦 | Contactor 🟥 |
|----|----|----|
| **Primary Use** | Low-power switching (signals, control circuits) | High-power switching (motors, HVAC, lighting, industrial loads) |
| **Typical Current Rating** | Up to ~20A | From ~9A up to 1000A+ |
| **Voltage Rating** | Usually \<250V | Up to 1000V AC or more |
| **Size** | Small, compact | Larger, bulkier |
| **Contacts** | Light-duty, smaller gaps | Heavy-duty, arc-suppressed, designed for frequent operation |
| **Durability** | Lower mechanical & electrical life | High mechanical & electrical endurance |
| **Auxiliary Contacts** | Limited, often 1–2 | Multiple (NO/NC), easily extendable |
| **Coil Consumption** | Low (mW to a few W) | Higher (W to tens of W) |
| **Applications** | Electronics, automation signals, small appliances | Industrial motors, compressors, heaters, pumps, lighting circuits |
| **Arc Suppression** | Minimal, not designed for heavy arcs | Built-in arc chutes or suppression systems |
| **Cost** | Cheaper | More expensive |

👉 In short:

- **Relay = control small loads/signals**
- **Contactor = switch big loads safely and repeatedly**

## Battery

> Samsung 18650

------------------------------------------------------------------------

Back to top
