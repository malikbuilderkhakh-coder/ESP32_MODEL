# ESP32 Development Board Identification and Programming Lab

## Task

Identify the ESP32 module, voltage regulator, programming interface, LEDs, buttons, and connectors. Produce a schematic-based pin map, install programming tools, and upload a serial Hello program.

> **Hardware note:** The exact ESP32 board has not yet been confirmed. This report uses a classic ESP32-WROOM-32 development board as a reference example. Verify the text on your module's metal shield and the board's official schematic before treating any pinout as exact.

## 1. Objectives

- Identify the ESP32 module and development board.
- Locate the voltage regulator and understand the power rails.
- Identify the USB programming interface.
- Locate LEDs, buttons, and connectors.
- Create a reference pin map based on a schematic.
- Set up VS Code with PlatformIO.
- Upload and verify a serial Hello program.

## 2. Equipment and Tools

- ESP32 development board
- USB cable that supports data (USB-C if that is the connector on your board)
- Windows computer
- Visual Studio Code
- PlatformIO IDE extension for VS Code
- Internet connection for tool installation
- Correct USB serial driver if Windows requires one

## 3. Identify the Module

1. Disconnect USB power before inspecting the board.
2. Find the rectangular metal shield on the ESP32 module.
3. Read and record the full marking, for example `ESP32-WROOM-32`, `ESP32-WROOM-32E`, `ESP32-S3-WROOM-1`, or `ESP32-C3-WROOM-02`.
4. Record any model name printed on the development-board PCB.
5. Consult the official documentation for the exact board model.

The module is the packaged radio/microcontroller assembly; the development board is the larger PCB that adds connectors and supporting circuitry.

**Record your result:** Module marking: `________________________`

Board model: `________________________`

## 4. Identify the Voltage Regulator and Power Rails

A voltage regulator provides a suitable, stable supply voltage to the circuit. Many classic ESP32 development boards accept power from USB at approximately 5 V and regulate it to 3.3 V for the ESP32.

1. Disconnect USB power.
2. Locate the small regulator IC, often near the USB connector or power input.
3. Read its part number if it is legible.
4. Use the board schematic or regulator datasheet to verify the part and output voltage. Do not identify a regulator from its location alone.

Common markings vary by board; some boards use an `AMS1117-3.3` regulator, while others use different parts.

| Label | Typical meaning | Important note |
|---|---|---|
| `3V3` | Regulated 3.3 V rail | Observe the board's current limits |
| `GND` | Common circuit reference | Connect grounds together for external circuits |
| `VIN` / `5V` | Board power connection | Input/output direction and safe voltage depend on the board |
| `EN` | Enable/reset | Used to enable or restart the chip |

**Safety:** ESP32 GPIO pins generally use 3.3 V logic. Do not apply 5 V directly to GPIO unless the specific pin is explicitly documented as 5 V tolerant. Do not power the board through an unknown pin.

**Record your result:** Regulator marking: `________________________`

## 5. Identify the Programming Interface

Many classic ESP32 development boards use a USB-to-UART bridge. It converts USB communication from the computer into serial communication for the ESP32. Some ESP32 variants support native USB instead.

1. Connect the board using a known data-capable USB cable.
2. Open Windows **Device Manager**.
3. Expand **Ports (COM & LPT)**, if present.
4. Record the device name and COM port. Common names include CP210x USB-to-UART Bridge, USB-SERIAL CH340/CH341, or USB Serial Device.

You can also run this in the VS Code PowerShell terminal:

```powershell
Get-CimInstance Win32_SerialPort |
    Select-Object DeviceID, Name
```

This command lists serial ports Windows reports. The USB serial interface helps identify the programming connection, but it does not prove the exact ESP32 module model.

**Record your result:** Serial device and COM port: `________________________`

## 6. Identify LEDs, Buttons, and Connectors

Inspect both sides of the unpowered board and use the board documentation to confirm labels.

### LEDs

- **Power LED:** May illuminate when the board is powered.
- **User/status LED:** If present, may be controlled by firmware. Its GPIO assignment varies by board.
- Not every board has a programmable LED.

### Buttons

- **BOOT:** Often used to enter download mode on classic ESP32 boards.
- **EN / RST:** Resets or enables the ESP32.
- Button behavior can differ between ESP32 variants and board designs.

### Connectors and headers

- **USB connector:** Often provides power and data.
- **GPIO headers:** Connect sensors, LEDs, and peripherals.
- **3V3 and GND:** Power and reference pins.
- **VIN / 5V:** Board power pin; verify its permitted use.
- **TX/RX:** Serial signals, if exposed on the headers.

Record which of these are physically present on your own board.

## 7. Schematic-Based Reference Pin Map

The following table is a **reference for a classic ESP32** and is not a verified pin map for an unidentified board. For your final lab submission, use the official schematic for your exact development-board model.

| Pin / signal | Typical function on classic ESP32 boards | Notes |
|---|---|---|
| `3V3` | 3.3 V power rail | Board supply output on many boards |
| `GND` | Ground | Common electrical reference |
| `VIN` / `5V` | Power connection | Verify direction and voltage in the board schematic |
| `EN` | Enable/reset | Resets or enables the ESP32 |
| `GPIO21` | General-purpose I/O; often used as I²C SDA | Peripheral functions can be configured in software |
| `GPIO22` | General-purpose I/O; often used as I²C SCL | Peripheral functions can be configured in software |
| `GPIO16`, `GPIO17` | General-purpose I/O on many classic modules | Availability depends on module/board |
| `GPIO32`, `GPIO33` | General-purpose I/O | Also support touch sensing on applicable classic ESP32 chips |
| `GPIO34`–`GPIO39` | Input-only GPIO on classic ESP32 | Cannot drive an LED directly |
| `GPIO1` / `TX0` | UART0 transmit | Commonly used for serial output |
| `GPIO3` / `RX0` | UART0 receive | Commonly used for serial input |
| `GPIO0` / BOOT | Boot-strapping pin | Used for download mode on classic ESP32 |

### Simplified functional schematic

```text
                  USB cable
                     |
              +------+------+
              |             |
           USB power     USB data
              |             |
       Board power circuit  USB-to-UART bridge
       / voltage regulator       |
              |                 | TX/RX
             3.3 V               |
              |                 v
              +-------------> ESP32 module
              |                 |
             GND ---------------+---- Common ground
```

This diagram is a functional overview, not an exact electrical schematic. The actual board schematic is needed to confirm the regulator, bridge chip, LED connections, and pin routing.

Official documentation:
- ESP32-DevKitC guide: https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32/esp32-devkitc/user_guide.html
- PlatformIO VS Code integration: https://docs.platformio.org/en/latest/integration/ide/vscode.html

## 8. Install Tools in VS Code

1. Install and open Visual Studio Code.
2. Open **Extensions** with `Ctrl+Shift+X`.
3. Search for **PlatformIO IDE** and install it.
4. Open PlatformIO Home.
5. Choose **New Project**.
6. Set the project name to `ESP32_Hello`.
7. Choose a board target that matches your hardware. `Espressif ESP32 Dev Module` is commonly used for a generic classic ESP32 board; select the corresponding ESP32-S3 or ESP32-C3 board for those variants.
8. Select the **Arduino** framework and create the project.

PlatformIO will install the needed toolchain and framework components when required. A compatible USB serial driver may be needed on Windows.

## 9. Write the Serial Hello Program

In the project, open `src/main.cpp` and replace its contents with:

```cpp
#include <Arduino.h>

void setup() {
    Serial.begin(115200);
    delay(1000);

    Serial.println("Hello from ESP32!");
    Serial.println("ESP32 is working.");
}

void loop() {
    Serial.println("Hello again!");
    delay(2000);
}
```

### Code explanation

- `setup()` runs once when the ESP32 starts.
- `Serial.begin(115200)` starts serial communication at 115200 baud.
- `Serial.println()` sends a line of text to the serial port.
- `loop()` repeats continuously.
- `delay(2000)` waits about two seconds between repeated messages.

## 10. Build, Upload, and Verify

1. Connect the ESP32 with a USB data cable.
2. In PlatformIO, click the **Build** checkmark.
3. If the build succeeds, click **Upload** (right arrow).
4. Choose the correct COM port if prompted.
5. Open **PlatformIO: Serial Monitor**.
6. Set the monitor speed to `115200` baud.

Expected output:

```text
Hello from ESP32!
ESP32 is working.
Hello again!
Hello again!
Hello again!
```

The `Hello again!` line should repeat approximately every two seconds.

If upload fails with a connection timeout, verify the board target and COM port. On many classic ESP32 boards, holding **BOOT** while the upload begins and releasing it once connection starts can help. The exact procedure depends on the board.

## 11. Troubleshooting

| Problem | Checks |
|---|---|
| No COM port appears | Try a known data-capable USB cable and another USB port; inspect Device Manager; install the correct driver if needed |
| Upload times out | Check the board target and COM port; try the BOOT-button procedure if supported |
| No serial output | Confirm the Serial Monitor is on the correct port at 115200 baud; press EN/RST if appropriate |
| Build fails | Read the first compiler error and confirm the correct PlatformIO board target and Arduino framework |
| Board model is uncertain | Read the module shield marking and find the matching board schematic |

## 12. Results and Evidence

Complete this section after testing the real hardware:

- Module marking: `________________________`
- Development-board model: `________________________`
- Regulator part number: `________________________`
- Programming interface / USB bridge: `________________________`
- COM port: `________________________`
- LEDs identified: `________________________`
- Buttons identified: `________________________`
- Connectors identified: `________________________`
- Build result: `Pass / Fail`
- Upload result: `Pass / Fail`
- Serial Hello output observed: `Yes / No`

Attach a photograph of the board and a screenshot of the Serial Monitor as evidence.

## 13. Conclusion

This lab covers identification of the ESP32 module and board components, a schematic-based reference pin map, setup of VS Code with PlatformIO, and a serial Hello program. The pin map and component details must be checked against the actual board schematic before wiring external circuits or submitting the report as a board-specific result.
