---
title: TypeScript Code Style Guide
description: TypeScript style rules optimized for AI agent consumption.
trigger: always_on
---

# TypeScript Code Style Guide

## 1. Clean Code

### Guard Clauses

Use guard clauses and early returns. Do not nest core logic.

```typescript
// 🚫 BAD
function processUser(user: User | null): void {
  if (user !== null) {
    if (user.isActive) {
      saveUser(user);
    } else {
      throw new Error("User is inactive");
    }
  } else {
    throw new Error("User not found");
  }
}

//  GOOD
function processUser(user: User | null): void {
  if (!user) {
    throw new Error("User not found");
  }
  if (!user.isActive) {
    throw new Error("User is inactive");
  }
  saveUser(user);
}
```

### Single Responsibility

One responsibility per function or class. Extract sub-logic into helper functions.

## 2. Types & Declarations

### Strict Typing

- **No `any`**: Use `unknown` with type narrowing.
- **Strong Typing**: Explicitly type maps, dictionaries, and event handlers.

```typescript
// 🚫 BAD
function parsePayload(payload: any): void {
  console.log(payload.id);
}

//  GOOD
function parsePayload(payload: unknown): void {
  if (payload && typeof payload === "object" && "id" in payload) {
    console.log((payload as { id: string }).id);
  }
}
```

### Type Declarations

- **Prefer `type`**: Use `type` over `interface` unless inheritance/merging is required.

```typescript
// 🚫 BAD
interface User {
  id: string;
  name: string;
}

//  GOOD
type User = {
  id: string;
  name: string;
};
```

### Explicit Return Types

- Declare return types for all functions.

```typescript
// 🚫 BAD
const add = (a: number, b: number) => a + b;

//  GOOD
const add = (a: number, b: number): number => a + b;
```

### Immutability

- Use `readonly` for objects/arrays that should not mutate.

```typescript
// 🚫 BAD
type Configuration = {
  apiUrl: string;
  timeout: number;
};
const tags: string[] = ["typescript", "rules"];

//  GOOD
type Configuration = {
  readonly apiUrl: string;
  readonly timeout: number;
};
const tags: readonly string[] = ["typescript", "rules"];
```

## 3. API & Network Standards

### Type-Safe Payloads

- Define types/schemas for all network inputs, outputs, query parameters, and messages. No unstructured dictionaries.

### Runtime Schema Validation

- Validate data at application boundaries (HTTP, configs, DB, external systems) using libraries like Zod or Valibot.

```typescript
// 🚫 BAD
async function fetchUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

//  GOOD
import { z } from "zod";

const UserSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email(),
});

async function fetchUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  const rawData = await response.json();
  return UserSchema.parse(rawData);
}
```

### Standardized Errors

- Use structured API error responses distinguishing client from server errors.

## 4. Development Best Practices

### Error Handling

- Throw and catch `Error` (or subclass) instances. Do not throw strings or plain objects.

```typescript
// 🚫 BAD
if (!isValid) {
  throw "Invalid argument";
}

//  GOOD
if (!isValid) {
  throw new Error("Invalid argument");
}
```

### Asynchronous Operations

- **Use `async/await`**: Prefer over `.then()/.catch()`.
- **Handle Rejections**: Catch all async errors. No unhandled floating promises.

```typescript
// 🚫 BAD
function loadData() {
  fetch("/data")
    .then((res) => res.json())
    .then((data) => process(data));
}

//  GOOD
async function loadData(): Promise<void> {
  try {
    const res = await fetch("/data");
    const data = await res.json();
    process(data);
  } catch (error) {
    console.error("Failed to load data:", error);
  }
}
```

## 5. Future-Proofing

### Nullish Coalescing & Optional Chaining

- Use `??` and `?.` instead of `||` or boolean casts when falsy values (e.g., `0`, `""`) are valid.

```typescript
// 🚫 BAD
const itemsCount = settings.count || 10;

//  GOOD
const itemsCount = settings.count ?? 10;
```

### Modern Features Only

- Avoid `namespace`, `module`, or constructor parameter properties. Use ES modules and standard classes.

## 6. Supporting Configurations

Use these configurations to enforce style rules:

### `tsconfig.json`

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true
  }
}
```

### `eslint.config.mjs`

```javascript
import typescriptEslint from "@typescript-eslint/eslint-plugin";
import typescriptParser from "@typescript-eslint/parser";

export default [
  {
    files: ["**/*.ts", "**/*.tsx"],
    languageOptions: {
      parser: typescriptParser,
      parserOptions: {
        project: "./tsconfig.json",
      },
    },
    plugins: {
      "@typescript-eslint": typescriptEslint,
    },
    rules: {
      "@typescript-eslint/consistent-type-definitions": ["error", "type"],
      "@typescript-eslint/no-explicit-any": "error",
      "@typescript-eslint/explicit-function-return-type": [
        "error",
        {
          allowExpressions: true,
          allowTypedFunctionExpressions: true,
        },
      ],
      "@typescript-eslint/consistent-type-assertions": [
        "error",
        {
          assertionStyle: "as",
          objectLiteralTypeAssertions: "never",
        },
      ],
      "@typescript-eslint/no-unused-vars": [
        "error",
        {
          argsIgnorePattern: "^_",
          varsIgnorePattern: "^_",
        },
      ],
      "@typescript-eslint/prefer-readonly": "error",
      "@typescript-eslint/await-thenable": "error",
      "@typescript-eslint/no-floating-promises": "error",
      "@typescript-eslint/no-misused-promises": "error",
    },
  },
];
```
