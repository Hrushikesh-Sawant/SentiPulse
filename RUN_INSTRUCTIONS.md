# Project Setup and Run Instructions

This guide provides step-by-step instructions on how to set up and run the Physiological Monitoring System (SentiPulse) on your local machine.

## Prerequisites

Ensure you have the following installed on your system:
-   **Node.js** (v18 or higher)
-   **Python** (v3.8 or higher)

## Quick Setup (Windows)

The project includes a setup script that automates the installation process.

1.  Open a terminal (Command Prompt or PowerShell) in the project root directory.
2.  Run the setup script:
    ```powershell
    .\setup.bat
    ```
    This script will:
    -   Check for Node.js and Python.
    -   Install root, client, and Python dependencies.
    -   Create necessary directories (`server/uploads`, `python_services/static`).
    -   Create a `.env` file from `env.example`.

## Manual Setup

If you prefer to set up manually or run into issues with the script:

1.  **Install Root Dependencies:**
    ```bash
    npm install
    ```

2.  **Install Client Dependencies:**
    ```bash
    cd client
    npm install
    cd ..
    ```

3.  **Install Python Dependencies:**
    ```bash
    cd python_services
    pip install -r requirements.txt
    cd ..
    ```

4.  **Environment Configuration:**
    -   Copy `env.example` to `.env`:
        ```bash
        copy env.example .env
        ```
    -   Create the upload directory:
        ```bash
        mkdir server\uploads
        ```

## Running the Application

To run both the backend and frontend concurrently (Development Mode):

```bash
npm run dev
```

-   **Frontend:** [http://localhost:5173](http://localhost:5173)
-   **Backend API:** [http://localhost:3000](http://localhost:3000)
-   **WebSocket:** `ws://localhost:8080`

### Running Services Individually

-   **Backend Only:** `npm run server:dev`
-   **Frontend Only:** `npm run client:dev`

## File Structure & Explanations

Here is an overview of the key files and directories in the project:

### Root Directory
-   **`package.json`**: Manages project dependencies and scripts (start, dev, install).
-   **`env.example`**: Template for environment variables (ports, database URLs, API keys).
-   **`setup.bat` / `setup.sh`**: Scripts to automate the setup process for Windows and Linux/macOS.
-   **`docker-compose.yml`**: Configuration for running the project using Docker.

### `client/` (Frontend)
-   **`src/`**: Contains the React source code (components, pages, styles).
-   **`vite.config.js`**: Configuration for Vite (the build tool and dev server).
-   **`package.json`**: Frontend-specific dependencies.

### `server/` (Backend)
-   **`index.js`**: The main entry point for the Node.js/Express server. It handles API requests, file uploads, and WebSocket connections.
-   **`uploads/`**: Directory where uploaded video files are temporarily stored.

### `python_services/` (Data Processing)
-   **`process_video.py`**: The main Python script called by the backend to process video files. It likely performs signal processing (rPPG) to extract heart rate data.
-   **`heart_rate_model.py`**: Contains the logic/algorithms for heart rate estimation.
-   **`requirements.txt`**: List of Python libraries required (OpenCV, NumPy, SciPy, etc.).
