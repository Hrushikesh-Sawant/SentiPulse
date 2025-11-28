# Plan: SentiPulse System Architecture Diagram & Documentation

## Goal
Create a comprehensive System Architecture Diagram and a supporting explanation document that clearly illustrates the SentiPulse system's components, data flows, and interactions.

## Deliverables
1.  **`SentiPulse_Architecture_Diagram.mmd`**: A Mermaid.js definition file representing the system architecture.
2.  **`SentiPulse_Architecture_Explanation.md`**: A detailed markdown document explaining the diagram, components, and data flows.

## Diagram Structure (Mermaid)
The diagram will use a Top-Down or Left-Right flow to represent the following layers:

### 1. Client Layer (Frontend)
*   **Component**: `Client (React/Vite)`
*   **Sub-components**:
    *   `VideoUpload` (Input)
    *   `Results` (Output)
    *   `ProcessingStatus` (Feedback)
    *   `Services` (Wellbeing, API)
*   **Storage**: `Browser Storage` (Session History)

### 2. Server Layer (Backend)
*   **Component**: `Server (Node.js/Express)`
*   **Sub-components**:
    *   `API Routes` (/api/process-video)
    *   `WebSocket Server` (Real-time updates)
    *   `Static File Server` (Serving plots/client)
    *   `Process Manager` (Spawning Python)

### 3. Processing Layer (Worker)
*   **Component**: `Worker (Python Microservice)`
*   **Sub-components**:
    *   `process_video.py` (Entry point)
    *   `heart_rate_model.py` (Logic)
    *   `FER` (Emotion Lib)
    *   `SciPy/Sklearn` (Signal Processing)

### 4. Data Layer (Storage)
*   **Component**: `File System`
    *   `uploads/` (Temp Video Storage)
    *   `static/` (Generated Plots)

### 5. Future Extensions (Dashed/Greyed)
*   `Database` (User Data, Persistent History)
*   `Auth Service` (Login/Signup)

## Data Flows to Represent
1.  **Video Upload**: Client -> HTTP POST -> Server -> File System.
2.  **Processing Trigger**: Server -> Spawn Process -> Python Worker.
3.  **Signal Extraction**: Python Worker -> Read Video -> Process (ICA/PCA) -> Generate Plots.
4.  **Real-time Feedback**: Python (stdout) -> Server -> WebSocket -> Client.
5.  **Result Delivery**: Python (JSON) -> Server -> HTTP Response -> Client.

## Explanation Document Outline
1.  **Title & Overview**: Brief summary of the architecture.
2.  **Legend**: Explanation of symbols (Boxes, Arrows, Cylinders, Dashed lines).
3.  **Component Details**:
    *   **Frontend**: React, Vite, Tailwind.
    *   **Backend**: Express, WebSocket, Multer.
    *   **Worker**: Python, OpenCV, Signal Processing.
4.  **Data Flow Walkthrough**: Step-by-step narrative of a user session.
5.  **Interface Definitions**:
    *   `POST /api/process-video`
    *   `WS: processing_start`, `processing_complete`
6.  **Design Decisions**: Why this 3-tier structure? (Separation of concerns, heavy lifting in Python).
7.  **Future Extensions**: Planned DB and Auth integration.

## Notation & Style
*   **Mermaid Syntax**: `graph TD` or `C4` style if supported, otherwise standard flowchart.
*   **Colors**:
    *   Blue/Green for active components.
    *   Grey/Dashed for future components.
    *   Yellow/Orange for storage.
