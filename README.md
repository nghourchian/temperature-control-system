# Temperature Control System

An Arduino-based temperature control model developed in MATLAB/Simulink. The model reads an analog temperature signal, filters the measurement, and calculates a PWM output using a PID controller.

The controller is implemented using separate proportional, integral, and derivative branches, making each contribution to the control signal visible in the block diagram.

## Project Overview

The system compares the measured temperature with a desired setpoint and adjusts its output according to the resulting error.

The model includes:

- Analog input from an Arduino Mega 2560.
- Conversion of the sensor reading into a temperature signal.
- First-order filtering of the measured temperature.
- A PID controller with individually adjustable gains.
- Output saturation to the PWM range of 0–255.
- Scopes for observing the unfiltered and filtered measurements.

## Control Method

The temperature error is calculated as:

$$
e(t) = T_{\mathrm{set}} - T_{\mathrm{filtered}}(t)
$$

The PID controller combines three terms:

$$
u(t) = K_p e(t) + K_i \int e(t)\,dt + K_d \frac{de(t)}{dt}
$$

- **Proportional:** Responds to the current temperature error.
- **Integral:** Accumulates error over time to address a persistent offset.
- **Derivative:** Responds to how quickly the error changes.

The combined output is limited to the range **0–255** before being sent to the Arduino PWM block.

## Model Settings

| Parameter | Saved value |
|---|---|
| Hardware target | Arduino Mega 2560 |
| Analog input | A4 |
| PWM output | Digital pin 7 |
| Temperature setpoint | 40°C |
| Proportional gain, Kp | 12 |
| Integral gain, Ki | 0.15 |
| Derivative gain, Kd | 0.2 |
| Sensor conversion gain | 500/1024 |
| Measurement filter | 1 / (0.3s + 1) |
| Output limits | 0–255 |
| Stop time | 200 seconds |
| MATLAB release used to save the model | R2022a |

These values are the configuration saved in the model. Sensor scaling and controller gains should be checked against the connected hardware.

## Measurement Processing

The analog reading is multiplied by `500/1024` and converted to a double-precision signal before filtering.

This scaling corresponds to a nominal 5 V, 10-bit ADC measurement with a temperature sensor sensitivity of 10 mV/°C and no voltage offset. A different sensor or analog reference requires a different conversion.

A first-order low-pass filter processes the temperature measurement:

$$
H(s) = \frac{1}{0.3s + 1}
$$

The filtered signal is used for feedback. One scope compares the unfiltered and filtered temperature signals, while a second displays the filtered temperature separately.

## Requirements

- MATLAB and Simulink.
- Simulink Support Package for Arduino Hardware.
- Arduino Mega 2560 for hardware operation.
- An analog temperature sensor with suitable calibration.
- An actuator and driver compatible with the PWM control signal.

The PWM pin provides a control signal; a heating element or other power load requires an appropriate driver.

## Opening and Using the Model

1. Download and extract the repository.
2. Open MATLAB and navigate to the downloaded folder.
3. Open `temp_control.slx`.
4. Verify the Arduino board and connection settings.
5. Check the analog input, PWM output, and sensor conversion against your wiring.
6. Set the desired temperature and review the PID gains.
7. Use the appropriate Simulink hardware workflow to build and run the controller on the Arduino.

For live observation, use **Monitor & Tune** with the connected board and inspect the scope signals.

The model uses Arduino input/output blocks and does not include a simulated thermal plant. A standalone temperature-response simulation would require adding a plant model.

## Adjusting the Controller

The main adjustable blocks are:

| Block | Purpose |
|---|---|
| `Desired` | Temperature setpoint |
| `Gain` | Sensor conversion |
| `Gain2` | Proportional gain |
| `Gain3` | Integral gain |
| `Gain4` | Derivative gain |
| `Transfer Fcn` | Measurement filter |
| `Saturation` | PWM output limits |

The current implementation uses an explicit integrator and derivative block. Anti-windup logic is not included, so prolonged output saturation can allow the integral term to accumulate.

## Repository Contents

| File | Description |
|---|---|
| `temp_control.slx` | Simulink temperature controller with Arduino I/O, measurement filtering, PID control, and scopes |

