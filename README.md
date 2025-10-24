---

## Real-Time Face Recognition & Surveillance Platform (FastAPI Microservices)

### Summary

This project is a high-performance, scalable platform for real-time face recognition across multiple IP camera streams, built using a modern microservices architecture with FastAPI and Docker. It features a web-based dashboard for live monitoring, subject management, access control, and data analytics. The system prioritizes efficiency through GPU acceleration (CUDA, ONNX Runtime) and resilience with self-healing video stream management and background notifications. This represents a complete rebuild and significant enhancement over a previous Flask-based system.

---

### Architecture

The system employs a decoupled microservices approach for better scalability, fault isolation, and maintainability:

1.  **Control Service (FastAPI):**
    * **Role:** The central API gateway and orchestration layer.
    * **Responsibilities:** Manages the PostgreSQL database (cameras, subjects, permissions, detections, events), handles all frontend API requests, implements business logic for user authentication, access control, subject management, and dashboard analytics, sends commands to the Processing Service (start/stop streams, configure AI). Uses SQLAlchemy Core/ORM for asynchronous database interactions.
    * **Technology:** Python, FastAPI, SQLAlchemy (asyncpg), Pydantic, PostgreSQL.

2.  **Processing Service (FastAPI):**
    * **Role:** The dedicated engine for computationally intensive video and AI tasks.
    * **Responsibilities:** Decodes multiple RTSP streams concurrently using FFmpeg with CUDA hardware acceleration, performs real-time face detection and recognition using InsightFace models via ONNX Runtime (GPU), manages self-healing stream processes (automatic restarts on failure), handles WebRTC/WebSocket communication for streaming live video and AI metadata to the dashboard, sends structured AI results back to the Control Service.
    * **Technology:** Python, FastAPI, FFmpeg (with CUDA), ONNX Runtime (GPU), InsightFace, NumPy, OpenCV, WebSockets, potentially WebRTC libraries (`aiortc`).

3.  **PostgreSQL Database:**
    * **Role:** Persistent storage for all application data.
    * **Schema:** Includes tables for users, cameras (with AI task config), subjects (with images/embeddings), face detections, camera events (start/stop/feed), and subject-camera permissions. Utilizes foreign keys with `ON DELETE CASCADE` for data integrity.

4.  **React Frontend:**
    * **Role:** User interface for monitoring and management.
    * **Features:** Displays live video feeds with bounding boxes, lists detections, manages subjects and permissions, shows system statistics and camera timelines. Communicates with the Control Service API.

5.  **Docker & Docker Compose:**
    * **Role:** Containerization and orchestration of all services.
    * **Setup:** Defines services, networks, volumes (including GPU device access via NVIDIA Container Toolkit), and environment variables for configuration.



---

### Key Features

* **Microservices Architecture:** Scalable and resilient design using FastAPI.
* **Real-Time Multi-Camera Processing:** Concurrently decodes and analyzes RTSP streams.
* **GPU-Accelerated AI:** Leverages NVIDIA GPUs via CUDA, cuDNN (installed via pip), and ONNX Runtime for high-performance face detection (InsightFace `buffalo_l`) and recognition.
* **Self-Healing Video Streams:** Individual streams automatically detect failures (e.g., camera reboots, network drops) and attempt restarts with exponential backoff, enhancing system uptime without manual intervention.
* **Live Dashboard Feed:** Streams processed video with detection overlays to multiple clients via WebSockets.
* **Comprehensive Subject Management:** Full CRUD operations for subjects, including bulk upload via CSV and image management.
* **Granular Access Control:** A detailed permission system allowing specific subjects access to designated cameras.
* **Persistent AI Configuration:** AI tasks (e.g., face detection) enabled per camera are stored in the database and automatically applied by the Processing Service on stream start.
* **Asynchronous Operations:** Utilizes Python's `asyncio` throughout the FastAPI services for non-blocking I/O, especially database interactions and external service calls.
* **Background Notification System:** Decoupled "fire-and-forget" alert system using `asyncio.Queue` to send notifications (Telegram, Email) for events like camera stops without blocking API responses.
* **Analytics & Reporting:** API endpoints provide system statistics, time-based detection counts (hourly/daily), and calendar heatmaps.
* **Configuration Driven:** Key settings (database URLs, camera lists, AI thresholds, notification keys) managed via `.env` files and Pydantic settings.
* **Containerized Deployment:** Fully containerized using Docker and Docker Compose for easy setup and deployment.

---

### Technologies Used

* **Backend:** Python, FastAPI, Uvicorn
* **AI/ML:** InsightFace, ONNX Runtime (GPU), PyTorch (potentially used in model training/conversion), NumPy, OpenCV
* **GPU:** NVIDIA CUDA, cuDNN (via pip packages)
* **Database:** PostgreSQL, SQLAlchemy (with asyncpg driver)
* **Video Processing:** FFmpeg (with CUDA acceleration)
* **Real-Time Communication:** WebSockets
* **Asynchronous Programming:** `asyncio`
* **Containerization:** Docker, Docker Compose
* **Notifications:** `httpx` (for Telegram), `smtplib` (for Email)
* **Data Handling:** Pydantic, Pandas (for bulk upload)
* **OS/Environment:** Linux (Ubuntu base in Docker)

---

### Challenges & Solutions

* **GPU Integration in Docker:** Correctly configuring the Dockerfile with NVIDIA base images, installing CUDA/cuDNN dependencies (via pip), setting `LD_LIBRARY_PATH`, and granting device access in `docker-compose.yml`.
* **Real-Time Performance:** Optimizing the FFmpeg pipeline (`-hwaccel cuda`, `-tune zerolatency`), implementing FPS reduction (`-vf fps=...`), and identifying potential bottlenecks like the GIL (leading to future consideration of multiprocessing).
* **Stream Stability:** Designing the self-healing `VideoStream` class to handle network interruptions and camera reboots automatically using background threads and exponential backoff.
* **Database Synchronization:** Resolving `IntegrityError` issues related to foreign key constraints (`ON DELETE CASCADE`) by ensuring the database schema correctly matched the SQLAlchemy models (initially using `reset_db`, potentially moving to Alembic).
* **Asynchronous Database Operations:** Avoiding `MissingGreenlet` and `InvalidRequestError` errors by correctly using `async/await` and ensuring database operations were performed sequentially within a single session context (avoiding concurrent operations on the same session object with `asyncio.gather`).
* **Configuration Management:** Setting up a robust system using `.env` files and Pydantic `BaseSettings` for managing secrets, URLs, and initial camera setups, including parsing multi-line JSON.
* **Microservice Communication:** Handling HTTP requests between the Control and Processing services using `httpx`.

---
