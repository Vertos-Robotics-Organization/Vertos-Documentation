# Installing Vendor Dependencies

## Introduction

To use **CANSense** in your WPILib-based FRC project, you need to install the vendor dependency. This allows you to access the necessary libraries for communicating with **CANSense** over CAN.

## Steps to Install Vendor Dependencies

**1. Open WPILib VS Code**

Make sure you have WPILib installed and open your project in **VS Code**.

**2. Navigate to the Vendor Libraries Manager**

* Click on the **WPILib Command Palette** (Ctrl+Shift+P or Cmd+Shift+P on macOS).
* Search for and select **Manage Vendor Libraries**.

**3. Install from a JSON URL**

* In the **Manage Vendor Libraries** menu, select **Install new library (online)**.
*   Enter the following URL and click **OK**:

    ```
    https://vertos-maven-repo.s3.us-west-1.amazonaws.com/VertosLib.json
    ```

**4. Verify the Installation**

* After installation, you should see **CANSense** available under **Referenced Libraries** in your WPILib project.
* You can also check the `vendordeps` folder in your project directory for the `VertosLib.json` file.
