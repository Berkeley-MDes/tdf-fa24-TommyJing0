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
