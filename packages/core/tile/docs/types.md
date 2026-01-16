# Types and Dynamic Values

Core type definitions, UI element structure, and dynamic value resolution system with JSON Pointer path support.

## Capabilities

### Dynamic Values

Dynamic values can be either literal values or path references to the data model. Paths use JSON Pointer notation (RFC 6901).

```typescript { .api }
/**
 * Dynamic value - can be a literal or a path reference to data model
 */
type DynamicValue<T = unknown> = T | { path: string };

/**
 * Dynamic string value
 */
type DynamicString = DynamicValue<string>;

/**
 * Dynamic number value
 */
type DynamicNumber = DynamicValue<number>;

/**
 * Dynamic boolean value
 */
type DynamicBoolean = DynamicValue<boolean>;
```

**Usage Examples:**

```typescript
import { resolveDynamicValue } from "@json-render/core";

const dataModel = {
  user: { name: "Alice", age: 25 },
  settings: { theme: "dark" },
};

// Literal values
const literal: DynamicValue<string> = "Hello";

// Path references
const pathRef: DynamicValue<string> = { path: "/user/name" };
const ageRef: DynamicValue<number> = { path: "/user/age" };

// Resolve dynamic values
const name = resolveDynamicValue(pathRef, dataModel); // "Alice"
const age = resolveDynamicValue(ageRef, dataModel); // 25
const greeting = resolveDynamicValue(literal, dataModel); // "Hello"
```

### Dynamic Value Resolution

Resolve dynamic values against a data model.

```typescript { .api }
/**
 * Resolve a dynamic value against a data model
 * @param value - The dynamic value (literal or path reference)
 * @param dataModel - The data model to resolve paths against
 * @returns The resolved value or undefined if path not found
 */
function resolveDynamicValue<T>(
  value: DynamicValue<T>,
  dataModel: DataModel
): T | undefined;
```

**Usage Examples:**

```typescript
import { resolveDynamicValue } from "@json-render/core";

const data = {
  form: { email: "user@example.com", age: 30 },
  settings: { notifications: true },
};

// Resolve path reference
const email = resolveDynamicValue({ path: "/form/email" }, data);
// Result: "user@example.com"

// Resolve literal
const count = resolveDynamicValue(5, data);
// Result: 5

// Resolve missing path
const missing = resolveDynamicValue({ path: "/nonexistent" }, data);
// Result: undefined
```

### JSON Pointer Path Operations

Get and set values in objects using JSON Pointer paths.

```typescript { .api }
/**
 * Get a value from an object by JSON Pointer path
 * @param obj - The object to read from
 * @param path - JSON Pointer path (e.g., "/user/name" or "user/name")
 * @returns The value at the path or undefined if not found
 */
function getByPath(obj: unknown, path: string): unknown;

/**
 * Set a value in an object by JSON Pointer path
 * Creates intermediate objects as needed
 * @param obj - The object to modify
 * @param path - JSON Pointer path (e.g., "/user/name" or "user/name")
 * @param value - The value to set
 */
function setByPath(
  obj: Record<string, unknown>,
  path: string,
  value: unknown
): void;
```

**Usage Examples:**

```typescript
import { getByPath, setByPath } from "@json-render/core";

const data = {
  user: { name: "Alice", profile: { age: 25 } },
  items: [1, 2, 3],
};

// Get nested values
const name = getByPath(data, "/user/name"); // "Alice"
const age = getByPath(data, "/user/profile/age"); // 25
const root = getByPath(data, "/"); // entire object

// Set nested values
setByPath(data, "/user/name", "Bob");
setByPath(data, "/user/profile/city", "NYC"); // creates intermediate objects
setByPath(data, "/settings/theme", "dark"); // creates settings object

// After modifications:
// {
//   user: { name: "Bob", profile: { age: 25, city: "NYC" } },
//   items: [1, 2, 3],
//   settings: { theme: "dark" }
// }
```

### UI Element Structure

Base structure for UI elements in the flat tree format.

```typescript { .api }
/**
 * Base UI element structure
 */
interface UIElement<
  T extends string = string,
  P = Record<string, unknown>
> {
  /** Unique key for reconciliation */
  key: string;
  /** Component type from the catalog */
  type: T;
  /** Component props */
  props: P;
  /** Child element keys (flat structure) */
  children?: string[];
  /** Parent element key (null for root) */
  parentKey?: string | null;
  /** Visibility condition */
  visible?: VisibilityCondition;
}
```

**Usage Examples:**

```typescript
import { type UIElement } from "@json-render/core";

// Simple element
const button: UIElement = {
  key: "btn-1",
  type: "Button",
  props: { label: "Click me" },
};

// Element with children
const card: UIElement = {
  key: "card-1",
  type: "Card",
  props: { title: "Welcome" },
  children: ["text-1", "btn-1"],
};

// Child element with parent reference
const text: UIElement = {
  key: "text-1",
  type: "Text",
  props: { content: "Hello" },
  parentKey: "card-1",
};

// Element with visibility condition
const adminPanel: UIElement = {
  key: "admin-1",
  type: "Panel",
  props: { title: "Admin" },
  visible: { auth: "signedIn" },
};
```

### UI Tree Structure

Flat tree structure optimized for AI generation.

```typescript { .api }
/**
 * Flat UI tree structure (optimized for LLM generation)
 */
interface UITree {
  /** Root element key */
  root: string;
  /** Flat map of elements by key */
  elements: Record<string, UIElement>;
}
```

**Usage Examples:**

```typescript
import { type UITree } from "@json-render/core";

const tree: UITree = {
  root: "card-1",
  elements: {
    "card-1": {
      key: "card-1",
      type: "Card",
      props: { title: "Dashboard" },
      children: ["text-1", "btn-1"],
    },
    "text-1": {
      key: "text-1",
      type: "Text",
      props: { content: "Welcome back!" },
      parentKey: "card-1",
    },
    "btn-1": {
      key: "btn-1",
      type: "Button",
      props: { label: "Get Started" },
      parentKey: "card-1",
    },
  },
};

// Access elements
const root = tree.elements[tree.root]; // card-1
const children = root.children?.map((key) => tree.elements[key]); // [text-1, btn-1]
```

### Data Model

Type definition for data models used throughout the system.

```typescript { .api }
/**
 * Data model type
 */
type DataModel = Record<string, unknown>;
```

### Auth State

Auth state for visibility evaluation.

```typescript { .api }
/**
 * Auth state for visibility evaluation
 */
interface AuthState {
  isSignedIn: boolean;
  user?: Record<string, unknown>;
}
```

### Visibility Condition

Visibility condition types (see Visibility System for details).

```typescript { .api }
/**
 * Visibility condition types
 */
type VisibilityCondition =
  | boolean
  | { path: string }
  | { auth: "signedIn" | "signedOut" }
  | LogicExpression;
```

### Logic Expression

Logic expression for complex conditions (see Visibility System for details).

```typescript { .api }
/**
 * Logic expression for complex conditions
 */
type LogicExpression =
  | { and: LogicExpression[] }
  | { or: LogicExpression[] }
  | { not: LogicExpression }
  | { path: string }
  | { eq: [DynamicValue, DynamicValue] }
  | { neq: [DynamicValue, DynamicValue] }
  | { gt: [DynamicValue<number>, DynamicValue<number>] }
  | { gte: [DynamicValue<number>, DynamicValue<number>] }
  | { lt: [DynamicValue<number>, DynamicValue<number>] }
  | { lte: [DynamicValue<number>, DynamicValue<number>] };
```

### Component Schema

Type for component prop schemas using Zod.

```typescript { .api }
/**
 * Component schema definition using Zod
 */
type ComponentSchema = z.ZodType<Record<string, unknown>>;
```

### Validation Mode

Validation mode for catalog validation.

```typescript { .api }
/**
 * Validation mode for catalog validation
 */
type ValidationMode = "strict" | "warn" | "ignore";
```

### JSON Patch

JSON patch operation types.

```typescript { .api }
/**
 * JSON patch operation types
 */
type PatchOp = "add" | "remove" | "replace" | "set";

/**
 * JSON patch operation
 */
interface JsonPatch {
  op: PatchOp;
  path: string;
  value?: unknown;
}
```

## Zod Schemas

The library provides Zod schemas for runtime validation of dynamic values.

```typescript { .api }
/**
 * Zod schema for dynamic values
 */
const DynamicValueSchema: z.ZodType<DynamicValue>;

/**
 * Zod schema for dynamic strings
 */
const DynamicStringSchema: z.ZodType<DynamicString>;

/**
 * Zod schema for dynamic numbers
 */
const DynamicNumberSchema: z.ZodType<DynamicNumber>;

/**
 * Zod schema for dynamic booleans
 */
const DynamicBooleanSchema: z.ZodType<DynamicBoolean>;
```
