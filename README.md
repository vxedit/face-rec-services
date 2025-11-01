
---

## Real-Time Face Recognition & Surveillance Platform (FastAPI Microservices)

### Summary

This project is a high-performance, scalable platform for real-time face recognition across multiple IP camera streams, built using a modern microservices architecture with FastAPI and Docker. It features a web-based dashboard for live monitoring, subject management, access control, and data analytics. The system prioritizes efficiency through GPU acceleration (CUDA, ONNX Runtime) and resilience with self-healing video stream management and background notifications. This represents a complete rebuild and significant enhancement over a previous Flask-based system.

### Key Features

* **Microservices Architecture:** Scalable and resilient design using FastAPI.
* **Real-Time Multi-Camera Processing:** Concurrently decodes and analyzes RTSP streams.
* **GPU-Accelerated AI:** Leverages NVIDIA GPUs via CUDA and ONNX Runtime for high-performance face detection and recognition (e.g., InsightFace).
* **Self-Healing Video Streams:** Individual streams automatically detect failures (e.g., camera reboots, network drops) and attempt restarts with exponential backoff, enhancing system uptime without manual intervention.
* **Live Dashboard Feed:** Streams processed video with detection overlays to multiple clients via WebSockets.
* **Comprehensive Subject Management:** Full CRUD operations for subjects, including bulk upload via CSV and image management.
* **Granular Access Control:** A detailed permission system allowing specific subjects access to designated cameras.
* **Persistent AI Configuration:** AI tasks enabled per camera are stored in the database and automatically applied by the Processing Service.
* **Asynchronous Operations:** Utilizes Python's `asyncio` for non-blocking I/O.
* **Background Notification System:** Decoupled "fire-and-forget" alert system for notifications (Telegram, Email).
* **Analytics & Reporting:** API endpoints provide system statistics, time-based detection counts, and calendar heatmaps.
* **Containerized Deployment:** Fully containerized using Docker and Docker Compose.

### Technologies Used

* **Backend:** Python, FastAPI, Uvicorn
* **AI/ML:** InsightFace, ONNX Runtime (GPU), PyTorch, NumPy, OpenCV
* **GPU:** NVIDIA CUDA, cuDNN
* **Database:** PostgreSQL, SQLAlchemy (with asyncpg driver)
* **Video Processing:** FFmpeg (with CUDA acceleration)
* **Real-Time Communication:** WebSockets
* **Asynchronous Programming:** `asyncio`
* **Containerization:** Docker, Docker Compose
* **Notifications:** `httpx` (for Telegram), `smtplib` (for Email)
* **Data Handling:** Pydantic, Pandas
* **OS/Environment:** Linux (Ubuntu base in Docker)

---
