## Week 6
## Week of 10/07/2024
## My Experience with Particle Photon and OLED Display

After successfully printing “Hello World” on the OLED screen using the Particle Photon platform, I started diving deeper into the project. To be honest, the past week (and the beginning of this week) has been quite rough. I managed to brick not one, but two Photon 2 devices. However, after a lot of troubleshooting, I finally found the root cause of my problem.

The issue stemmed from a combination of soldering the pins while also having the I2C wires connected, and then placing the entire setup on a breadboard. While it might seem harmless to do all these steps together, it can lead to problems because:

1. I2C bus sensitivity: The I2C bus is quite sensitive to noise and electrical disturbances. When you solder pins and wires simultaneously, you might inadvertently cause unstable connections or interference. Soldering can introduce slight voltage fluctuations or even shorts if not handled properly.

2. Breadboard limitations: Breadboards, though very handy, are notorious for introducing poor or intermittent connections, especially when used in combination with soldered components. The combination of soldering and connecting wires to a breadboard can sometimes cause unreliable contacts or even unexpected short circuits, especially with high-frequency signals like I2C.

The lesson I learned: you can solder the pins and connect the I2C wires, but do not put the setup directly on a breadboard while doing this. It’s much better to either solder the connections fully or use header pins on the Photon that allow easier connection via jumper wires, but avoid combining soldered connections with breadboard use to prevent unexpected issues.

Hey, at least I got one more Photon left, so fingers crossed it survives! 😅

## SparkFun Thing Plus Qwiic Shield
This week, I had the chance to solder the SparkFun Thing Plus Qwiic Shield, and I’ve got to say, I really like the design of this board. It provides me with two I2C connection ports, which means I don’t have to wire 8 individual connections if I need to output to multiple devices. The Qwiic system simplifies the wiring process significantly.

<p align="center">
  <img src="https://github.com/user-attachments/assets/c1bdbe84-2f33-414c-a44a-81dd05033fa0" width="45%" />
  <img src="https://github.com/user-attachments/assets/8b48372b-d522-4980-82b9-09913a4495f0" width="45%" />
</p>

The board itself looks and feels very high quality, and it fits into the breadboard perfectly. It stays snug without wobbling around, which is a huge plus when working on projects that require steady connections. Overall, it’s a solid board that makes prototyping much easier and cleaner.

![221728544588_ pic](https://github.com/user-attachments/assets/13457de4-ba7a-4211-900d-165e69ea0b78)

## APDS9960 Gesture and Proximity Detection

I wrote the code for the APDS9960 sensor to handle both proximity sensing and gesture detection, with feedback displayed on an OLED screen. This system has 2 modes mainly because the sensor cannot handle both proximity and gesture detection simultaneously. The system starts in proximity mode, where it continuously reads and displays proximity data. When a valid gesture (up, down, left, or right) is detected, the code switches to gesture mode and shows the corresponding gesture on the display. If no valid gestures are detected for a certain period (5 seconds), the system automatically reverts back to proximity mode. To avoid false gesture triggers after switching modes, I’ve added a temporary 2-second delay where gesture detection is disabled. This ensures the system works smoothly, with the OLED providing real-time feedback for both proximity readings and gestures.

```cpp
#include <Adafruit_APDS9960_Particle.h>
#include "Particle.h"
#include <Adafruit_SSD1306_RK.h>
#include "Adafruit_SSD1306.h"
#include "Adafruit_GFX.h"

Adafruit_APDS9960 apds;

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define SCREEN_ADDRESS 0x3D
Adafruit_SSD1306 disp(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

unsigned long lastGestureTime = 0;
unsigned long gestureDisableTime = 0;
const unsigned long gestureTimeout = 5000;  // 5 seconds timeout for switching back to proximity mode
const unsigned long gestureDisableDuration = 2000; // 2 seconds of disabled gesture mode after switching back to proximity
bool gestureMode = false;

void setup() {
    if (!disp.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
        disp.clearDisplay();
        disp.setTextSize(1);
        disp.setTextColor(WHITE);
        disp.setCursor(0, 0);
        disp.println("OLED failed");
        disp.display();
        for (;;);
    } else {
        disp.clearDisplay();
        disp.setTextSize(1);
        disp.setTextColor(WHITE);
        disp.setCursor(0, 0);
        disp.println("APDS9960 Initialized");
        disp.display();
    }

    delay(1000);

    if (!apds.begin()) {
        disp.clearDisplay();
        disp.setCursor(0, 0);
        disp.println("Failed to start APDS9960");
        disp.display();
        for (;;);
    }

    // Start in proximity mode, but also enable gesture detection
    apds.enableProximity(true);
    apds.enableGesture(true); // Gesture mode is always enabled for detection
    disp.clearDisplay();
}

void loop() {
    unsigned long currentMillis = millis();

    if (!gestureMode) {
        // We are in proximity mode
        uint8_t proximity = apds.readProximity();

        disp.clearDisplay();
        disp.setCursor(0, 0);
        disp.setTextSize(1);
        disp.setTextColor(WHITE);
        disp.println("Proximity Sensor:");
        disp.printf("Value: %d", proximity);
        Serial.printf("Proximity: %d\n", proximity);

        // Temporarily disable gesture mode for a short period after switching back to proximity mode
        if (gestureDisableTime > 0 && currentMillis - gestureDisableTime < gestureDisableDuration) {
            // Keep gesture mode disabled for the gestureDisableDuration
            return;
        }

        // Re-enable gesture detection after the cooldown period
        apds.enableGesture(true);

        // Check if a gesture is detected
        if (apds.gestureValid()) {
            gestureMode = true;
            lastGestureTime = currentMillis;
        }

    } else {
        // We are in gesture mode
        if (apds.gestureValid()) {
            uint8_t gesture = apds.readGesture();

            disp.clearDisplay();
            disp.setCursor(0, 0);
            disp.setTextSize(1);
            disp.setTextColor(WHITE);
            disp.println("Gesture detected:");

            switch (gesture) {
                case APDS9960_UP:
                    disp.println("UP");
                    Serial.println("Gesture: UP");
                    lastGestureTime = currentMillis;  // Stay in gesture mode
                    break;
                case APDS9960_DOWN:
                    disp.println("DOWN");
                    Serial.println("Gesture: DOWN");
                    lastGestureTime = currentMillis;  // Stay in gesture mode
                    break;
                case APDS9960_LEFT:
                    disp.println("LEFT");
                    Serial.println("Gesture: LEFT");
                    lastGestureTime = currentMillis;  // Stay in gesture mode
                    break;
                case APDS9960_RIGHT:
                    disp.println("RIGHT");
                    Serial.println("Gesture: RIGHT");
                    lastGestureTime = currentMillis;  // Stay in gesture mode
                    break;
                default:
                    // UNKNOWN gesture, start countdown
                    disp.println("UNKNOWN");
                    Serial.println("Gesture: UNKNOWN");
                    break;
            }

            // If the gesture is UNKNOWN, start the countdown to switch back to proximity mode
            if (gesture == 0) {  // UNKNOWN is generally represented by 0
                unsigned long timeSinceLastGesture = currentMillis - lastGestureTime;
                int remainingTime = max(0, (gestureTimeout - timeSinceLastGesture) / 1000); // Ensure countdown doesn't go below 0

                // Display countdown on the OLED
                disp.setCursor(0, 30);
                disp.setTextSize(1);
                disp.setTextColor(WHITE);
                disp.printf("Switching in: %d sec", remainingTime);

                // Check if 5 seconds have passed with no valid gestures or only "UNKNOWN"
                if (timeSinceLastGesture > gestureTimeout) {
                    gestureMode = false;  // Switch back to proximity mode
                    apds.enableGesture(false);  // Disable gesture detection temporarily
                    gestureDisableTime = currentMillis;  // Record the time when gesture detection was disabled
                }
            }
        }
    }

    disp.display();
    delay(100);  // Short delay for smooth updates
} 
```
<p align="center">
  <img src="https://github.com/user-attachments/assets/485107a4-063a-4364-8240-c5a4d95661d5" width="30%" />
  <img src="https://github.com/user-attachments/assets/bf3fa911-ed13-4a23-a712-a1ea16f5479f" width="30%" />
  <img src="https://github.com/user-attachments/assets/3ae8e903-2c1b-4d7f-a3b2-525c8d3587a9" width="30%" />
</p>


The APDS9960 is not the most accurate proximity sensor in the world, but for the most part, it gets the job done. For future improvements, I’m considering writing a calibration code to convert the raw proximity values (which range from 0 to 255) into actual distance measurements. This would make the data more meaningful and easier to interpret. I’m also thinking about adjusting the polling rate to make the sensor more responsive and improve the sensitivity of movement detection. However, the polling rate also impacts the system’s scheduling, and since the power on the Photon 2 is quite limited, I’ll need to balance responsiveness with resource consumption. Overall, this experiment was really fun! 

## Project Proposal
[Tommy_Jing_Jiawen_Chen_Project_2_Proposal.pdf](https://github.com/user-attachments/files/17330774/Tommy_Jing_Jiawen_Chen_Project_2_Proposal.pdf)





