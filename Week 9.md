# Week 9
# Week of 10/20/2024
# Exploring LLMs on ZeroWidth Plantform 
This week, we began our third project, which focuses on LLMs and utilizes a platform called ZeroWidth. I successfully logged in using the provided invitation link and created my first project, naming it “Tommy’s Project.”

<img width="1512" alt="Screenshot 2024-10-31 at 10 12 50 AM" src="https://github.com/user-attachments/assets/286b268d-4b13-4d03-90be-b140fa8e9fe9">

I created my first workflow in ZeroWidth using a simple flow to test how the platform handles prompt inputs and responses from an LLM. I set up a “Single Prompt Input” as the starting node, where users can input their prompt text. This connects to an “OpenAI GPT-4o Mini” node, which processes the input based on predefined instructions and generates a response. Finally, the response flows to an “Output” node that can display the LLM’s output. This workflow allows me to see how prompts are processed through each stage and experiment with different configurations for the LLM responses.

<img width="1512" alt="Screenshot 2024-10-31 at 10 21 11 AM" src="https://github.com/user-attachments/assets/f701a4b0-3589-4286-aff0-d3bb1c05530f">

I tested the workflow with a “hello” message, and it worked perfectly, processing through each node as expected.

<img width="1512" alt="Screenshot 2024-10-31 at 10 23 53 AM" src="https://github.com/user-attachments/assets/1655876b-505d-478c-a0f4-ad666d4bd63d">

I then created and experimented with a knowledge-based search flow. Starting with a Single Prompt Input node to capture user input, I connected it to the Search Knowledge node to retrieve relevant information from a knowledge database. The retrieved chunks were then processed through a Variable Replacer to dynamically adjust terms or variables. This refined input was fed into OpenAI GPT-4o Mini, which generated a response based on the user prompt and relevant knowledge. Finally, the response was displayed in the Output node, completing the flow.

<img width="1512" alt="Screenshot 2024-10-31 at 10 35 27 AM" src="https://github.com/user-attachments/assets/0ae2192a-fca6-4684-8792-cb7a17ee2001">

At this point, I was feeling too lazy to create my own knowledge base, so I borrowed Shryas’s existing knowledge base (hoping he doesn’t mind) and asked GPT who he is.

<img width="1512" alt="Screenshot 2024-10-31 at 10 40 24 AM" src="https://github.com/user-attachments/assets/15cd19e0-f9bd-4db5-a035-9a07551082cd">

As you can see, the information is retrieved from the knowledge base, and here we have it!

<img width="1512" alt="Screenshot 2024-10-31 at 2 57 47 PM" src="https://github.com/user-attachments/assets/bf8691f2-f811-438b-8a1a-57b647c6e4b2">

I then added my GitHub weekly reports from Week 1 to Week 8, along with the README file, to the knowledge base using the single chunk method for efficiency and simplicity. This setup worked smoothly using the same workflow shown above.

<img width="1512" alt="Screenshot 2024-10-31 at 3 01 57 PM" src="https://github.com/user-attachments/assets/8d70a2ec-f6f0-4fe2-a49d-24a698082932">

To test it, I asked, “What did Tommy (me) do in Week 1?” and the answer was spot on!

## Speculations
The future of LLMs lies in creating highly personalized AI systems that adapt to individual preferences and behaviors. These AI agents could provide tailored recommendations, predict needs, and streamline tasks across domains like health monitoring, home automation, and professional workflows. As LLMs become more advanced and accessible, creating personalized AI could become even easier, allowing users to build systems that offer seamless, intuitive support, enhancing productivity and quality of life.
