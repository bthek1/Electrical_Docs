# Brushless Motors (BLDC / PMSM)

A brushless motor puts permanent magnets on the rotor and windings on the stator, replacing mechanical brushes with electronic commutation. This gives high efficiency, high power density and long life, at the cost of needing a controller (ESC). This guide covers how they work, how they are commutated and controlled, and how to program them.

Author

Benedict Thekkel

## 1. Why Brushless

In a brushed motor, brushes physically switch current in the spinning rotor. In a **brushless** motor that switching is done electronically, outside the motor:

- **Rotor:** permanent magnets (no windings, no brushes).
- **Stator:** three phase windings (A, B, C).
- **Controller (ESC):** energizes the phases in the right sequence based on rotor position.

| **Benefit**        | **Why**                                |
|--------------------|----------------------------------------|
| High efficiency    | No brush friction/resistance (85-95%). |
| Long life          | Nothing wears but the bearings.        |
| High power density | Windings on stator shed heat easily.   |
| Quiet, low EMI     | No brush sparking.                     |

**BLDC vs PMSM:** physically similar. BLDC is optimized for **trapezoidal** back-EMF and six-step drive; PMSM has **sinusoidal** back-EMF and is driven with FOC for smooth torque. In practice the same hardware is often driven either way.

**Inrunner vs outrunner:** inrunner spins an internal rotor (high RPM, e.g. drills); outrunner spins the outer bell (high torque at lower RPM, e.g. drones, gimbals).

------------------------------------------------------------------------

## 2. Commutation: Making It Spin

The controller must energize the three phases in a rotating pattern synced to the rotor. It uses a **3-phase inverter**: six MOSFETs (three half-bridges), one pair per phase.

``` plaintext
        +V
         |
   [Q1] [Q3] [Q5]   <- high-side switches
    |    |    |
    A----B----C  ---> motor phases
    |    |    |
   [Q2] [Q4] [Q6]   <- low-side switches
         |
        GND
```

### 2.1. Six-Step (Trapezoidal)

There are 6 commutation states per electrical revolution. At any moment one phase is +, one is -, one floats. The controller advances through the 6 states as the rotor turns.

### 2.2. FOC (Field-Oriented Control / Sinusoidal)

Instead of 6 discrete states, FOC continuously computes the ideal current vector to keep the stator field 90 degrees ahead of the rotor field for maximum torque. It transforms the 3 phase currents into a rotating (d-q) frame, runs PI loops on torque (q) and flux (d), and modulates the inverter with SVPWM. Result: smooth torque, no torque ripple, high efficiency down to zero speed.

------------------------------------------------------------------------

## 3. Rotor Position Sensing

The controller must know where the rotor is to commutate correctly.

| **Method** | **How** | **Trade-off** |
|----|----|----|
| **Hall sensors** | 3 Hall-effect sensors report the 6 sectors. | Reliable from zero speed; extra wires. |
| **Sensorless (back-EMF)** | Measure voltage on the floating phase. | No sensors; poor at very low/zero speed. |
| **Encoder / resolver** | High-resolution absolute/incremental position. | Needed for FOC servos; costlier. |

Sensorless six-step is why drone motors need an initial spin-up ramp: below a few hundred RPM the back-EMF is too small to read.

------------------------------------------------------------------------

## 4. The ESC and How It Is Controlled

An **Electronic Speed Controller (ESC)** contains the 3-phase inverter + the commutation logic. You do not switch phases yourself; you send the ESC a throttle/speed command.

| **ESC type** | **Command interface** |
|----|----|
| **Hobby / RC ESC** | 50 Hz PWM, 1.0-2.0 ms pulse = throttle (like a servo). |
| **DShot (digital)** | Packetized digital throttle; noise-immune, telemetry. |
| **Industrial drive** | Torque/velocity/position over analog or fieldbus. |

So at the top level a hobby BLDC is commanded exactly like a servo: a **1.0-2.0 ms pulse at 50 Hz** where 1.0 ms = stop and 2.0 ms = full throttle. The ESC translates that into commutation internally.

------------------------------------------------------------------------

## 5. Programming a Brushless Motor

### 5.1. Arduino driving a hobby ESC

Because a PWM ESC speaks the same signal as a servo, the `Servo` library works. **Always arm first** (send minimum throttle) or the ESC will refuse to run.

``` cpp
#include <Servo.h>

Servo esc;

void setup() {
  esc.attach(9);              // ESC signal wire on D9
  esc.writeMicroseconds(1000); // arm: minimum throttle
  delay(3000);                 // wait for ESC arming beeps
}

void loop() {
  esc.writeMicroseconds(1300); // low throttle
  delay(2000);
  esc.writeMicroseconds(1600); // mid throttle
  delay(2000);
  esc.writeMicroseconds(1000); // stop
  delay(2000);
}
```

### 5.2. Raspberry Pi (Python) driving an ESC

``` python
from gpiozero import PWMOutputDevice
from time import sleep

# 50 Hz; duty as fraction of the 20 ms period.
esc = PWMOutputDevice(18, frequency=50)

def throttle(us):
    esc.value = us / 20000.0     # 1000 us -> 0.05, 2000 us -> 0.10

throttle(1000); sleep(3)         # arm
throttle(1400); sleep(2)         # spin up
throttle(1000)                   # stop
```

### 5.3. Closed-loop FOC (SimpleFOC style)

For precise torque/position control you drive the inverter directly with a FOC library and a position sensor. Conceptually:

``` cpp
#include <SimpleFOC.h>

BLDCMotor motor = BLDCMotor(11);                 // 11 pole pairs
BLDCDriver3PWM driver = BLDCDriver3PWM(9,10,11,8);
MagneticSensorI2C sensor = MagneticSensorI2C(AS5600_I2C);

void setup() {
  sensor.init();
  motor.linkSensor(&sensor);
  driver.voltage_power_supply = 12;
  driver.init();
  motor.linkDriver(&driver);
  motor.controller = MotionControlType::angle;   // position control
  motor.init();
  motor.initFOC();                                // align + calibrate
}

void loop() {
  motor.loopFOC();          // run the current/commutation loop
  motor.move(1.57);         // command target angle in radians
}
```

Here the code, not an ESC, computes commutation every cycle using the sensor angle. Industrial drives do the same internally and expose only a high-level target over EtherCAT/CANopen.

------------------------------------------------------------------------

## 6. Practical Considerations

- **Kv rating:** RPM per volt (no load). High Kv = fast/low torque (racing props); low Kv = slow/high torque (heavy lift, gimbals).
- **Arming and calibration:** Hobby ESCs need a throttle-range calibration and an arming sequence; skipping it causes no-spin or runaway.
- **Pole pairs:** Electrical revolutions = pole pairs x mechanical revolutions; FOC needs the correct count.
- **Timing/advance:** Six-step ESCs advance commutation timing with speed for efficiency.
- **Cooling and current:** High power density means real heat; match ESC current rating to the motor and add airflow.
- **Safety:** Props/loads can spin up instantly; bench-test with the load secured.

------------------------------------------------------------------------

## 7. Summary

| **Aspect**        | **Brushless (BLDC / PMSM)**                            |
|-------------------|--------------------------------------------------------|
| Commutation       | Electronic: six-step (trapezoidal) or FOC (sinusoidal) |
| Position sense    | Hall sensors, sensorless back-EMF, or encoder          |
| Controller        | ESC (hobby) or servo drive (industrial)                |
| Command (hobby)   | 50 Hz PWM, 1.0-2.0 ms = throttle, or DShot             |
| Command (precise) | Torque/velocity/position via FOC                       |
| Strength          | Efficient, powerful, long-lived, quiet                 |
| Weakness          | Needs a controller and position info; more complex     |

Brushless motors are the high-performance core inside modern \[\[02-servos\]\] and drone drives. Where a \[\[03-stepper-motors\]\] counts open-loop steps, a brushless servo closes the loop with FOC for efficiency and speed.

------------------------------------------------------------------------

Back to top
