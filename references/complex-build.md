# COMPLEX_BUILD Mode — Detailed Workflow

> **Note:** This is an extended COMPLEX_BUILD workflow reference. The core workflow is inlined in SKILL.md. This file is optional enrichment.

---

## When to Use

Activate COMPLEX_BUILD when the project involves any of:
- **Database persistence** — SQL, NoSQL, ORMs, migrations
- **API design** — REST, GraphQL, gRPC endpoints
- **Authentication / Authorization** — session management, JWT, OAuth, RBAC
- **Multi-service architecture** — microservices, message queues, workers
- **Stateful workflows** — data pipelines, CRON jobs, background tasks
- **Schema migrations** — evolving data models across versions

---

## System Design Checklist

Before writing code, work through each item with the learner:

### 1. Entities & Relationships
- Identify the core domain objects (User, Post, Order, etc.)
- Draw an ER diagram (or describe relationships in text)
- Decide cardinality: one-to-one, one-to-many, many-to-many

### 2. Data Flow Mapping
- Trace the path of a request from client → API → service → DB → response
- Identify where transformations, validation, and side-effects occur

### 3. Persistence Strategy

| Factor | SQL (Postgres, SQLite) | NoSQL (MongoDB, Redis) |
|--------|----------------------|----------------------|
| Schema stability | High — structured, relational | Low — schema-less, flexible |
| Query complexity | Complex joins, aggregations | Simple key-value or document lookups |
| Transactions | Strong ACID guarantees | Eventual consistency (usually) |
| Scale pattern | Vertical first, then read replicas | Horizontal sharding |
| Best for | E-commerce, finance, CMS | Caching, real-time feeds, IoT |

### 4. Error Boundary Identification
- Define where errors are caught, logged, and translated to user-facing messages
- Separate infrastructure errors (DB down) from domain errors (invalid input)

### 5. Auth Model Selection
- **Session-based**: server-side state, cookie-based, good for SSR apps
- **JWT**: stateless, good for SPAs and mobile, watch token size/expiry
- **OAuth2**: third-party auth delegation, use for social login

### 6. API Design
- **REST**: resource-oriented, HTTP verbs, status codes, versioning (`/api/v1/`)
- **GraphQL**: single endpoint, client-driven queries, good for complex frontends

---

## Milestone Planning Guide

Break every complex project into **4–6 milestones**. Each milestone must produce a **testable artifact** — something you can run, query, or demonstrate.

### How to Decompose
1. **M1 — Foundation**: Project scaffold, DB connection, basic model, health-check endpoint
2. **M2 — Core CRUD**: Create, read, update, delete for the primary entity
3. **M3 — Business Logic**: Validation rules, computed fields, relationships between entities
4. **M4 — Auth & Access Control**: User registration, login, route protection, role checks
5. **M5 — Integration**: External APIs, background jobs, notifications, file uploads
6. **M6 — Polish**: Error handling, logging, pagination, rate limiting, deployment config

### Example Milestone Breakdowns

**REST API + Database (e.g., Task Manager)**

| Milestone | Deliverable | Test |
|-----------|------------|------|
| M1 | Express + Prisma + Postgres; `GET /health` returns 200 | `curl localhost:3000/health` |
| M2 | CRUD for Tasks: `POST/GET/PATCH/DELETE /api/tasks` | Create + fetch a task via curl |
| M3 | Task validation, status transitions, due-date filtering | Unit tests for validation logic |
| M4 | JWT auth, `POST /auth/register`, `POST /auth/login`, protected routes | Auth flow integration test |
| M5 | Email reminders via background worker | Verify job queued on task creation |

**Real-Time Chat App**

| Milestone | Deliverable | Test |
|-----------|------------|------|
| M1 | WebSocket server + client connects; echo test | Send message, see echo |
| M2 | Rooms: create, join, leave; broadcast within room | Two clients chat in a room |
| M3 | Message persistence in DB; load history on join | Rejoin room, see old messages |
| M4 | Auth: login required, user display names | Only authenticated users can chat |
| M5 | Typing indicators, read receipts, presence | UI shows "user is typing…" |

**CLI Tool with Database (e.g., Expense Tracker)**

| Milestone | Deliverable | Test |
|-----------|------------|------|
| M1 | CLI scaffold with `add`, `list` commands; SQLite file | `expense add 50 "Groceries"` |
| M2 | Categories, date filtering, `summary` command | `expense summary --month 2024-01` |
| M3 | CSV/JSON export, `import` from bank statement | Export + re-import round-trip |
| M4 | Budget limits with warnings, recurring expenses | Warning when budget exceeded |

---

## Architecture Decision Record (ADR) Template

For each significant technical choice, create a brief ADR:

```
### ADR-{N}: {Decision Title}

**Context**: What is the situation that requires a decision?

**Alternatives Considered**:
1. Option A — description
2. Option B — description
3. Option C — description

**Trade-offs**:
| Criterion       | Option A | Option B | Option C |
|----------------|----------|----------|----------|
| Complexity     | Low      | Medium   | High     |
| Performance    | High     | Medium   | High     |
| Maintainability| High     | High     | Low      |

**Chosen Approach**: Option A

**Rationale**: Explain why — tie back to project constraints, team skill level, timeline.
```

---

## Testing Strategy per Layer

| Layer | Test Type | What to Test | Tools |
|-------|-----------|-------------|-------|
| Models / Utilities | Unit tests | Validation logic, transformations, pure functions | pytest, vitest, `go test` |
| API Routes | Integration tests | Request → response, status codes, error responses | supertest, httptest, requests |
| Database | Fixture-based tests | Migrations run cleanly, queries return expected data | Factory libraries, test containers |
| External Services | Mock tests | HTTP calls to third-party APIs, message queues | nock, httpx mock, wiremock |

### Database Test Fixtures
- Use a **separate test database** or in-memory SQLite for speed
- Seed known data before each test, tear down after
- Never run tests against production data

### Mocking External Services
- Mock at the HTTP boundary, not the function level
- Record real responses and replay them in tests
- Test both success and failure (timeout, 500, rate-limit) paths

---

## Common Complex-Build Pitfalls

| Pitfall | Why It's Dangerous | Prevention |
|---------|--------------------|------------|
| Premature optimization | Wastes time on performance before correctness is proven | Build correct first, profile second; optimize only measured bottlenecks |
| Scope creep | "Just one more feature" delays delivery indefinitely | Freeze scope per milestone; add new ideas to a backlog |
| Skipping migrations | Manual schema changes break reproducibility | Always use migration files; never ALTER TABLE by hand in production |
| Hardcoding config | Secrets in code, environment-specific values baked in | Use environment variables or config files; `.env` + `dotenv` pattern |
| No error handling strategy | Errors silently swallowed or leaked to users as stack traces | Define error types (domain, infra, validation); centralize error formatting |
| No input validation | SQL injection, XSS, corrupted data | Validate at API boundary; use schema validation (Zod, Pydantic, joi) |
