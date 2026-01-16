# @json-render/core

Framework-agnostic TypeScript library for safe, predictable, and guardrailed AI-driven UI generation. It enables developers to define type-safe component catalogs using Zod schemas that constrain what AI models can generate, ensuring output always matches expected schemas.

## Package Information

- **Package Name**: @json-render/core
- **Package Type**: npm
- **Language**: TypeScript
- **Installation**: `npm install @json-render/core` or `pnpm add @json-render/core`
- **Peer Dependencies**: zod ^4.0.0

## Core Imports

ESM:

```typescript
import {
  createCatalog,
  evaluateVisibility,
  resolveAction,
  executeAction,
  runValidation,
  type UIElement,
  type UITree,
  type Catalog,
} from "@json-render/core";
```

CommonJS:

```javascript
const {
  createCatalog,
  evaluateVisibility,
  resolveAction,
  executeAction,
  runValidation,
} = require("@json-render/core");
```

## Basic Usage

```typescript
import { createCatalog } from "@json-render/core";
import { z } from "zod";

// Create a catalog with type-safe component definitions
const catalog = createCatalog({
  name: "My Dashboard",
  components: {
    Card: {
      props: z.object({
        title: z.string(),
        description: z.string().nullable(),
      }),
      hasChildren: true,
      description: "A card container",
    },
    Button: {
      props: z.object({
        label: z.string(),
      }),
      description: "A clickable button",
    },
  },
});

// Validate UI elements against the catalog
const element = {
  key: "card-1",
  type: "Card",
  props: { title: "Welcome", description: "Hello world" },
};

const result = catalog.validateElement(element);
if (result.success) {
  console.log("Valid element:", result.data);
}
```

## Architecture

The library is built around several key subsystems:

- **Type System**: Dynamic values, UI elements, and data models with JSON Pointer path resolution
- **Catalog System**: Type-safe component definitions using Zod schemas with validation
- **Visibility System**: Conditional rendering based on data paths, auth state, and logic expressions
- **Action System**: Rich action definitions with dynamic parameters, confirmations, and callbacks
- **Validation System**: Field validation with built-in and custom validation functions

All systems are framework-agnostic and designed for AI-driven UI generation workflows.

## Capabilities

### Core Types and Dynamic Values

Foundation types for UI elements, trees, and dynamic value resolution with JSON Pointer paths.

```typescript { .api }
interface UIElement<T extends string = string, P = Record<string, unknown>> {
  key: string;
  type: T;
  props: P;
  children?: string[];
  parentKey?: string | null;
  visible?: VisibilityCondition;
}

interface UITree {
  root: string;
  elements: Record<string, UIElement>;
}

type DynamicValue<T = unknown> = T | { path: string };

function resolveDynamicValue<T>(
  value: DynamicValue<T>,
  dataModel: DataModel
): T | undefined;

function getByPath(obj: unknown, path: string): unknown;

function setByPath(
  obj: Record<string, unknown>,
  path: string,
  value: unknown
): void;
```

[Types and Dynamic Values](./types.md)

### Catalog Creation

Define type-safe component catalogs with Zod schemas, actions, and validation functions.

```typescript { .api }
interface CatalogConfig<TComponents, TActions, TFunctions> {
  name?: string;
  components: TComponents;
  actions?: TActions;
  functions?: TFunctions;
  validation?: ValidationMode;
}

interface Catalog<TComponents, TActions, TFunctions> {
  readonly name: string;
  readonly componentNames: (keyof TComponents)[];
  readonly actionNames: (keyof TActions)[];
  readonly functionNames: (keyof TFunctions)[];
  readonly validation: ValidationMode;
  readonly components: TComponents;
  readonly actions: TActions;
  readonly functions: TFunctions;
  readonly elementSchema: z.ZodType<UIElement>;
  readonly treeSchema: z.ZodType<UITree>;
  hasComponent(type: string): boolean;
  hasAction(name: string): boolean;
  hasFunction(name: string): boolean;
  validateElement(element: unknown): {
    success: boolean;
    data?: UIElement;
    error?: z.ZodError;
  };
  validateTree(tree: unknown): {
    success: boolean;
    data?: UITree;
    error?: z.ZodError;
  };
}

function createCatalog<TComponents, TActions, TFunctions>(
  config: CatalogConfig<TComponents, TActions, TFunctions>
): Catalog<TComponents, TActions, TFunctions>;

function generateCatalogPrompt<TComponents, TActions, TFunctions>(
  catalog: Catalog<TComponents, TActions, TFunctions>
): string;
```

[Catalog System](./catalog.md)

### Visibility Conditions

Evaluate visibility conditions based on data paths, auth state, and complex logic expressions.

```typescript { .api }
type VisibilityCondition =
  | boolean
  | { path: string }
  | { auth: "signedIn" | "signedOut" }
  | LogicExpression;

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

interface VisibilityContext {
  dataModel: DataModel;
  authState?: AuthState;
}

function evaluateVisibility(
  condition: VisibilityCondition | undefined,
  ctx: VisibilityContext
): boolean;

function evaluateLogicExpression(
  expr: LogicExpression,
  ctx: VisibilityContext
): boolean;
```

[Visibility System](./visibility.md)

### Actions

Resolve and execute rich actions with dynamic parameters, confirmations, and callbacks.

```typescript { .api }
interface Action {
  name: string;
  params?: Record<string, DynamicValue>;
  confirm?: ActionConfirm;
  onSuccess?: ActionOnSuccess;
  onError?: ActionOnError;
}

interface ActionConfirm {
  title: string;
  message: string;
  confirmLabel?: string;
  cancelLabel?: string;
  variant?: "default" | "danger";
}

type ActionOnSuccess =
  | { navigate: string }
  | { set: Record<string, unknown> }
  | { action: string };

type ActionOnError = { set: Record<string, unknown> } | { action: string };

interface ResolvedAction {
  name: string;
  params: Record<string, unknown>;
  confirm?: ActionConfirm;
  onSuccess?: ActionOnSuccess;
  onError?: ActionOnError;
}

function resolveAction(action: Action, dataModel: DataModel): ResolvedAction;

function executeAction(ctx: ActionExecutionContext): Promise<void>;

function interpolateString(template: string, dataModel: DataModel): string;
```

[Action System](./actions.md)

### Validation

Run validation checks with built-in and custom validation functions.

```typescript { .api }
interface ValidationCheck {
  fn: string;
  args?: Record<string, DynamicValue>;
  message: string;
}

interface ValidationConfig {
  checks?: ValidationCheck[];
  validateOn?: "change" | "blur" | "submit";
  enabled?: LogicExpression;
}

interface ValidationResult {
  valid: boolean;
  errors: string[];
  checks: ValidationCheckResult[];
}

function runValidation(
  config: ValidationConfig,
  ctx: ValidationContext & { authState?: { isSignedIn: boolean } }
): ValidationResult;

function runValidationCheck(
  check: ValidationCheck,
  ctx: ValidationContext
): ValidationCheckResult;

const builtInValidationFunctions: Record<string, ValidationFunction>;
```

[Validation System](./validation.md)

## Types

### Data Model

```typescript { .api }
type DataModel = Record<string, unknown>;
```

### Auth State

```typescript { .api }
interface AuthState {
  isSignedIn: boolean;
  user?: Record<string, unknown>;
}
```

### Validation Mode

```typescript { .api }
type ValidationMode = "strict" | "warn" | "ignore";
```

### JSON Patch

```typescript { .api }
type PatchOp = "add" | "remove" | "replace" | "set";

interface JsonPatch {
  op: PatchOp;
  path: string;
  value?: unknown;
}
```

### Component Schema

```typescript { .api }
type ComponentSchema = z.ZodType<Record<string, unknown>>;
```
