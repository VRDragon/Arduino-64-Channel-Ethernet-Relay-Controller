# Arduino 64-Channel Ethernet Relay Controller
### Web-Based GPIO Control System Using Arduino and Ethernet

**64arduino-web-relay** is an embedded systems project developed by **Vratislav Režo (VRDRAGON)**.

The project implements a web-based remote control system capable of managing up to 64 digital output channels using an Arduino microcontroller equipped with an Ethernet interface and an SD card.

The Arduino operates as a standalone HTTP web server, hosting an HTML control interface directly from the SD card. Users can access the interface through a web browser and independently switch output channels ON or OFF over a local Ethernet network.

The application was originally designed for the **FORled-SHOWROOM** environment.

---

## Project Overview

The main objective was to create a simple network-connected hardware control platform without requiring an external computer or dedicated web server.

The Arduino handles HTTP communication, processes incoming commands, controls GPIO outputs and returns their current states to the browser.

The system combines embedded programming, Ethernet networking and asynchronous web communication.

### Main Features

- Control interface for 64 output channels.
- Ethernet-based remote access.
- Standalone HTTP web server running directly on Arduino.
- HTML interface hosted on an SD card.
- Individual ON/OFF switching using checkboxes.
- AJAX communication using XMLHttpRequest.
- XML-based reporting of output states.
- Automatic status synchronization every second.
- Static IP address configuration.
- Serial communication for debugging.
- Direct control of digital and analog-designated GPIO pins.

---

## Technologies

| Technology | Purpose |
|---|---|
| Arduino | Embedded control platform |
| Arduino C/C++ | Firmware development |
| Ethernet Library | Network communication |
| Ethernet Shield | Ethernet connectivity |
| SD Library | Web interface storage |
| HTML | Web control interface |
| CSS | Interface styling |
| JavaScript | Asynchronous communication |
| XMLHttpRequest | AJAX requests |
| XML | Output state communication |
| SPI | Communication with Ethernet and SD hardware |

---

## System Architecture

The system uses a standalone embedded web server architecture.

Unlike conventional web applications, no Apache, PHP or external server is required.

### Architecture Overview

```text
        Web Browser
             |
             | HTTP / AJAX
             |
       Ethernet Network
             |
             |
      Arduino Ethernet
       HTTP Web Server
             |
       +-----+------+
       |            |
       |            |
    SD Card       GPIO
       |            |
    index.htm    64 output
       |         channels
       |
    HTML / JS
    Interface
```

### 1. Arduino HTTP Server

The firmware uses the Arduino Ethernet library to create an HTTP server listening on port 80.

The network configuration is defined directly in the firmware:

```cpp
byte mac[] = {
    0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED
};

IPAddress ip(192, 168, 15, 104);

EthernetServer server(80);
```

The Arduino uses a predefined MAC address and static IP address.

### 2. SD Card Web Hosting

The HTML control interface is stored on an SD card.

During initialization, the firmware:

1. Initializes the SD card.
2. Checks whether `index.htm` exists.
3. Configures the GPIO outputs.
4. Initializes the Ethernet interface.
5. Starts the HTTP server.

When a browser requests the main page, the Arduino reads `index.htm` from the SD card and transfers its contents to the client.

### 3. Web-Based Output Control

The HTML interface contains 64 independent checkboxes.

Each checkbox represents an individual output channel.

The user can switch a channel ON or OFF directly from the browser.

JavaScript collects the requested output changes and sends them to the Arduino through HTTP GET requests.

Example request parameters:

```text
LED1=1
LED2=0
LED3=1
```

Where:

- `1` – output ON.
- `0` – output OFF.

### 4. AJAX Communication

The JavaScript implementation uses XMLHttpRequest for asynchronous communication.

The main function is:

```javascript
function GetArduinoIO()
```

Its responsibilities include:

- Sending output control requests.
- Receiving XML responses.
- Reading individual output states.
- Updating checkbox states in the browser.
- Repeating synchronization every 1000 milliseconds.

This allows the user interface to communicate with the Arduino without reloading the entire web page.

### 5. GPIO Output Control

The firmware processes incoming HTTP parameters inside:

```cpp
void SetLEDs(void)
```

The function checks the received request for output commands.

Depending on the requested state, it executes:

```cpp
digitalWrite(pin, HIGH);
```

or:

```cpp
digitalWrite(pin, LOW);
```

The firmware also stores the requested output state in an internal array:

```cpp
unsigned char LED_state[70] = {0};
```

### 6. XML State Reporting

The function:

```cpp
void XML_response(EthernetClient cl)
```

generates an XML response containing the current software-maintained output states.

Example:

```xml
<?xml version="1.0" ?>
<inputs>
    <LED>checked</LED>
    <LED>unchecked</LED>
    <LED>checked</LED>
</inputs>
```

JavaScript parses the XML response and updates the corresponding checkboxes.

---

## Hardware Configuration

The firmware uses an Ethernet interface and an SD card connected through SPI.

### Main Hardware Components

- Arduino board with sufficient GPIO resources.
- Ethernet Shield or compatible Ethernet interface.
- SD card.
- External relay modules or other suitable output drivers.
- Ethernet network connection.
- Appropriate power supply.

The use of analog pins A0–A15 suggests an Arduino Mega 2560-compatible platform.

### GPIO Mapping

The firmware defines a GPIO mapping array:

```cpp
char led[] = {
    0,2,3,5,6,7,8,9,14,15,16,17,
    18,19,20,21,22,23,24,25,26,27,
    28,29,30,31,32,33,34,35,36,37,
    38,39,40,41,42,43,44,45,46,47,
    48,49,50,51,52,53
};
```

The first 47 channels are mapped through this digital pin array.

Additional channels are explicitly assigned to analog-designated pins:

| Channel | Arduino pin |
|---|---|
| LED 48 | A0 |
| LED 49 | A1 |
| LED 50 | A2 |
| LED 51 | A3 |
| LED 52 | A4 |
| LED 53 | A5 |
| LED 54 | A6 |
| LED 55 | A7 |
| LED 56 | A8 |
| LED 57 | A9 |
| LED 58 | A10 |
| LED 59 | A11 |
| LED 60 | A12 |
| LED 61 | A13 |
| LED 62 | A14 |
| LED 63 | A15 |
| LED 64 | Not explicitly mapped |

**Note:** The mapping above describes the intended assignments visible in the source code. The original implementation contains array-indexing inconsistencies that must be corrected before using all channels.

---

## Project Structure

```text
64arduino-web-relay/
│
├── forled_server.ino
│   └── Arduino firmware
│       ├── Ethernet initialization
│       ├── SD card initialization
│       ├── HTTP server
│       ├── HTTP request processing
│       ├── GPIO output control
│       └── XML response generation
│
├── index.htm
│   └── Web control interface
│       ├── HTML structure
│       ├── CSS styling
│       ├── 64 output checkboxes
│       └── JavaScript AJAX communication
│
└── .gitattributes
```

---

## Installation and Deployment

### Requirements

- Arduino IDE.
- Compatible Arduino board with sufficient GPIO pins.
- Ethernet Shield or compatible Ethernet hardware.
- SD card.
- Ethernet connection.
- Appropriate relay driver hardware.

### Required Arduino Libraries

The firmware uses the following libraries:

```cpp
#include <SPI.h>
#include <Ethernet.h>
#include <SD.h>
```

These libraries are available through the Arduino development environment.

### Deployment Steps

1. Open `forled_server.ino` in Arduino IDE.
2. Connect the Ethernet Shield and SD card.
3. Copy `index.htm` to the root directory of the SD card.
4. Configure the MAC address and static IP address in the firmware.
5. Select the appropriate Arduino board and port.
6. Compile and upload the firmware.
7. Insert the SD card.
8. Connect the Ethernet interface to the local network.
9. Open the configured IP address in a web browser.

Default IP address:

```text
http://192.168.15.104
```

---

## Network Configuration

The original firmware uses static network configuration.

```cpp
byte mac[] = {
    0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED
};

IPAddress ip(192, 168, 15, 104);
EthernetServer server(80);
```

The IP address should be adjusted to match the local network configuration.

The Ethernet interface uses SPI chip-select pin 10, while the SD card uses chip-select pin 4.

---

## Technical Limitations and Considerations

The repository represents an experimental embedded web control implementation.

Several details should be considered when reproducing or extending the original project:

- The GPIO mapping array contains only 48 elements, although the firmware accesses indexes up to 64.
- The initialization loop also accesses array elements beyond the declared array size.
- The XML response loop generates only 63 channel states, although the interface contains 64 checkboxes.
- LED 64 does not have an explicit GPIO mapping in the original switch statement.
- The output state array represents software-maintained states rather than verified physical relay feedback.
- HTTP GET requests are used for state-changing operations.
- The HTTP server does not implement authentication or encrypted communication.
- The request buffer is limited to 60 characters, which can restrict the number of simultaneous commands processed in one request.

The original implementation should therefore be treated as a historical prototype. GPIO mapping, request handling and output synchronization should be reviewed before practical deployment.

External relay drivers must also be selected according to the electrical characteristics of the connected loads. Arduino GPIO pins must never drive relay coils or high-power loads directly.

---

## Historical Context

This project demonstrates an early implementation of browser-based embedded hardware control.

It combines microcontroller programming, Ethernet communication, SD card storage and web technologies into one standalone system.

The project illustrates several important concepts:

- Embedded web servers.
- Ethernet-connected microcontrollers.
- GPIO-based hardware automation.
- Client-server communication.
- AJAX and XML data exchange.
- Browser-based remote control.
- Integration of software and physical hardware.

Rather than relying on a dedicated Linux server or cloud platform, the project places the complete web server functionality directly on the microcontroller.

---

## Author

**Vratislav Režo (VRDRAGON)**

GitHub: [@VRDragon](https://github.com/VRDragon)

Repository: [64arduino-web-relay](https://github.com/VRDragon/64arduino-web-relay)

---

*Standalone 64-channel Ethernet-based Arduino output control system developed for the FORled-SHOWROOM environment.*
