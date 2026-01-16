# Dashboard Catalog

The dashboard catalog defines all components with Zod schemas, providing type safety and guardrails for AI-generated UI components. It's the source of truth for what components are available and how they can be configured.

## Capabilities

### Dashboard Catalog

Complete catalog definition with component schemas, actions, and validation rules.

```typescript { .api }
import { dashboardCatalog, componentList } from "./lib/catalog";

// Catalog structure
interface Catalog {
  name: string; // 'dashboard'
  components: Record<string, ComponentDefinition>;
  actions: Record<string, ActionDefinition>;
  validation: "strict" | "loose"; // 'strict'
}

interface ComponentDefinition {
  props: z.ZodSchema; // Zod schema for props
  hasChildren?: boolean; // Whether component accepts children
  description: string; // AI-facing description
}

interface ActionDefinition {
  description: string; // What the action does
}
```

**Usage Example:**

```typescript
import { createCatalog } from "@json-render/core";
import { dashboardCatalog } from "./lib/catalog";

// The catalog is used to validate UI definitions
// and provide guardrails for AI generation
console.log(dashboardCatalog.name); // 'dashboard'
console.log(dashboardCatalog.validation); // 'strict'
```

### Component List

Array of all available component names in the catalog.

```typescript { .api }
import { componentList } from "./lib/catalog";

// Component list type
const componentList: string[];

// Contains 16 component names (TextField is NOT included in catalog):
// Alert, Badge, Button, Card, Chart, DatePicker, Divider, Empty,
// Grid, Heading, List, Metric, Select, Stack, Table, Text
```

**Usage Example:**

```typescript
import { componentList } from "./lib/catalog";

console.log(componentList.length); // 16
console.log(componentList.includes("Button")); // true
console.log(componentList.includes("TextField")); // false - not in catalog
```

### Actions Registry

The catalog defines 4 available actions that can be triggered by Button components:

```typescript { .api }
// Actions defined in catalog
interface Actions {
  export_report: {
    description: "Export the current dashboard to PDF";
  };
  refresh_data: {
    description: "Refresh all metrics and charts";
  };
  view_details: {
    description: "View detailed information";
  };
  apply_filter: {
    description: "Apply the current filter settings";
  };
}
```

**Usage Example:**

```typescript
// Button component triggering an action (action is a string, not an object)
<Button label="Export Report" action="export_report" />
<Button label="Refresh" action="refresh_data" />
<Button label="View Details" action="view_details" />
<Button label="Apply Filters" action="apply_filter" />
```

### Component Schemas

Each component in the catalog has a Zod schema defining its props. All optional fields use `.nullable()` instead of `.optional()` for OpenAI structured output compatibility.

```typescript { .api }
import { z } from "zod";
import { createCatalog } from "@json-render/core";

// Example: Button component schema
const ButtonSchema = z.object({
  label: z.string(),
  variant: z.enum(["primary", "secondary", "danger", "ghost"]).nullable(),
  size: z.enum(["sm", "md", "lg"]).nullable(),
  action: z.string(), // Action name as string
  disabled: z.boolean().nullable(),
});

// Example: Metric component schema
const MetricSchema = z.object({
  label: z.string(),
  valuePath: z.string(),
  format: z.enum(["number", "currency", "percent"]).nullable(),
  trend: z.enum(["up", "down", "neutral"]).nullable(),
  trendValue: z.string().nullable(),
});

// Example: Card component schema (with children)
const CardSchema = z.object({
  title: z.string().nullable(),
  description: z.string().nullable(),
  padding: z.enum(["sm", "md", "lg"]).nullable(),
});
// Card has hasChildren: true
```

**Usage Example:**

```typescript
import { createCatalog } from "@json-render/core";
import { z } from "zod";

// Creating a catalog with component definitions
const catalog = createCatalog({
  name: "dashboard",
  components: {
    Button: {
      props: z.object({
        label: z.string(),
        variant: z.enum(["primary", "secondary", "danger", "ghost"]).nullable(),
        size: z.enum(["sm", "md", "lg"]).nullable(),
        action: z.string(), // Action name as string
        disabled: z.boolean().nullable(),
      }),
      description:
        "A clickable button that triggers actions. Use for user interactions like export, refresh, or navigation.",
    },
    Card: {
      props: z.object({
        title: z.string().nullable(),
        description: z.string().nullable(),
        padding: z.enum(["sm", "md", "lg"]).nullable(),
      }),
      hasChildren: true,
      description:
        "A container card with optional title and description. Use to group related content.",
    },
  },
  actions: {
    export_report: {
      description: "Export the current dashboard to PDF",
    },
  },
  validation: "strict",
});
```

### Catalog Usage Pattern

The catalog is typically used with the AI SDK to generate UI components:

```typescript { .api }
import { generateObject } from "ai";
import { dashboardCatalog } from "./lib/catalog";

// The catalog provides the schema for AI generation
const result = await generateObject({
  model: openai("gpt-4"),
  schema: dashboardCatalog.schema, // Generated from Zod schemas
  prompt: "Create a dashboard showing revenue metrics",
});
```

## Schema Patterns

### Nullable vs Optional

All optional props use `.nullable()` instead of `.optional()`:

```typescript { .api }
// Correct for OpenAI structured output
z.object({
  title: z.string().nullable(), // Can be null
  description: z.string().nullable(), // Can be null
});

// Not used (optional would require default values)
z.object({
  title: z.string().optional(),
  description: z.string().optional(),
});
```

### Enum Fields

Constrained values use Zod enums:

```typescript { .api }
// Button variants
variant: z.enum(["primary", "secondary", "danger", "ghost"]).nullable();

// Chart types
type: z.enum(["bar", "line", "pie", "area"]);

// Text variant (for text style)
variant: z.enum(["body", "caption", "label"]).nullable();

// Text color (for text color)
color: z.enum(["default", "muted", "success", "warning", "danger"]).nullable();
```

### Nested Objects

Complex props use nested Zod objects:

```typescript { .api }
// Table columns
columns: z.array(
  z.object({
    key: z.string(),
    label: z.string(),
    format: z.enum(["text", "currency", "date", "badge"]).nullable(),
  })
);

// Select options
options: z.array(
  z.object({
    value: z.string(),
    label: z.string(),
  })
);

// TextField validation checks
checks: z
  .array(
    z.object({
      fn: z.string(),
      message: z.string(),
    })
  )
  .nullable();
```

### Data Binding Paths

Props that reference data use string paths (JSON pointers):

```typescript { .api }
// Single value binding
valuePath: z.string(); // e.g., "/analytics/revenue"

// Array data binding
dataPath: z.string(); // e.g., "/analytics/salesByRegion"
```

## Catalog File Location

The catalog is defined in `lib/catalog.ts`:

```typescript { .api }
// File: lib/catalog.ts
import { createCatalog } from "@json-render/core";
import { z } from "zod";

export const dashboardCatalog = createCatalog({
  /* catalog definition */
});

export const componentList = dashboardCatalog.componentNames as string[];
// Contains 16 components: Alert, Badge, Button, Card, Chart, DatePicker,
// Divider, Empty, Grid, Heading, List, Metric, Select, Stack, Table, Text
// Note: TextField is in the component registry but NOT in this catalog
```

## Validation Mode

The catalog uses `validation: 'strict'` mode, which means:
- All props must match their Zod schemas exactly
- Unknown props are rejected
- Type mismatches cause validation errors
- AI-generated components are validated before rendering
