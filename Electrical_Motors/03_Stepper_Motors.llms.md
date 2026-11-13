# Stepper Motors

A stepper motor divides a full rotation into many equal steps and moves one step at a time as its coils are energized in sequence. This gives precise, repeatable positioning without feedback (open loop). This guide covers how they work, how drivers control them, and how to program step/direction motion.

Author

Benedict Thekkel

## 1. How a Stepper Works

A stepper has multiple coil groups (**phases**) around the stator and a toothed/magnetized rotor. Energizing phases in a fixed sequence pulls the rotor from tooth to tooth, one discrete **step** at a time.

- Common resolution: **200 steps/revolution = 1.8 degrees per step** (also 0.9 deg = 400 steps).
- No feedback needed: count the steps you send and you know the position (as long as you never lose one).
- Very high **holding torque** at standstill; the rotor is magnetically latched.

| **Type** | **Description** | **Notes** |
|----|----|----|
| **Permanent Magnet** | Magnetized rotor, coarse steps. | Cheap, low res. |
| **Variable Reluctance** | Soft-iron toothed rotor, no magnet. | Legacy. |
| **Hybrid** | PM + reluctance; fine steps, high torque. | The common NEMA type. |

------------------------------------------------------------------------

## 2. Coil Configuration: Bipolar vs. Unipolar

| **Type** | **Wires** | **Drive** | **Torque** |
|----|----|----|----|
| **Bipolar** | 4 | Needs an H-bridge per phase (current reverses). | Higher |
| **Unipolar** | 5-6 | Center-tapped; simpler switching, no reversal. | Lower |

Most modern NEMA-17/23 steppers are **4-wire bipolar** driven by a dedicated chip (A4988, DRV8825, TMC2209) that handles the H-bridges and current regulation for you.

------------------------------------------------------------------------

## 3. Step Modes and Microstepping

How the driver energizes the coils sets the smoothness and resolution:

| **Mode** | **Coil pattern** | **Effect** |
|----|----|----|
| **Full step (wave)** | One phase on at a time. | Lowest torque. |
| **Full step (2-phase)** | Two phases on. | Full rated torque. |
| **Half step** | Alternate 1 and 2 phases. | 2x resolution, smoother. |
| **Microstepping** | Sine/cosine current levels per phase. | Up to 256x resolution, very smooth. |

**Microstepping** drives each coil with a graded current (approximating sine/cosine) so the rotor settles between full steps. It improves smoothness and reduces resonance/noise, but the *incremental holding torque* per microstep is small, so effective positioning accuracy does not scale 1:1 with microstep count.

------------------------------------------------------------------------

## 4. How Drivers Are Controlled: Step / Direction

The dominant interface is **STEP + DIR**: the microcontroller does not switch coils directly; it just pulses a wire.

``` plaintext
MCU ---> STEP pin: each rising edge = advance one (micro)step
MCU ---> DIR pin : HIGH = clockwise, LOW = counter-clockwise
MCU ---> EN pin  : enable/disable the driver outputs
MS1/MS2/MS3 pins : set microstep resolution (full ... 1/16, 1/32...)
```

Speed = STEP pulse frequency. Distance = number of STEP pulses. A key setting is the driver’s **current limit** (via a Vref potentiometer or register), which must match the motor’s rated coil current to avoid overheating or lost torque.

``` plaintext
STEP:  _|-|_|-|_|-|_|-|_|-|_   (5 pulses = 5 microsteps)
DIR:   ---------------------   (held HIGH = one direction)
```

------------------------------------------------------------------------

## 5. Programming a Stepper

### 5.1. Arduino, raw STEP/DIR (A4988 / DRV8825)

``` cpp
#define STEP_PIN 3
#define DIR_PIN  4

void setup() {
  pinMode(STEP_PIN, OUTPUT);
  pinMode(DIR_PIN, OUTPUT);
  digitalWrite(DIR_PIN, HIGH);       // pick a direction
}

void loop() {
  // 200 pulses = one full revolution at full step
  for (int i = 0; i < 200; i++) {
    digitalWrite(STEP_PIN, HIGH);
    delayMicroseconds(800);          // shorter delay = faster
    digitalWrite(STEP_PIN, LOW);
    delayMicroseconds(800);
  }
  delay(1000);
}
```

### 5.2. Arduino with AccelStepper (accel/decel profiles)

Manual pulsing gives no acceleration; `AccelStepper` ramps speed so the motor does not stall or lose steps.

``` cpp
#include <AccelStepper.h>

AccelStepper stepper(AccelStepper::DRIVER, 3, 4);  // STEP=3, DIR=4

void setup() {
  stepper.setMaxSpeed(1000);        // steps/sec
  stepper.setAcceleration(500);     // steps/sec^2
  stepper.moveTo(2000);             // target position (absolute)
}

void loop() {
  if (stepper.distanceToGo() == 0)
    stepper.moveTo(-stepper.currentPosition());   // reverse
  stepper.run();                    // call often; steps as needed
}
```

### 5.3. Raspberry Pi (Python, RPi.GPIO)

``` python
import RPi.GPIO as GPIO
from time import sleep

STEP, DIR = 20, 21
GPIO.setmode(GPIO.BCM)
GPIO.setup([STEP, DIR], GPIO.OUT)

def move(steps, clockwise=True, delay=0.001):
    GPIO.output(DIR, clockwise)
    for _ in range(steps):
        GPIO.output(STEP, GPIO.HIGH)
        sleep(delay)
        GPIO.output(STEP, GPIO.LOW)
        sleep(delay)

move(200, clockwise=True)   # one revolution
GPIO.cleanup()
```

### 5.4. Silent driver (TMC2209)

TMC drivers add UART configuration, StealthChop (near-silent) and StallGuard (sensorless homing) on top of the same STEP/DIR interface, so the code above still works while you tune current/microsteps over serial.

------------------------------------------------------------------------

## 6. Practical Considerations

- **Lost steps:** If load exceeds torque or acceleration is too aggressive, the motor skips steps and position is silently wrong. Ramp speed and size with margin.
- **Torque vs. speed:** Holding torque is highest at standstill and falls as step rate rises; there is a top speed beyond which it stalls.
- **Heat:** Steppers draw full current even when holding still. Lower the hold current if the driver supports it.
- **Resonance:** Mid-speed resonance can cause missed steps; microstepping and damping help.
- **Closed-loop steppers:** Adding an encoder (e.g. servo-stepper hybrids) recovers lost steps and gives servo-like reliability while keeping stepper simplicity.

------------------------------------------------------------------------

## 7. Summary

| **Aspect**  | **Stepper**                                      |
|-------------|--------------------------------------------------|
| Feedback    | None (open loop) by default                      |
| Resolution  | 200 full steps/rev, up to 256x microstepping     |
| Interface   | STEP + DIR + EN, microstep select pins           |
| Strength    | Precise, cheap, strong holding torque            |
| Weakness    | Loses steps if overloaded; torque drops at speed |
| Programming | Pulse STEP; use AccelStepper for ramps           |

Steppers trade the feedback loop of a \[\[02-servos\]\] for dead-simple open-loop counting. When they lose steps or you need higher speed/efficiency, step up to a \[\[04-brushless-motors\]\] servo.

------------------------------------------------------------------------

Back to top
