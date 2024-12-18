# Week 10
# Week of 11/09/2024
# Crafting Custom AI through Experimental Design on Zerowidth
## Experiment 1 (Ask a GPT)
In this experiment, I tested two different models—GPT-4 and GPT-4 Turbo—to find the right balance between creativity and consistency. GPT-4 gave me more detailed and thorough responses, while GPT-4 Turbo was noticeably faster. I also played around with the temperature settings. Higher values made the AI more creative, but lower values kept the responses more focused and consistent. Overall, this experiment helped me better understand the trade-offs between speed, detail, and creativity when using these models.
<img width="1512" alt="Screenshot 2024-11-09 at 8 50 04 AM" src="https://github.com/user-attachments/assets/3faeca02-b3f5-4fdb-93a2-63b06254a321">

## Experiment 2 (Ask a GPT with Instructions)
In this experiment, I instructed GPT to act as an agent representing me. While it performed this role effectively, the absence of a dedicated knowledge base meant that its responses were often generic rather than tailored to my specific context or needs. This highlighted the importance of integrating personalized data to enhance the relevance and accuracy of the agent’s output.
<img width="1512" alt="Screenshot 2024-11-09 at 8 51 49 AM" src="https://github.com/user-attachments/assets/328ebc49-62d6-4450-94af-4124a37ef953">

## Experiment 3 (Ask a GPT with Instructions and RAG)
In this experiment, I tackled the issue of generic responses by building a knowledge base using chunked data from my GitHub reports. This structured dataset enabled the agent to deliver more accurate and personalized answers.
<img width="1512" alt="Screenshot 2024-11-09 at 8 58 37 AM" src="https://github.com/user-attachments/assets/8532c6c4-f008-4897-bcf9-05e91516a6c2">

To fine-tune the system, I implemented sliders to control chunk size and similarity thresholds. Increasing the chunk size allowed the agent to access more detailed information, while adjusting the similarity threshold helped strike a balance between generality and specificity in its responses. This approach significantly improved the agent’s ability to match user queries with relevant information from the knowledge base.
<img width="1512" alt="Screenshot 2024-11-09 at 8 59 36 AM" src="https://github.com/user-attachments/assets/2beeb5a9-3480-44d6-88ee-a3c5209de988">

## Experiment 4 (Ask a GPT with Instructions, RAG, and variables)
In this experiment, I incorporated “Location” and “Year” variables to enhance the context specificity of the agent’s responses. Given that I’m based in Berkeley, CA, and primarily working in 2024, the agent was configured to prioritize information relevant to this specific location and time frame. This adjustment ensured that responses were more aligned with my current context, avoiding irrelevant data from other locations or years.
<img width="1512" alt="Screenshot 2024-11-09 at 9 04 08 AM" src="https://github.com/user-attachments/assets/6b229603-06e4-43e9-a8c9-ac5b578d919c">

## Experiment 5 (AI Engine Oil Advisor)
In this experiment, I built on previous frameworks to develop a specialized tool for car maintenance. Users input their car’s year, make, model, and driving style—whether comfort, sport, or track. Based on these parameters, the AI generates tailored recommendations, including the top three oil and filter options and suggested service intervals. The goal is to help users optimize their vehicle’s performance and longevity by aligning maintenance with their specific driving habits and conditions.
<img width="1512" alt="Screenshot 2024-11-09 at 9 07 05 AM" src="https://github.com/user-attachments/assets/94127ff1-a5f6-4839-ac84-5909da3df7b0">
<img width="1512" alt="Screenshot 2024-11-09 at 9 09 25 AM" src="https://github.com/user-attachments/assets/dfff7b60-29bd-4688-892e-e634c9faf539">

To achieve this, I employed multiple prompts to guide the AI on task execution and output formatting. A knowledge base was integrated, containing a comprehensive list of oil brands and product options, along with detailed data sheets for various high-performance oils. This setup enabled the AI to address advanced queries, such as identifying oils with higher ZDDP content, and offer recommendations tailored to individual needs.
<img width="1512" alt="Screenshot 2024-11-09 at 9 21 53 AM" src="https://github.com/user-attachments/assets/f054dab0-c495-4ca2-b06c-f7312dd609e4">

I tested the tool by asking the AI different questions. It successfully provided detailed responses, consolidating all engine oil and filter recommendations into a single, structured output tailored to the car’s specifications and driving style. The AI also handled advanced queries, such as identifying high ZDDP oils, with ease. This experiment highlights the effectiveness of a custom AI designed for a specific task, delivering precise and reliable solutions.
<img width="1512" alt="Screenshot 2024-11-09 at 9 24 11 AM" src="https://github.com/user-attachments/assets/ebeb26f8-e308-40f9-8852-84691ac19c89">
<img width="1512" alt="Screenshot 2024-11-09 at 9 27 02 AM" src="https://github.com/user-attachments/assets/abb8853e-ca32-4a5e-b254-2a7b9c9a11ae">

## Speculations
This car maintenance tool could evolve to offer even more advanced personalization by integrating real-time data, such as local climate conditions and driving patterns tracked via GPS. Future versions might also predict potential maintenance issues using AI-driven diagnostics, proactively suggesting services before problems arise. Additionally, it could incorporate user feedback and machine learning to refine recommendations over time, further enhancing its accuracy and usefulness.
