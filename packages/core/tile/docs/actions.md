# Action System

Rich action system with dynamic parameter resolution, confirmation dialogs, string interpolation, and success/error callbacks.

## Capabilities

### Resolve Action

Resolve all dynamic values in an action against a data model.

```typescript { .api }
/**
 * Resolve all dynamic values in an action
 * @param action - The action with potential dynamic values
 * @param dataModel - The data model to resolve against
 * @returns Resolved action with all dynamic values replaced
 */
function resolveAction(action: Action, dataModel: DataModel): ResolvedAction;
```

**Usage Examples:**

```typescript
import { resolveAction } from "@json-render/core";

const dataModel = {
  selected: { id: "user-123", name: "Alice" },
  form: { amount: 100 },
};

// Action with dynamic params
const action = {
  name: "refund",
  params: {
    userId: { path: "/selected/id" },
    amount: { path: "/form/amount" },
    reason: "Customer request",
  },
  confirm: {
    title: "Confirm Refund",
    message: "Refund $${/form/amount} to ${/selected/name}?",
  },
};

const resolved = resolveAction(action, dataModel);
// Result:
// {
//   name: "refund",
//   params: { userId: "user-123", amount: 100, reason: "Customer request" },
//   confirm: {
//     title: "Confirm Refund",
//     message: "Refund $100 to Alice?"
//   }
// }
```

### Execute Action

Execute an action with callbacks for success and error handling.

```typescript { .api }
/**
 * Execute an action with all callbacks
 * @param ctx - Action execution context with handler and callbacks
 * @returns Promise that resolves when action and callbacks complete
 */
function executeAction(ctx: ActionExecutionContext): Promise<void>;
```

**Usage Examples:**

```typescript
import { executeAction, resolveAction } from "@json-render/core";

const dataModel = {
  payment: { id: "pay-123" },
};

const action = {
  name: "processPayment",
  params: { paymentId: { path: "/payment/id" } },
  onSuccess: { navigate: "/success" },
  onError: { set: { "/ui/error": "$error.message" } },
};

const resolved = resolveAction(action, dataModel);

// Execute with context
await executeAction({
  action: resolved,
  handler: async (params) => {
    // Process payment
    console.log("Processing payment:", params.paymentId);
    return { success: true };
  },
  setData: (path, value) => {
    console.log("Setting data:", path, value);
  },
  navigate: (path) => {
    console.log("Navigating to:", path);
  },
});
```

### Interpolate String

Interpolate `${path}` expressions in strings with data from the model.

```typescript { .api }
/**
 * Interpolate ${path} expressions in a string
 * @param template - Template string with ${path} expressions
 * @param dataModel - The data model to resolve paths against
 * @returns Interpolated string with resolved values
 */
function interpolateString(template: string, dataModel: DataModel): string;
```

**Usage Examples:**

```typescript
import { interpolateString } from "@json-render/core";

const dataModel = {
  user: { name: "Alice", email: "alice@example.com" },
  order: { id: "ORD-123", total: 99.99 },
};

// Simple interpolation
const greeting = interpolateString("Hello, ${/user/name}!", dataModel);
// Result: "Hello, Alice!"

// Multiple interpolations
const message = interpolateString(
  "Order ${/order/id} for ${/user/email} totals $${/order/total}",
  dataModel
);
// Result: "Order ORD-123 for alice@example.com totals $99.99"

// Missing paths become empty strings
const withMissing = interpolateString(
  "User ${/user/name}, status: ${/user/status}",
  dataModel
);
// Result: "User Alice, status: "
```

### Action Helpers

Helper functions for creating action objects.

```typescript { .api }
/**
 * Helper object to create actions
 */
const action: {
  /** Create a simple action */
  simple: (name: string, params?: Record<string, DynamicValue>) => Action;
  /** Create an action with confirmation */
  withConfirm: (
    name: string,
    confirm: ActionConfirm,
    params?: Record<string, DynamicValue>
  ) => Action;
  /** Create an action with success handler */
  withSuccess: (
    name: string,
    onSuccess: ActionOnSuccess,
    params?: Record<string, DynamicValue>
  ) => Action;
};
```

**Usage Examples:**

```typescript
import { action } from "@json-render/core";

// Simple action
const refresh = action.simple("refresh");

// Action with params
const exportData = action.simple("export", {
  format: "csv",
  includeHeaders: true,
});

// Action with confirmation
const deleteUser = action.withConfirm(
  "deleteUser",
  {
    title: "Delete User",
    message: "Are you sure you want to delete this user?",
    variant: "danger",
  },
  { userId: { path: "/selected/id" } }
);

// Action with success handler
const saveForm = action.withSuccess(
  "saveForm",
  { navigate: "/dashboard" },
  { formData: { path: "/form" } }
);
```

### Action Types

Action definition with dynamic parameters and callbacks.

```typescript { .api }
/**
 * Rich action definition
 */
interface Action {
  /** Action name (must be in catalog) */
  name: string;
  /** Parameters to pass to the action handler */
  params?: Record<string, DynamicValue>;
  /** Confirmation dialog before execution */
  confirm?: ActionConfirm;
  /** Handler after successful execution */
  onSuccess?: ActionOnSuccess;
  /** Handler after failed execution */
  onError?: ActionOnError;
}
```

**Usage Examples:**

```typescript
import { type Action } from "@json-render/core";

// Basic action
const action1: Action = {
  name: "submit",
};

// Action with params
const action2: Action = {
  name: "updateUser",
  params: {
    userId: { path: "/user/id" },
    name: { path: "/form/name" },
    email: { path: "/form/email" },
  },
};

// Action with confirmation
const action3: Action = {
  name: "deleteItem",
  params: { itemId: { path: "/selected/id" } },
  confirm: {
    title: "Confirm Delete",
    message: "This action cannot be undone.",
    variant: "danger",
    confirmLabel: "Delete",
    cancelLabel: "Cancel",
  },
};

// Action with callbacks
const action4: Action = {
  name: "processPayment",
  params: { amount: { path: "/cart/total" } },
  confirm: {
    title: "Confirm Payment",
    message: "Process payment of $${/cart/total}?",
  },
  onSuccess: { navigate: "/success" },
  onError: { set: { "/ui/errorMessage": "$error.message" } },
};
```

### Action Confirm

Confirmation dialog configuration.

```typescript { .api }
/**
 * Confirmation dialog configuration
 */
interface ActionConfirm {
  title: string;
  message: string;
  confirmLabel?: string;
  cancelLabel?: string;
  variant?: "default" | "danger";
}
```

**Usage Examples:**

```typescript
import { type ActionConfirm } from "@json-render/core";

// Basic confirmation
const confirm1: ActionConfirm = {
  title: "Confirm",
  message: "Are you sure?",
};

// Full confirmation
const confirm2: ActionConfirm = {
  title: "Delete Account",
  message: "This will permanently delete your account and all data.",
  confirmLabel: "Yes, Delete",
  cancelLabel: "No, Keep It",
  variant: "danger",
};

// Confirmation with interpolation
const confirm3: ActionConfirm = {
  title: "Refund Payment",
  message: "Refund $${/payment/amount} to ${/customer/email}?",
  confirmLabel: "Process Refund",
  variant: "default",
};
```

### Action Success Handler

Success handler types for post-execution actions.

```typescript { .api }
/**
 * Action success handler
 */
type ActionOnSuccess =
  | { navigate: string }
  | { set: Record<string, unknown> }
  | { action: string };
```

**Usage Examples:**

```typescript
import { type ActionOnSuccess } from "@json-render/core";

// Navigate on success
const success1: ActionOnSuccess = { navigate: "/dashboard" };

// Set data on success
const success2: ActionOnSuccess = {
  set: {
    "/ui/successMessage": "Payment processed successfully",
    "/ui/showConfetti": true,
  },
};

// Chain another action
const success3: ActionOnSuccess = { action: "refreshData" };
```

### Action Error Handler

Error handler types for failure scenarios.

```typescript { .api }
/**
 * Action error handler
 */
type ActionOnError = { set: Record<string, unknown> } | { action: string };
```

**Usage Examples:**

```typescript
import { type ActionOnError } from "@json-render/core";

// Set error message
const error1: ActionOnError = {
  set: {
    "/ui/errorMessage": "$error.message",
    "/ui/showError": true,
  },
};

// Chain error handler action
const error2: ActionOnError = { action: "handleError" };
```

### Action Handler

Function type for action handlers.

```typescript { .api }
/**
 * Action handler function signature
 */
type ActionHandler<TParams = Record<string, unknown>, TResult = unknown> = (
  params: TParams
) => Promise<TResult> | TResult;
```

**Usage Examples:**

```typescript
import { type ActionHandler } from "@json-render/core";

// Async handler
const submitHandler: ActionHandler = async (params) => {
  const response = await fetch("/api/submit", {
    method: "POST",
    body: JSON.stringify(params),
  });
  return response.json();
};

// Sync handler
const logHandler: ActionHandler = (params) => {
  console.log("Action params:", params);
  return { logged: true };
};

// Typed handler
interface PaymentParams {
  amount: number;
  currency: string;
}
const paymentHandler: ActionHandler<PaymentParams> = async (params) => {
  // params is typed as PaymentParams
  return { transactionId: "txn-123" };
};
```

### Resolved Action

Action with all dynamic values resolved to literals.

```typescript { .api }
/**
 * Resolved action with all dynamic values resolved
 */
interface ResolvedAction {
  name: string;
  params: Record<string, unknown>;
  confirm?: ActionConfirm;
  onSuccess?: ActionOnSuccess;
  onError?: ActionOnError;
}
```

### Action Execution Context

Context for executing actions with callbacks.

```typescript { .api }
/**
 * Context for action execution
 */
interface ActionExecutionContext {
  /** The resolved action */
  action: ResolvedAction;
  /** The action handler from the host */
  handler: ActionHandler;
  /** Function to update data model */
  setData: (path: string, value: unknown) => void;
  /** Function to navigate */
  navigate?: (path: string) => void;
  /** Function to execute another action */
  executeAction?: (name: string) => Promise<void>;
}
```

**Usage Examples:**

```typescript
import { type ActionExecutionContext } from "@json-render/core";

const ctx: ActionExecutionContext = {
  action: {
    name: "saveData",
    params: { data: { foo: "bar" } },
    onSuccess: { navigate: "/success" },
  },
  handler: async (params) => {
    // Save data
    return { saved: true };
  },
  setData: (path, value) => {
    // Update data model
    console.log(`Setting ${path} to`, value);
  },
  navigate: (path) => {
    // Navigate to path
    window.location.href = path;
  },
  executeAction: async (name) => {
    // Execute another action
    console.log(`Executing action: ${name}`);
  },
};
```

## Zod Schemas

The library provides Zod schemas for runtime validation of actions.

```typescript { .api }
/**
 * Full action schema
 */
const ActionSchema: z.ZodType<Action>;

/**
 * Schema for action confirmation
 */
const ActionConfirmSchema: z.ZodType<ActionConfirm>;

/**
 * Schema for success handlers
 */
const ActionOnSuccessSchema: z.ZodType<ActionOnSuccess>;

/**
 * Schema for error handlers
 */
const ActionOnErrorSchema: z.ZodType<ActionOnError>;
```

## Common Patterns

### Action with Dynamic Parameters

```typescript
import { resolveAction } from "@json-render/core";

const action = {
  name: "updateProfile",
  params: {
    userId: { path: "/user/id" },
    name: { path: "/form/name" },
    email: { path: "/form/email" },
  },
};

const dataModel = {
  user: { id: "123" },
  form: { name: "Alice", email: "alice@example.com" },
};

const resolved = resolveAction(action, dataModel);
// params: { userId: "123", name: "Alice", email: "alice@example.com" }
```

### Action with Confirmation and Callbacks

```typescript
const action = {
  name: "deleteAccount",
  params: { userId: { path: "/user/id" } },
  confirm: {
    title: "Delete Account",
    message: "Are you sure? This cannot be undone.",
    variant: "danger" as const,
  },
  onSuccess: { navigate: "/goodbye" },
  onError: {
    set: {
      "/ui/error": "$error.message",
      "/ui/showError": true,
    },
  },
};
```

### String Interpolation in Messages

```typescript
import { interpolateString } from "@json-render/core";

const dataModel = {
  user: { name: "Bob" },
  order: { total: 149.99 },
};

const message = interpolateString(
  "Thank you, ${/user/name}! Your order total is $${/order/total}.",
  dataModel
);
// Result: "Thank you, Bob! Your order total is $149.99."
```
