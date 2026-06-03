# System Architecture

## Overview

The platform consists of three interconnected AI systems that together form a complete automated recruitment pipeline.

| Project | Purpose | Interaction |
|---------|---------|-------------|
| Project 1 — Proctoring System | Monitors candidate behavior in real time | AI observes silently |
| Project 2 — Assessment System | Generates and evaluates technical questions | Human answers AI questions |
| Project 3 — Voice Interview System | Conducts fully automated voice interview | Human speaks to AI |

---

## Candidate Journey

```
Stage 1 — HR schedules interview for candidate
    HR selects: domain, interview type (assessment / voice / both), date

Stage 2 — Candidate completes scheduled interview
    Assessment: AI generates questions → Candidate types answers → AI evaluates
    Voice Interview: AI asks questions → Candidate speaks → Whisper transcribes → AI evaluates
    Proctoring runs silently in background for both

Stage 3 — HR Dashboard
    Combined report: scores, risk level, transcript, recommendation
```

---

## System Architecture

```
Candidate Browser (React — port 5173)
         |
         ├───────────────────────────────────┐
         ↓                                   ↓
 Auth Server                          Backend API
 (Node.js — port 5000)               (FastAPI — port 8000)
 - JWT authentication                        |
 - User management              ┌────────────┼────────────┐
                                ↓            ↓            ↓
                          PostgreSQL     Gemini AI     AI Modules
                          (port 5432)    (free tier)   YOLO + MediaPipe
                          Data storage   Questions     Computer vision
                                         Evaluation    Speech recognition
```

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
- Role management (candidate / hr / admin)

### Backend API — port 8000
- Session lifecycle management (start, end, terminate)
- Answer evaluation via Gemini AI
- Real-time proctoring via WebSocket
- Speech to text streaming via WebSocket
- Risk scoring engine (violations → risk level)
- Performance report generation
- Admin dashboard data

### Database — port 5432

Seven core tables

| Table | Purpose |
|-------|---------|
| `users` | Candidate, HR, and admin profiles |
| `interview_schedules` | HR schedules interviews for candidates |
| `interview_sessions` | Session lifecycle and status |
| `object_detection_events` | Phone and multiple person violations |
| `face_pose_events` | Head and eye movement violations |
| `audio_transcripts` | Speech to text output |
| `answer_evaluations` | Question scores and AI feedback |

---

## API Endpoints

### Auth Server (port 5000)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/auth/register` | Register new user |
| POST | `/auth/login` | Login and receive JWT token |
| GET | `/auth/verify` | Verify token validity |

### Backend API (port 8000)

**Session Management**

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/session/start` | Start a new interview session |
| POST | `/session/{id}/end` | End session normally |
| POST | `/session/{id}/terminate` | Force terminate session |
| GET | `/session/{id}/status` | Get current session state |
| GET | `/session/{id}/results` | Get final results |
| GET | `/session/{id}/answers` | Get all answers with AI feedback |
| GET | `/users/me/sessions` | Candidate views their session history |

**Evaluation**

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/session/{id}/evaluate` | Submit answer for AI evaluation |
| GET | `/session/{id}/answers` | Get all answers with feedback |

**Reports**

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/report/{candidate_id}` | Get candidate report |
| POST | `/report/{id}/generate` | Generate performance summary |

**Proctoring**

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/session/{id}/proctor/event` | Log a violation event |

**Schedules — HR Only**

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/schedules` | HR schedules interview for candidate |
| GET | `/schedules` | HR views all their scheduled interviews |
| PATCH | `/schedules/{id}/cancel` | HR cancels a scheduled interview |

**Admin**
| GET | `/admin/dashboard` | System overview — admin only |
| GET | `/admin/sessions` | All sessions across all HR — admin only |

**WebSocket**

| Type | Endpoint | Description |
|------|----------|-------------|
| WS | `/ws/proctor/{id}` | Real-time camera frame processing |
| WS | `/ws/stt/{id}` | Real-time speech to text streaming |

---

## Domain List

HR selects when scheduling

| Domain |
|--------|
| Python |
| Data Science |
| Web Development |
| System Design |
| Java |
| SQL |
| Machine Learning |

## Session Rules

| Rule | Assessment | Voice Interview |
|------|-----------|-----------------|
| Total questions | 10 | 10 |
| Max score | 100 | 100 |
| Pass threshold | 60% | 60% |
| Timeout per question | 30 minutes | 3 minutes |
| Negative marking | No | No |
| Sessions independent | Yes | Yes |

## Proctoring Risk Weights

| Violation | Assessment | Voice Interview |
|-----------|-----------|-----------------|
| PHONE_DETECTED | 60 | 60 |
| MULTIPLE_PERSONS | 55 | 55 |
| NO_FACE | 30 | 30 |
| TAB_SWITCH | 25 | 25 |
| LOOKING_AWAY | 8 | 15 |
| ABNORMAL_MOVEMENT | 5 | 10 |

## Risk Levels

| Score | Level | Action |
|-------|-------|--------|
| 0 – 30 | SAFE | No action |
| 31 – 60 | WARNING | Warning shown to candidate |
| 61 – 85 | FLAGGED | HR notified |
| 86 – 100 | TERMINATE | Session ends automatically |

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
