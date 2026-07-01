# REPO_REPLICA Mode — Detailed Workflow

## When to Use
User shares an existing codebase (paste, zip, or paths) and wants to learn how to rebuild it from scratch.

## Workflow

### 1. Capture the Repo
- Ask for the repo contents, a zip, or key file paths.
- If too large, ask for: `README.md`, `package.json`/`pyproject.toml`, and the main entry point.

### 2. Build the Project Map
Create a living document in the conversation:

```
Project: __________
├── Entry point: __________
├── Modules:
│   ├── auth/     — JWT handling, bcrypt
│   └── db/       — connection pool, queries
├── Data flow:    Request → Router → Controller → Service → DB
├── Side effects: File writes, external API calls
└── Key types:    User, Post, AuthToken
```

### 3. Teach Bottom-Up Layers
Start from the innermost dependency, move outward.

| Layer | What to Build | Example |
|-------|---------------|---------|
| L1 Config & Deps | `.env`, `package.json` | Install deps, define env vars |
| L2 Types / Schema | TypeScript interfaces, SQL schema | `interface User { id: string; name: string; }` |
| L3 Utilities | Hashing, validation, helpers | `hashPassword()`, `validateEmail()` |
| L4 Data Layer | DB connection, queries | `getUserById()`, `createUser()` |
| L5 Business Logic | Services, use cases | `authenticateUser()`, `registerUser()` |
| L6 Routes / API | Controllers, route handlers | `POST /auth/login` |
| L7 Entry Point | Wire everything together | `app.listen(3000)` |

### 4. After Each Layer
- Run (or "imagine running") the code.
- Compare to the original repo.
- Ask: **"Why did the original author choose this approach?"**
- Note trade-offs (performance, readability, maintainability).

### 5. Full Recap
When done, produce a summary:
- What we built layer by layer
- Key decisions the original author made
- Your own improvements you could make now

---

## Handling Unknown Languages
If the repo is in a language the user didn't choose:
1. Ask if they want to learn that language or port the logic to their chosen language.
2. If porting, use the BLANK_BUILD mode with the repo as a specification.
3. If learning the new language, dynamically materialize a `{language}.md` first (see SKILL.md).

---

## Toy Example: Replicating a Tiny REST API

**Original repo:**
```
src/
  index.ts       # Express app
  routes/
    users.ts     # GET /users, POST /users
  db.ts          # In-memory array
```

**Teaching layers:**
1. **Init** — `npm init -y && npm install express`
2. **Types** — `interface User { id: string; name: string; }`
3. **DB layer** — `const users: User[] = []; export const getUsers = () => users;`
4. **Routes** — `router.get('/users', ...)`, `router.post('/users', ...)`
5. **App** — `app.use('/users', userRoutes); app.listen(3000)`

After each layer: *"Run it. Does it match? What's different from the original?"*
