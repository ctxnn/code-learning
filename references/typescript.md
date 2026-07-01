# TypeScript Teaching Reference

Quick reference for the TypeScript lane. Loaded when the user is working in TypeScript.

---

## Type System Essentials

### Narrowing
```typescript
function process(input: string | number) {
  if (typeof input === "string") {
    return input.toUpperCase();  // TS knows it's string here
  }
  return input.toFixed(2);       // TS knows it's number here
}
```

### Generics
```typescript
function identity<T>(arg: T): T {
  return arg;
}

// With constraints
function longest<T extends { length: number }>(a: T, b: T) {
  return a.length >= b.length ? a : b;
}
```

### Conditional & Mapped Types
```typescript
// Remove 'optional' from all properties
type Required<T> = {
  [K in keyof T]-?: T[K];
};

// Extract keys where value is a function
type FunctionKeys<T> = {
  [K in keyof T]: T[K] extends Function ? K : never;
}[keyof T];
```

### Template Literal Types
```typescript
type HTTPMethod = "GET" | "POST" | "PUT" | "DELETE";
type Endpoint = `/api/${string}`;
```

---

## tsconfig for Learners

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "strict": true,              // Enforce strong typing
    "esModuleInterop": true,     // Sensible defaults for imports
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

| Option | Why It Matters |
|--------|----------------|
| `strict` | Catches implicit `any`, unchecked nulls, and more |
| `esModuleInterop` | Lets you `import x from 'x'` for CJS modules |
| `module: NodeNext` | Native ESM in Node + TS together |

---

## ESM vs CJS

| ESM (`"type": "module"`) | CJS (default) |
|---------------------------|---------------|
| `import { x } from './x.js'` | `const { x } = require('./x')` |
| `export const foo = 1` | `exports.foo = 1` |
| Top-level `await` | No top-level `await` |

**Prefer ESM for new projects.** Use `.js` extension in imports even for `.ts` files — TS compiles to `.js`.

---

## Testing

```typescript
// vitest (recommended)
import { test, expect } from "vitest";

test("adds correctly", () => {
  expect(add(2, 3)).toBe(5);
});
```

---

## Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| `any` creep | Using `any` bypasses all type checking | Enable `strict` and `noImplicitAny`; use `unknown` instead |
| Implicit `any` | Function params without types | Add types or enable strict mode |
| Type widening | `const x = "hello"` is narrowed, but object keys widen | Use `as const` or explicit types |
| `strictNullChecks` surprises | `user.name` might be `undefined` | Use `?.` optional chaining or narrow with `if (user)` |
| `noUncheckedIndexedAccess` | Arrays/objects might return `undefined` | Enable for safety; handle `undefined` cases |

---

## Quick Snippets

**Safe API call with error handling:**
```typescript
async function fetchUser(id: string): Promise<User | null> {
  try {
    const res = await fetch(`/api/users/${id}`);
    if (!res.ok) return null;
    return await res.json() as User;
  } catch {
    return null;
  }
}
```

**Type-safe event emitter pattern:**
```typescript
type EventMap = {
  userLogin: { id: string; name: string };
  userLogout: { id: string };
};

function emit<K extends keyof EventMap>(event: K, data: EventMap[K]) {
  // Implementation here
}
```
