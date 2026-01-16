# Catalog System

Type-safe component catalog creation and management using Zod schemas. Catalogs define available components, actions, and validation functions, and provide schemas for validating AI-generated UI elements.

## Capabilities

### Create Catalog

Create a catalog with type-safe component definitions, actions, and validation functions.

```typescript { .api }
/**
 * Create a catalog with visibility, actions, and validation support
 * @param config - Catalog configuration
 * @returns Catalog instance with validation methods and schemas
 */
function createCatalog<TComponents, TActions, TFunctions>(
  config: CatalogConfig<TComponents, TActions, TFunctions>
): Catalog<TComponents, TActions, TFunctions>;
```

**Usage Examples:**

```typescript
import { createCatalog, ActionSchema } from "@json-render/core";
import { z } from "zod";

// Basic catalog
const catalog = createCatalog({
  name: "My App",
  components: {
    Button: {
      props: z.object({
        label: z.string(),
        variant: z.enum(["primary", "secondary"]).optional(),
      }),
      description: "A clickable button",
    },
    Card: {
      props: z.object({
        title: z.string(),
        description: z.string().nullable(),
      }),
      hasChildren: true,
      description: "A card container",
    },
  },
});

// Catalog with actions
const catalogWithActions = createCatalog({
  name: "Dashboard",
  components: {
    Button: {
      props: z.object({
        label: z.string(),
        action: ActionSchema,
      }),
      description: "Button with action",
    },
  },
  actions: {
    submit: {
      description: "Submit the form",
    },
    export: {
      params: z.object({
        format: z.enum(["csv", "pdf"]),
      }),
      description: "Export data in specified format",
    },
  },
});

// Catalog with custom validation functions
const catalogWithValidation = createCatalog({
  name: "Form Builder",
  components: {
    Input: {
      props: z.object({
        label: z.string(),
        value: z.string(),
      }),
      description: "Text input field",
    },
  },
  functions: {
    customValidator: (value) => typeof value === "string" && value.length > 0,
    phoneNumber: (value) =>
      typeof value === "string" && /^\d{3}-\d{3}-\d{4}$/.test(value),
  },
  validation: "strict",
});
```

### Generate Catalog Prompt

Generate an AI-readable prompt describing the catalog.

```typescript { .api }
/**
 * Generate a prompt for AI that describes the catalog
 * @param catalog - The catalog to generate a prompt for
 * @returns Markdown-formatted prompt string
 */
function generateCatalogPrompt<TComponents, TActions, TFunctions>(
  catalog: Catalog<TComponents, TActions, TFunctions>
): string;
```

**Usage Examples:**

```typescript
import { createCatalog, generateCatalogPrompt } from "@json-render/core";
import { z } from "zod";

const catalog = createCatalog({
  name: "My Dashboard",
  components: {
    Card: {
      props: z.object({ title: z.string() }),
      hasChildren: true,
      description: "A card container",
    },
  },
  actions: {
    refresh: { description: "Refresh data" },
  },
});

const prompt = generateCatalogPrompt(catalog);
// Returns markdown string with:
// - Component list with descriptions
// - Available actions
// - Visibility conditions documentation
// - Validation functions documentation
```

### Catalog Configuration

Configuration interface for creating catalogs.

```typescript { .api }
/**
 * Catalog configuration
 */
interface CatalogConfig<TComponents, TActions, TFunctions> {
  /** Catalog name */
  name?: string;
  /** Component definitions */
  components: TComponents;
  /** Action definitions with param schemas */
  actions?: TActions;
  /** Custom validation functions */
  functions?: TFunctions;
  /** Validation mode */
  validation?: ValidationMode;
}
```

### Catalog Instance

The catalog instance provides methods for validation and metadata access.

```typescript { .api }
/**
 * Catalog instance
 */
interface Catalog<TComponents, TActions, TFunctions> {
  /** Catalog name */
  readonly name: string;
  /** Component names */
  readonly componentNames: (keyof TComponents)[];
  /** Action names */
  readonly actionNames: (keyof TActions)[];
  /** Function names */
  readonly functionNames: (keyof TFunctions)[];
  /** Validation mode */
  readonly validation: ValidationMode;
  /** Component definitions */
  readonly components: TComponents;
  /** Action definitions */
  readonly actions: TActions;
  /** Custom validation functions */
  readonly functions: TFunctions;
  /** Full element schema for AI generation */
  readonly elementSchema: z.ZodType<UIElement>;
  /** Full UI tree schema */
  readonly treeSchema: z.ZodType<UITree>;

  /** Check if component exists */
  hasComponent(type: string): boolean;
  /** Check if action exists */
  hasAction(name: string): boolean;
  /** Check if function exists */
  hasFunction(name: string): boolean;

  /** Validate an element */
  validateElement(element: unknown): {
    success: boolean;
    data?: UIElement;
    error?: z.ZodError;
  };

  /** Validate a UI tree */
  validateTree(tree: unknown): {
    success: boolean;
    data?: UITree;
    error?: z.ZodError;
  };
}
```

**Usage Examples:**

```typescript
import { createCatalog } from "@json-render/core";
import { z } from "zod";

const catalog = createCatalog({
  name: "My Catalog",
  components: {
    Button: {
      props: z.object({ label: z.string() }),
      description: "A button",
    },
  },
  actions: {
    submit: { description: "Submit form" },
  },
});

// Check component existence
if (catalog.hasComponent("Button")) {
  console.log("Button component exists");
}

// Access metadata
console.log(catalog.name); // "My Catalog"
console.log(catalog.componentNames); // ["Button"]
console.log(catalog.actionNames); // ["submit"]

// Validate element
const element = {
  key: "btn-1",
  type: "Button",
  props: { label: "Click me" },
};

const result = catalog.validateElement(element);
if (result.success) {
  console.log("Valid element:", result.data);
} else {
  console.error("Validation error:", result.error);
}

// Validate tree
const tree = {
  root: "btn-1",
  elements: {
    "btn-1": element,
  },
};

const treeResult = catalog.validateTree(tree);
if (treeResult.success) {
  console.log("Valid tree:", treeResult.data);
}
```

### Component Definition

Definition for a component in the catalog.

```typescript { .api }
/**
 * Component definition with visibility and validation support
 */
interface ComponentDefinition<TProps extends ComponentSchema = ComponentSchema> {
  /** Zod schema for component props */
  props: TProps;
  /** Whether this component can have children */
  hasChildren?: boolean;
  /** Description for AI generation */
  description?: string;
}
```

**Usage Examples:**

```typescript
import { z } from "zod";
import { type ComponentDefinition } from "@json-render/core";

// Simple component
const buttonDef: ComponentDefinition = {
  props: z.object({
    label: z.string(),
    onClick: z.function().args(z.any()).returns(z.void()).optional(),
  }),
  description: "A clickable button",
};

// Container component
const cardDef: ComponentDefinition = {
  props: z.object({
    title: z.string(),
    description: z.string().nullable(),
    variant: z.enum(["default", "bordered", "elevated"]).default("default"),
  }),
  hasChildren: true,
  description: "A card container for grouping content",
};

// Form input component
const inputDef: ComponentDefinition = {
  props: z.object({
    name: z.string(),
    label: z.string(),
    type: z.enum(["text", "email", "password", "number"]).default("text"),
    value: z.string().default(""),
    placeholder: z.string().optional(),
    required: z.boolean().default(false),
  }),
  description: "A form input field",
};
```

### Action Definition

Definition for an action in the catalog.

```typescript { .api }
/**
 * Action definition in catalog
 */
interface ActionDefinition<TParams = Record<string, unknown>> {
  /** Zod schema for params validation */
  params?: z.ZodType<TParams>;
  /** Description for AI */
  description?: string;
}
```

**Usage Examples:**

```typescript
import { z } from "zod";
import { type ActionDefinition } from "@json-render/core";

// Action without params
const refreshAction: ActionDefinition = {
  description: "Refresh the data from the server",
};

// Action with params
const exportAction: ActionDefinition = {
  params: z.object({
    format: z.enum(["csv", "json", "pdf"]),
    includeHeaders: z.boolean().default(true),
  }),
  description: "Export data in specified format",
};

// Action with complex params
const sendEmailAction: ActionDefinition = {
  params: z.object({
    to: z.string().email(),
    subject: z.string().min(1),
    body: z.string(),
    attachments: z.array(z.string()).optional(),
    priority: z.enum(["low", "normal", "high"]).default("normal"),
  }),
  description: "Send an email with optional attachments",
};
```

### Validation Function

Type for custom validation functions.

```typescript { .api }
/**
 * Validation function signature
 */
type ValidationFunction = (
  value: unknown,
  args?: Record<string, unknown>
) => boolean;
```

### Infer Component Props

Type helper to infer component props from a catalog.

```typescript { .api }
/**
 * Type helper to infer component props from catalog
 */
type InferCatalogComponentProps<
  C extends Catalog<Record<string, ComponentDefinition>>
> = {
  [K in keyof C["components"]]: z.infer<C["components"][K]["props"]>;
};
```

**Usage Examples:**

```typescript
import { createCatalog, type InferCatalogComponentProps } from "@json-render/core";
import { z } from "zod";

const catalog = createCatalog({
  name: "My Catalog",
  components: {
    Button: {
      props: z.object({
        label: z.string(),
        variant: z.enum(["primary", "secondary"]),
      }),
    },
    Card: {
      props: z.object({
        title: z.string(),
        content: z.string(),
      }),
      hasChildren: true,
    },
  },
});

// Infer props types
type CatalogProps = InferCatalogComponentProps<typeof catalog>;
// Result:
// {
//   Button: { label: string; variant: "primary" | "secondary" };
//   Card: { title: string; content: string };
// }

// Use inferred types
const buttonProps: CatalogProps["Button"] = {
  label: "Click me",
  variant: "primary",
};
```
