# Fault Management

## CANSense Encoder Fault System Documentation

### Overview

The CANSense encoder provides a comprehensive fault detection and monitoring system to help diagnose hardware, communication, and operational issues. Faults are categorized as **sticky faults** (persist until cleared) and **current faults** (real-time status).

### Fault Severity Types

The CANSense encoder categorizes faults into two severity levels:

- **Error Faults**: Critical issues that may significantly impact encoder functionality or safety. These require immediate attention and may affect position accuracy or device operation.
- **Warning Faults**: Non-critical issues that indicate potential problems but don't immediately compromise core functionality. These should be monitored and addressed during maintenance windows.

### Fault Types

#### 1. Hardware Fault (Error)

**Description**: Indicates a physical hardware problem with the encoder.

- **Sticky Fault**: `getStickyFault_Hardware()`, `resetStickyFault_Hardware()`
- **Current Fault**: `Error_Hardware()`

**Troubleshooting**:
- Check wiring, mounting, and power supply.
- Inspect for physical damage.

#### 2. Boot During Enable Fault (Error)

**Description**: Encoder reset/booted while robot was enabled, possibly affecting position tracking.

- **Sticky Fault**: `getStickyFault_BootDuringEnable()`, `resetStickyFault_BootDuringEnable()`
- **Current Fault**: `Error_BootDuringEnable()`

**Troubleshooting**:
- Check for power interruptions or CAN bus issues.

#### 3. Loop Overrun Fault (Warning)

**Description**: Encoder's internal loop took too long to execute.

- **Sticky Fault**: `getStickyFault_LoopOverrun()`, `resetStickyFault_LoopOverrun()`
- **Current Fault**: `Warning_LoopOverrun()`

**Troubleshooting**:
- Reduce CAN bus traffic.
- Check for excessive noise or firmware issues.

#### 4. Bad Magnet Fault (Error)

**Description**: Issues with magnetic field detection, affecting accuracy.

- **Sticky Fault**: `getStickyFault_BadMagnet()`, `resetStickyFault_BadMagnet()`
- **Current Fault**: `Error_BadMagnet()`

**Troubleshooting**:
- Check magnet alignment, strength, and for debris.

#### 5. CAN General Fault (Warning)

**Description**: General CAN bus communication issues.

- **Sticky Fault**: `getStickyFault_CANGeneral()`, `resetStickyFault_CANGeneral()`
- **Current Fault**: `Warning_CANGeneral()`

**Troubleshooting**:
- Check CAN wiring, speed, and termination.

#### 6. Momentary CAN Bus Loss Fault (Warning)

**Description**: Temporary loss of CAN bus communication, automatically recovered. Detected when a CAN fault clears after being set.

_Short explanation: In code, the sticky fault is set when either `CANClogged` or `CANGeneral` is true, and then later both become false; this transition indicates a momentary CAN bus loss and sets the sticky flag._

- **Sticky Fault**: `getStickyFault_MomentaryCanBusLoss()`, `resetStickyFault_MomentaryCanBusLoss()`
- **Current Fault**: `Warning_MomentaryCanBusLoss()`

**Troubleshooting**:
- Check for loose connections or intermittent wiring.

#### 7. CAN Clogged Fault (Warning)

**Description**: CAN bus overloaded with traffic.

- **Sticky Fault**: `getStickyFault_CANClogged()`, `resetStickyFault_CANClogged()`
- **Current Fault**: `Warning_CANClogged()`

**Troubleshooting**:
- Reduce message frequency or optimize bus usage.

#### 8. Rotation Overspeed Fault (Error)

**Description**: Encoder rotation speed exceeded safe limits.

- **Sticky Fault**: `getStickyFault_RotationOverspeed()`, `resetStickyFault_RotationOverspeed()`
- **Current Fault**: `Error_RotationOverspeed()`

**Troubleshooting**:
- Check RPM, gear ratios, and mechanical limits.

#### 9. Under Voltage Fault (Error)

**Description**: Power supply voltage dropped below minimum threshold.

- **Sticky Fault**: `getStickyFault_UnderVolted()`, `resetStickyFault_UnderVolted()`
- **Current Fault**: `Error_UnderVolted()`

**Troubleshooting**:
- Check power supply and wiring quality.

### Code Examples

#### Basic Fault Monitoring

```java
CANSense encoder = new CANSense(1, true);

if (encoder.Error_Hardware()) {
    System.out.println("Hardware fault detected!");
}
if (encoder.Error_BadMagnet()) {
    System.out.println("Magnet alignment issue detected!");
}
if (encoder.getStickyFault_CANGeneral()) {
    System.out.println("CAN communication issues have occurred");
    encoder.resetStickyFault_CANGeneral();
}
```

#### Comprehensive Fault Check

```java
public void checkAllFaults(CANSense encoder) {
    boolean hasErrorFaults = encoder.Error_Hardware() ||
                            encoder.Error_BootDuringEnable() ||
                            encoder.Error_BadMagnet() ||
                            encoder.Error_RotationOverspeed() ||
                            encoder.Error_UnderVolted();

    boolean hasWarningFaults = encoder.Warning_LoopOverrun() ||
                              encoder.Warning_CANGeneral() ||
                              encoder.Warning_MomentaryCanBusLoss() ||
                              encoder.Warning_CANClogged();

    if (hasErrorFaults) {
        System.out.println("Critical faults detected - immediate attention required");
    }
    
    if (hasWarningFaults) {
        System.out.println("Warning faults detected - monitor and address during maintenance");
    }

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
    encoder.resetStickyFaults();

    if (encoder.getStickyFault_MomentaryCanBusLoss()) {
        System.out.println("Clearing momentary CAN loss fault");
        encoder.resetStickyFault_MomentaryCanBusLoss();
    }
}
```

#### Fault-Safe Operation

```java
public boolean isEncoderHealthy(CANSense encoder) {
    return !encoder.Error_Hardware() &&
           !encoder.Error_BadMagnet() &&
           !encoder.Error_UnderVolted();
}

public double getPositionWithFaultCheck(CANSense encoder) {
    if (isEncoderHealthy(encoder)) {
        return encoder.getAbsRotations();
    } else {
        System.err.println("Encoder fault detected - position may be unreliable");
        return Double.NaN;
    }
}
```

### Best Practices

- Monitor error faults continuously for immediate response.
- Check warning faults periodically and address during maintenance.
- Use sticky faults for historical tracking.
- Reset sticky faults at appropriate intervals.
- Integrate fault checks into robot code for safe fallback.

### API Reference Summary

- `getStickyFault_*()` / `resetStickyFault_*()` — Check/reset sticky faults.
- `resetStickyFaults()` — Clear all sticky faults.
- `Error_*()` — Check current error fault status (critical).
- `Warning_*()` — Check current warning fault status (non-critical).

**Sticky faults** persist until cleared.  
**Current faults** reflect real-time status.  
**Error faults** require immediate attention.  
**Warning faults** should be monitored and addressed during maintenance.

Handle faults gracefully to maintain robot functionality.
