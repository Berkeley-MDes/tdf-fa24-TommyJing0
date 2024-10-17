## Week 7
## Week of 10/14/2024
## Custom Webhook, Google Cloud Run Functions, and API Calls
This week, I made great strides on the project by integrating ChatGPT and speech-to-text functionality into the Particle Photon. I used custom webhook functions in the Particle console, and hosted the code through Google Cloud Run Functions. This setup allows Google Cloud Run Functions to make different API calls, communicate back to the Particle Cloud, and eventually connect to the Photon.
Figuring this out took some time, but I’m excited about how it came together. I’m happy to share my process and hope it can help others working on similar projects.

***

### Setting Up the Project
First and foremost, I registered a Google Cloud account and created a project called **Project 1**. After that, I navigated to the Google Cloud dashboard and used the search bar to find **Google Cloud Run Functions** to begin setting up my webhook.
It's important to note that **Google Cloud Run Functions** and **Cloud Run** serve similar purposes in terms of running code in the cloud, but there are key differences:
- **Google Cloud Run Functions** is a fully managed serverless platform where you can run functions in response to events without managing the infrastructure. It is ideal for lightweight and event-driven use cases.
- **Cloud Run**, on the other hand, allows you to run containerized applications in a serverless environment. This makes it better suited for more complex or persistent applications, as you can deploy any containerized code.

<img width="1504" alt="Screenshot 2024-10-16 at 7 46 55 PM" src="https://github.com/user-attachments/assets/a07b820f-02d7-4a80-8dd8-23d9cae7e030">

I chose **Cloud Run Functions** because it suited my needs for handling event-driven tasks without the overhead of managing infrastructure.

***

### Creating the Function
To create a new function, click on the **CREATE FUNCTION** button. This will take you to the function creation page, where you'll be prompted to fill out some key details like the **Function Name**, **Region**, and **Trigger**. You can specify the event type that will trigger your function, such as an HTTP request or a cloud event. 

<img width="1504" alt="Screenshot 2024-10-16 at 8 01 29 PM" src="https://github.com/user-attachments/assets/9f9c69d2-7a82-4197-a276-dfeac6f6fb2b">

Since I am using **ChatGPT** and **Speech to Text**, the trigger type will be **HTTPS**. For testing purposes only, I chose to allow **unauthenticated invocations**. 
> **Note**: Allowing unauthenticated invocations is highly insecure and should not be used in production environments. Always secure your endpoints in live applications to protect against unauthorized access.

If you scroll down, you will see the **Runtime service account** selection box. You do not want to select the first two options, as shown in the picture below. Instead, navigate to the **Particle Console** to set up the connection. This will allow your Particle device to communicate with your Google Cloud function. In my case, I chose the third option named **Particle**, which is the service account I set up for this purpose.

<p float="left">
  <img src="https://github.com/user-attachments/assets/fe0b7a97-5656-41ca-b66c-099700cd8ad5" width="49%" />
  <img src="https://github.com/user-attachments/assets/a5bcbdc1-6a77-4aed-9bb8-b9c0afdb1fb5" width="49%" />
</p>

Alright, so now you're in the **Particle Console**. Here's the link to get there: [Particle Console Integrations](https://console.particle.io/integrations/gallery).

Once you're on this page, search for **Google Cloud Platform** in the integrations gallery. After you click **Start**, follow the instructions provided—it will guide you through the integration process. Since you already have your Google Cloud set up, you might be able to skip a few steps.

The key component here is **Pub/Sub**. Pub/Sub (Publish/Subscribe) is a messaging service that allows your Particle device to send and receive messages with your Google Cloud function. This communication is essential for passing data between Particle and Google Cloud. Once Pub/Sub is configured, your device can trigger your Google Cloud function using published events, ensuring seamless interaction between the platforms.

<img width="1512" alt="Screenshot 2024-10-16 at 8 30 00 PM" src="https://github.com/user-attachments/assets/ad973c4f-4771-42ec-b63a-8d2872c32560">

After you finish setting up the integration, you'll now be able to select the **third option** shown in the image above when creating Cloud Run functions. This is the service account linked to Particle, which you want to choose for proper communication between Google Cloud and your Particle device.

Once you’ve selected this, you’re all set to proceed to the next step—the code! Yeah! 🎉 (Don’t worry, I got you!)

<img width="1512" alt="Screenshot 2024-10-16 at 8 48 28 PM" src="https://github.com/user-attachments/assets/db6c4cfb-0f6b-40b4-8cc2-cff80ccb66a0">

>**Important:** Remember to set up your Entry Point correctly. In my case, I named it `particleWebhookHandler`, so make sure that matches what you have in your code. The Entry Point defines the function that will handle incoming requests.

***

### Particle Webhook Handler with ChatGPT Integration (index.js)
```javascript
const functions = require('@google-cloud/functions-framework');
const axios = require('axios');

// Define your OpenAI API key
const OPENAI_API_KEY = 'your-openai-api-key-here'; // Insert your API key here

// Cloud Function Entry Point
functions.http('particleWebhookHandler', async (req, res) => {
    console.log('Received request:', req.body);

    // Check if data exists
    const eventData = req.body.data || 'Hello, how can I help you today?';
    console.log(`Processing event data: ${eventData}`);

    try {
        // Call the OpenAI API
        const openAiResponse = await axios.post(
            'https://api.openai.com/v1/chat/completions',
            {
                model: 'gpt-4',
                messages: [{ role: 'user', content: eventData }],
            },
            {
                headers: {
                    'Authorization': `Bearer ${OPENAI_API_KEY}`,
                    'Content-Type': 'application/json',
                },
            }
        );

        // Extract the response from OpenAI
        const assistantResponse = openAiResponse.data.choices[0].message.content;
        console.log(`Response from ChatGPT: ${assistantResponse}`);

        // Send the response back to Particle Cloud
        res.status(200).send({ message: assistantResponse });
    } catch (error) {
        console.error('Error calling ChatGPT:', error.response ? error.response.data : error.message);
        res.status(500).send({ error: 'Failed to call GPT API' });
    }
});
```
This code is a Google Cloud Function designed to receive data from a Particle device, send it to the ChatGPT API (using the GPT-4 model), and return a response back to the Particle Cloud. It begins by defining an HTTP endpoint (particleWebhookHandler), which processes incoming requests. The function extracts the message (event data) from the request and sends it to the ChatGPT API using axios. Once the response from ChatGPT is received, the function extracts the message content and sends it back to the Particle Cloud as a response. If there’s an error during the API call, the function logs it and returns a 500 error code. 

***

### Particle Webhook Handler with ChatGPT Integration (package.json)
```json
{
  "name": "particle-cloud-function",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "axios": "^1.4.0",
    "@google-cloud/functions-framework": "^3.0.0"
  }
}
```
This is the package.json file. It is needed because it defines the dependencies and scripts for your Node.js project. It ensures that all necessary packages, such as axios and @google-cloud/functions-framework, are installed and your cloud function runs properly.

***

### Test Code
If everything is set up correctly, you can run this test code in the **Triggering Event** section to send a message to ChatGPT. You can change `"Hello"` to anything you want ChatGPT to respond to.

```
{
  "data": "Hello"
}
```

<img width="1512" alt="Screenshot 2024-10-16 at 9 09 57 PM" src="https://github.com/user-attachments/assets/d7e9602b-611a-4833-a4ca-3476d65c1be4">

If you see the green letters saying **"Function is ready to test"**, that means only one thing: **you are a genius**! Give yourself a pat on the back and click **Run Test**.

<img width="1512" alt="Screenshot 2024-10-16 at 9 21 46 PM" src="https://github.com/user-attachments/assets/1a8c35d4-28f9-4dc5-b55b-e58770c302c8">

Now you are talking to ChatGPT! 🎉 Remember to hit Deploy Code once everything is set up, and you’re ready to move on to the next step.

***

### Receiving Responses in Particle Photon
Now that the Cloud Run function is set up, you need to navigate back to the Particle Console integration section. Scroll all the way down to the Custom Webhook Services and click on Start Now to begin setting up the webhook integration.

<img width="1512" alt="Screenshot 2024-10-16 at 9 40 38 PM" src="https://github.com/user-attachments/assets/468b22cd-06e9-4c99-a83a-0a8409fc88d5">

The page should look like this ↑

<img width="1512" alt="Screenshot 2024-10-16 at 9 44 49 PM" src="https://github.com/user-attachments/assets/4540307c-c892-42ad-9c7d-de9890ee9185">

During the setup, you can assign any name to your integration; however, pay close attention to the **Event Name**, as this will be the trigger for your function call in the code. 
Ensure that the **URL** matches the one provided in your Cloud Run Function configuration. See picture below ↓

<img width="1512" alt="Screenshot 2024-10-16 at 9 54 08 PM" src="https://github.com/user-attachments/assets/7d1192eb-5f7d-4b7e-a332-08c913216249">

<img width="1512" alt="Screenshot 2024-10-16 at 9 58 14 PM" src="https://github.com/user-attachments/assets/b37b50c5-d132-4910-8d35-324cea10db92">

Now you want to hit the **Extra Settings** button, and under the drop-down menu, select **Custom**. This will allow you to customize the data being sent in your webhook request.

Next, paste the following code into the **Custom** field:

```json
{
  "data": "{{PARTICLE_EVENT_VALUE}}"
}
```
This piece of code ensures that the Particle Event Value (which is the data sent from your Particle device) is properly transmitted in the body of the webhook request. Essentially, the data that you publish with your Particle event is included in the request sent to the Google Cloud Run Function, so it can process the data and provide a response.

Now, if you click on your **Integrations**, it will take you to a detailed page. On this page, you can click the **Test** button on the right side to verify if the connection to Google Cloud Run Functions is successful.

<img width="1512" alt="Screenshot 2024-10-16 at 10 29 22 PM" src="https://github.com/user-attachments/assets/5e6d1beb-4b8e-4adb-bf23-21f6a87bb570">

If everything is set up correctly, you should see a message on the bottom left corner that says:

**Success! Your integration is behaving as expected.**

<img width="1512" alt="Screenshot 2024-10-16 at 10 37 59 PM" src="https://github.com/user-attachments/assets/684e16cb-d221-4cd6-81b9-a38b4e636f52">


***

### Particle Photon Code for ChatGPT Integration with Webhook Response

Hey, don’t give up! 💪 I know you’re tired, but you’re almost there—this is the last step! 🎉

```cpp
#include <ArduinoJson.h>
#include "Particle.h"
#include <Adafruit_SSD1306_RK.h>  // Include Adafruit SSD1306 library
#include <ArduinoJson.h>           // Include Arduino JSON library for parsing

// OLED setup
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define SCREEN_ADDRESS 0x3D  // I2C address of the OLED
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

// Define event name
const char* EVENT_NAME = "assistant_request";
bool requestSent = false;  // Ensure the request is sent only once

void setup() {
    // Initialize OLED
    if (!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
        while (1);  // Halt if OLED initialization fails
    }

    display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.setCursor(0, 0);
    display.println("Sending Request...");
    display.display();

    // Subscribe to the webhook response
    Particle.subscribe("hook-response/assistant_request", webhookResponseHandler, MY_DEVICES);

    // Send the request once at startup
    sendRequest();
}

void loop() {
    // No repeated logic needed in loop
}

// Function to send the event to Particle Cloud
void sendRequest() {
    if (!requestSent) {
        String data = "Hello, how are you?";
        bool success = Particle.publish(EVENT_NAME, data, PRIVATE);
        
        display.clearDisplay();
        display.setCursor(0, 0);

        if (success) {
            display.println("Request Sent");
        } else {
            display.println("Failed to Send");
        }
        display.display();

        requestSent = true;  // Ensure request is only sent once
    }
}

// Webhook response handler to display response content on OLED
void webhookResponseHandler(const char* event, const char* data) {
    display.clearDisplay();
    display.setCursor(0, 0);
    display.setTextSize(1);
    display.setTextColor(WHITE);

    // Parse the JSON response to extract the "message" field
    StaticJsonDocument<256> doc;
    DeserializationError error = deserializeJson(doc, data);

    if (error) {
        display.println("Parsing failed!");
    } else {
        const char* message = doc["message"];
        display.println(message);  // Display only the message content
    }

    display.display();
}
```

***

### Code Breakdown

#### 1. OLED Display Setup:
- The first part of the code sets up an OLED screen using the Adafruit library. It’s initialized to show information such as whether the request has been sent successfully or if any errors occur.
- `display.begin()` initializes the screen, and if the OLED fails to initialize, the system halts.

#### 2. Event Name and Subscription:
- The event name `"assistant_request"` is defined, and this is the key part that links your Photon with the webhook from Google Cloud. This event will be triggered when data is published to the Particle Cloud, which in turn calls the Google Cloud Run Function.
- The code subscribes to responses coming from the webhook with `Particle.subscribe("hook-response/assistant_request", webhookResponseHandler, MY_DEVICES);`. This ensures that when the webhook sends back data (from ChatGPT), the Photon can receive and handle it.

#### 3. Sending a Request:
- The function `sendRequest()` publishes a message (in this example: “Hello, how are you?”) to the Particle Cloud, using `Particle.publish()`. This action triggers the webhook, which connects to the Google Cloud Run Function.
- To ensure the request is sent only once, a flag `requestSent` is set to `true` after the request is published.

#### 4. Webhook Response Handling:
- The `webhookResponseHandler()` function listens for a response from the webhook. Once the Google Cloud Run Function returns a response (i.e., the ChatGPT-generated message), it parses the response using `ArduinoJson` to extract the message.
- The extracted message is then displayed on the OLED screen.

#### 5. Error Handling:
- If any errors occur during the parsing of the response (e.g., if the JSON response is malformed), the OLED will display “Parsing failed!”.

***

### Demo Video: ChatGPT Integration

This is the demo video where I sent the message **"Hello, how are you?"** to ChatGPT. Of course, the response was as exciting as a weather report, but hey, it works!

<div style="display: flex; justify-content: space-between;">
  <img src="https://github.com/user-attachments/assets/61b1d92c-428d-48ec-9d7c-c292c7c2508e" width="49%" alt="Demo Video GIF">
  <img src="https://github.com/user-attachments/assets/9bf9bafc-16c3-4541-9af3-c1632b026777" width="49%" alt="Additional Screenshot">
</div>

The image on the right shows the response when I asked **"Tell me a joke"**. And of course, the joke ChatGPT came up with is *very* funny! 
