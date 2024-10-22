```cpp
#include <Wire.h>
#include <Adafruit_SSD1306_RK.h>
#include <MPU6050.h>
#include <google-maps-device-locator.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define SCREEN_ADDRESS 0x3D
#define LINES_PER_PAGE 8
bool motorTriggered = false;

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

MPU6050 mpu;
GoogleMapsDeviceLocator locator;

const int buttonPinNext = D7;
const int motorPin = A5;
bool awaitingResponse = false;
unsigned long requestStartTime = 0;
const unsigned long responseTimeout = 15000;

bool gptResponseReceived = false;
bool locationReceived = false;

float latitude = 0.0;
float longitude = 0.0;
float accuracy = 0.0;

const float THRESHOLD_X = -0.7;
const float THRESHOLD_Y = 0.1;
const float THRESHOLD_Z = 0.4;

String gptResponse;

void setup() {
    Serial.begin(9600);

    if (!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
        while (1);
    }

    pinMode(buttonPinNext, INPUT_PULLDOWN);
    pinMode(motorPin, OUTPUT);
    digitalWrite(motorPin, HIGH);

    display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.setCursor(0, 0);
    display.println("OLED Ready");
    display.display();
    delay(1000);

    mpu.initialize();
    if (!mpu.testConnection()) {
        display.clearDisplay();
        display.setCursor(0, 0);
        display.println("MPU6050 Not Ready");
        display.display();
        while (1);
    }

    display.clearDisplay();
    display.setCursor(0, 0);
    display.println("MPU6050 Ready");
    display.display();
    delay(1000);

    Particle.subscribe("hook-response/assistant_request", webhookResponseHandler, MY_DEVICES);

    locator.withEventName("deviceLocator")
           .withSubscribe(locationCallback)
           .withLocatePeriodic(60);

    display.clearDisplay();
    display.setCursor(0, 0);
    display.println("Location Ready");
    display.display();
    delay(1000);
}

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

bool isHandUp(float ax, float ay, float az) {
    return (ax < THRESHOLD_X) && (abs(ay) < THRESHOLD_Y) && (az > THRESHOLD_Z);
}

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

void webhookResponseHandler(const char* event, const char* data) {
    static int testDirection = 0;
    switch(testDirection) {
        case 0: gptResponse = "Go straight"; break;
        case 1: gptResponse = "Turn left"; break;
        case 2: gptResponse = "Move forward"; break;
        case 3: gptResponse = "Step back"; break;
        case 4: gptResponse = "Go east"; break;
        case 5: gptResponse = "Turn right"; break;
    }
    testDirection = (testDirection + 1) % 6;
    
    gptResponseReceived = true;
    processGPTResponse();
}

void locationCallback(float lat, float lon, float acc) {
    latitude = lat;
    longitude = lon;
    accuracy = acc;
    locationReceived = true;
}

void requestDirections() {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0, 0);
    display.println("Requesting directions");
    display.display();

    String query = String::format(
        "Guide me from my current coordinates (latitude: %.6f, longitude: %.6f) to 3200 California Street, Berkeley, California, 94703. Please provide concise step-by-step directions, only one step at a time. The response must be less than 15 words.",
        latitude, longitude
    );

    bool success = Particle.publish("assistant_request", query, PRIVATE);

    if (success) {
        awaitingResponse = true;
        requestStartTime = millis();
        int dotCount = 0;
        while (awaitingResponse && millis() - requestStartTime < responseTimeout) {
            if (gptResponseReceived) {
                displayGPTResponse();
                return;
            }

            display.clearDisplay();
            display.setCursor(0, 0);
            display.println("Requesting directions");
            for (int i = 0; i < dotCount; i++) {
                display.print(".");
            }
            display.display();
            
            dotCount = (dotCount + 1) % 4;
            delay(500);
        }
        display.clearDisplay();
        display.println("Request timed out");
        display.display();
        delay(2000);
    } else {
        display.clearDisplay();
        display.println("Failed to send request");
        display.display();
        delay(2000);
    }
}

void displayGPTResponse() {
    display.clearDisplay();
    display.setTextSize(1);
    display.setCursor(0, 0);
    display.println(gptResponse);
    display.display();

    while (true) {
        if (digitalRead(buttonPinNext) == HIGH) {
            delay(200);
            return;
        }
    }
}

void processGPTResponse() {
    String lowerResponse = gptResponse.toLowerCase();
    
    if (lowerResponse.indexOf("east") != -1 || lowerResponse.indexOf("right") != -1) {
        triggerDirectionalVibration(1);
    } else if (lowerResponse.indexOf("west") != -1 || lowerResponse.indexOf("left") != -1) {
        triggerDirectionalVibration(2);
    } else if (lowerResponse.indexOf("north") != -1 || lowerResponse.indexOf("forward") != -1 || lowerResponse.indexOf("straight") != -1) {
        triggerDirectionalVibration(3);
    } else if (lowerResponse.indexOf("south") != -1 || lowerResponse.indexOf("back") != -1) {
        triggerDirectionalVibration(4);
    }
}

void triggerDirectionalVibration(int count) {
    for (int i = 0; i < count; i++) {
        digitalWrite(motorPin, LOW);
        delay(200);
        digitalWrite(motorPin, HIGH);
        if (i < count - 1) {
            delay(200);
        }
    }
    delay(500);
}
```
