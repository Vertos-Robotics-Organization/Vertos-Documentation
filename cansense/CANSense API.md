# CoreDevice API Documentation

This document lists the public API methods available in the `CoreDevice` class for user access.

---

## Constructor

```java
CoreDevice(int deviceID, boolean debugMode)
```
Creates a new CoreDevice instance.

---


## Device Thread Lifecycle

```java
void start()
```
Starts periodic reading of encoder data (called automatically in constructor).

```java
void stop()
```
Stops periodic reading of encoder data.

---

## CAN "Under the Hood" Communication

```java
void sendPacket(int apiID, byte[] data)
```
Sends a CAN packet to the device.

```java
List<byte[]> queryDevices()
```
Queries devices using API ID 1 and returns a list of received CAN packet data.

---

## Device Status

```java
boolean getFaulted()
```
Returns whether the device is in a faulted state.

```java
void clearFaultState()
```
Manually clears the fault state.

```java
boolean isConnected()
```
Checks if the CAN device is connected by sending and receiving packets.

---

## CANSense API Methods

### Encoder Data Access

```java
long getMultiTurnCounts()
```
Returns the raw multi-turn encoder counts (native units).

```java
long getSingleTurnCounts()
```
Returns the single-turn encoder counts (0 to CountsPerRevolution-1).

```java
int getVelocityCPS()
```
Returns the velocity in counts per second.

```java
int getAccelerationCPS2()
```
Returns the acceleration in counts per second squared.

---

### Rotation and Position Data

```java
double getAbsRotations()
```
Returns the absolute rotations calculated from encoder counts.

```java
double getRotations()
```
Returns the relative rotations (0.0 to 1.0) calculated from absolute rotations.

```java
double getSensorVelocityRPS()
```
Returns the sensor velocity in rotations per second.

```java
double getSensorAccelerationRPS2()
```
Returns the sensor acceleration in rotations per second squared.

```java
double getAbsPositionDegrees()
```
Returns the absolute position in degrees.

```java
double getPositionDegrees()
```
Returns the relative position in degrees (0.0 to 360.0).

```java
double getAbsPositionRadians()
```
Returns the absolute position in radians.

```java
double getPositionRadians()
```
Returns the relative position in radians (0.0 to 2π).

```java
double getVelocityDegreesPerSecond()
```
Returns the velocity in degrees per second.

```java
double getVelocityRadiansPerSecond()
```
Returns the velocity in radians per second.

```java
double getVelocityRPM()
```
Returns the velocity in rotations per minute (RPM).

---

### Fault Status Methods

#### Sticky Faults

```java
boolean getStickyFault_Hardware()
void resetStickyFault_Hardware()
boolean getStickyFault_BootDuringEnable()
void resetStickyFault_BootDuringEnable()
boolean getStickyFault_BadMagnet()
void resetStickyFault_BadMagnet()
boolean getStickyFault_CANGeneral()
void resetStickyFault_CANGeneral()
boolean getStickyFault_LoopOverrun()
void resetStickyFault_LoopOverrun()
boolean getStickyFault_MomentaryCanBusLoss()
void resetStickyFault_MomentaryCanBusLoss()
boolean getStickyFault_CANClogged()
void resetStickyFault_CANClogged()
boolean getStickyFault_RotationOverspeed()
void resetStickyFault_RotationOverspeed()
boolean getStickyFault_UnderVolted()
void resetStickyFault_UnderVolted()
void resetStickyFaults()
```
Get or reset sticky fault status for various fault types, or reset all sticky faults.

#### Non-Sticky Faults

```java
boolean getFault_Hardware()
boolean getFault_BootDuringEnable()
boolean getFault_LoopOverrun()
boolean getFault_BadMagnet()
boolean getFault_CANGeneral()
boolean getFault_MomentaryCanBusLoss()
boolean getFault_CANClogged()
boolean getFault_RotationOverspeed()
boolean getFault_UnderVolted()
```
Get the current (non-sticky) fault status for each fault type.

---

### Encoder Command Methods

```java
boolean zeroEncoder()
```
Sends a command to zero the encoder (set current position as zero).

```java
boolean setDirectionCounterClockWise()
```
Sets the encoder direction to counter-clockwise.

```java
boolean setDirectionClockWise()
```
Sets the encoder direction to clockwise.

```java
boolean setPosition(double position)
boolean setPosition(double position, double timeout)
```
Sets the encoder position to a specific value (in rotations), with optional timeout.

```java
boolean resetFactoryDefaults()
```
Resets the encoder to factory default settings.

---

### Command State Methods

```java
boolean isCommandInProgress()
```
Returns true if a command is currently in progress.

```java
long getTimeSinceLastCommand()
```
Returns the time in milliseconds since the last command was sent, or -1 if none.

```java
void cancelCommand()
```
Cancels any command currently in progress (resets local state).

---

### Utility Methods

```java
void close() throws Exception
```
Closes the device and removes it from the SendableRegistry.
