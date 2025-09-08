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
   * `sensor1.update;` - Fetches the latest data from the CAN bus.
   * `long position = sensor1.getSingleTurnRotations();` - Retrieves the current angle between 0 and 1.0.
5. **Output Encoder Data**
   * `System.out.println("Encoder Position: " + position);` - Prints the encoder's position to the console for monitoring.

## Main.java

```java
// Copyright (c) FIRST and other WPILib contributors.
// Open Source Software; you can modify and/or share it under the terms of
// the WPILib BSD license file in the root directory of this project.

package frc.robot;

import edu.wpi.first.wpilibj.TimedRobot;
import edu.wpi.first.wpilibj2.command.Command;
import edu.wpi.first.wpilibj2.command.CommandScheduler;

import com.vertos.encoder.CANSense; // Import
import com.vertos.encoder.VertosHttpServer;

public class Robot extends TimedRobot {
  private Command m_autonomousCommand;
  private final RobotContainer m_robotContainer;
  private CANSense sensor1; // CANSense encoder instance
  VertosHttpServer httpServer;

  public Robot() {
    m_robotContainer = new RobotContainer();

    // Vertos ID0 does not conflict ID0 from other vendors
    sensor1 = new CANSense(0, false); // Initialize encoder with CAN ID 0, debug mode off

    // Only needed for configuring CAN IDs
    try {
      httpServer = new VertosHttpServer(5806);
      httpServer.start();

    } catch (Exception e) {
      System.err.println("Failed to start VertosHttpServer: " + e);
    }
  }

  @Override
  public void robotPeriodic() {
    CommandScheduler.getInstance().run();

    // Read encoder data
    sensor1.update();

    // Get multi-turn counts. Resets to 0 during power down
    //double position = sensor1.getMultiTurnCounts();

    // Get multi-turn Degrees. Resets to 0 during power down
    // double position = sensor1.getMultiTurnDegrees();

    // Get angle between 0 and 1.0
    double position = sensor1.getSingleTurnRotations();

    // Print encoder position to console
    System.out.println("Encoder Position: " + position);
  }

  @Override
  public void disabledInit() {
  }

  @Override
  public void disabledPeriodic() {
  }

  @Override
  public void autonomousInit() {
    m_autonomousCommand = m_robotContainer.getAutonomousCommand();
    if (m_autonomousCommand != null) {
      m_autonomousCommand.schedule();
    }
  }

  @Override
  public void autonomousPeriodic() {
  }

  @Override
  public void teleopInit() {
    if (m_autonomousCommand != null) {
      m_autonomousCommand.cancel();
    }
  }

  @Override
  public void teleopPeriodic() {
  }

  @Override
  public void testInit() {
    CommandScheduler.getInstance().cancelAll();
  }

  @Override
  public void testPeriodic() {
  }

  @Override
  public void simulationInit() {
  }

  @Override
  public void simulationPeriodic() {
  }
}

```

```java
```
