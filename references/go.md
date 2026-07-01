# Go Teaching Reference

> **Note:** This is an extended Go teaching reference. Core content is inlined in SKILL.md. This file is optional enrichment.

Quick reference for the Go lane. Loaded when the user is working in Go.

---

## Idioms

### Error Handling
```go
// Always check errors — never discard them
val, err := doSomething()
if err != nil {
    return fmt.Errorf("doSomething failed: %w", err)  // wrap with %w
}
```

### Struct Methods & Interfaces
```go
type Shape interface {
    Area() float64
}

type Circle struct {
    Radius float64
}

// Pointer receiver — mutates or avoids copy
func (c *Circle) Area() float64 {
    return math.Pi * c.Radius * c.Radius
}
```

### Goroutines & Channels
```go
// Fan-out: launch workers, collect results
results := make(chan int, len(jobs))
for _, job := range jobs {
    go func(j Job) {
        results <- process(j)
    }(job)
}
```

### Defer
```go
func readFile(path string) ([]byte, error) {
    f, err := os.Open(path)
    if err != nil {
        return nil, err
    }
    defer f.Close()  // runs when function returns
    return io.ReadAll(f)
}
```

### Table-Driven Tests
```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 2, 3, 5},
        {"negative", -1, -2, -3},
        {"zero", 0, 0, 0},
    }
    for _, tc := range tests {
        t.Run(tc.name, func(t *testing.T) {
            got := Add(tc.a, tc.b)
            if got != tc.expected {
                t.Errorf("Add(%d, %d) = %d, want %d", tc.a, tc.b, got, tc.expected)
            }
        })
    }
}
```

---

## stdlib Modules to Know

| Package | Use Case |
|---------|----------|
| `net/http` | HTTP client and server; `http.ListenAndServe`, `http.HandleFunc` |
| `encoding/json` | Marshal/Unmarshal JSON; use struct tags (`json:"name"`) |
| `fmt` | Formatted I/O; `Sprintf`, `Errorf` (with `%w` for wrapping) |
| `os` | File operations, env vars, process control |
| `context` | Cancellation, deadlines, request-scoped values |
| `testing` | Built-in test framework; `t.Run`, `t.Parallel`, benchmarks |
| `database/sql` | Generic SQL interface; use with driver (e.g., `lib/pq`, `go-sqlite3`) |
| `sync` | `Mutex`, `WaitGroup`, `Once` for concurrency primitives |
| `io` | Reader/Writer interfaces; `io.Copy`, `io.ReadAll` |
| `strings` | String manipulation; `Contains`, `Split`, `TrimSpace`, `Builder` |
| `strconv` | String ↔ number conversion; `Atoi`, `Itoa`, `ParseFloat` |
| `log` | Simple logging; prefer `log/slog` (Go 1.21+) for structured logs |

---

## Tooling

| Tool | Purpose |
|------|---------|
| `go mod` | Dependency management; `go mod init`, `go mod tidy` |
| `go test` | Run tests; `-v` for verbose, `-cover` for coverage, `-race` for race detection |
| `go vet` | Static analysis; catches common mistakes (printf args, unreachable code) |
| `go fmt` / `gofmt` | Auto-format code; run before every commit |
| `golangci-lint` | Meta-linter aggregating 50+ linters; use `.golangci.yml` for config |
| `delve` (`dlv`) | Debugger; `dlv debug`, breakpoints, goroutine inspection |

---

## Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| Ignoring errors | `val, _ := fn()` discards the error | Always handle or explicitly document why it's safe to ignore |
| Goroutine leaks | Goroutine blocks on channel with no reader/writer | Use `context.WithCancel`, buffered channels, or `select` with `done` channel |
| Nil pointer dereference | Uninitialized pointer or interface with nil concrete value | Check `!= nil` before dereferencing; use constructors (`NewFoo()`) |
| Variable shadowing | `:=` inside `if`/`for` creates a new variable instead of assigning outer one | Use `=` when updating existing variable; enable `shadow` linter |
| Slice append gotcha | `append` may or may not allocate a new backing array | Never assume append modifies the original; always reassign `s = append(s, x)` |
| `init()` overuse | Hidden initialization order, hard to test | Prefer explicit initialization in `main()` or constructors |

---

## Quick Snippets

**HTTP handler with error handling:**
```go
func handleGetUser(w http.ResponseWriter, r *http.Request) {
    id := r.PathValue("id")  // Go 1.22+ routing
    user, err := db.FindUser(r.Context(), id)
    if err != nil {
        http.Error(w, "user not found", http.StatusNotFound)
        return
    }
    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(user)
}
```

**Table-driven test with subtests:**
```go
func TestParseAge(t *testing.T) {
    tests := []struct {
        input   string
        want    int
        wantErr bool
    }{
        {"25", 25, false},
        {"abc", 0, true},
        {"-1", 0, true},
    }
    for _, tc := range tests {
        t.Run(tc.input, func(t *testing.T) {
            got, err := ParseAge(tc.input)
            if (err != nil) != tc.wantErr {
                t.Fatalf("ParseAge(%q) error = %v, wantErr %v", tc.input, err, tc.wantErr)
            }
            if got != tc.want {
                t.Errorf("ParseAge(%q) = %d, want %d", tc.input, got, tc.want)
            }
        })
    }
}
```
