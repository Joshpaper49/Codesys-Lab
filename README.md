# Motor Start/Stop Control with Overload Protection

This project simulates an industrial motor-control system using Ladder Logic in CODESYS. The program implements motor start/stop control, a five-second startup delay, simulated overload protection, and a fault-latching mechanism with manual reset. For clarification, overload is in reference to the motor drawing more current than safely rated.

The goal of this project was to develop a reliable motor-control sequence while gaining practical experience with PLC programming concepts, including rising-edge detection, Set/Reset coils, Boolean operators, timers, and interlocking conditions.

The program was developed and tested entirely using the CODESYS simulation environment, without physical PLC hardware.

## Prerequisites

To run this project, you will need:

- [CODESYS Development System](https://www.codesys.com/)
- CODESYS simulation mode

No physical PLC or motor hardware is required.

## System Description

The system controls a simulated motor using Start and Stop commands, overload relay feedback, and a manual fault reset.

When the Start button is pressed, the program checks whether the Stop circuit and overload relay are healthy and whether any fault is active.

If all operating conditions are satisfied, a start request is latched and a five-second timer begins counting. Once the timer finishes, the motor is activated.

If the Stop button is pressed or an overload occurs, the motor is immediately disabled and the start request is cancelled.

An overload additionally activates a latched fault, which must be manually reset before the motor can be restarted.

## Key Features

 - **Start/Stop Control**: Motor operation is initiated by a Start command and cancelled by Stop.
 - **Delayed Startup**: A five-second TON timer delays motor activation.
 - **Start Requested Latching**: A Set coil maintains the start request after the Start button is released.
 - **Overload Monitoring**: Simulated overload relay feedback prevents motor operation when an overload is detected.
 - **Fault Latching**: An overload sets a fault that remains active even after the overload condition clears.
 - **Manual Fault Reset**: The fault can only be cleared when the overload circuit is healthy and the Start button is released.
 - **Restart Prevention**: The motor requires a new Start command after a Stop or fault condition.

## Key Components

### Inputs and Outputs

| Variable  | Description |
| ------------- | ------------- |
| `xStartBtn`  | Start button input; `TRUE` when pressed |
| `xStopHealthy`  | Stop circuit status; `TRUE` when the circuit is closed  |
| `xOverloadHealthy`  | Overload relay status; TRUE when no overload trip is present  |
| `xResetBtn`  | Manual fault reset button  |
| `xMotor`  | Motor control output  |

The `xStopHealthy` and `xOverloadHealthy` variables use a healthy-high convention, consistent with the expected signals from normally closed physical control contacts.

### Internal Variables

| Variable  | Description |
| ------------- | ------------- |
| `xStartRequested`  | Latched start request |
| `xfaultActive`  | Latched overload fault  |
| `tonMotorStartDelay`  | TON function block instance  |
| `tMotorElapsed`  | Elapsed time of the startup timer  |

## Ladder Logic Components

### Rising-Edge Contacts

Used for the Start and Reset buttons to detect new button presses. This prevents a continuously held button from repeatedly initiating its associated action.

### Set/Reset Coils

Used to retain the start request and overload fault until their respective reset conditions are satisfied.

### OR Boolean Operator

Combines three conditions that cancel the start request:

- Stop circuit unhealthy (`NOT xStopHealthy`)
- Overload circuit unhealthy (`NOT xOverloadHealthy`)
- Active fault (`xfaultActive`)

If any of these conditions occurs, `xStartRequested` is reset.

### TON (Timer ON Delay)

The `tonMotorStartDelay function` block delays motor activation by five seconds (`T#5s`).

The timer begins counting when a valid start request is active. Its `Q` output becomes `TRUE` after the preset delay and remains `TRUE` while the timer input remains active.

## How the System Works










