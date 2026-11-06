# Swarajya AI (स्वराज्य AI)

> Voice-first, AI-assisted information system delivering accurate, grounded answers for Student Services & Scholarships.

---

## 📌 Overview

**Swarajya AI** is a voice-first conversational AI assistant built for the **Great Indian AI Internship Challenge**. It bridges the gap between complex official procedures and users by allowing queries in spoken natural or code-mixed language and providing clear, audible, factually grounded answers.

### End-to-End Pipeline
```text
User Voice / Text Input
          │
          ▼
Gnani Prisma (Speech-to-Text)
          │
          ▼
Editable Transcript
          │
          ▼
Query Interpretation & Intent Detection
          │
          ▼
Curated Knowledge Retrieval (Deterministic / Grounded)
          │
          ▼
Grounded Answer Formulation
          │
          ▼
Gnani Timbre (Text-to-Speech)
          │
          ▼
Audio Playback & Source Citations
```

---

## 🏗️ Project Architecture

```text
Swarajya-AI/
├── .gitignore                      # Comprehensive Git exclusion rules
├── .env.example                    # Environment variable template (no secrets)
├── README.md                       # Project documentation
│
├── docs/
│   ├── MASTER_PROJECT_SPEC.md      # Master SRS and technical blueprint
│   └── API_VERIFICATION.md         # Verified Gnani API contracts (Phase 1)
│
├── data/
│   └── knowledge/                  # Curated JSON knowledge entries (Scholarships & Student Services)
│
├── backend/
│   ├── app/
│   │   ├── main.py                 # FastAPI application gateway
│   │   ├── api/                    # Route handlers (/health, /api/ask, /api/voice-query, /api/speak)
│   │   ├── core/                   # Configuration, settings & structured logging
│   │   ├── models/                 # Pydantic request/response schemas
│   │   ├── services/               # Prisma, Timbre, Interpretation, Retrieval, Answer services
│   │   ├── repositories/           # Knowledge base repository loader
│   │   └── utils/                  # Audio validator and MIME helpers
│   ├── tests/                      # Pytest unit and integration test suite
│   ├── requirements.txt            # Core backend dependencies
│   └── .env.example                # Backend configuration template
│
├── frontend/
│   ├── src/
│   │   ├── components/             # VoiceRecorder, TranscriptEditor, AnswerCard, AudioPlayer, etc.
│   │   ├── hooks/                  # useRecorder MediaRecorder hook
│   │   ├── services/               # Backend API communication
│   │   ├── App.jsx                 # Main application state and flow
│   │   └── main.jsx
│   ├── package.json
│   └── vite.config.js
│
└── scripts/                        # Standalone API verification scripts (Prisma/Timbre POCs)
```

---

## 🚀 Getting Started

### Prerequisites
- **Python**: 3.11+ (Python 3.13.7 supported)
- **Node.js**: 20+ (Node.js 24 supported)
- **Git**

### 1. Backend Setup
```powershell
# Navigate to backend
cd backend

# Activate virtual environment (Windows PowerShell)
.\venv\Scripts\Activate

# Install dependencies
pip install -r requirements.txt

# Configure environment variables
Copy-Item .env.example .env
# Edit .env and supply your verified GNANI_API_KEY and endpoints
```

### 2. Frontend Setup
```powershell
# Navigate to frontend
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev
```

---

## 🛡️ Security & Privacy
- **No Client Secrets:** All external API communications (Gnani Prisma, Timbre) are strictly brokered through the FastAPI backend.
- **Environment Isolation:** Secrets are managed via `.env` and kept strictly out of version control via `.gitignore`.
- **Audio Minimization:** User audio is processed in-memory or transiently validated and discarded immediately after transcription.

---

## 🚦 Phased Implementation Roadmap
- **Phase 0:** Setup, repository baseline, virtual environment, and dependency provisioning. *(Completed)*
- **Phase 1:** Gnani Prisma & Timbre API documentation verification & standalone POC test scripts.
- **Phase 2:** Core FastAPI backend, configuration, and `/health` gateway.
- **Phase 3:** Curated knowledge repository, intent interpretation, and grounded answer pipeline (`/api/ask`).
- **Phase 4:** React + Vite UI with audio controls, transcript display/editor, and source cards.
- **Phase 5:** Full voice pipeline (`/api/voice-query` and `/api/speak`).
- **Phase 6:** End-to-end integration, error handling, and test suite.
- **Phase 7:** Docker & Docker Compose containerization.
- **Phase 8:** Optional advanced additions (Authorized Voice Cloning, Caching, Event streaming).
