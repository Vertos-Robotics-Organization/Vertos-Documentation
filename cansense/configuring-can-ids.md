# Configuring CAN IDs

### **Getting Started**

1. **Launch the Tool:**
   * Run Vertos Config.exe
2. **Main Interface:**
   * The tool's interface has fields for entering the host IP, port, operation selection, and CAN ID (if applicable).

<figure><img src="../.gitbook/assets/Screenshot 2025-01-06 105336.png" alt=""><figcaption></figcaption></figure>

#### **Steps to Use**

1. **Configure Host and Port**
   * **Connection:** Connect to the roboRIO via USB.
   * **Host/IP Address:** Enter the IP address of the roboRIO (Default: **172.22.11.2** for USB).
   * **Port:** Enter the port number (Default: **58005**).
2. **Select Operation**\
   Use the dropdown menu to choose an operation:
   * **Set ID:** Assigns a CAN ID to all connected devices without IDs. CAN IDs must be between **1 and 31**. (Setting IDs by serial number is not supported.)
   * **Factory Reset ALL:** Resets **ALL** connected encoders to factory settings.
   * **Show Devices:** Displays all connected encoders on the CAN bus.
3. **CAN ID Field**
   * Enabled only for the **Set ID** operation.
   * Enter your desired CAN ID (values between **1 and 31**).
   * For **Factory Reset ALL,** the field is disabled, and a default value (0) is sent.
4. **Send the Request**\
   Click **Send** to send the selected operation to the roboRIO.

***

#### **Example Use Cases**

**Set a CAN ID:**

1. Configure the host as **172.22.11.2** and port as **58005**.
2. Use **Show Devices** to list connected encoders and verify they are detected.
3. Perform a **Factory Reset ALL** to reset all encoders.
4. Disconnect the CAN bus from all encoders except the one you wish to configure.
5. Select **Set ID** from the dropdown.
6. Enter the desired CAN ID (e.g., **5**, **20**, or **31**) and click **Send**.
7. Reconnect the next encoder and repeat the process to assign a unique ID.
