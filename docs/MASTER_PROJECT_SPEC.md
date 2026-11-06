# SWARAJYA AI
## ULTIMATE MASTER SRS + TECHNICAL ARCHITECTURE + IMPLEMENTATION BLUEPRINT
Complete build guide: requirements, technologies, AI/RAG, voice, Kafka, RabbitMQ, Docker, APIs, testing, deployment, demo and exact execution order.

**IMPORTANT:** This is an ambitious production-style architecture with a 4-day MVP path. The core MVP must work WITHOUT Kafka/RabbitMQ/Docker first. Add infrastructure only after the core pipeline works. Exact Gnani API details must be verified from official documentation.

---

### 1. Product Vision
Swarajya AI is a voice-first, AI-assisted information system that lets users ask questions naturally through speech or text and receive understandable, grounded answers. The system demonstrates a complete voice AI pipeline: Speech-to-Text → interpretation → retrieval → reasoning/answer generation → Text-to-Speech. Optional authorized Voice Cloning provides personalized output.

---

### 2. Recommended MVP Domain
For reliability, start with Student Services, Scholarships, document requirements and application guidance. Architect the system so additional domains can be added later.
- Scholarship guidance
- Document requirements
- Application steps
- Basic eligibility guidance where verified
- Frequently asked questions

*Do not claim nationwide coverage unless your knowledge base genuinely supports it.*

---

### 3. Priority Matrix
| Priority | Features |
|---|---|
| 🔴 MUST BUILD | React UI, FastAPI, typed input, microphone recording, Prisma STT, transcript, interpretation, retrieval, grounded answer, Timbre TTS, audio playback, error handling |
| 🟡 BUILD IF CORE IS STABLE | Voice Cloning, PostgreSQL, Redis cache, Docker Compose, structured analytics/logging |
| 🟢 ADVANCED / ONLY IF TIME | Kafka, RabbitMQ, vector DB, full RAG embeddings, background workers, production deployment |
| ⚪ FUTURE | Authentication, admin panel, multi-domain knowledge, monitoring dashboards, Kubernetes |

---

### 4. Functional Requirements
| ID | Requirement |
|---|---|
| FR-01 | Typed user query |
| FR-02 | Browser microphone recording |
| FR-03 | Audio validation |
| FR-04 | Gnani Prisma Speech-to-Text |
| FR-05 | Display/edit transcript |
| FR-06 | Detect topic and intent |
| FR-07 | Retrieve relevant verified knowledge |
| FR-08 | Generate grounded answer |
| FR-09 | Display answer text |
| FR-10 | Gnani Timbre Text-to-Speech |
| FR-11 | Audio playback |
| FR-12 | Loading/error/retry states |
| FR-13 | Source metadata |
| FR-14 | Optional authorized Voice Cloning |
| FR-15 | Optional conversation history |

---

### 5. Non-Functional Requirements
- **Security:** API secrets only on backend.
- **Privacy:** Minimize audio retention.
- **Reliability:** Text answer survives TTS failure.
- **Usability:** Simple voice controls and typed fallback.
- **Maintainability:** Modular services.
- **Accuracy:** Factual claims grounded in curated knowledge.
- **Performance:** Responsive UX, async I/O.
- **Scalability:** Infrastructure can be added without rewriting business logic.
- **Observability:** Structured logs and request IDs.
- **Ethics:** Explicit consent for personalized voice.

---

### 6. Full Technology Stack
| Layer | Recommended Technology | Role |
|---|---|---|
| Frontend | React + Vite | Web UI |
| Frontend styling | CSS / Tailwind optional | UI styling |
| Frontend HTTP | fetch or Axios | Backend calls |
| Browser audio | MediaRecorder API | Microphone capture |
| Backend | Python | Server-side logic |
| API framework | FastAPI | REST APIs |
| ASGI server | Uvicorn | Development/runtime server |
| Validation | Pydantic | Request/response schemas |
| HTTP client | httpx | Gnani API calls |
| Configuration | python-dotenv / pydantic-settings | Secrets/config |
| STT | Gnani Prisma | Speech → text |
| TTS | Gnani Timbre | Text → speech |
| Voice personalization | Gnani Voice Cloning if verified | Optional |
| Reasoning | Gnani Evon or verified LLM option | Interpretation/answer generation |
| MVP knowledge | JSON/Markdown | Curated source of truth |
| Database optional | PostgreSQL | Users/history/knowledge metadata |
| ORM optional | SQLAlchemy | Database access |
| Cache optional | Redis | Caching/session/rate limiting |
| Vector search optional | Qdrant/FAISS/Chroma | Semantic retrieval |
| Embeddings optional | Sentence Transformers | Vector creation |
| Event streaming optional | Apache Kafka | Events/analytics |
| Task queue optional | RabbitMQ + worker | Background jobs |
| Containerization | Docker | Consistent environments |
| Orchestration | Docker Compose | Multi-service local stack |
| Testing | pytest | Backend tests |
| Frontend testing optional | Vitest/React Testing Library | UI tests |
| E2E optional | Playwright | End-to-end tests |
| Version control | Git + GitHub | Source control |
| CI optional | GitHub Actions | Automated checks |

---

### 7. Important Architecture Decision: Kafka vs RabbitMQ
You do NOT need both for the 4-day MVP. They solve different problems.
- **Kafka:** High-throughput event streaming and durable event logs (Future: analytics, interaction events, audit streams).
- **RabbitMQ:** Background task queues and work distribution (Optional: long-running audio/processing jobs).
- **Redis:** Fast cache / simple coordination (More useful early than Kafka/RabbitMQ).
- **None initially:** Simple synchronous MVP (Recommended starting point).

*Recommended sequence:* Core synchronous app → Docker Compose → Redis if needed → RabbitMQ for background work OR Kafka for event streaming. Do not add both merely because they are impressive.

---

### 8. System Architecture
```text
BROWSER / REACT + VITE
Mic + Text Input + Transcript + Answer + Audio Player
                     │
                     ▼
              FASTAPI API GATEWAY
                     │
      ┌──────────────┼──────────────┐
      ▼              ▼              ▼
Prisma Service  AI/Interpreter  Retrieval Service
      │              │              │
Gnani Prisma    Evon/LLM opt.   JSON / DB / Vector DB
      │              │              │
      └──────────────┼──────────────┘
                     ▼
               Answer Service
                     ▼
               Timbre Service
                     ▼
                Audio Response

OPTIONAL INFRASTRUCTURE:
PostgreSQL ← persistent data
Redis ← cache
RabbitMQ ← background jobs
Kafka ← event stream/analytics
Docker Compose ← runs services together
```

---

### 9. Core Request Pipeline
1. User speaks or types.
2. Frontend creates audio Blob or text request.
3. FastAPI validates input.
4. Voice request goes to verified Prisma integration.
5. Transcript is returned and displayed.
6. User can edit transcript if necessary.
7. Interpreter extracts intent/topic.
8. Retrieval service finds relevant knowledge.
9. Answer service builds grounded response.
10. Answer is returned as text.
11. Timbre converts answer to audio if requested.
12. Frontend plays audio.
13. Relevant events/errors are logged.

---

### 10. AI and Reasoning Architecture
- **10.1 Interpreter:** Transforms natural language into structured intent:
  `{"topic":"scholarship","intent":"document_requirements","language_style":"code_mixed","response_style":"simple"}`
- **10.2 Retrieval:** Starts with deterministic keyword/topic matching. Upgrade later to semantic retrieval using embeddings and a vector database.
- **10.3 Answer Generator:** Receives user query plus retrieved context. Answers from supplied context and explicitly acknowledges missing info.
- **10.4 Grounding Prompt Rules:**
  - Use supplied verified context.
  - Do not invent facts.
  - If context is insufficient, say what is missing.
  - Use simple language.
  - Separate steps/documents/notes where helpful.
  - Keep response concise enough for speech.

---

### 11. RAG Evolution Path
- **MVP:** JSON knowledge + keyword/topic retrieval
- **Phase 2:** Chunk documents + Sentence Transformers embeddings
- **Phase 3:** Store vectors in Qdrant/FAISS/Chroma
- **Phase 4:** Hybrid semantic + metadata filtering + reranking

---

### 12. Data Architecture
#### MVP Knowledge Entry Schema
```json
{
  "id": "SCH-001",
  "topic": "scholarship",
  "intent": "document_requirements",
  "title": "Required documents",
  "content": "Verified information here",
  "source_name": "Official source",
  "source_url": "Verified URL",
  "last_reviewed": "YYYY-MM-DD",
  "language": "en"
}
```

---

### 13. API Contracts
- `GET /health` → Health check
- `POST /api/ask` → Typed question
- `POST /api/voice-query` → Audio query
- `POST /api/speak` → Standalone TTS
- `POST /api/voice-clone/...` → Optional authorized voice cloning workflow

---

### 14. Error Handling and Fallbacks
| Problem | Fallback |
|---|---|
| Microphone denied | Typed input |
| Empty audio | Ask user to record again |
| STT failure | Retry or typed fallback |
| Poor transcript | Allow transcript editing |
| No relevant knowledge | Clearly state information is unavailable |
| AI/LLM failure | Safe fallback response |
| TTS failure | Still show text answer |
| Voice cloning failure | Use default voice |

---

### 15. Security & Privacy
- API keys strictly server-side.
- `.env` in `.gitignore`.
- Maintain `.env.example` without real secrets.
- Validate audio sizes & MIME types.
- Never log secrets.
