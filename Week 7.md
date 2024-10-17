## Week 7
## Week of 10/14/2024
## Custom Webhook, Google Cloud Functions, and API Calls
This week, I made great strides on the project by integrating ChatGPT and speech-to-text functionality into the Particle Photon. I used custom webhook functions in the Particle console, and hosted the code through Google Cloud Run Functions. This setup allows Google Cloud Run Functions to make different API calls, communicate back to the Particle Cloud, and eventually connect to the Photon.
Figuring this out took some time, but I’m excited about how it came together. I’m happy to share my process and hope it can help others working on similar projects.

### Setting Up the Project
First and foremost, I registered a Google Cloud account and created a project called **Project 1**. After that, I navigated to the Google Cloud dashboard and used the search bar to find **Google Cloud Functions** to begin setting up my webhook.
It's important to note that **Google Cloud Functions** and **Cloud Run** serve similar purposes in terms of running code in the cloud, but there are key differences:
- **Google Cloud Functions** is a fully managed serverless platform where you can run functions in response to events without managing the infrastructure. It is ideal for lightweight and event-driven use cases.
- **Cloud Run**, on the other hand, allows you to run containerized applications in a serverless environment. This makes it better suited for more complex or persistent applications, as you can deploy any containerized code.

<img width="1504" alt="Screenshot 2024-10-16 at 7 46 55 PM" src="https://github.com/user-attachments/assets/a07b820f-02d7-4a80-8dd8-23d9cae7e030">

I chose **Cloud Run Functions** because it suited my needs for handling event-driven tasks without the overhead of managing infrastructure.

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

### Test Code
If everything is set up correctly, you can run this test code in the **Triggering Event** section to send a message to ChatGPT. You can change `"Hello"` to anything you want ChatGPT to respond to.

```
{
  "data": "Hello"
}
```
<img width="1512" alt="Screenshot 2024-10-16 at 9 09 57 PM" src="https://github.com/user-attachments/assets/d7e9602b-611a-4833-a4ca-3476d65c1be4">

If you see the green letters saying **"Function is ready to test"**, that means only one thing: **you are a genius**! Give yourself a pat on the back and click **Run Test**.

