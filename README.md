# AI Chat Agent Platform

> A production-ready Spring Boot + Spring AI backend demonstrating enterprise patterns for AI-powered applications: RAG, function calling, JWT auth, streaming, and full observability.

---
## Chat with AI Agent:
<img width="2978" height="1458" alt="image" src="https://github.com/user-attachments/assets/fc45ed80-a96d-47d1-b347-e593cc8cd790" />


---

## Architecture
<img width="1472" height="1440" alt="image" src="https://github.com/user-attachments/assets/87693d9e-bb9e-487c-822b-3449d8813a45" />

```
┌─────────────────────────────────────────────────────────────────────┐
│                          Clients                                    │
│          Browser (Dev UI)  ·  REST API Client  ·  SSE Stream        │
└───────────────────────────────┬─────────────────────────────────────┘
                                │ HTTPS / SSE
┌───────────────────────────────▼─────────────────────────────────────┐
│                    Security Layer                                   │
│         JWT Auth Filter  ·  Rate Limit Filter (per-minute)          │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
┌───────────────────────────────▼─────────────────────────────────────┐
│                     REST API Controllers                            │
│   /auth  ·  /chat/sessions  ·  /chat/messages  ·  /knowledge        │
└──────────────┬───────────────────────────────┬──────────────────────┘
               │                               │
┌──────────────▼──────────────┐  ┌────────────▼──────────────────────┐
│       Business Services     │  │         AI / Agent Services       │
│  UserService                │  │  AgentService                     │
│  ChatSessionService         │  │   ├─ chat()   (sync)              │
│  KnowledgeService           │  │   ├─ streamChat()  (SSE / Flux)   │
│  (vector ingest)            │  │   ├─ generateImage()  (DALL-E 3)  │
└──────────────┬──────────────┘  │   └─ runTask()  (ReAct)           │
               │                 └──────────┬──────────────────────┬─┘
               │                            │                      │
┌──────────────▼──────────────┐  ┌───────────▼──────────────┐  ┌───▼────────────────────┐
│  Spring Data JPA            │  │   Spring AI ChatClient   │  │  RAG Advisor           │
│  Repositories               │  │   (OpenAI GPT-4o-mini)   │  │  (VectorStoreRag       │
│  ├─ UserRepository          │  │                          │  │   Advisor)             │
│  ├─ ChatSessionRepository   │  │   LLM Tools              │  │   PgVector similarity  │
│  └─ ChatMessageRepository   │  │   ├─ searchWeb           │  │   search → inject into │
└──────────────┬──────────────┘  │   ├─ searchKnowledgeBase │  │   system prompt        │
               │                 │   ├─ calculate           │  └────────────────────────┘
               │                 │   ├─ getCurrentDateTime  │
               │                 │   ├─ findUserProfile     │
               │                 │   ├─ saveToMemory        │
               │                 │   └─ learnConcept        │
               │                 └──────────┬───────────────┘
               │                            │
┌──────────────▼────────────────────────────▼───────────────────────────────────────────┐
│                          Infrastructure                                               │
│                                                                                       │
│  ┌───────────────────────┐   ┌──────────────┐   ┌───────────────────────────────────┐ │
│  │  PostgreSQL + pgvector│   │    Redis     │   │  OpenAI API        Tavily API     │ │
│  │  ─ users              │   │  (cache /    │   │  ─ Chat (GPT-4o)   (web search)   │ │
│  │  ─ chat_sessions      │   │   rate limit │   │  ─ Embeddings                     │ │
│  │  ─ chat_messages      │   └──────────────┘   │  ─ DALL-E 3 Images                │ │
│  │  ─ vector_store       │                      └───────────────────────────────────┘ │
│  │    (HNSW, 1536 dims)  │                                                            │
│  └───────────────────────┘                                                            │
│                                                                                       │
│  ┌──────────────────────────────────────────────────────────────────────────────────┐ │
│  │  Observability  →  Micrometer → Prometheus → Grafana                             │ │
│  │  Metrics: agent.calls · agent.latency · agent.tokens.total                       │ │
│  └──────────────────────────────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────────────────────┘
```

---

## Technologies


| Layer                | Technology                                                    |
| -------------------- | ------------------------------------------------------------- |
| **Language**         | Java 21 (virtual threads ready)                               |
| **Framework**        | Spring Boot 3.4.2                                             |
| **AI / LLM**         | Spring AI 1.0.0 · OpenAI GPT-4o-mini · DALL-E 3               |
| **RAG**              | PgVector (HNSW index, cosine similarity, 1536-dim embeddings) |
| **Web Search Tool**  | Tavily API                                                    |
| **Database**         | PostgreSQL 16 + pgvector extension                            |
| **Caching**          | Redis 7                                                       |
| **Security**         | Spring Security · JJWT 0.12 (stateless JWT) · BCrypt          |
| **Streaming**        | Server-Sent Events (SSE) · Project Reactor (Flux)             |
| **Observability**    | Micrometer · Prometheus · Grafana                             |
| **Mapping**          | MapStruct 1.6                                                 |
| **Build**            | Apache Maven 3.9                                              |
| **Containerization** | Docker · Docker Compose                                       |
| **Testing / API**    | Bruno API collection                                          |


---

## Features

- **Multi-turn Chat** — Persistent sessions with full conversation history
- **Streaming Responses** — Real-time token streaming via SSE
- **RAG (Retrieval-Augmented Generation)** — Inject private knowledge into every AI response
- **Image Generation** — DALL-E 3 image creation via chat endpoint
- **LLM Function Calling** — 7 built-in tools (web search, calculator, user lookup, scratchpad memory, knowledge base search, tutor)
- **JWT Authentication** — Stateless, per-request auth with 24-hour tokens
- **Rate Limiting** — Configurable per-minute caps on auth, API, and chat endpoints
- **Observability** — Prometheus metrics (calls, latency, token cost) + optional Grafana dashboard
- **Dev UI** — Browser-based test pages for auth and chat (local only)

---

## Quick Start

### Prerequisites


| Tool                    | Minimum Version            |
| ----------------------- | -------------------------- |
| Java                    | 21                         |
| Maven                   | 3.9                        |
| Docker + Docker Compose | latest                     |
| OpenAI API Key          | —                          |
| Tavily API Key          | optional (web search tool) |


### 1 — Clone & configure

```bash
git clone https://github.com/kranthi561/spring-ai-chat-agent.git
cd spring-ai-chat-agent

# Copy the env template and fill in your API keys
cp .env.example .env
```

Edit `.env`:

```env
OPENAI_API_KEY=sk-...
TAVILY_API_KEY=tvly-...      # optional
JWT_SECRET=change-me-256-bits-minimum
```

### 2 — Build the JAR

```bash
./mvnw -DskipTests package
```

### 3 — Start the full stack

```bash
# Core stack: app + postgres + redis
docker-compose up

# With Prometheus + Grafana
docker-compose --profile observability up
```

### 4 — Use the Dev UI


| Page                  | URL                                                                              |
| --------------------- | -------------------------------------------------------------------------------- |
| Login / Register      | [http://localhost:8080/dev-ui/auth.html](http://localhost:8080/dev-ui/auth.html) |
| Create Session + Chat | [http://localhost:8080/dev-ui/chat.html](http://localhost:8080/dev-ui/chat.html) |


Pre-filled credentials: `dev@example.com` / `DevPassword123!`

---

## API Reference

### Authentication

```http
POST /api/v1/auth/register
POST /api/v1/auth/login         → returns { token }
```

### Chat Sessions

```http
POST   /api/v1/chat/sessions                      # create session
GET    /api/v1/chat/sessions                      # list sessions
POST   /api/v1/chat/sessions/{id}/messages        # send message (sync)
POST   /api/v1/chat/sessions/{id}/messages/stream # send message (SSE)
GET    /api/v1/chat/sessions/{id}/messages        # conversation history
POST   /api/v1/chat/sessions/{id}/images          # generate image
POST   /api/v1/chat/sessions/{id}/tasks           # ReAct agent task
```

### Knowledge

```http
POST /api/v1/knowledge    # ingest document into vector store
```

### Observability

```http
GET /actuator/health
GET /actuator/metrics
GET /actuator/prometheus
GET /api/v1/agent/tools   # list available LLM tools
```

All endpoints except `/auth/**`, `/actuator/health`, and `/dev-ui/**` require:

```http
Authorization: Bearer <jwt-token>
```

---

## Environment Variables


| Variable                 | Default                | Required   |
| ------------------------ | ---------------------- | ---------- |
| `OPENAI_API_KEY`         | —                      | Yes        |
| `TAVILY_API_KEY`         | —                      | No         |
| `JWT_SECRET`             | dev-only key           | Yes (prod) |
| `DATABASE_URL`           | localhost:5432 (dev)   | Yes (prod) |
| `DATABASE_USERNAME`      | aiengineering          | Yes (prod) |
| `DATABASE_PASSWORD`      | aiengineering          | Yes (prod) |
| `REDIS_HOST`             | localhost              | Yes (prod) |
| `REDIS_PORT`             | 6379                   | No         |
| `OPENAI_CHAT_MODEL`      | gpt-4o-mini            | No         |
| `OPENAI_TEMPERATURE`     | 0.7 (dev) / 0.3 (prod) | No         |
| `RATE_LIMIT_AUTH`        | 10 req/min             | No         |
| `RATE_LIMIT_API`         | 60 req/min             | No         |
| `RATE_LIMIT_CHAT`        | 20 req/min             | No         |
| `SPRING_PROFILES_ACTIVE` | dev                    | No         |


---

## Project Structure

```
src/main/java/com/aiengineering/
├── advisor/          # RAG context injection (BaseAdvisor)
├── agent/            # LLM tool implementations (@Tool)
├── config/           # ChatClient, JWT, rate-limit beans
├── domain/           # JPA entities (User, ChatSession, ChatMessage)
├── observability/    # Micrometer metrics (AgentMetrics)
├── repository/       # Spring Data JPA repositories
├── security/         # JwtFilter, RateLimitFilter, SecurityConfig
├── service/          # Business logic (AgentService, KnowledgeService)
└── web/
    ├── controller/   # REST controllers
    ├── dto/          # Request / response DTOs
    ├── exception/    # GlobalExceptionHandler
    ├── interceptor/  # Request ID / logging
    └── mapper/       # MapStruct mappers
```

---

## License

MIT
