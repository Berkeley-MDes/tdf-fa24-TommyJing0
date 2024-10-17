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




