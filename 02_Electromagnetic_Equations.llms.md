# Electromagnetic Equations

Electromagnetism is described by **Maxwell’s equations**, **the Lorentz force**, **wave equations**, **energy in EM fields**, and **electromagnetic induction**. Below is a complete breakdown of these concepts.

Author

Benedict Thekkel

## **1. Maxwell’s Equations (Foundation of Electromagnetism)**

Maxwell’s equations describe the behavior of **electric fields (**$E$), **magnetic fields (**$B$), **electric charge (**$\rho$), and **current (**$J$).

| Equation | Name | Differential Form | Integral Form | Meaning |
|----|----|----|----|----|
| **Gauss’s Law** | **Electric Fields** | $\nabla \cdot \mathbf{E} = \frac{\rho}{\varepsilon_0}$ | $\oint_S \mathbf{E} \cdot d\mathbf{A} = \frac{Q_{\text{enc}}}{\varepsilon_0}$ | Electric field flux through a closed surface is proportional to enclosed charge. |
| **Gauss’s Law for Magnetism** | **Magnetic Fields** | $\nabla \cdot \mathbf{B} = 0$ | $\oint_S \mathbf{B} \cdot d\mathbf{A} = 0$ | Magnetic monopoles don’t exist; net magnetic flux is always zero. |
| **Faraday’s Law** | **Electromagnetic Induction** | $\nabla \times \mathbf{E} = -\frac{\partial \mathbf{B}}{\partial t}$ | $\oint_C \mathbf{E} \cdot d\mathbf{l} = -\frac{d}{dt} \int_S \mathbf{B} \cdot d\mathbf{A}$ | A changing magnetic field creates an electric field (basis for electric generators). |
| **Ampère’s Law (with Maxwell’s correction)** | **Magnetic Fields & Current** | $\nabla \times \mathbf{B} = \mu_0 \mathbf{J} + \mu_0 \varepsilon_0 \frac{\partial \mathbf{E}}{\partial t}$ | $\oint_C \mathbf{B} \cdot d\mathbf{l} = \mu_0 I_{\text{enc}} + \mu_0 \varepsilon_0 \frac{d}{dt} \int_S \mathbf{E} \cdot d\mathbf{A}$ | Electric currents and changing electric fields create magnetic fields. |

------------------------------------------------------------------------

## **2. Lorentz Force Equation (Force on a Charged Particle)**

The **Lorentz Force** describes how an electric and magnetic field acts on a moving charge.

$$\mathbf{F} = q (\mathbf{E} + \mathbf{v} \times \mathbf{B})$$

### **Example**

A proton (\$ q = 1.6 ^{-19} C \$) moving at \$ v = 10^6 \$ m/s perpendicular to a magnetic field of \$ B = 0.1 \$ T experiences a force:

$$F = q v B = (1.6 \times 10^{-19}) (10^6) (0.1) = 1.6 \times 10^{-14} N$$

------------------------------------------------------------------------

## **3. Wave Equation for Electromagnetic Waves**

From Maxwell’s equations, we derive the **wave equation**:

$$\frac{\partial^2 \mathbf{E}}{\partial t^2} = c^2 \nabla^2 \mathbf{E}, \quad \frac{\partial^2 \mathbf{B}}{\partial t^2} = c^2 \nabla^2 \mathbf{B}$$

where \$ c = \$ is the **speed of light**.

### **Example: Electromagnetic Waves in Vacuum**

- If \$ = E_0 (kx - t) \$, then \$ \$ is perpendicular to \$ \$.
- The wave moves at speed **\$ c \$**.

------------------------------------------------------------------------

## **4. Energy in Electromagnetic Fields**

### **Poynting Vector (Energy Flow)**

The power per unit area carried by an EM wave:

$$\mathbf{S} = \frac{1}{\mu_0} (\mathbf{E} \times \mathbf{B})$$

### **Electromagnetic Energy Density**

Total energy per unit volume in an EM field:

$$u = \frac{1}{2} \left( \varepsilon_0 E^2 + \frac{B^2}{\mu_0} \right)$$

### **Example: Energy Density of an EM Wave**

For an EM wave with **\$ E = 1000 \$ V/m** and **\$ B = 3.33 ^{-6} \$ T**:

$$u = \frac{1}{2} \left( (8.85 \times 10^{-12}) (1000)^2 + \frac{(3.33 \times 10^{-6})^2}{4\pi \times 10^{-7}} \right) = 4.42 \times 10^{-6} \text{ J/m}^3$$

------------------------------------------------------------------------

## **5. Electromagnetic Induction (Faraday’s & Lenz’s Laws)**

### **Faraday’s Law of Induction**

$$\mathcal{E} = -\frac{d\Phi_B}{dt}$$ where \$ \_B = B A \$ is the **magnetic flux**.

### **Lenz’s Law**

The induced current flows in a direction that **opposes the change** in flux.

### **Example**

A coil with **10 turns** experiences a changing magnetic field from **0.5 T to 0.1 T** in **2 s**, and has an area **\$ A = 0.01 m^2 \$**.

$$\mathcal{E} = -N \frac{d\Phi_B}{dt} = -10 \times \frac{(0.1 - 0.5) \times 0.01}{2} = 0.02 V$$

------------------------------------------------------------------------

## **6. Electromagnetic Radiation Pressure**

The **radiation pressure** exerted by an electromagnetic wave is:

$$P = \frac{S}{c} = \frac{E \times B}{\mu_0 c}$$

For **solar radiation** with \$ S = 1361 W/m^2 \$:

$$P = \frac{1361}{3 \times 10^8} = 4.54 \times 10^{-6} \text{ N/m}^2$$

------------------------------------------------------------------------

## **7. Summary Table of Electromagnetic Equations**

| Equation Name | Formula | Description |
|----|----|----|
| **Gauss’s Law** | $\nabla \cdot \mathbf{E} = \frac{\rho}{\varepsilon_0}$ | Electric flux is proportional to charge. |
| **Gauss’s Law for Magnetism** | $\nabla \cdot \mathbf{B} = 0$ | No magnetic monopoles exist. |
| **Faraday’s Law** | $\nabla \times \mathbf{E} = -\frac{\partial \mathbf{B}}{\partial t}$ | A changing magnetic field induces an electric field. |
| **Ampère’s Law** | $\nabla \times \mathbf{B} = \mu_0 \mathbf{J} + \mu_0 \varepsilon_0 \frac{\partial \mathbf{E}}{\partial t}$ | Electric currents and changing electric fields generate magnetic fields. |
| **Lorentz Force** | $\mathbf{F} = q (\mathbf{E} + \mathbf{v} \times \mathbf{B})$ | Force on a charged particle. |
| **Wave Equation** | $\frac{\partial^2 \mathbf{E}}{\partial t^2} = c^2 \nabla^2 \mathbf{E}$ | Describes EM wave propagation. |
| **Poynting Vector** | $\mathbf{S} = \frac{1}{\mu_0} (\mathbf{E} \times \mathbf{B})$ | Energy flow of an EM wave. |
| **Induced EMF** | $\mathcal{E} = -\frac{d\Phi_B}{dt}$ | Voltage induced by changing magnetic flux. |

------------------------------------------------------------------------

Back to top
