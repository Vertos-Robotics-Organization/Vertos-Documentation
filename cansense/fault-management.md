# Fault Management

## CANSense Encoder Fault System Documentation

### Overview

The CANSense encoder provides a comprehensive fault detection and monitoring system to help diagnose hardware issues, communication problems, and operational faults. The system includes both **sticky faults** (persistent until manually cleared) and **non-sticky faults** (current status only).

### Fault Types

#### 1. Hardware Fault

**Description**: Indicates a physical hardware problem with the encoder itself.

**Sticky Fault Methods**:

* `getStickyFault_Hardware()` - Returns true if a hardware fault has occurred since last reset
* `resetStickyFault_Hardware()` - Clears the sticky hardware fault flag

**Current Fault Methods**:

* `getFault_Hardware()` - Returns true if a hardware fault is currently present

**Troubleshooting**:

* Check physical connections and mounting
* Verify power supply voltage and stability
* Inspect for physical damage to the encoder

#### 2. Boot During Enable Fault

**Description**: Occurs when the encoder resets or boots while the robot is enabled, which can cause position tracking issues.

**Sticky Fault Methods**:

* `getStickyFault_BootDuringEnable()` - Returns true if the encoder has rebooted during enable
* `resetStickyFault_BootDuringEnable()` - Clears the sticky boot during enable fault

**Current Fault Methods**:

* `getFault_BootDuringEnable()` - Returns true if currently experiencing boot issues

**Troubleshooting**:

* Check for power supply fluctuations
* Verify CAN bus wiring integrity
* Monitor for electromagnetic interference

#### 3. Loop Overrun Fault

**Description**: Indicates the encoder's internal control loop is taking too long to execute, potentially causing timing issues.

**Sticky Fault Methods**:

* `getStickyFault_LoopOverrun()` - Returns true if a loop overrun has occurred
* `resetStickyFault_LoopOverrun()` - Clears the sticky loop overrun fault

**Current Fault Methods**:

* `getFault_LoopOverrun()` - Returns true if currently experiencing loop overrun

**Troubleshooting**:

* Reduce CAN bus traffic if possible
* Check for excessive electrical noise
* Verify proper encoder firmware version

#### 4. Bad Magnet Fault

**Description**: Indicates issues with the magnetic field detection, which can affect position accuracy.

**Sticky Fault Methods**:

* `getStickyFault_BadMagnet()` - Returns true if magnet issues have been detected
* `resetStickyFault_BadMagnet()` - Clears the sticky bad magnet fault

**Current Fault Methods**:

* `getFault_BadMagnet()` - Returns true if currently experiencing magnet issues

**Troubleshooting**:

* Check magnet alignment and distance from sensor
* Verify magnet strength and condition
* Ensure no metallic debris interfering with magnetic field
* Check for proper air gap between magnet and encoder

#### 5. CAN General Fault

**Description**: General CAN bus communication issues that don't fall into specific categories.

**Sticky Fault Methods**:

* `getStickyFault_CANGeneral()` - Returns true if general CAN issues have occurred
* `resetStickyFault_CANGeneral()` - Clears the sticky CAN general fault

**Current Fault Methods**:

* `getFault_CANGeneral()` - Returns true if currently experiencing CAN issues

**Troubleshooting**:

* Check CAN bus wiring and termination
* Verify CAN bus speed settings
* Monitor for bus-off conditions
* Check for proper 120Ω termination resistors

#### 6. Momentary CAN Bus Loss Fault

**Description**: Temporary loss of CAN bus communication that was automatically recovered.

**Sticky Fault Methods**:

* `getStickyFault_MomentaryCanBusLoss()` - Returns true if momentary CAN loss has occurred
* `resetStickyFault_MomentaryCanBusLoss()` - Clears the sticky momentary CAN bus loss fault

**Current Fault Methods**:

* `getFault_MomentaryCanBusLoss()` - Returns true if currently experiencing momentary CAN loss

**Troubleshooting**:

* Check for loose CAN connections
* Monitor for electrical interference
* Verify proper CAN bus loading
* Check for intermittent wiring issues

#### 7. CAN Clogged Fault

**Description**: CAN bus is overloaded with too much traffic, causing message delays or losses.

**Sticky Fault Methods**:

* `getStickyFault_CANClogged()` - Returns true if CAN bus congestion has occurred
* `resetStickyFault_CANClogged()` - Clears the sticky CAN clogged fault

**Current Fault Methods**:

* `getFault_CANClogged()` - Returns true if CAN bus is currently congested

**Troubleshooting**:

* Reduce CAN message frequency from other devices
* Optimize CAN message prioritization
* Check for devices sending excessive messages
* Consider using higher CAN bus speed if supported

#### 8. Rotation Overspeed Fault

**Description**: Encoder rotation speed has exceeded safe operational limits.

**Sticky Fault Methods**:

* `getStickyFault_RotationOverspeed()` - Returns true if overspeed has occurred
* `resetStickyFault_RotationOverspeed()` - Clears the sticky rotation overspeed fault

**Current Fault Methods**:

* `getFault_RotationOverspeed()` - Returns true if currently overspeeding

**Troubleshooting**:

* Check maximum rated RPM for the encoder
* Verify gear ratios and mechanical limits
* Implement software speed limiting
* Check for mechanical resonance issues

#### 9. Under Voltage Fault

**Description**: Power supply voltage has dropped below the minimum operating threshold.

**Sticky Fault Methods**:

* `getStickyFault_UnderVolted()` - Returns true if under voltage has occurred
* `resetStickyFault_UnderVolted()` - Clears the sticky under voltage fault

**Current Fault Methods**:

* `getFault_UnderVolted()` - Returns true if currently under voltage

**Troubleshooting**:

* Check power supply voltage and current capacity
* Verify wiring gauge and connection quality
* Monitor for voltage drops during high current draw
* Check for proper power distribution

### Usage Examples

#### Basic Fault Monitoring

```java
CANSense encoder = new CANSense(1, true); // Device ID 1, debug enabled

// Check for any current faults
if (encoder.getFault_Hardware()) {
    System.out.println("Hardware fault detected!");
}

if (encoder.getFault_BadMagnet()) {
    System.out.println("Magnet alignment issue detected!");
}

// Check for sticky faults (historical)
if (encoder.getStickyFault_CANGeneral()) {
    System.out.println("CAN communication issues have occurred");
    // Clear the sticky fault after handling
    encoder.resetStickyFault_CANGeneral();
}
```

#### Comprehensive Fault Check

```java
public void checkAllFaults(CANSense encoder) {
    // Check all current faults
    boolean hasCurrentFaults = encoder.getFault_Hardware() ||
                              encoder.getFault_BootDuringEnable() ||
                              encoder.getFault_LoopOverrun() ||
                              encoder.getFault_BadMagnet() ||
                              encoder.getFault_CANGeneral() ||
                              encoder.getFault_MomentaryCanBusLoss() ||
                              encoder.getFault_CANClogged() ||
                              encoder.getFault_RotationOverspeed() ||
                              encoder.getFault_UnderVolted();
    
    if (hasCurrentFaults) {
        System.out.println("Active faults detected - check encoder status");
    }
    
    // Check for any sticky faults
    if (encoder.getStickyFault_Hardware()) {
        System.out.println("Hardware fault has occurred");
    }
    
    if (encoder.getStickyFault_BadMagnet()) {
        System.out.println("Magnet issues detected - check alignment");
    }
}
```

#### Periodic Fault Reset

```java
public void periodicFaultMaintenance(CANSense encoder) {
    // Reset all sticky faults periodically (e.g., at start of each match)
    encoder.resetStickyFaults();
    
    // Or reset individual sticky faults as needed
    if (encoder.getStickyFault_MomentaryCanBusLoss()) {
        System.out.println("Clearing momentary CAN loss fault");
        encoder.resetStickyFault_MomentaryCanBusLoss();
    }
}
```

#### Fault-Safe Operation

```java
public boolean isEncoderHealthy(CANSense encoder) {
    // Check critical faults that affect position accuracy
    return !encoder.getFault_Hardware() && 
           !encoder.getFault_BadMagnet() && 
           !encoder.getFault_UnderVolted();
}

public double getPositionWithFaultCheck(CANSense encoder) {
    if (isEncoderHealthy(encoder)) {
        return encoder.getAbsRotations();
    } else {
        System.err.println("Encoder fault detected - position may be unreliable");
        return Double.NaN; // Return NaN to indicate invalid reading
    }
}
```

### Best Practices

#### 1. Regular Monitoring

* Check fault status periodically in your robot code
* Log fault occurrences for post-match analysis
* Monitor sticky faults to identify recurring issues

#### 2. Fault Response Strategy

* Implement graceful degradation when faults occur
* Have backup sensors or estimation methods ready
* Don't ignore intermittent faults - they often indicate developing problems

#### 3. Maintenance Schedule

* Reset sticky faults at appropriate intervals (match start, practice sessions)
* Use sticky faults to track encoder health over time
* Keep fault logs for trending analysis

#### 4. Integration with Robot Code

```java
// In your robot periodic method
public void robotPeriodic() {
    // Check encoder health
    if (encoder.getFault_Hardware() || encoder.getFault_BadMagnet()) {
        // Switch to backup positioning method
        useBackupPositioning = true;
    }
    
    // Log sticky faults for analysis
    if (encoder.getStickyFault_CANGeneral()) {
        DriverStation.reportWarning("CAN issues detected on encoder", false);
    }
}
```

### API Reference Summary

#### Sticky Fault Methods

* `getStickyFault_*()` - Check if fault has occurred since last reset
* `resetStickyFault_*()` - Clear individual sticky fault
* `resetStickyFaults()` - Clear all sticky faults at once

#### Current Fault Methods

* `getFault_*()` - Check current fault status
* Updates automatically based on encoder telemetry

#### Key Points

* **Sticky faults** persist until manually cleared - use for historical tracking
* **Current faults** reflect real-time status - use for immediate response
* Always handle faults gracefully to maintain robot functionality
* Use debug mode (`debugMode = true`) for detailed fault information during development
