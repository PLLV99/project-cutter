> **⚠️ This is a VISION document — the target state, written in June 2026 before building started.**
> Nothing described below exists yet. Do NOT publish this as the repo README: every claim in a README must be true.
> The real README grows feature-by-feature as things actually work. Current plan: see [reference/roadmap.html](../../reference/roadmap.html). Decision record: [ADR-0002](../adr/0002-readme-becomes-vision-doc.md).

# Project Cutter — AI-Powered Event-Driven Trading & Risk Engine

A cloud-native, full-stack, event-driven trading platform that combines a real-time web dashboard, an asynchronous Kafka pipeline, and an **AI-powered risk-evaluation engine**. Trade orders flow from a Next.js frontend through a Spring Boot API, into a Kafka-driven backend where an AI worker evaluates risk and explains its decisions in natural language.

This is a personal project built to explore production-grade backend patterns used in financial systems — asynchronous messaging, the Transactional Outbox pattern, read/write separation — combined with a modern full-stack UI and practical LLM integration.

> **Status:** MVP — full trading flow, event pipeline, AI risk engine, web UI, and CI/CD are functional. Some production concerns (WAF, full observability stack, admin portal) are documented under [Production Considerations](#production-considerations) but intentionally scoped out of the MVP.

---

## Highlights

- 🖥️ **Full-stack** — Next.js + React dashboard talking to a Java backend
- ⚡ **Event-driven** — Kafka pipeline with the Transactional Outbox pattern (zero message loss)
- 🤖 **AI-powered** — LLM-based risk explanation + ML-assisted risk scoring
- 🔁 **Real-time** — live order updates pushed to the UI via Server-Sent Events
- 🐳 **Production-minded** — Dockerized, CI/CD, designed for read/write DB splitting and compliance

---

## Architecture

```
   ┌─────────────────┐
   │  Next.js + React│  ◀── live updates (SSE)
   │   Dashboard     │
   └────────┬────────┘
            │ REST
            ▼
   ┌─────────────────┐
   │  API Gateway    │ ──── JWT auth, rate limiting
   │  (Spring Boot)  │
   └────────┬────────┘
            │ HTTP 202 (Fire-and-Forget)
            ▼
   ┌─────────────────────┐
   │ PostgreSQL + Outbox │ ──── single transaction
   └──────────┬──────────┘
              │ relay (Transactional Outbox)
              ▼
       ┌─────────────┐
       │ Apache Kafka│
       └──────┬──────┘
              │ consume (async)
              ▼
   ┌──────────────────────┐
   │   AI Risk Worker     │ ──── ML scoring + LLM explanation
   │   (Python, FastAPI)  │
   └──────────┬───────────┘
              │ commit result + SSE push
              ▼
         PostgreSQL
```

A full architecture diagram (draw.io) is available in [`/docs/architecture.png`](docs/).

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js, React, Tailwind CSS |
| Real-time | Server-Sent Events (SSE) |
| API Gateway | Java 21, Spring Boot |
| Messaging | Apache Kafka |
| AI Risk Worker | Python, FastAPI |
| AI / ML | LLM API (risk explanation), scikit-learn (risk scoring) |
| Database | PostgreSQL |
| Cache / Rate Limiting | Redis |
| Containerization | Docker, docker-compose |
| CI/CD | GitHub Actions |
| Cloud | AWS |

---

## AI Capabilities

The risk worker does two things when it receives a trade order:

**1. ML-assisted risk scoring** — A lightweight model scores each order on configurable risk factors (order size relative to portfolio, volatility, frequency) and produces a risk score. The model is intentionally simple and explainable rather than a black box, which matters in a financial / compliance context.

**2. LLM-based risk explanation** — For flagged orders, the worker calls an LLM API to generate a plain-language explanation of *why* an order was flagged (e.g. "This order is 8× the user's average trade size and was placed during a high-volatility window"). This turns an opaque rejection into something a human reviewer — or the trader — can actually understand.

> **Design note:** LLM calls are wrapped with a timeout and a fallback to the rule-based explanation, so the system stays responsive and never blocks a trade waiting on an external API. This reflects how AI is realistically integrated into latency-sensitive financial systems.

---

## Key Patterns Implemented

**Transactional Outbox** — Trade orders are written to an `outbox` table in the *same* database transaction as the order itself. A relay process then publishes them to Kafka. This guarantees no message is lost even if the application crashes between writing the order and publishing the event.

**Fire-and-Forget (HTTP 202)** — The API acknowledges the order with `202 Accepted` and a correlation ID within ~50ms, then processes it asynchronously. The frontend receives the final result over SSE. This keeps the API responsive under load.

**Saga / Compensating Transaction** — When the risk worker rejects a trade, a compensating action automatically reverses the reserved balance, achieving eventual consistency without manual intervention.

**Rate Limiting** — A Redis-backed token-bucket limiter protects the API from abuse, returning `429 Too Many Requests` when limits are exceeded.

---

## Getting Started

### Prerequisites
- Docker & docker-compose
- An LLM API key (set `LLM_API_KEY` in `.env`) — the AI explanation feature falls back gracefully if not provided
- (Optional) Java 21, Python 3.12, and Node.js for local development outside containers

### Run the full stack

```bash
git clone https://github.com/<your-username>/project-cutter.git
cd project-cutter
cp .env.example .env       # add your LLM_API_KEY here
docker-compose up --build
```

This starts the Next.js frontend, API Gateway, Kafka, PostgreSQL, Redis, and the AI risk worker.

- Frontend: `http://localhost:3000`
- API + Swagger docs: `http://localhost:8080/swagger-ui.html`

### Try it via API

```bash
# Register and log in to get a JWT
curl -X POST http://localhost:8080/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username": "trader1", "password": "secure123"}'

# Submit a trade order (returns 202 + correlationId)
curl -X POST http://localhost:8080/api/v1/orders \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"symbol": "AAPL", "side": "BUY", "quantity": 10}'
```

Or just use the web dashboard at `http://localhost:3000` — submit an order and watch the AI risk decision appear in real time.

---

## Testing

```bash
./mvnw test           # API Gateway (Java)
pytest                # AI Risk Worker (Python)
npm test              # Frontend (Next.js)
```

Tests run automatically on every push via GitHub Actions ([`.github/workflows/ci.yml`](.github/workflows/)).

---

## Project Structure

```
project-cutter/
├── frontend/           # Next.js + React dashboard (SSE live updates)
├── api-gateway/        # Spring Boot REST API + JWT + Outbox relay
├── risk-worker/        # Python AI worker: ML scoring + LLM explanation
├── db/
│   └── migrations/     # Flyway SQL migrations
├── docs/
│   └── architecture.png
├── docker-compose.yml
├── .env.example
└── .github/workflows/  # CI pipeline
```

---

## Production Considerations

The following were designed as part of the system but scoped out of the MVP to keep it focused. In a production deployment they would be essential:

- **AWS WAF** in front of the CDN for DDoS / SQL injection / XSS protection
- **Circuit Breaker** (Resilience4j) on the API → worker call path to prevent cascading failures
- **Immutable audit log** (append-only table) for regulatory compliance
- **PII data masking** in all logs and API responses
- **Read replica** with read/write split routing for scaling reads
- **Observability**: OpenTelemetry tracing, Prometheus metrics, Grafana dashboards

---

## What I Learned

Building this taught me how event-driven systems handle the hard problems synchronous APIs hide: partial failures, message ordering, and consistency across service boundaries. Integrating an LLM into a latency-sensitive pipeline also taught me a practical lesson — AI is most useful when it *augments* a deterministic system (here, explaining rule-based decisions) rather than replacing it, and must always have a fast fallback path.

---

## License

MIT
