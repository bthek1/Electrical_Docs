# Electrical Docs

Electrical theory, signal processing, load forecasting, energy cost, and the equipment and motors that put it to work.

Author

Benedict Thekkel

This site covers electricity on paper, in data, and in the equipment that carries it. It starts with the equations and circuit theory, works through three-phase power systems, single line diagrams and control, then moves to filters and signal processing, forecasts grid load from real demand data, and asks whether a home battery pays for itself. It ends with the practical kit: the electrical toolbox, what a switchboard contains, and the motor families. The newer pages are runnable: each works an example in code and checks it with assertions.

For microcontrollers, builds and industrial automation see [Hardware Tools](https://bthek1.github.io/Hardware_Tools/). For the general forecasting toolkit these pages build on see [Time Series ML](https://bthek1.github.io/ML_Timeseries/).

------------------------------------------------------------------------

## Theory

| Page | Covers |
|----|----|
| [Physics Equations](physics_equations.llms.md) | A reference sheet across mechanics, electricity and magnetism, waves and optics, thermodynamics, relativity, quantum and nuclear physics |
| [Electromagnetic Equations](electromagnetic_equations.llms.md) | Maxwell’s equations, the Lorentz force, the EM wave equation, field energy and the Poynting vector, Faraday’s and Lenz’s laws, and radiation pressure |
| [Electricity Equations](electricity_equations.llms.md) | Ohm’s and Kirchhoff’s laws, AC and DC circuits, capacitance and inductance, power, machines, semiconductors, digital electronics, control systems and safety |
| [Network Theorems](network_theorems.llms.md) | Superposition, source transformation, Thevenin, Norton and maximum power transfer, each checked against a nodal solution of one two-source circuit, and why power systems never run at the matched point |
| [Three-Phase Power Systems](three_phase_power_systems.llms.md) | Phasors, star and delta, three-phase power and the power triangle, sizing a power factor correction bank, why transmission runs at high voltage, and a swing-equation simulation of grid frequency after a generator trip |
| [Utility Single Line Diagrams](utility_sld.llms.md) | Reading a utility SLD from transmission to customer: symbols, ratings and protection numbers, bus arrangements, normally open points, and a per-unit fault level calculation down the diagram |
| [Control Systems](control_systems.llms.md) | A DC motor from physics model to transfer function, poles and step response, P against PI against PID, Bode plots with gain and phase margins, and a discrete 1 kHz PID with anti-windup |

------------------------------------------------------------------------

## Signals and Forecasting

| Page | Covers |
|----|----|
| [Filter Design](filter_design.llms.md) | Pulling a 5 Hz signal out of 50 Hz hum and noise: RC filters, order and the Butterworth, Chebyshev and Bessel families, aliasing, and FIR against IIR filters, including the cost of delay |
| [FFT Examples](fft_examples.llms.md) | Forecasting strongly seasonal series with the Darts FFT (Fast Fourier Transform) model, improved step by step by cropping, filtering low-amplitude waves and detrending, then applied to hourly nuclear generation |
| [Electricity Load Forecasting](electricity_load_forecasting.llms.md) | Hourly PJM grid load forecast with MSTL (Multiple Seasonal-Trend decomposition using Loess), which handles daily and weekly seasonality together |
| [Electricity Peak Forecasting](electricity_peak_forecasting.llms.md) | Forecasting ERCOT (Texas) load with MSTL to predict which hour of the day will carry the demand peak |

------------------------------------------------------------------------

## Energy Cost

| Page | Covers |
|----|----|
| [IotaWatt to Battery ROI](iotawatt_battery_roi.llms.md) | The full loop for a home: measure circuits with an IotaWatt, forecast load and peaks, then decide whether a home battery pays for itself, and why self-consumption decides the answer |

------------------------------------------------------------------------

## Equipment

| Page | Covers |
|----|----|
| [Electrical Tools](electrical_tools.llms.md) | What belongs in an electrical toolbox, in ten groups: hand tools, measurement and testing, specialised, safety, design and analysis, installation, network, labelling, workshop items, and reference material |
| [Electrical Switch Components](electrical_switch_components.llms.md) | What a switchboard actually contains, focused on the protection devices that guard against short circuits, overloads, and ground faults, plus contactors, relays, and batteries |

------------------------------------------------------------------------

## Motors

Four pages, one per family, each covering how it works, how it is driven, and how to program it. Read [Electric Motors](Electrical_Motors/electric_motors.llms.md) first for the map.

| Page | Covers |
|----|----|
| [Electric Motors](Electrical_Motors/electric_motors.llms.md) | The overview. How a motor converts current into motion, the DC, AC, and special families, drivers and control, the specifications that matter, and how to pick one for a job |
| [Servo Motors](Electrical_Motors/servos.llms.md) | Closed-loop actuators that hold a commanded position. Hobby servo anatomy and PWM control, then industrial servos with encoders, drives, tuning, and motion profiles |
| [Stepper Motors](Electrical_Motors/stepper_motors.llms.md) | Open-loop precision. Bipolar against unipolar coils, step modes and microstepping, and step/direction driver control |
| [Brushless Motors](Electrical_Motors/brushless_motors.llms.md) | BLDC and PMSM: electronic commutation in place of brushes, rotor position sensing, and the ESC that makes it all work |

------------------------------------------------------------------------

## Not Covered Yet

- **No power electronics.** Nothing on rectifiers, buck and boost converters or inverters, which are what connect solar panels and batteries to the three-phase grid.
- **No circuit transients.** RC, RL and RLC step responses, and the switching surges they cause, appear only indirectly through the motor model in Control Systems.
- **No test and measurement page.** Oscilloscopes and logic analysers are named in the tools list but never explained.
- **No protection grading.** The SLD page computes fault levels, but nothing covers fuse and breaker time-current curves or how upstream and downstream devices are coordinated.

------------------------------------------------------------------------

Back to top
