# Week 9
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
