# System Architecture

## Overview

The platform consists of three interconnected AI systems that together form a complete automated recruitment pipeline.

| Project | Purpose | Interaction |
|----------|----------|-------------|
| Project 1 – Proctoring System | Monitors candidate behavior during assessments and interviews | Human ↔ Human (AI observes) |
| Project 2 – Assessment System | Generates and evaluates technical tests | Human ↔ AI Questions |
| Project 3 – Voice Interview System | Conducts automated interviews | Human ↔ AI Voice |

## Candidate Workflow

```text
Stage 1 → Candidate takes Assessment (Project 2)
          ↓ (if passed)

Stage 2 → Candidate takes Voice Interview (Project 3)

Project 1 (Proctoring) runs in the background during both stages

Stage 3 → HR receives a combined evaluation report

## architecture

Candidate Browser
|
├── Auth Server (Node.js — port 5000)
│       JWT authentication and user management
│
└── Backend API (FastAPI — port 8000)
|
├── PostgreSQL (port 5432)
│       Persistent storage for all data
│
├── Gemini AI
│       Question generation and answer evaluation
│
└── AI Modules
Computer vision, speech recognition

## System Responsibilities

### Frontend — port 5173
- Candidate interface for all three projects
- Assessment dashboard
- WebRTC for camera and microphone streaming
- WebSocket client for real-time proctoring and STT

### Auth Server — port 5000
- User registration and login
- JWT token generation and verification
- Role management

### Backend API — port 8000
- Session lifecycle management
- Answer evaluation pipeline
- Real-time proctoring via WebSocket
- Speech to text via WebSocket
- Risk scoring engine
- Report generation

### Database — port 5432
Six core tables (planned):
- `users`
- `interview_sessions`
- `object_detection_events`
- `face_pose_events`
- `audio_transcripts`
- `answer_evaluations`

## API Endpoints

### Auth Server
POST   /auth/register
POST   /auth/login
GET    /auth/verify

### Backend API
POST   /session/start
POST   /session/{id}/end
POST   /session/{id}/terminate
GET    /session/{id}/status
GET    /session/{id}/results
POST   /session/{id}/evaluate
GET    /session/{id}/answers
GET    /report/{candidate_id}
POST   /report/{id}/generate
POST   /session/{id}/proctor/event
GET    /admin/dashboard
WS     /ws/proctor/{id}
WS     /ws/stt/{id}

## Domain List

Candidates select one domain at the start of their session:

- Python
- Data Science
- Web Development
- System Design
- Java
- SQL
- Machine Learning

## Security

- JWT authentication on all protected routes
- No secrets committed to repository
- CORS restricted to frontend origin only
- All credentials loaded from environment variables

## Phase 2 — Planned

The following are planned for a future phase and not yet implemented:

- Redis for session caching
- Alembic for database migrations
- Docker containerization
- Kubernetes deployment
- Load balancing