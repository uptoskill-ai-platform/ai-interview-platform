# System Architecture

## Overview

The platform consists of three interconnected AI systems that together form
a complete automated recruitment pipeline.

| Project | Purpose | Interaction |
|---------|---------|-------------|
| Project 1 — Proctoring System | Monitors candidate behavior in real time | AI observes silently |
| Project 2 — Assessment System | Generates and evaluates technical questions | Human answers AI questions |
| Project 3 — Voice Interview System | Conducts fully automated voice interview | Human speaks to AI |

---

## Candidate Journey
Stage 1 — Assessment (Project 2)
AI generates questions → Candidate answers → AI evaluates
Proctoring (Project 1) runs silently in background
↓ if passed
Stage 2 — Voice Interview (Project 3)
AI asks questions → Candidate speaks → Whisper transcribes → AI evaluates
Proctoring (Project 1) continues in background
↓
Stage 3 — HR Dashboard
Combined report with scores, risk level, transcript, and recommendation

---

## System Architecture
Candidate Browser (React — port 5173)
|
├──────────────────────────────────────┐
↓                                      ↓
Auth Server                            Backend API
(Node.js — port 5000)                 (FastAPI — port 8000)
JWT authentication                            |
User management                    ┌──────────┼──────────┐
↓          ↓          ↓
PostgreSQL   Gemini AI   AI Modules
(port 5432)  (free tier) YOLO + MediaPipe
All data     Questions   Computer vision
storage      Evaluation  Speech recognition

---

## Service Responsibilities

### Frontend — port 5173
- Candidate interface for all three projects
- Assessment dashboard with timer and question display
- Voice interview interface with microphone controls
- WebRTC for camera and microphone streaming
- WebSocket client for real-time proctoring alerts and STT

### Auth Server — port 5000
- Candidate and HR registration and login
- JWT token generation and verification
- Role management (candidate / admin)

### Backend API — port 8000
- Session lifecycle management (start, end, terminate)
- Answer evaluation via Gemini AI
- Real-time proctoring via WebSocket
- Speech to text streaming via WebSocket
- Risk scoring engine (violations → risk level)
- Performance report generation
- Admin dashboard data

### Database — port 5432

Six core tables:

| Table | Purpose |
|-------|---------|
| `users` | Candidate and admin profiles |
| `interview_sessions` | Session lifecycle and status |
| `object_detection_events` | Phone and multiple person violations |
| `face_pose_events` | Head and eye movement violations |
| `audio_transcripts` | Speech to text output |
| `answer_evaluations` | Question scores and AI feedback |

---

## API Endpoints

### Auth Server (port 5000)
POST   /auth/register          Register new user
POST   /auth/login             Login and receive JWT token
GET    /auth/verify            Verify token validity

### Backend API (port 8000)
Session Management
POST   /session/start                    Start a new interview session
POST   /session/{id}/end                 End session normally
POST   /session/{id}/terminate           Force terminate session
GET    /session/{id}/status              Get current session state
GET    /session/{id}/results             Get final results
Evaluation
POST   /session/{id}/evaluate            Submit answer for AI evaluation
GET    /session/{id}/answers             Get all answers with feedback
Reports
GET    /report/{candidate_id}            Get candidate report
POST   /report/{id}/generate             Generate performance summary
Proctoring
POST   /session/{id}/proctor/event       Log a violation event
Admin
GET    /admin/dashboard                  HR overview dashboard
WebSocket
WS     /ws/proctor/{id}                  Real-time camera frame processing
WS     /ws/stt/{id}                      Real-time speech to text streaming

---

## Domain List

Candidates select one domain at the start of their session:

| Domain |
|--------|
| Python |
| Data Science |
| Web Development |
| System Design |
| Java |
| SQL |
| Machine Learning |

---

## Security

- JWT authentication required on all protected routes
- CORS restricted to frontend origin only
- No secrets or API keys committed to repository
- All credentials loaded from environment variables via `.env`
- Input validation on all endpoints via Pydantic

---

## Phase 2 — Planned

Not yet implemented. Planned for future phases:

| Feature | Purpose |
|---------|---------|
| Redis | Session caching and real-time state |
| Alembic | Database migration version control |
| Docker Compose | Full containerized local setup |
| Kubernetes | Production deployment and scaling |
| Load Balancer | Handle concurrent users at scale |
