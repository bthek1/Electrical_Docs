# Electricity Equations

Electrical engineering is a field of engineering that deals with the study and application of electricity, electronics, and electromagnetism. This document covers key concepts, equations, and examples essential for an electrical engineer.

Author

Benedict Thekkel

## **1. Ohm’s Law**

Ohm’s Law states the relationship between voltage ($V$), current ($I$), and resistance ($R$):

$$V = IR$$

### **Example:**

If $I = 2A$ and $R = 10\Omega$, then: $$V = (2A)(10\Omega) = 20V$$

------------------------------------------------------------------------

## **2. Kirchhoff’s Laws**

### **Kirchhoff’s Current Law (KCL)**

The sum of currents entering a junction is equal to the sum of currents leaving:

$$\sum I_{in} = \sum I_{out}$$

### **Kirchhoff’s Voltage Law (KVL)**

The sum of voltage drops around a closed loop is zero:

$$\sum V = 0$$

### **Example:**

For a closed loop with resistors $R_1$ and $R_2$ and voltage source $V$: $$V - I R_1 - I R_2 = 0$$

------------------------------------------------------------------------

## **3. AC & DC Circuits**

### **Direct Current (DC) Circuits**

A steady flow of electric charge in one direction.

### **Alternating Current (AC) Circuits**

A flow of electric charge that periodically reverses direction:

$$V(t) = V_m \sin(\omega t)$$

where: - $V_m$ = Peak voltage - $\omega = 2\pi f$ (angular frequency) - $f$ = Frequency in Hz

------------------------------------------------------------------------

## **4. Capacitance & Inductance**

### **Capacitor:**

Stores electrical energy in an electric field: $$Q = CV$$

where: - $Q$ = Charge (Coulombs) - $C$ = Capacitance (Farads) - $V$ = Voltage (Volts)

### **Inductor:**

Stores energy in a magnetic field: $$V = L \frac{dI}{dt}$$

where: - $L$ = Inductance (Henrys) - $\frac{dI}{dt}$ = Rate of change of current

------------------------------------------------------------------------

## **5. Power in Electrical Circuits**

### **DC Power:**

$$P = VI$$

### **AC Power:**

For sinusoidal voltages and currents: $$P = VI \cos(\phi)$$

where: - $\phi$ = Phase angle between voltage and current

### **Example:**

If $V = 230V$, $I = 10A$, and $\phi = 30^\circ$: $$P = (230)(10) \cos(30^\circ) = 1992W$$

------------------------------------------------------------------------

## **6. Electrical Machines**

### **Transformers:**

Used to step up or step down voltage: $$\frac{V_1}{V_2} = \frac{N_1}{N_2}$$

where: - $V_1, V_2$ = Primary and secondary voltages - $N_1, N_2$ = Number of turns in primary and secondary windings

### **Motors & Generators:**

Faraday’s Law of Electromagnetic Induction: $$\mathcal{E} = -N \frac{d\Phi}{dt}$$

where: - $\mathcal{E}$ = Induced EMF (Volts) - $N$ = Number of turns - $\Phi$ = Magnetic flux

------------------------------------------------------------------------

## **7. Semiconductor Devices**

### **Diodes:**

Allows current to flow in one direction: $$I = I_S \left( e^{\frac{V}{nV_T}} - 1 \right)$$

### **Transistors:**

Used for amplification and switching. For a bipolar junction transistor (BJT): $$I_C = \beta I_B$$

where: - $I_C$ = Collector current - $I_B$ = Base current - $\beta$ = Current gain

------------------------------------------------------------------------

## **8. Digital Electronics**

### **Logic Gates:**

Basic building blocks of digital circuits:

- AND: $Y = A \cdot B$
- OR: $Y = A + B$
- NOT: $Y = \overline{A}$

### **Flip-Flops:**

Used for memory storage and sequential logic.

------------------------------------------------------------------------

## **9. Control Systems**

A system that regulates its own behavior. \### **Transfer Function:** $$H(s) = \frac{Y(s)}{X(s)}$$

### **PID Controller:**

$$u(t) = K_p e(t) + K_i \int e(t) dt + K_d \frac{de(t)}{dt}$$

where: - $K_p, K_i, K_d$ = Proportional, Integral, and Derivative gains

------------------------------------------------------------------------

## **10. Electrical Safety**

- **Grounding:** Ensures safety by redirecting excess current.
- **Circuit Breakers:** Protect against overcurrent.
- **Insulation Resistance:** Prevents current leakage.

------------------------------------------------------------------------

Back to top
