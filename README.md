# VisualAidForVisuallyImpaired-G55-PS24
# Visual Aid for the Visually Impaired
This project is a comprehensive assistive technology designed to help visually impaired individuals perceive and understand their surroundings. It combines a mobile application with a powerful backend to provide real-time descriptions of images and videos. Users can capture live scenes, receive audible descriptions, and even ask follow-up questions about the content.

## Features

-   **Real-time Image Captioning**: Capture an image using the mobile app to receive an instant, audible description of the scene.
-   **Video Summarization**: Process a video to get a concise paragraph summarizing its content. Frame-by-frame captions are generated and then synthesized into a coherent summary.
-   **Conversational Q&A (RAG)**: After a video is processed, users can ask specific questions about it (e.g., "What color is the car?"). A Retrieval-Augmented Generation (RAG) system provides answers based on the video's content.
-   **Voice-Driven Interface**: The Flutter application is built with accessibility in mind, utilizing Text-to-Speech (TTS) and Speech-to-Text (STT) for a hands-free, voice-controlled experience.
-   **Hazard Detection**: An integrated safety feature scans generated captions for potentially hazardous keywords (like "fire" or "knife") and triggers an audible alarm to alert the user.
-   **Volunteer Portal**: A dedicated interface for volunteers to review the images and captions submitted by users, ensuring quality and providing a human-in-the-loop for assistance.

## Tech Stack & Architecture

-   **Frontend (Mobile App)**:
    -   **Framework**: Flutter
    -   **Core Libraries**: `camera`, `video_player`, `image_picker` for media capture; `http` for API communication; `flutter_tts` & `speech_to_text` for the voice interface.

-   **Backend (APIs & Models)**:
    -   **Framework**: Flask
    -   **Database**: MongoDB for storing image-caption pairs for the volunteer portal.
    -   **Image Captioning Model**: A custom transformer-based model with a MobileNetV3 encoder and a LLaMA-style decoder, trained on the Flickr8k dataset using PyTorch Lightning.
    -   **Video Processing Pipeline**:
        1.  Frames are extracted from the input video using OpenCV.
        2.  A pre-trained model (`Salesforce/blip-image-captioning-large`) generates a caption for each keyframe.
        3.  The collected captions are summarized into a paragraph using a generative model (`gpt-3.5-turbo` via G4F).
    -   **Q&A System**: A Retrieval-Augmented Generation (RAG) pipeline powered by:
        -   **Retriever**: `thenlper/gte-large` (SentenceTransformer) to find the most relevant frame captions for a given question.
        -   **Generator**: `facebook/bart-large` to generate a natural language answer from the retrieved context.

## Setup and Installation

### Prerequisites

-   Git
-   Python 3.8+
-   Flutter SDK
-   MongoDB account (for connection URI)

### Backend Setup

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/SaiAbhinav36/VisualAidForVisuallyImpaired-G55-PS24.git
    cd VisualAidForVisuallyImpaired-G55-PS24/Backend
    ```

2.  **Install dependencies:**
    A `requirements.txt` file is not provided. Install the necessary packages based on the project's imports:
    ```bash
    pip install lightning torch torchvision torchmetrics spacy flask flask_pymongo g4f gTTS pygame transformers sentence-transformers flask-cors flask-bcrypt python-dotenv numpy opencv-python Pillow
    python -m spacy download en_core_web_sm
    ```

3.  **Environment Configuration:**
    Create a `.env` file in the `Backend` directory and populate it with your credentials:
    ```
    DB_URL=mongodb+srv://<user>:<password>@<cluster-url>/VisualAid
    API_TOKEN={"Authorization": "Bearer YOUR_HUGGINGFACE_TOKEN"}
    API_URL=https://api-inference.huggingface.co/models/Salesforce/blip-image-captioning-large
    ```

4.  **Download Model Checkpoint:**
    The custom image captioning model (`imageApi.py`) requires a trained checkpoint file (`ImageCaptioning_best.ckpt`). Ensure this file is placed in a `Model` directory within the `Backend` directory.

5.  **Run the Backend Servers:**
    The backend consists of four separate Flask services. Run each one in a separate terminal from the `Backend` directory:
    ```bash
    # Terminal 1: Main Gateway & DB Interface (Port 5000)
    python image_processor.py

    # Terminal 2: Custom Image Captioning API (Port 5001)
    python imageApi.py

    # Terminal 3: Video Processing API (Port 5002)
    python videoProcessing.py

    # Terminal 4: Q&A API (Port 5003)
    python query_api.py
    ```

### Frontend Setup

1.  **Navigate to the app directory:**
    ```bash
    cd ../Frontend/classico
    ```

2.  **Get Flutter packages:**
    ```bash
    flutter pub get
    ```

3.  **Update API Endpoints:**
    Open `lib/main.dart` and replace the hardcoded IP address (e.g., `192.168.212.229`) with the local IP address of the machine running your backend servers.

4.  **Run the app:**
    ```bash
    flutter run
    ```

## Usage Guide

1.  **Start the App**: Launch the application on your mobile device.
2.  **Navigate**: On the home screen, tap the top half for "Visual Assistance".
3.  **Choose a Tool**:
    -   **Image Captioning**: Tap the top half for "Image Processor". In the next screen, tap anywhere to open the camera, take a picture, and wait for the audible description.
    -   **Video Analysis**: Tap the bottom half for "Video Processor". Tap to select a video. The app will upload and process it, then read a summary aloud.
4.  **Ask Questions (Video)**: After the video summary, the app will ask if you have any questions. Say "yes" and then state your query to get a specific answer about the video's content.

## Project Structure

```
.
├── Backend/
│   ├── Model.ipynb               # Jupyter notebook for training the captioning model
│   ├── imageApi.py               # Flask API for the custom image captioner
│   ├── image_processor.py        # Main gateway API that connects to DB and other services
│   ├── query_api.py              # Flask API for the RAG Q&A system
│   ├── videoProcessing.py        # Flask API for video summarization
│   └── .env                      # Environment variables
└── Frontend/
    └── classico/                # Flutter application
        ├── lib/
        │   └── main.dart         # Main application logic and UI
        └── pubspec.yaml          # Flutter dependencies
```

## License

This project is licensed under the MIT License.
