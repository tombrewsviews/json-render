# Validation System

Field validation system with built-in validation functions, custom validators, conditional validation, and flexible timing control.

## Capabilities

### Run Validation

Run all validation checks for a field against a value.

```typescript { .api }
/**
 * Run all validation checks for a field
 * @param config - Validation configuration with checks and settings
 * @param ctx - Context with value, data model, and optional custom functions
 * @returns Validation result with valid flag, errors, and individual check results
 */
function runValidation(
  config: ValidationConfig,
  ctx: ValidationContext & { authState?: { isSignedIn: boolean } }
): ValidationResult;
```

**Usage Examples:**

```typescript
import { runValidation, check } from "@json-render/core";

// Basic validation
const result1 = runValidation(
  {
    checks: [check.required("Email is required"), check.email("Invalid email")],
  },
  {
    value: "user@example.com",
    dataModel: {},
  }
);
// Result: { valid: true, errors: [], checks: [...] }

// Validation with dynamic args
const result2 = runValidation(
  {
    checks: [
      check.required(),
      check.minLength(8, "Password must be at least 8 characters"),
      check.maxLength(128),
    ],
  },
  {
    value: "mypassword123",
    dataModel: {},
  }
);
// Result: { valid: true, errors: [], checks: [...] }

// Conditional validation
const result3 = runValidation(
  {
    checks: [check.required()],
    enabled: { path: "/form/requireEmail" },
  },
  {
    value: "",
    dataModel: { form: { requireEmail: false } },
  }
);
// Result: { valid: true, errors: [], checks: [] } (validation skipped)
```

### Run Validation Check

Run a single validation check.

```typescript { .api }
/**
 * Run a single validation check
 * @param check - The validation check to run
 * @param ctx - Context with value, data model, and optional custom functions
 * @returns Check result with fn name, valid flag, and message
 */
function runValidationCheck(
  check: ValidationCheck,
  ctx: ValidationContext
): ValidationCheckResult;
```

**Usage Examples:**

```typescript
import { runValidationCheck } from "@json-render/core";

// Run required check
const result1 = runValidationCheck(
  { fn: "required", message: "This field is required" },
  { value: "hello", dataModel: {} }
);
// Result: { fn: "required", valid: true, message: "This field is required" }

// Run check with args
const result2 = runValidationCheck(
  {
    fn: "minLength",
    args: { min: 5 },
    message: "Must be at least 5 characters",
  },
  { value: "hi", dataModel: {} }
);
// Result: { fn: "minLength", valid: false, message: "Must be at least 5 characters" }

// Run check with dynamic args
const result3 = runValidationCheck(
  {
    fn: "matches",
    args: { other: { path: "/form/password" } },
    message: "Passwords must match",
  },
  {
    value: "abc123",
    dataModel: { form: { password: "abc123" } },
  }
);
// Result: { fn: "matches", valid: true, message: "Passwords must match" }
```

### Validation Check Helpers

Helper functions for creating validation checks.

```typescript { .api }
/**
 * Helper object to create validation checks
 */
const check: {
  required: (message?: string) => ValidationCheck;
  email: (message?: string) => ValidationCheck;
  minLength: (min: number, message?: string) => ValidationCheck;
  maxLength: (max: number, message?: string) => ValidationCheck;
  pattern: (pattern: string, message?: string) => ValidationCheck;
  min: (min: number, message?: string) => ValidationCheck;
  max: (max: number, message?: string) => ValidationCheck;
  url: (message?: string) => ValidationCheck;
  matches: (otherPath: string, message?: string) => ValidationCheck;
};
```

**Usage Examples:**

```typescript
import { check } from "@json-render/core";

// Required check
const check1 = check.required("Email is required");

// Email check
const check2 = check.email("Please enter a valid email");

// Length checks
const check3 = check.minLength(8, "Password must be at least 8 characters");
const check4 = check.maxLength(100, "Input too long");

// Pattern check
const check5 = check.pattern("^[A-Z]", "Must start with uppercase letter");

// Numeric checks
const check6 = check.min(18, "Must be at least 18");
const check7 = check.max(120, "Must be at most 120");

// URL check
const check8 = check.url("Please enter a valid URL");

// Matches check (for password confirmation)
const check9 = check.matches("/form/password", "Passwords must match");
```

### Built-in Validation Functions

Object containing all built-in validation functions.

```typescript { .api }
/**
 * Built-in validation functions
 */
const builtInValidationFunctions: Record<string, ValidationFunction>;
```

The built-in validation functions include:

- **required** - Check if value is not null, undefined, or empty string
- **email** - Check if value is a valid email address
- **minLength** - Check minimum string length (requires `args.min`)
- **maxLength** - Check maximum string length (requires `args.max`)
- **pattern** - Check if string matches regex pattern (requires `args.pattern`)
- **min** - Check minimum numeric value (requires `args.min`)
- **max** - Check maximum numeric value (requires `args.max`)
- **numeric** - Check if value is a number
- **url** - Check if value is a valid URL
- **matches** - Check if value matches another field (requires `args.other`)

**Usage Examples:**

```typescript
import { builtInValidationFunctions } from "@json-render/core";

// Use built-in functions directly
const isRequired = builtInValidationFunctions.required("hello");
// Result: true

const isEmail = builtInValidationFunctions.email("user@example.com");
// Result: true

const meetsMinLength = builtInValidationFunctions.minLength("hi", { min: 5 });
// Result: false

const isNumeric = builtInValidationFunctions.numeric("123");
// Result: true

const isValidUrl = builtInValidationFunctions.url("https://example.com");
// Result: true
```

### Validation Types

Validation check definition with function name, args, and error message.

```typescript { .api }
/**
 * Validation check definition
 */
interface ValidationCheck {
  /** Function name (built-in or from catalog) */
  fn: string;
  /** Additional arguments for the function */
  args?: Record<string, DynamicValue>;
  /** Error message to display if check fails */
  message: string;
}
```

**Usage Examples:**

```typescript
import { type ValidationCheck } from "@json-render/core";

// Basic check
const check1: ValidationCheck = {
  fn: "required",
  message: "This field is required",
};

// Check with args
const check2: ValidationCheck = {
  fn: "minLength",
  args: { min: 8 },
  message: "Must be at least 8 characters",
};

// Check with dynamic args
const check3: ValidationCheck = {
  fn: "min",
  args: { min: { path: "/settings/minAge" } },
  message: "Must meet minimum age requirement",
};
```

### Validation Config

Configuration for field validation.

```typescript { .api }
/**
 * Validation configuration for a field
 */
interface ValidationConfig {
  /** Array of checks to run */
  checks?: ValidationCheck[];
  /** When to run validation */
  validateOn?: "change" | "blur" | "submit";
  /** Condition for when validation is enabled */
  enabled?: LogicExpression;
}
```

**Usage Examples:**

```typescript
import { type ValidationConfig } from "@json-render/core";

// Basic config
const config1: ValidationConfig = {
  checks: [
    { fn: "required", message: "Required" },
    { fn: "email", message: "Invalid email" },
  ],
};

// Config with timing
const config2: ValidationConfig = {
  checks: [{ fn: "required", message: "Required" }],
  validateOn: "blur",
};

// Config with conditional validation
const config3: ValidationConfig = {
  checks: [{ fn: "required", message: "Required" }],
  enabled: { path: "/form/requireField" },
};

// Full config
const config4: ValidationConfig = {
  checks: [
    { fn: "required", message: "Email is required" },
    { fn: "email", message: "Invalid email format" },
    { fn: "maxLength", args: { max: 100 }, message: "Email too long" },
  ],
  validateOn: "change",
  enabled: {
    and: [{ auth: "signedIn" }, { path: "/form/emailRequired" }],
  },
};
```

### Validation Result

Result of running validation checks.

```typescript { .api }
/**
 * Full validation result for a field
 */
interface ValidationResult {
  valid: boolean;
  errors: string[];
  checks: ValidationCheckResult[];
}
```

**Usage Examples:**

```typescript
import { runValidation, check } from "@json-render/core";

const result = runValidation(
  {
    checks: [check.required(), check.email()],
  },
  {
    value: "invalid-email",
    dataModel: {},
  }
);

console.log(result.valid); // false
console.log(result.errors); // ["Invalid email address"]
console.log(result.checks); // [{ fn: "required", valid: true, ... }, { fn: "email", valid: false, ... }]
```

### Validation Check Result

Result of a single validation check.

```typescript { .api }
/**
 * Validation result for a single check
 */
interface ValidationCheckResult {
  fn: string;
  valid: boolean;
  message: string;
}
```

### Validation Function

Function type for validation functions.

```typescript { .api }
/**
 * Validation function signature
 */
type ValidationFunction = (
  value: unknown,
  args?: Record<string, unknown>
) => boolean;

/**
 * Validation function definition in catalog
 */
interface ValidationFunctionDefinition {
  /** The validation function */
  validate: ValidationFunction;
  /** Description for AI */
  description?: string;
}
```

**Usage Examples:**

```typescript
import { type ValidationFunction } from "@json-render/core";

// Simple validation function
const notEmpty: ValidationFunction = (value) => {
  return typeof value === "string" && value.trim().length > 0;
};

// Validation function with args
const hasMinWords: ValidationFunction = (value, args) => {
  if (typeof value !== "string") return false;
  const minWords = args?.minWords as number;
  if (typeof minWords !== "number") return false;
  const wordCount = value.trim().split(/\s+/).length;
  return wordCount >= minWords;
};

// Validation function for custom business logic
const isValidProductCode: ValidationFunction = (value) => {
  if (typeof value !== "string") return false;
  return /^PROD-\d{4}-[A-Z]{2}$/.test(value);
};
```

### Validation Context

Context for running validation.

```typescript { .api }
/**
 * Context for running validation
 */
interface ValidationContext {
  /** Current value to validate */
  value: unknown;
  /** Full data model for resolving paths */
  dataModel: DataModel;
  /** Custom validation functions from catalog */
  customFunctions?: Record<string, ValidationFunction>;
}
```

**Usage Examples:**

```typescript
import { runValidation, check, type ValidationFunction } from "@json-render/core";

// Custom validation function
const phoneNumber: ValidationFunction = (value) => {
  return typeof value === "string" && /^\d{3}-\d{3}-\d{4}$/.test(value);
};

const ctx = {
  value: "123-456-7890",
  dataModel: { settings: { requirePhone: true } },
  customFunctions: { phoneNumber },
};

const result = runValidation(
  {
    checks: [
      { fn: "required", message: "Phone is required" },
      { fn: "phoneNumber", message: "Invalid phone format" },
    ],
  },
  ctx
);
```

## Zod Schemas

The library provides Zod schemas for runtime validation.

```typescript { .api }
/**
 * Schema for validation check
 */
const ValidationCheckSchema: z.ZodType<ValidationCheck>;

/**
 * Schema for validation config
 */
const ValidationConfigSchema: z.ZodType<ValidationConfig>;
```

## Common Patterns

### Email Validation

```typescript
import { runValidation, check } from "@json-render/core";

const config = {
  checks: [
    check.required("Email is required"),
    check.email("Please enter a valid email address"),
    check.maxLength(100, "Email is too long"),
  ],
};

const result = runValidation(config, {
  value: "user@example.com",
  dataModel: {},
});
```

### Password Validation

```typescript
import { check } from "@json-render/core";

const passwordConfig = {
  checks: [
    check.required("Password is required"),
    check.minLength(8, "Password must be at least 8 characters"),
    check.maxLength(128, "Password is too long"),
    check.pattern(
      "(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)",
      "Password must contain uppercase, lowercase, and number"
    ),
  ],
};

const confirmConfig = {
  checks: [
    check.required("Please confirm your password"),
    check.matches("/form/password", "Passwords must match"),
  ],
};
```

### Conditional Validation

```typescript
import { runValidation, check } from "@json-render/core";

const config = {
  checks: [check.required("This field is required")],
  enabled: {
    and: [{ auth: "signedIn" }, { path: "/settings/strictMode" }],
  },
};

const result = runValidation(config, {
  value: "",
  dataModel: { settings: { strictMode: false } },
  authState: { isSignedIn: true },
});
// Validation skipped because strictMode is false
```

### Custom Validation

```typescript
import { runValidation, type ValidationFunction } from "@json-render/core";

const customValidators: Record<string, ValidationFunction> = {
  isEven: (value) => typeof value === "number" && value % 2 === 0,
  isPrime: (value) => {
    if (typeof value !== "number" || value < 2) return false;
    for (let i = 2; i <= Math.sqrt(value); i++) {
      if (value % i === 0) return false;
    }
    return true;
  },
};

const config = {
  checks: [
    { fn: "required", message: "Number is required" },
    { fn: "isEven", message: "Must be even" },
    { fn: "min", args: { min: 10 }, message: "Must be at least 10" },
  ],
};

const result = runValidation(config, {
  value: 12,
  dataModel: {},
  customFunctions: customValidators,
});
```

### Validation Timing

```typescript
import { type ValidationConfig } from "@json-render/core";

// Validate on every change
const onChangeConfig: ValidationConfig = {
  checks: [{ fn: "required", message: "Required" }],
  validateOn: "change",
};

// Validate on blur (when field loses focus)
const onBlurConfig: ValidationConfig = {
  checks: [{ fn: "email", message: "Invalid email" }],
  validateOn: "blur",
};

// Validate on form submit
const onSubmitConfig: ValidationConfig = {
  checks: [{ fn: "required", message: "Required" }],
  validateOn: "submit",
};
```
