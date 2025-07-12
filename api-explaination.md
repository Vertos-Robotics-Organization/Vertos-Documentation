# API explaination

## CANSense Encoder API Documentation

### Overview

The CANSense encoder provides high-resolution position, velocity, and acceleration feedback with comprehensive fault monitoring. This documentation covers all available getter methods for data retrieval and setter methods for configuration.

***

### Data Retrieval Methods (Getters)

#### Raw Encoder Data

**`getMultiTurnCounts()`**

* **Returns:** `long` - Raw multi-turn encoder counts
* **Description:** Returns the native 64-bit signed encoder counts that accumulate across multiple rotations
* **Use Case:** Direct access to raw encoder data for advanced applications

**`getSingleTurnCounts()`**

* **Returns:** `long` - Single-turn encoder counts (0 to 2,097,151)
* **Description:** Returns the encoder counts within a single revolution (modulo operation on multi-turn counts)
* **Range:** 0 to CountsPerRevolution-1 (2,097,151)

**`getVelocityCPS()`**

* **Returns:** `int` - Velocity in counts per second
* **Description:** Raw velocity data from the encoder in native count units
* **Note:** 32-bit signed integer, scaled by 2048 internally

**`getAccelerationCPS2()`**

* **Returns:** `int` - Acceleration in counts per second squared
* **Description:** Raw acceleration data from the encoder in native count units
* **Note:** 32-bit signed integer, scaled by 65536 internally

***

#### Position Data (Rotations)

**`getAbsRotations()`**

* **Returns:** `double` - Absolute rotations
* **Description:** Total rotations from the initial position, can be positive or negative
* **Calculation:** `multiTurnCounts / CountsPerRevolution`
* **Example:** -2.5 means 2.5 rotations counter-clockwise from start

**`getRotations()`**

* **Returns:** `double` - Relative rotations (0.0 to 1.0)
* **Description:** Position within the current rotation cycle
* **Range:** 0.0 to 0.999... (never reaches 1.0)
* **Example:** 0.25 means 1/4 rotation from the zero position

***

#### Position Data (Degrees)

**`getAbsPositionDegrees()`**

* **Returns:** `double` - Absolute position in degrees
* **Description:** Total angular position in degrees from initial position
* **Calculation:** `absoluteRotations × 360.0`
* **Example:** -900.0 means 900 degrees counter-clockwise from start

**`getPositionDegrees()`**

* **Returns:** `double` - Relative position in degrees (0.0 to 360.0)
* **Description:** Angular position within the current rotation cycle
* **Range:** 0.0 to 359.999... degrees
* **Example:** 90.0 means 90 degrees from the zero position

***

#### Position Data (Radians)

**`getAbsPositionRadians()`**

* **Returns:** `double` - Absolute position in radians
* **Description:** Total angular position in radians from initial position
* **Calculation:** `absoluteRotations × 2π`

**`getPositionRadians()`**

* **Returns:** `double` - Relative position in radians (0.0 to 2π)
* **Description:** Angular position within the current rotation cycle
* **Range:** 0.0 to 2π radians

***

#### Velocity Data

**`getSensorVelocityRPS()`**

* **Returns:** `double` - Velocity in rotations per second
* **Description:** Current rotational velocity
* **Calculation:** `velocityCPS / CountsPerRevolution`

**`getVelocityDegreesPerSecond()`**

* **Returns:** `double` - Velocity in degrees per second
* **Description:** Current angular velocity in degrees
* **Calculation:** `velocityRPS × 360.0`

**`getVelocityRadiansPerSecond()`**

* **Returns:** `double` - Velocity in radians per second
* **Description:** Current angular velocity in radians
* **Calculation:** `velocityRPS × 2π`

**`getVelocityRPM()`**

* **Returns:** `double` - Velocity in rotations per minute
* **Description:** Current rotational velocity in RPM
* **Calculation:** `velocityRPS × 60.0`

***

#### Acceleration Data

**`getSensorAccelerationRPS2()`**

* **Returns:** `double` - Acceleration in rotations per second squared
* **Description:** Current rotational acceleration
* **Calculation:** `accelerationCPS2 / CountsPerRevolution`

***

#### Fault Status Methods

**Sticky Fault Getters**

Sticky faults remain set until manually cleared:

* `getStickyFault_Hardware()` - Hardware fault detection
* `getStickyFault_BootDuringEnable()` - Boot occurred during enable
* `getStickyFault_LoopOverrun()` - Control loop overrun
* `getStickyFault_BadMagnet()` - Magnetic sensor fault
* `getStickyFault_CANGeneral()` - General CAN communication fault
* `getStickyFault_MomentaryCanBusLoss()` - Temporary CAN bus loss
* `getStickyFault_CANClogged()` - CAN bus congestion
* `getStickyFault_RotationOverspeed()` - Rotation speed exceeded limits
* `getStickyFault_UnderVolted()` - Supply voltage too low

**Current Fault Getters**

Current faults reflect the present state:

* `getFault_Hardware()` - Current hardware fault status
* `getFault_BootDuringEnable()` - Current boot during enable status
* `getFault_LoopOverrun()` - Current loop overrun status
* `getFault_BadMagnet()` - Current magnet fault status
* `getFault_CANGeneral()` - Current CAN general fault status
* `getFault_MomentaryCanBusLoss()` - Current CAN bus loss status
* `getFault_CANClogged()` - Current CAN clogged status
* `getFault_RotationOverspeed()` - Current overspeed status
* `getFault_UnderVolted()` - Current under-voltage status

All fault methods return `boolean` (true = fault present, false = no fault).

***

### Configuration Methods (Setters)

#### Position Control

**`zeroEncoder()`**

* **Returns:** `boolean` - Success status
* **Description:** Sets the current position as the new zero reference point
* **Timeout:** 2 seconds for acknowledgment
* **Usage:** `boolean success = encoder.zeroEncoder();`

**`setPosition(double position)`**

* **Parameters:** `position` - Target position in rotations
* **Returns:** `boolean` - Success status
* **Description:** Sets the encoder position to a specific value
* **Default Timeout:** 0.2 seconds
* **Usage:** `boolean success = encoder.setPosition(1.5);`

**`setPosition(double position, double timeout)`**

* **Parameters:**
  * `position` - Target position in rotations
  * `timeout` - Maximum time to wait (seconds)
* **Returns:** `boolean` - Success status
* **Description:** Sets position with custom timeout
* **Usage:** `boolean success = encoder.setPosition(1.5, 0.5);`

***

#### Direction Control

**`setDirectionClockWise()`**

* **Returns:** `boolean` - Success status
* **Description:** Sets encoder direction to clockwise
* **Effect:** Changes sign of position, velocity, and acceleration readings
* **Timeout:** 2 seconds for acknowledgment

**`setDirectionCounterClockWise()`**

* **Returns:** `boolean` - Success status
* **Description:** Sets encoder direction to counter-clockwise
* **Effect:** Changes sign of position, velocity, and acceleration readings
* **Timeout:** 2 seconds for acknowledgment

***

#### Fault Management

**`resetStickyFaults()`**

* **Returns:** `void`
* **Description:** Clears all sticky fault flags
* **Usage:** `encoder.resetStickyFaults();`

**Individual Sticky Fault Reset Methods**

* `resetStickyFault_Hardware()`
* `resetStickyFault_BootDuringEnable()`
* `resetStickyFault_LoopOverrun()`
* `resetStickyFault_BadMagnet()`
* `resetStickyFault_CANGeneral()`
* `resetStickyFault_MomentaryCanBusLoss()`
* `resetStickyFault_CANClogged()`
* `resetStickyFault_RotationOverspeed()`
* `resetStickyFault_UnderVolted()`

All return `void` and clear their respective sticky fault flags.

***

#### System Control

**`resetFactoryDefaults()`**

* **Returns:** `boolean` - Success status
* **Description:** Resets encoder to factory default settings
* **Effect:** Clears all user configurations and fault states
* **Timeout:** 5 seconds for acknowledgment (extended for factory reset)
* **Usage:** `boolean success = encoder.resetFactoryDefaults();`

***

#### Command Status Methods

**`isCommandInProgress()`**

* **Returns:** `boolean` - Command execution status
* **Description:** Checks if a command is currently being executed
* **Note:** Automatically handles command timeouts (5 seconds)

**`getTimeSinceLastCommand()`**

* **Returns:** `long` - Time in milliseconds since last command
* **Description:** Returns elapsed time since last command was sent
* **Returns:** -1 if no command has been sent

**`cancelCommand()`**

* **Returns:** `void`
* **Description:** Cancels any command currently in progress
* **Note:** Only resets local state, doesn't stop encoder execution

***

### Technical Specifications

#### Encoder Resolution

* **Counts Per Revolution:** 2,097,152 (2²¹)
* **Position Resolution:** \~0.00000048 rotations per count
* **Angular Resolution:** \~0.0006 arc-seconds per count

#### Data Types

* **Position:** 64-bit signed integer (counts), double (converted)
* **Velocity:** 32-bit signed integer (counts/sec), double (converted)
* **Acceleration:** 32-bit signed integer (counts/sec²), double (converted)

#### Communication

* **Protocol:** CAN bus
* **Command Timeout:** 5 seconds (configurable per command)
* **API IDs:** Different for position (0), velocity/acceleration (16), and faults (32)

***

### Usage Examples

#### Basic Position Reading

```java
CANSense encoder = new CANSense(1, true); // Device ID 1, debug enabled

// Get position in different units
double rotations = encoder.getAbsRotations();
double degrees = encoder.getAbsPositionDegrees();
double radians = encoder.getAbsPositionRadians();

// Get velocity in different units
double rps = encoder.getSensorVelocityRPS();
double rpm = encoder.getVelocityRPM();
double degPerSec = encoder.getVelocityDegreesPerSecond();
```

#### Command Execution with Error Handling

```java
// Zero the encoder
if (encoder.zeroEncoder()) {
    System.out.println("Encoder zeroed successfully");
} else {
    System.out.println("Failed to zero encoder");
}

// Set position with timeout
if (encoder.setPosition(2.5, 1.0)) {
    System.out.println("Position set successfully");
} else {
    System.out.println("Failed to set position");
}
```

#### Fault Monitoring

```java
// Check for any sticky faults
if (encoder.getStickyFault_Hardware() || 
    encoder.getStickyFault_BadMagnet() ||
    encoder.getStickyFault_CANGeneral()) {
    
    System.out.println("Encoder faults detected!");
    encoder.resetStickyFaults(); // Clear all sticky faults
}
```
