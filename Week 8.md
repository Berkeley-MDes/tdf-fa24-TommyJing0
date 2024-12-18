# Week 8
# Week of 10/20/2024
# Project Major Software Update

This week, I continued working on our project and essentially rewrote the entire code to enhance its structure, readability, and efficiency. The primary goal was to make the system more modular and easier to maintain, as well as to ensure that all components are properly initialized before the main operation begins.

# System Initialization and Checks

At the beginning of this new version, the system performs comprehensive checks on all components before proceeding. This includes:

1. **OLED Display**: The system initializes the OLED display using the Adafruit SSD1306 library. It checks if the display begins successfully; if not, it enters an infinite loop to prevent further execution. This ensures that any display-related issues are caught early.

2. **MPU6050 Gyroscope and Accelerometer Module**: The MPU6050 sensor is initialized and tested for a successful connection. If the test fails, the system displays an error message on the OLED and halts execution. This step is crucial for applications that rely on motion sensing.

3. **Google Maps API for Location Requests**: The system sends location requests to the Google Maps API using the `GoogleMapsDeviceLocator` library. This allows the device to obtain accurate geographical coordinates, which can be used for location-based features.

## Removal of GPT Connection Check

I decided to remove the GPT connection check from the initialization process. This API integration is credit-based and incurs real costs with each request. Since the GPT service is consistently stable and less prone to downtime, it was more economical and practical to eliminate this check.

## Initialization Sequence

```cpp
#include <Wire.h>
#include <Adafruit_SSD1306_RK.h>
#include <MPU6050.h>
#include <google-maps-device-locator.h>

// OLED display configuration
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define SCREEN_ADDRESS 0x3D

// Hardware pins
const int buttonPinNext = D7;
const int motorPin = A5;

// Thresholds for sensor readings
const float THRESHOLD_X = -0.7;
const float THRESHOLD_Y = 0.1;
const float THRESHOLD_Z = 0.4;

// Global variables
bool motorTriggered = false;
bool awaitingResponse = false;
unsigned long requestStartTime = 0;
const unsigned long responseTimeout = 15000;
bool gptResponseReceived = false;
bool locationReceived = false;
float latitude = 0.0;
float longitude = 0.0;
float accuracy = 0.0;
String gptResponse;

// Object instances
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);
MPU6050 mpu;
GoogleMapsDeviceLocator locator;

void setup() {
    Serial.begin(9600);

    // Initialize OLED display
    if (!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
        Serial.println("OLED initialization failed");
        while (1); // Halt execution
    }
    display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.setCursor(0, 0);
    display.println("OLED Ready");
    display.display();
    delay(1000);

    // Initialize MPU6050 sensor
    mpu.initialize();
    if (!mpu.testConnection()) {
        display.clearDisplay();
        display.setCursor(0, 0);
        display.println("MPU6050 Not Ready");
        display.display();
        Serial.println("MPU6050 initialization failed");
        while (1); // Halt execution
    }
    display.clearDisplay();
    display.setCursor(0, 0);
    display.println("MPU6050 Ready");
    display.display();
    delay(1000);

    // Set up button and motor pins
    pinMode(buttonPinNext, INPUT_PULLDOWN);
    pinMode(motorPin, OUTPUT);
    digitalWrite(motorPin, HIGH); // Ensure motor is off

    // Subscribe to webhook response
    Particle.subscribe("hook-response/assistant_request", webhookResponseHandler, MY_DEVICES);

    // Initialize Google Maps Device Locator
    locator.withEventName("deviceLocator")
           .withSubscribe(locationCallback)
           .withLocatePeriodic(60); // Update location every 60 seconds

    display.clearDisplay();
    display.setCursor(0, 0);
    display.println("Location Ready");
    display.display();
    delay(1000);
}
```

# Main Loop

The next part of the code is the `loop()` function, which serves as the core execution loop of the program. It continuously updates the display and processes location data, as well as handling the interaction with the GPT-based response system.

## Function: `void loop()`

The `loop()` function performs the following tasks:

1. **Display Data**: 
   - It calls the `displayData()` function, which retrieves and displays accelerometer data or a countdown if certain conditions are met.

2. **Process Location Updates**: 
   - The `locator.loop()` function is called, which processes any location data and updates the display if new data is available from the Google Maps Device Locator.

3. **Handle GPT Response**: 
   - If the system is awaiting a response from GPT (`awaitingResponse` is `true`), and the GPT response has been received (`gptResponseReceived` is `true`), and the motor hasn't been triggered yet (`motorTriggered` is `false`):
     - It calls `displayGPTResponse()` to show the GPT response on the display.
     - After the response is displayed, the flags `awaitingResponse`, `gptResponseReceived`, and `motorTriggered` are reset appropriately to ensure the system is ready for the next interaction.

```cpp
void loop() {
    displayData();
    locator.loop();

    if (awaitingResponse && gptResponseReceived && !motorTriggered) {
        displayGPTResponse();
        awaitingResponse = false;
        gptResponseReceived = false;
        motorTriggered = true;
    }
}
```

# Hand-Up Motion Detection and Countdown

The next part of the code focuses on detecting the hand-up motion based on accelerometer data and initiating a countdown when the motion is detected.

## Function: `bool isHandUp(float ax, float ay, float az)`

The `isHandUp()` function determines whether the hand is in an "up" position based on the accelerometer readings (`ax`, `ay`, `az`):

- The hand is considered "up" when:
  - The X-axis acceleration (`ax`) is less than `THRESHOLD_X`.
  - The absolute value of the Y-axis acceleration (`ay`) is less than `THRESHOLD_Y`.
  - The Z-axis acceleration (`az`) is greater than `THRESHOLD_Z`.

This check ensures accurate motion detection based on predefined thresholds.

```cpp
bool isHandUp(float ax, float ay, float az) {
    return (ax < THRESHOLD_X) && (abs(ay) < THRESHOLD_Y) && (az > THRESHOLD_Z);
}
```

## Function: `void displayData()`

The `displayData()` function retrieves the current accelerometer readings from the MPU6050 sensor and processes them to determine if the hand is raised:

- The function converts the raw accelerometer values (`ax`, `ay`, `az`) into floating-point values in terms of g-force.
- If `isHandUp()` returns `true`, indicating that the hand is raised, the function triggers the `displayUpCountdown()` to initiate a countdown.
- Otherwise, it calls `displayMainScreen()` to show the accelerometer readings on the display.

```cpp
void displayData() {
    int16_t ax, ay, az;
    mpu.getAcceleration(&ax, &ay, &az);

    float accelX = ax / 16384.0;
    float accelY = ay / 16384.0;
    float accelZ = az / 16384.0;

    if (isHandUp(accelX, accelY, accelZ)) {
        displayUpCountdown();
    } else {
        displayMainScreen(accelX, accelY, accelZ);
    }
}
```

## Function: `void displayUpCountdown()`

When a hand-up motion is detected, the `displayUpCountdown()` function initiates a 5-second countdown on the display:

- The countdown is displayed with the word “UP!” along with the remaining time (from 5 to 0 seconds).
- During each countdown second, the system checks if a button press is detected (`buttonPinNext`), and if so, it interrupts the countdown and calls `requestDirections()` to initiate the next action.
- If no button is pressed during the countdown, the display continues updating the countdown every 100 milliseconds.

```cpp
void displayUpCountdown() {
    for (int countdown = 5; countdown > 0; countdown--) {
        display.clearDisplay();
        display.setTextSize(2);
        display.setCursor(0, 0);
        display.println("UP!");
        display.setTextSize(3);
        display.setCursor(50, 30);
        display.println(countdown);
        display.display();

        for (int i = 0; i < 10; i++) {  
            if (digitalRead(buttonPinNext) == HIGH) {
                requestDirections();
                return;
            }
            delay(100);
        }
    }
}
```
# Home Page

## Function: `void displayMainScreen(float accelX, float accelY, float accelZ)`

The `displayMainScreen()` function updates the OLED display with the current accelerometer readings and location data, if available. It performs the following tasks:

- The display is cleared using `display.clearDisplay()` to ensure that each frame is fresh.
- The text size and cursor position are set for organized display output.
- If location data is available (`locationReceived` is `true`), the function displays:
  - **Latitude**: Displayed with 6 decimal places of precision.
  - **Longitude**: Displayed with 6 decimal places of precision.
  - **Accuracy**: Displayed in meters with 2 decimal places.
- If location data is not available, the `displayAcquiringLocation()` function is called, which provides feedback to the user that the system is still acquiring location information.
- After the location information (or acquiring message), the function displays the accelerometer readings:
  - **X, Y, and Z** positions are shown in floating-point format with 1 decimal place.
- Finally, `display.display()` is called to render all the data on the screen.

This function ensures that both location and position data are displayed dynamically, allowing real-time feedback on the current state of the device.

```cpp
void displayMainScreen(float accelX, float accelY, float accelZ) {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0, 0);

    if (locationReceived) {
        display.printf("Location\n");
        display.printf("Lat: %.6f\n", latitude);
        display.printf("Lon: %.6f\n", longitude);
        display.printf("Acc: %.2fm\n", accuracy);
    } else {
        displayAcquiringLocation();
    }

    display.printf("Position\n");
    display.printf("X %.1f\n", accelX);
    display.printf("Y %.1f\n", accelY);
    display.printf("Z %.1f\n", accelZ);

    display.display();
}
```
# Google Maps Integration

## Function: `void locationCallback(float lat, float lon, float acc)`

The `locationCallback()` function is responsible for handling location data once it is received.

### Key Parameters:
- **`lat` (Latitude)**: Represents the latitude of the current location.
- **`lon` (Longitude)**: Represents the longitude of the current location.
- **`acc` (Accuracy)**: Indicates the accuracy of the location data in meters.

### What the Function Does:
- **Updates Global Variables**: 
  - The values of latitude, longitude, and accuracy passed into the function are assigned to their corresponding global variables (`latitude`, `longitude`, and `accuracy`), making them available throughout the program.
  
- **Sets `locationReceived` Flag**: 
  - The `locationReceived` flag is set to `true`. This flag indicates that the system has successfully received location data and can now proceed with any tasks that depend on having location information.
  
This callback function is a critical part of the location handling system, as it ensures that the most recent location data is updated and available for the rest of the program to use.

```cpp
void locationCallback(float lat, float lon, float acc) {
    latitude = lat;
    longitude = lon;
    accuracy = acc;
    locationReceived = true;
}
```

## Function: `void displayAcquiringLocation()`

The `displayAcquiringLocation()` function provides a visual feedback to the user that the system is in the process of acquiring location data. It simulates a loading animation by printing dots that change periodically.

### Key elements of the function:

- **Static Variables**:
  - `dotCount`: Keeps track of the number of dots to display, simulating a loading effect.
  - `lastUpdateTime`: Stores the time at which the last update occurred. It is used to determine when the dots should be updated.

- **Flow of the Function**:
  - The function starts by printing the message **"Acquiring location"** on the display.
  - It then enters a `for` loop that prints `dotCount` number of dots on the screen, creating a simple animation effect.
  - After the dots, it adds two empty lines for spacing.
  
- **Dot Animation Timing**:
  - The animation updates every 500 milliseconds. This is controlled by comparing the current time (`currentTime`, obtained using `millis()`) with `lastUpdateTime`. If 500 milliseconds have passed, `dotCount` is incremented and the display is updated.
  - The number of dots cycles between 0 and 3, which is achieved using the expression `(dotCount + 1) % 4`.

This function keeps the user informed that the system is attempting to acquire location data and adds a dynamic loading effect with dots that appear and disappear in a repeating pattern.

```cpp
void displayAcquiringLocation() {
    static int dotCount = 0;
    static unsigned long lastUpdateTime = 0;
    unsigned long currentTime = millis();

    display.println("Acquiring location");
    for (int i = 0; i < dotCount; i++) {
        display.print(".");
    }
    display.println();
    display.println();

    if (currentTime - lastUpdateTime >= 500) {
        dotCount = (dotCount + 1) % 4;
        lastUpdateTime = currentTime;
    }
}
```

All the content above outlines the updates made this week. The ChatGPT integration remains largely the same, with some minor improvements in how responses are handled for smoother interactions.

# Project Video Finalized
Jiawen and I spent some time finishing the product demo portion of the video. I also recorded myself explaining the software, system diagram, and other related aspects. She took charge of most of the video editing, while I focused on finalizing the software development. We’re such a great team!

## Speculations
This project made me speculate how IoT systems could evolve into fully autonomous networks. Devices like the Particle Photon, integrated with APIs and sensors, could adapt to user behavior and environmental changes in real-time. In smart cities, such systems could optimize transportation or energy use autonomously, while in industries like logistics or disaster relief, they could provide real-time updates and adaptive solutions, revolutionizing efficiency and personalization.
