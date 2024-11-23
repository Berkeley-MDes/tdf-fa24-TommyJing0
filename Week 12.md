# Week 12
# Week of 11/17/2024
## Real-Time Detection Experiment with OpenCV

This week, I experimented with **OpenCV**, **MediaPipe**, and **YOLOv8** to create a real-time object detection and tracking system. The project integrates multiple technologies to perform the following tasks:

1. **Object Recognition (YOLOv8)**:  
   Using YOLOv8, I implemented real-time object detection, specifically limiting person detection to one individual while allowing multiple object classes to be recognized simultaneously.

2. **Hand Tracking (MediaPipe)**:  
   I integrated MediaPipe to track hand landmarks and display a skeleton overlay for detected hands. This feature works seamlessly alongside object detection.

3. **Face Detection and Emotion Recognition (FER)**:  
   MediaPipe’s face detection was used to locate faces, and FER (Facial Expression Recognition) was employed to analyze emotions in the detected face regions. The emotion and confidence score are displayed in real-time.
   
<img width="1512" alt="Screenshot 2024-11-22 at 3 48 56 PM" src="https://github.com/user-attachments/assets/c3596ffe-f64c-483a-86ef-edfa617510d3">

### My Python Experience
This screenshot shows one of my first times using Python for programming. While Python is simple and easy to start with, working with libraries like **OpenCV**, **MediaPipe**, and **YOLO** had some challenges. Here’s what I learned:

1. **Setting Up Libraries**: Installing libraries like `mediapipe`, `ultralytics`, and `FER` using `pip` was easy, but figuring out how to use them together took some time.

2. **Real-Time Feedback**: The terminal at the bottom shows live results from the YOLO model, such as detecting "person" and "bottle." This feedback helped me check if my code was working correctly.

3. **Learning Curve**: While Python is beginner-friendly, understanding how these tools work together required some extra effort and practice.

This experiment helped me learn how powerful Python is for combining different tools into one system, and I look forward to improving as I practice more.

<img width="1512" alt="Screenshot 2024-11-22 at 3 55 45 PM" src="https://github.com/user-attachments/assets/fa860313-a093-4b88-8091-ed038bee5675">
