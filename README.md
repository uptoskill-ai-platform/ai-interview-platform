# AI Interview Platform

An integrated AI-powered recruitment pipeline combining automated proctoring,
intelligent assessment, and voice-based interviews.

## Projects

| Project | Description |
|---------|-------------|
| Proctoring System | Real-time candidate monitoring via webcam and microphone |
| Assessment System | AI-generated questions with automated evaluation and scoring |
| Voice Interview | Fully automated AI-driven voice interview with speech recognition |

## Planned Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | React 18 + Vite + TypeScript |
| Backend | FastAPI + Python 3.11 |
| Auth | Node.js 18 + Express + JWT |
| Database | PostgreSQL 15 |
| AI / LLM | Google Gemini 1.5 Flash |
| Computer Vision | YOLOv8 + MediaPipe |
| Speech to Text | Faster-Whisper |

## Service Ports

| Service | Port |
|---------|------|
| Frontend | 5173 |
| Backend API | 8000 |
| Auth Server | 5000 |
| PostgreSQL | 5432 |

## Getting Started

```bash
git clone https://github.com/uptoskill-ai-platform/ai-interview-platform.git
cd ai-interview-platform
# Linux/macOS
cp .env.example .env

# Windows PowerShell
Copy-Item .env.example .env

# Fill in required values in .env before running anything
```
Full setup instructions in SETUP.md.

## Project Structure

```text
ai-interview-platform/
├── frontend/      React UI
├── backend/       FastAPI core backend
├── auth-server/   Node.js authentication server
├── database/      PostgreSQL schema and scripts
├── ai-modules/    AI model wrappers and scripts
└── docs/          Project documentation
```

## Contributing

Read CONTRIBUTING.md before writing any code.
