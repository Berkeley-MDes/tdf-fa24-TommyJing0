## Week 5
## Week of 09/28/2024
## OLED Display

I figured out how to use the display provided in our kit. The display is 128x64 pixels and uses an I2C connection (the default bus, as indicated on the back). If you want to use the SPI connection, you’d have to break the J1 and J2 copper pads on the back, but I don’t see the need for that. The good news is that only 4 wires are needed to get it working. I soldered the row of pin headers to the back so I can use it on a breadboard. Below is an example code for initializing and displaying ‘Hello, World!’ on this OLED screen using the **Adafruit_GFX** and **Adafruit_SSD1306** libraries. If this is your first time working with the display, maybe you can use this as a tutorial? I couldn’t find much helpful information online, and I understand the struggle, so I hope this helps you and the class.

## Code:

```cpp
!!! Extremely Important: Must Install `oled-wing-adafruit` Library !!!
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128  // OLED display width, in pixels
#define SCREEN_HEIGHT 64  // OLED display height, in pixels

// Declaration for an SSD1306 display connected to I2C
#define OLED_RESET    -1  // Reset pin # 
#define SCREEN_ADDRESS 0x3C  // I2C address for the display (0x3D)

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

void setup() {
  Serial.begin(9600);  // Start serial communication
  
  // Initialize the display
  if(!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
    Serial.println(F("SSD1306 allocation failed"));
    for(;;);  // Don't proceed, loop forever
  }

  // Clear the buffer
  display.clearDisplay();

  // Set text size and color
  display.setTextSize(1);  // Normal 1:1 pixel scale
  display.setTextColor(WHITE);  // White color

  // Display message
  display.setCursor(0, 10);  // Start at the top-left corner
  display.print(F("Hello, World!"));
  
  // Display the buffer on the screen
  display.display();
}

void loop() {
  // Too much infomation...
}
```
![WechatIMG10](https://github.com/user-attachments/assets/5eec5ebf-a1c6-4900-8e46-98b86655401c)
![image](https://github.com/user-attachments/assets/3a4a349c-c476-4405-8200-2b6ef30bd0cb)
![image](https://github.com/user-attachments/assets/877e530c-e6d1-46eb-94f6-2975e642daaa)

## Pin Map:

This pin map details how to connect the OLED display to the Particle Photon 2 using I2C.

| **Display Pin**   | **Photon 2 Pin**   | **Description**                  |
|:-----------------:|:------------------:|:---------------------------------:|
| **Data**          | **D0**             | I2C Data (SDA)                   |
| **Clk**           | **D1**             | I2C Clock (SCL)                  |
| **VIN**           | **Li+** or **3V3** | Power supply for the display     |
| **GND**           | **GND**            | Ground                           |

## Install Libraries
Installing libraries is a function I find very useful and absolutely necessary as the project progresses. Particle Workbench makes this process much easier, and here’s how you can do it:

1. Open **Particle Workbench** in Visual Studio Code.
2. Open the **Command Palette** by pressing `Cmd+Shift+P` (Mac) or `Ctrl+Shift+P` (Windows).
3. Select `Particle: Install Library` from the available commands.
4. In the input field that appears, enter the name of the library you want to install.
5. Wait for the installation to complete, and verify that the library has been added to your project's `lib` folder.

Additionally, you can use the **Find Libraries** feature to look up available libraries before installation by selecting `Particle: Find Library` in the Command Palette.
