# NORVI IIOT ESP32 W5500 ModbusTCP

This project implements a Modbus TCP server for wired ethernet connection using a W5500 chip on an ESP32. We're using it to control machine status lights connected to a Norvi ENET (AE-06 Series) via IOBroker.


### The code

This is a PlatformIO build (and if you haven't tried PlatformIO yet, you really should, it's great!), but is easily adapted to the Arduino IDE, if that happens to be your weapon of choice.

Under PlatformIO, the platformio.ini file will automatically download and install the correct Ethernet library for you when you type "pio run" for the first time.

The setup() section of the code does have some clunky hardware checks, so pay attention to the serial output from your ESP32 when you first run it; it will try to guide you to solutions for (simple!) hardware issues.

**You do** need to update the src/local_config.h file to fit with *your* specific home network; it will not work unless it can look up the IP address of the remote NTP server (via DNS) and find a valid route to it (via your gateway).


### DHCP

The original ESP8266 version of this project timed-out and crashed when configured to use DHCP rather than a hard-coded IP.  I would recommend you stick with a hard-coded IP for initial testing, as I haven't tried DHCP at all, yet (let me know how it goes if you do).


### Practical notes

The physical connections between the ESP32 and W5500 are different, but simpler than the ESP8266 version (you don't need to buffer SCS chip-select signal on the ESP32).

-  **GPIO23**   <-->   **MOSI**
-  **GPIO19**   <-->   **MISO**
-  **GPIO18**   <-->   **SCLK**
-  **GPIO5**    <-->   **SCS**
-  **GPIO27**   <-->   **RESET**

GPIO27 was chosen for the reset driver pin (from the ESP32 to the W5500) simply because it didn't clash with any of the other peripheral devices I intended to use and didn't have existing pull-up or pull-down requirements that might cause problems at power-up (as GPIO15 does on the ESP8266, for instance).

As always, you do need a good solid DC supply for both boards and a good ground connection between them in addition to the data lines listed above.


### Troubleshooting

- A common problem when wiring up this circuit on breadboard is that the W5500 doesn't get the assigned IP address at start-up, or displays all zeros or all ones, or apparently random numbers at each start up.  These are all symptoms of bad physical connections between the boards.  Try replacing each jumper wire, one by one and testing in between.  If that doesn't work, try moving the jumpers (and the modules) to a different row of holes.
- As already mentioned, a bad power-supply, bad grounding between the boards and/or a lack of decoupling/smoothing capacitors can also cause hard to find, intermittent problems.
- The display doesn't always display the time, just the sleep loop.  -- This is usually a symptom of busy networks and the use of the inherently unreliable UDP protocol in the example code.  Sometimes a reply to the NTP request gets lost, or doesn't arrive in time to be printed.  There's no error checking or retry code built into this simple demo.  You can probably improve performance by changing to an NTP server closer to you (ie:- use the country servers defined for your location, or if you're lucky enough to have one, use an existing NTP server on your local network).

