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





