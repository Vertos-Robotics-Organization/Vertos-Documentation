# Java Example

## Introduction

This is a simple Java example demonstrating how to use the **Vertos Encoder** with the **CANSense** interface in an FRC robot project

## Code Breakdown

1. **Import the CANSense Library**
   * `import com.vertos.encoder.CANSense;` - Allows communication with the **Vertos Encoder** over CAN.
2. **Declare the Encoder Instance**
   * `private CANSense sensor1;` - Creates an instance of **CANSense** to interact with the encoder.
3. **Initialize the Encoder**
   * `sensor1 = new CANSense(0, false);` - Instantiates the encoder with **CAN ID 0** and **debug mode disabled**.
4. **Read Encoder Data Periodically**
   * `sensor1.readMultiTurnCounts();` - Fetches the latest multi-turn count from the encoder.
   * `long position = sensor1.getMultiTurnCounts();` - Retrieves the current position in multi-turn counts.
5. **Output Encoder Data**
   * `System.out.println("Encoder Position: " + position);` - Prints the encoder's position to the console for monitoring.

## Code

```java
// Copyright (c) FIRST and other WPILib contributors.
// Open Source Software; you can modify and/or share it under the terms of
// the WPILib BSD license file in the root directory of this project.

package frc.robot;

import edu.wpi.first.wpilibj.TimedRobot;
import edu.wpi.first.wpilibj2.command.Command;
import edu.wpi.first.wpilibj2.command.CommandScheduler;

import com.vertos.encoder.CANSense; // Import

public class Robot extends TimedRobot {
  private Command m_autonomousCommand;
  private final RobotContainer m_robotContainer;
  private CANSense sensor1; // CANSense encoder instance

  public Robot() {
    m_robotContainer = new RobotContainer();
    sensor1 = new CANSense(0, false); // Initialize encoder with CAN ID 0, debug mode off
  }

  @Override
  public void robotPeriodic() {
    CommandScheduler.getInstance().run();
    
    // Read encoder data
    sensor1.readMultiTurnCounts(); 
    long position = sensor1.getMultiTurnCounts(); // Get multi-turn count
    
    // Print encoder position to console
    System.out.println("Encoder Position: " + position);
  }

  @Override
  public void disabledInit() {}
  @Override
  public void disabledPeriodic() {}

  @Override
  public void autonomousInit() {
    m_autonomousCommand = m_robotContainer.getAutonomousCommand();
    if (m_autonomousCommand != null) {
      m_autonomousCommand.schedule();
    }
  }
  @Override
  public void autonomousPeriodic() {}

  @Override
  public void teleopInit() {
    if (m_autonomousCommand != null) {
      m_autonomousCommand.cancel();
    }
  }
  @Override
  public void teleopPeriodic() {}

  @Override
  public void testInit() {
    CommandScheduler.getInstance().cancelAll();
  }
  @Override
  public void testPeriodic() {}

  @Override
  public void simulationInit() {}
  @Override
  public void simulationPeriodic() {}
}

```
## Fault Handling Examples

The **CANSense** encoder provides fault detection and reporting methods to help ensure safe and reliable operation. Below are examples of how to use these features within a WPILib robot project.

### Basic Fault Detection

```java
CANSense encoder = new CANSense(1, true);

if (encoder.getFault_Hardware()) {
  System.out.println("Hardware fault detected!");
}
if (encoder.getFault_BadMagnet()) {
  System.out.println("Magnet alignment issue detected!");
}
if (encoder.getStickyFault_CANGeneral()) {
  System.out.println("CAN communication issues have occurred");
  encoder.resetStickyFault_CANGeneral();
}
```

### Comprehensive Fault Check

You can create a utility method to check all relevant faults and sticky faults:

```java
public void checkAllFaults(CANSense encoder) {
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

  if (encoder.getStickyFault_Hardware()) {
    System.out.println("Hardware fault has occurred");
  }
  if (encoder.getStickyFault_BadMagnet()) {
    System.out.println("Magnet issues detected - check alignment");
  }
}
```

### Periodic Fault Reset

To maintain a clean fault state, you can periodically reset sticky faults:

```java
public void periodicFaultMaintenance(CANSense encoder) {
  encoder.resetStickyFaults();

  if (encoder.getStickyFault_MomentaryCanBusLoss()) {
    System.out.println("Clearing momentary CAN loss fault");
    encoder.resetStickyFault_MomentaryCanBusLoss();
  }
}
```

### Fault-Safe Operation

Before using encoder data, verify that no critical faults are present:

```java
public boolean isEncoderHealthy(CANSense encoder) {
  return !encoder.getFault_Hardware() &&
       !encoder.getFault_BadMagnet() &&
       !encoder.getFault_UnderVolted();
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

### Integrating Fault Checks in WPILib

You can incorporate these checks into your robot code, for example in `robotPeriodic()`:

```java
@Override
public void robotPeriodic() {
  CommandScheduler.getInstance().run();

  // Read encoder data
  sensor1.readMultiTurnCounts();
  long position = sensor1.getMultiTurnCounts();

  // Print encoder position
  System.out.println("Encoder Position: " + position);

  // Use encoder data only if healthy
  double absPosition = getPositionWithFaultCheck(sensor1);
  if (!Double.isNaN(absPosition)) {
    // Use absPosition as needed
  }
}
```

These examples help ensure your robot can detect, report, and recover from encoder faults during operation.

