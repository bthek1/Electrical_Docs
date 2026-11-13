# Servo Motors

A servo motor is a closed-loop actuator that moves to and holds a commanded position (or speed/torque) using feedback. Hobby servos take a simple PWM pulse and drive to an angle; industrial servos use encoders and a servo drive for high-precision motion. This guide covers how they work, how they are controlled, and how to program them.

Author

Benedict Thekkel

## 1. What a Servo Is

A **servo** is not a distinct motor type but a *control arrangement*: a motor + a feedback sensor + a controller that continuously corrects error between the commanded and actual position.

``` plaintext
Command ---> [ Controller ] ---> [ Motor ] ---> Output shaft
                  ^                                  |
                  |------------ [ Feedback ] <-------|
                          (error = command - actual)
```

| **Class** | **Motor inside** | **Feedback** | **Typical use** |
|----|----|----|----|
| **Hobby / RC servo** | Small brushed DC + gears | Potentiometer | RC, robotics, pan/tilt |
| **Industrial servo** | BLDC / PMSM | Encoder / resolver | CNC, robot arms, automation |

------------------------------------------------------------------------

## 2. Hobby Servo Anatomy

| **Part** | **Role** |
|----|----|
| **DC motor** | Provides raw rotation. |
| **Gear train** | Reduces speed and multiplies torque. |
| **Potentiometer** | Reports the current output-shaft angle. |
| **Control PCB** | Compares commanded pulse to pot reading and drives motor. |
| **Output horn** | Attaches to the load. |

- **Standard servo:** ~180 degrees of travel, holds position.
- **Continuous-rotation servo:** Pot loop removed/faked; the pulse commands *speed and direction* instead of angle.

**Wiring (3 wires):** Signal (orange/white), Vcc (red, ~4.8-6V), GND (brown/black).

------------------------------------------------------------------------

## 3. How a Hobby Servo Is Controlled: PWM

Hobby servos are commanded by a **50 Hz PWM signal** (one pulse every 20 ms). The *pulse width*, not the duty cycle percentage, sets the angle:

| **Pulse width** | **Position (typical)**   |
|-----------------|--------------------------|
| 1.0 ms          | 0 degrees (full left)    |
| 1.5 ms          | 90 degrees (center)      |
| 2.0 ms          | 180 degrees (full right) |

``` plaintext
|<-------------- 20 ms period (50 Hz) -------------->|
 __                                                  __
|  |________________________________________________|  |____ ...
|<>|  1.0-2.0 ms high pulse encodes the target angle
```

The servo’s internal controller drives the motor until the potentiometer reading matches the commanded pulse, then holds. Note: exact endpoints vary by servo (some are 500-2500 us); calibrate per unit.

------------------------------------------------------------------------

## 4. Programming a Hobby Servo

### 4.1. Arduino (Servo library)

The `Servo` library generates the 50 Hz pulse train for you; you just call `write(angle)`.

``` cpp
#include <Servo.h>

Servo myServo;

void setup() {
  myServo.attach(9);            // signal pin D9
}

void loop() {
  myServo.write(0);             // go to 0 degrees
  delay(1000);
  myServo.write(90);            // center
  delay(1000);
  myServo.write(180);           // full travel
  delay(1000);

  // For fine control, command the raw pulse width in microseconds:
  myServo.writeMicroseconds(1500);   // ~center
  delay(1000);
}
```

### 4.2. Raspberry Pi (Python, gpiozero)

`gpiozero` maps a -1..+1 value onto the servo’s pulse range.

``` python
from gpiozero import Servo
from time import sleep

servo = Servo(17)   # BCM pin 17

servo.min()         # -1  -> ~1.0 ms
sleep(1)
servo.mid()         #  0  -> ~1.5 ms
sleep(1)
servo.max()         # +1  -> ~2.0 ms
sleep(1)

servo.value = 0.25  # arbitrary position between -1 and 1
sleep(1)
```

### 4.3. ESP32 (MicroPython PWM)

Without a library, generate the pulse directly with a 50 Hz PWM channel and a 16-bit duty.

``` python
from machine import Pin, PWM

servo = PWM(Pin(13), freq=50)

def angle(deg):
    # 0 deg -> 1.0 ms, 180 deg -> 2.0 ms, 20 ms period
    us = 1000 + (deg / 180) * 1000
    duty = int(us / 20000 * 65535)   # fraction of period, 16-bit
    servo.duty_u16(duty)

angle(90)
```

**Tip:** Power servos from a separate 5-6V supply (not the MCU’s regulator) and tie grounds together; stall currents can brown out the controller.

------------------------------------------------------------------------

## 5. Industrial Servos and Closed-Loop Control

Industrial servos pair a BLDC/PMSM motor with a high-resolution **encoder** (or resolver) and a **servo drive** that closes the loop in real time. Control is typically nested (cascaded) loops:

``` plaintext
Position cmd -> [Position loop] -> [Velocity loop] -> [Current/Torque loop] -> Motor
                     ^                   ^                    ^
                  encoder            encoder             current sensor
```

- **Current (torque) loop** runs fastest (kHz), regulating motor current via **Field-Oriented Control (FOC)**.
- **Velocity loop** sits on top, regulating speed.
- **Position loop** is outermost, commanding the target angle/position.
- Each loop is usually a **PID** controller; tuning gains (P, I, D) trades off responsiveness vs. overshoot vs. stability.

**Command interfaces:** step/direction pulses, analog +-10V, or fieldbus (EtherCAT, CANopen, Modbus). Motion is programmed as position/velocity/torque targets or full trajectories (trapezoidal or S-curve profiles).

------------------------------------------------------------------------

## 6. Tuning and Motion Profiles

- **PID tuning:** Raise P for stiffness until it starts to oscillate, add D to damp overshoot, add I to remove steady-state error. Too much I causes wind-up and hunting.
- **Trapezoidal profile:** Accelerate, cruise at constant velocity, decelerate. Simple and common.
- **S-curve profile:** Smooths the acceleration itself (limits jerk) for gentler, quieter motion and less mechanical wear.
- **Feedforward:** Adds a predicted torque/velocity term so the loop does not have to react to everything, improving tracking.

------------------------------------------------------------------------

## 7. Summary

| **Aspect**  | **Hobby servo**       | **Industrial servo**           |
|-------------|-----------------------|--------------------------------|
| Motor       | Small brushed DC      | BLDC / PMSM                    |
| Feedback    | Potentiometer         | Encoder / resolver             |
| Control     | 50 Hz PWM pulse width | Cascaded PID + FOC in a drive  |
| Interface   | Single signal wire    | Step/dir, analog, or fieldbus  |
| Precision   | ~1 degree             | Arc-seconds                    |
| Programming | `Servo.write(angle)`  | Motion commands / trajectories |

Key idea: a servo is a **feedback loop**. Hobby servos hide that loop behind a single PWM wire; industrial servos expose it as tunable position/velocity/torque control. See \[\[03-stepper-motors\]\] for the open-loop alternative and \[\[04-brushless-motors\]\] for the motors inside high-end servos.

------------------------------------------------------------------------

Back to top
