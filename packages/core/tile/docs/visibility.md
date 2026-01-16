# Visibility System

Conditional rendering system for UI elements based on data paths, authentication state, and complex logic expressions.

## Capabilities

### Evaluate Visibility

Evaluate a visibility condition against data and auth state.

```typescript { .api }
/**
 * Evaluate a visibility condition
 * @param condition - The visibility condition (undefined means always visible)
 * @param ctx - Context with data model and optional auth state
 * @returns true if element should be visible, false otherwise
 */
function evaluateVisibility(
  condition: VisibilityCondition | undefined,
  ctx: VisibilityContext
): boolean;
```

**Usage Examples:**

```typescript
import { evaluateVisibility } from "@json-render/core";

const dataModel = {
  user: { role: "admin", notifications: 5 },
  form: { hasErrors: true },
};

const authState = { isSignedIn: true };

// Simple boolean
const alwaysVisible = evaluateVisibility(true, { dataModel });
// Result: true

// Path-based visibility
const showNotifications = evaluateVisibility(
  { path: "/user/notifications" },
  { dataModel }
);
// Result: true (5 is truthy)

// Auth-based visibility
const showAdminPanel = evaluateVisibility(
  { auth: "signedIn" },
  { dataModel, authState }
);
// Result: true

// Complex logic
const showWarning = evaluateVisibility(
  {
    and: [
      { path: "/form/hasErrors" },
      { auth: "signedIn" },
      { gt: [{ path: "/user/notifications" }, 3] },
    ],
  },
  { dataModel, authState }
);
// Result: true (all conditions met)
```

### Evaluate Logic Expression

Evaluate a logic expression against data and auth state.

```typescript { .api }
/**
 * Evaluate a logic expression against data and auth state
 * @param expr - The logic expression to evaluate
 * @param ctx - Context with data model and optional auth state
 * @returns boolean result of the expression
 */
function evaluateLogicExpression(
  expr: LogicExpression,
  ctx: VisibilityContext
): boolean;
```

**Usage Examples:**

```typescript
import { evaluateLogicExpression } from "@json-render/core";

const dataModel = {
  settings: { darkMode: true, notifications: true },
  user: { age: 25, premium: false },
};

// AND expression
const result1 = evaluateLogicExpression(
  {
    and: [
      { path: "/settings/darkMode" },
      { path: "/settings/notifications" },
    ],
  },
  { dataModel }
);
// Result: true

// OR expression
const result2 = evaluateLogicExpression(
  {
    or: [{ path: "/user/premium" }, { gt: [{ path: "/user/age" }, 18] }],
  },
  { dataModel }
);
// Result: true (age > 18)

// NOT expression
const result3 = evaluateLogicExpression(
  { not: { path: "/user/premium" } },
  { dataModel }
);
// Result: true (not premium)

// Comparison expressions
const result4 = evaluateLogicExpression(
  { eq: [{ path: "/user/age" }, 25] },
  { dataModel }
);
// Result: true

const result5 = evaluateLogicExpression(
  { gte: [{ path: "/user/age" }, 21] },
  { dataModel }
);
// Result: true
```

### Visibility Helpers

Helper functions for creating visibility conditions.

```typescript { .api }
/**
 * Helper object to create visibility conditions
 */
const visibility: {
  /** Always visible */
  always: true;
  /** Never visible */
  never: false;
  /** Visible when path is truthy */
  when: (path: string) => VisibilityCondition;
  /** Visible when signed in */
  signedIn: { auth: "signedIn" };
  /** Visible when signed out */
  signedOut: { auth: "signedOut" };
  /** AND multiple conditions */
  and: (...conditions: LogicExpression[]) => LogicExpression;
  /** OR multiple conditions */
  or: (...conditions: LogicExpression[]) => LogicExpression;
  /** NOT a condition */
  not: (condition: LogicExpression) => LogicExpression;
  /** Equality check */
  eq: (left: DynamicValue, right: DynamicValue) => LogicExpression;
  /** Not equal check */
  neq: (left: DynamicValue, right: DynamicValue) => LogicExpression;
  /** Greater than */
  gt: (
    left: DynamicValue<number>,
    right: DynamicValue<number>
  ) => LogicExpression;
  /** Greater than or equal */
  gte: (
    left: DynamicValue<number>,
    right: DynamicValue<number>
  ) => LogicExpression;
  /** Less than */
  lt: (
    left: DynamicValue<number>,
    right: DynamicValue<number>
  ) => LogicExpression;
  /** Less than or equal */
  lte: (
    left: DynamicValue<number>,
    right: DynamicValue<number>
  ) => LogicExpression;
};
```

**Usage Examples:**

```typescript
import { visibility, evaluateVisibility } from "@json-render/core";

const dataModel = {
  user: { role: "admin", age: 30 },
  settings: { notifications: true },
};

// Simple helpers
const element1 = {
  key: "always-visible",
  type: "Text",
  props: {},
  visible: visibility.always,
};

const element2 = {
  key: "when-notifications",
  type: "Banner",
  props: {},
  visible: visibility.when("/settings/notifications"),
};

const element3 = {
  key: "admin-only",
  type: "Panel",
  props: {},
  visible: visibility.signedIn,
};

// Composite conditions
const element4 = {
  key: "complex",
  type: "Section",
  props: {},
  visible: visibility.and(
    visibility.when("/settings/notifications"),
    visibility.gt({ path: "/user/age" }, 18),
    visibility.eq({ path: "/user/role" }, "admin")
  ),
};

// NOT condition
const element5 = {
  key: "guest-only",
  type: "Banner",
  props: {},
  visible: visibility.not(visibility.signedIn),
};

// OR condition
const element6 = {
  key: "special-access",
  type: "Feature",
  props: {},
  visible: visibility.or(
    visibility.eq({ path: "/user/role" }, "admin"),
    visibility.eq({ path: "/user/role" }, "moderator")
  ),
};
```

### Visibility Context

Context for evaluating visibility conditions.

```typescript { .api }
/**
 * Context for evaluating visibility
 */
interface VisibilityContext {
  dataModel: DataModel;
  authState?: AuthState;
}
```

### Visibility Condition Types

Union type for all visibility condition variants.

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

**Usage Examples:**

```typescript
import { type VisibilityCondition } from "@json-render/core";

// Boolean literal
const visible1: VisibilityCondition = true;
const visible2: VisibilityCondition = false;

// Path-based
const visible3: VisibilityCondition = { path: "/user/isActive" };

// Auth-based
const visible4: VisibilityCondition = { auth: "signedIn" };
const visible5: VisibilityCondition = { auth: "signedOut" };

// Logic expression
const visible6: VisibilityCondition = {
  and: [{ path: "/user/isActive" }, { auth: "signedIn" }],
};
```

### Logic Expression Types

Union type for all logic expression variants.

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

**Usage Examples:**

```typescript
import { type LogicExpression } from "@json-render/core";

// AND expression
const expr1: LogicExpression = {
  and: [{ path: "/user/active" }, { path: "/settings/enabled" }],
};

// OR expression
const expr2: LogicExpression = {
  or: [{ eq: [{ path: "/role" }, "admin"] }, { eq: [{ path: "/role" }, "moderator"] }],
};

// NOT expression
const expr3: LogicExpression = {
  not: { path: "/user/banned" },
};

// Comparison expressions
const expr4: LogicExpression = { eq: [{ path: "/status" }, "active"] };
const expr5: LogicExpression = { neq: [{ path: "/count" }, 0] };
const expr6: LogicExpression = { gt: [{ path: "/age" }, 18] };
const expr7: LogicExpression = { gte: [{ path: "/score" }, 100] };
const expr8: LogicExpression = { lt: [{ path: "/temperature" }, 32] };
const expr9: LogicExpression = { lte: [{ path: "/items" }, 10] };

// Nested expressions
const expr10: LogicExpression = {
  and: [
    { path: "/user/authenticated" },
    {
      or: [
        { eq: [{ path: "/user/role" }, "admin"] },
        {
          and: [
            { eq: [{ path: "/user/role" }, "user"] },
            { gte: [{ path: "/user/age" }, 18] },
          ],
        },
      ],
    },
  ],
};
```

## Zod Schemas

The library provides Zod schemas for runtime validation of visibility conditions.

```typescript { .api }
/**
 * Zod schema for visibility conditions
 */
const VisibilityConditionSchema: z.ZodType<VisibilityCondition>;

/**
 * Zod schema for logic expressions
 */
const LogicExpressionSchema: z.ZodType<LogicExpression>;
```

## Common Patterns

### Show/Hide Based on Data

```typescript
import { evaluateVisibility } from "@json-render/core";

const element = {
  key: "error-banner",
  type: "Alert",
  props: { message: "An error occurred" },
  visible: { path: "/form/hasError" },
};

const dataModel = { form: { hasError: true } };
const isVisible = evaluateVisibility(element.visible, { dataModel });
// Result: true
```

### Authenticated User Only

```typescript
const element = {
  key: "user-profile",
  type: "Profile",
  props: {},
  visible: { auth: "signedIn" },
};

const authState = { isSignedIn: true };
const isVisible = evaluateVisibility(element.visible, { dataModel: {}, authState });
// Result: true
```

### Complex Conditional Logic

```typescript
import { visibility } from "@json-render/core";

const element = {
  key: "premium-feature",
  type: "Feature",
  props: {},
  visible: visibility.and(
    visibility.signedIn,
    visibility.or(
      visibility.eq({ path: "/user/subscription" }, "premium"),
      visibility.gt({ path: "/user/credits" }, 100)
    ),
    visibility.not(visibility.eq({ path: "/user/banned" }, true))
  ),
};
```
