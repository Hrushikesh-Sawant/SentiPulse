# SentiPulse System Architecture

![Architecture Diagram](docs/SentiPulse_Architecture_Diagram.png)

## 1. Overview
The SentiPulse system follows a **three-tier architecture** comprising a React-based frontend, a Node.js/Express backend, and a Python-based microservice for heavy computational tasks. This separation of concerns allows for a responsive user interface while leveraging Python's rich ecosystem for signal processing and computer vision.

## 2. Legend & Notation
*   **Blue Boxes (Client):** Frontend components running in the user's browser.
*   **Green Boxes (Server):** Backend services running on the Node.js server.
*   **Orange Boxes (Worker):** Python scripts performing signal analysis.
*   **Yellow Cylinders (Storage):** Data persistence layers (File System, LocalStorage).
*   **Grey/Dashed (Future):** Planned extensions not yet implemented.
*   **Solid Arrows:** Synchronous or direct data flow (e.g., HTTP calls, Function calls).
*   **Dotted Arrows:** Asynchronous or indirect flow (e.g., Loading data from storage).

## 3. Component Details

### A. Client Layer (Frontend)
*   **Technology:** React, Vite, Tailwind CSS.
*   **Key Components:**
    *   `VideoUpload`: Handles video input via file selection or **Webcam Recording**. In webcam mode, it uses the `MediaRecorder API` to capture a blob and `face-api.js` for a live emotion overlay.
    *   `Results`: The main dashboard displaying Heart Rate, Emotion Confidence, and Signal Quality. It includes a **Self-Report** section for user feedback.
    *   `SessionHistory`: Visualizes past session trends using data from LocalStorage.
    *   `ProcessingStatus`: Displays real-time progress updates via WebSockets.
*   **Services:**
    *   `WellbeingService`: Computes the composite "Wellbeing Score" by fusing physiological and emotional data.
    *   `TextAnalysisService`: Analyzes user notes for sentiment and flags crisis keywords.
    *   `HistoryService`: Manages CRUD operations for `localStorage`.

### B. Server Layer (Backend)
*   **Technology:** Node.js, Express, `ws` (WebSocket).
*   **Key Components:**
    *   `API Routes`: Exposes endpoints like `POST /api/process-video`.
    *   `Process Manager`: Handles the spawning of Python child processes (`spawn`), managing their lifecycle and timeouts.
    *   `WebSocket Server`: Broadcasts events (`processing_start`, `processing_complete`) to connected clients.
    *   `Static File Server`: Serves the React bundle and generated plot images.

### C. Processing Layer (Worker)
*   **Technology:** Python 3, OpenCV, NumPy, SciPy, FER.
*   **Key Components:**
    *   `process_video.py`: The entry point that parses arguments and formats the final JSON output.
    *   `heart_rate_model.py`: Contains the core logic:
        *   **FaceDetection:** Uses Haar Cascades to locate the face ROI.
        *   **rPPG Extraction:** Extracts RGB signals and applies ICA/PCA to estimate Heart Rate.
        *   **SignalQualityEstimator:** Calculates SNR and Face Coverage to determine if the result is "Good", "Fair", or "Poor".
        *   **EmotionAnalysis:** Uses the FER library to classify facial expressions.

### D. Data Layer (Storage)
*   **File System:**
    *   `/uploads`: Temporary storage for incoming video files.
    *   `/static`: Storage for generated signal plots (Red/Green/Blue channels, FFT).
*   **LocalStorage:** Browser-based persistence for session history (privacy-focused, no central DB yet).

## 4. Data Flow Walkthrough

1.  **Input:** User records a video via `VideoUpload`. The `MediaRecorder` generates a video blob.
2.  **Upload:** The client sends the blob to `POST /api/process-video`.
3.  **Ingestion:** The Server saves the file to `/uploads` and spawns the Python worker.
4.  **Processing:**
    *   The Worker reads the video frame-by-frame.
    *   It detects the face and extracts the rPPG signal.
    *   It computes the Heart Rate and Emotion probabilities.
    *   It generates plot images in `/static`.
5.  **Feedback:** The Server receives the JSON result from the Worker and sends it to the Client.
6.  **Fusion:** The Client's `WellbeingService` combines the server results with the user's Self-Report (processed by `TextAnalysisService`) to calculate the final Wellbeing Score.
7.  **Storage:** The complete session summary is saved to `LocalStorage` via `HistoryService`.

## 5. Interface Definitions

*   **HTTP API:**
    *   `POST /api/process-video`: Accepts `multipart/form-data` (video file). Returns JSON with `heart_rate`, `emotions`, `signal_quality`, and `plots`.
*   **WebSocket Events:**
    *   `processing_start`: `{ sessionId, message }`
    *   `processing_complete`: `{ sessionId, result }`
    *   `processing_error`: `{ sessionId, error }`

## 6. Design Decisions
*   **Microservice Pattern:** Python was chosen for the worker to utilize robust scientific libraries (SciPy, OpenCV) which are less mature in Node.js.
*   **WebSocket:** Implemented to prevent request timeouts on long-running video processing tasks and provide better UX.
*   **Privacy-First:** No central database is currently used; all personal data resides on the user's device (LocalStorage) or is transient (temp files).

## 7. Future Extensions
*   **Database:** A persistent database (e.g., PostgreSQL) is planned to allow users to access history across devices.
*   **Auth Service:** User accounts will be required once central storage is implemented.
*   **Mobile Client:** A React Native app is proposed to leverage native camera capabilities.
