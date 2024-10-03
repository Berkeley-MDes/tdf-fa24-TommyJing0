## Week 4
## Week of 09/28/2024
## OLED Display

I figured out how to use the display provided in our kit. The display is 128x64 pixels and uses an I2C connection (the default bus, as indicated on the back). If you want to use the SPI connection, you’d have to break the J1 and J2 copper pads on the back, but I don’t see the need for that. The good news is that only 4 wires are needed to get it working. I soldered the row of pin headers to the back so I can use it on a breadboard. Below is an example code for initializing and displaying ‘Hello, World!’ on this OLED screen using the **Adafruit_GFX** and **Adafruit_SSD1306** libraries. If this is your first time working with the display, maybe you can use this as a tutorial? I couldn’t find much helpful information online, and I understand the struggle, so I hope this helps you and the class.

## Code:

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128  // OLED display width, in pixels
#define SCREEN_HEIGHT 64  // OLED display height, in pixels

// Declaration for an SSD1306 display connected to I2C
#define OLED_RESET    -1  // Reset pin # (or -1 if sharing Arduino reset pin)
#define SCREEN_ADDRESS 0x3C  // I2C address for the display (commonly 0x3C or 0x3D for 128x64 OLED)

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
  // Nothing needed here for this example
}
