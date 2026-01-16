# Demo Components

Registry of 22 demonstration components implementing the json-render component interface.

## Overview

These components serve as reference implementations showing how to build components compatible with json-render. They are used in the interactive demo and documentation examples.

```typescript { .api }
/**
 * Demo component registry
 * File: components/demo/index.ts
 */

import type { ComponentRegistry } from "./types";

export const demoRegistry: ComponentRegistry;
export const fallbackComponent: ComponentType;

// Type definitions
export type { ComponentRenderProps, ComponentRegistry } from "./types";

// Utility hook
export { useInteractiveState } from "./utils";
```

## Component Interface

All demo components implement the json-render component interface:

```typescript { .api }
/**
 * Component render props interface
 * File: components/demo/types.ts
 */

interface ComponentRenderProps {
  element: {
    key: string;
    type: string;
    props: Record<string, any>;
    children?: string[];
  };
  children?: React.ReactNode;
  onAction?: (action: any) => void;
}

type ComponentType = React.ComponentType<ComponentRenderProps>;

type ComponentRegistry = Record<string, ComponentType>;
```

## Capabilities

### Layout Components

Container and layout components for structuring UI.

```typescript { .api }
/**
 * Card - Container card component
 * File: components/demo/card.tsx
 */
export function Card({ element, children }: ComponentRenderProps): JSX.Element;

interface CardProps {
  title?: string;
  description?: string;
  maxWidth?: "sm" | "md" | "lg" | "full";
  centered?: boolean;
  className?: string[];
}

/**
 * Stack - Flex container
 * File: components/demo/stack.tsx
 */
export function Stack({ element, children }: ComponentRenderProps): JSX.Element;

interface StackProps {
  direction?: "horizontal" | "vertical";
  gap?: "sm" | "md" | "lg";
  className?: string[];
}

/**
 * Grid - Grid layout
 * File: components/demo/grid.tsx
 */
export function Grid({ element, children }: ComponentRenderProps): JSX.Element;

interface GridProps {
  columns?: 2 | 3 | 4;
  gap?: "sm" | "md" | "lg";
  className?: string[];
}

/**
 * Divider - Horizontal separator
 * File: components/demo/divider.tsx
 */
export function Divider({ element }: ComponentRenderProps): JSX.Element;

interface DividerProps {
  className?: string[];
}
```

### Form Input Components

Interactive form inputs with two-way binding support.

```typescript { .api }
/**
 * Input - Text input field
 * File: components/demo/input.tsx
 */
export function Input({ element }: ComponentRenderProps): JSX.Element;

interface InputProps {
  label: string;
  name: string;
  type?: "text" | "email" | "password" | "number";
  placeholder?: string;
  className?: string[];
}

/**
 * Textarea - Multi-line text input
 * File: components/demo/textarea.tsx
 */
export function Textarea({ element }: ComponentRenderProps): JSX.Element;

interface TextareaProps {
  label: string;
  name: string;
  placeholder?: string;
  rows?: number;
  className?: string[];
}

/**
 * Select - Dropdown select
 * File: components/demo/select.tsx
 */
export function Select({ element }: ComponentRenderProps): JSX.Element;

interface SelectProps {
  label: string;
  name: string;
  options: string[];
  placeholder?: string;
  className?: string[];
}

/**
 * Checkbox - Checkbox input
 * File: components/demo/checkbox.tsx
 */
export function Checkbox({ element }: ComponentRenderProps): JSX.Element;

interface CheckboxProps {
  label: string;
  name: string;
  checked?: boolean;
  className?: string[];
}

/**
 * Radio - Radio button group
 * File: components/demo/radio.tsx
 */
export function Radio({ element }: ComponentRenderProps): JSX.Element;

interface RadioProps {
  label: string;
  name: string;
  options: string[];
  className?: string[];
}

/**
 * Switch - Toggle switch
 * File: components/demo/switch.tsx
 */
export function Switch({ element }: ComponentRenderProps): JSX.Element;

interface SwitchProps {
  label: string;
  name: string;
  checked?: boolean;
  className?: string[];
}

/**
 * Form - Form wrapper (unused in current implementation)
 * File: components/demo/form.tsx
 */
export function Form({ element, children }: ComponentRenderProps): JSX.Element;

interface FormProps {
  className?: string[];
}
```

### Action Components

Interactive buttons and links.

```typescript { .api }
/**
 * Button - Clickable button
 * File: components/demo/button.tsx
 */
export function Button({ element }: ComponentRenderProps): JSX.Element;

interface ButtonProps {
  label: string;
  variant?: "primary" | "secondary" | "danger";
  actionText?: string;  // Text shown in toast (defaults to label)
  className?: string[];
}

// Action handler integration
// Calls: window.__demoAction?.(actionText || label)

/**
 * Link - Anchor link
 * File: components/demo/link.tsx
 */
export function Link({ element }: ComponentRenderProps): JSX.Element;

interface LinkProps {
  label: string;
  href: string;
  className?: string[];
}
```

### Typography Components

Text and heading elements.

```typescript { .api }
/**
 * Heading - Heading text
 * File: components/demo/heading.tsx
 */
export function Heading({ element }: ComponentRenderProps): JSX.Element;

interface HeadingProps {
  text: string;
  level?: 1 | 2 | 3 | 4;  // Maps to h1-h4
  className?: string[];
}

/**
 * Text - Paragraph text
 * File: components/demo/text.tsx
 */
export function Text({ element }: ComponentRenderProps): JSX.Element;

interface TextProps {
  content: string;
  variant?: "body" | "caption" | "muted";
  className?: string[];
}
```

### Data Display Components

Components for displaying data and status.

```typescript { .api }
/**
 * Image - Image element
 * File: components/demo/image.tsx
 */
export function Image({ element }: ComponentRenderProps): JSX.Element;

interface ImageProps {
  src: string;
  alt: string;
  width?: number;
  height?: number;
  className?: string[];
}

/**
 * Avatar - User avatar with initials fallback
 * File: components/demo/avatar.tsx
 */
export function Avatar({ element }: ComponentRenderProps): JSX.Element;

interface AvatarProps {
  src?: string;
  name: string;  // Used to generate fallback initials
  size?: "sm" | "md" | "lg";
  className?: string[];
}

// Initials generation:
// "John Doe" → "JD"
// "Alice" → "A"

/**
 * Badge - Status badge
 * File: components/demo/badge.tsx
 */
export function Badge({ element }: ComponentRenderProps): JSX.Element;

interface BadgeProps {
  text: string;
  variant?: "default" | "success" | "warning" | "danger";
  className?: string[];
}

/**
 * Alert - Alert banner
 * File: components/demo/alert.tsx
 */
export function Alert({ element }: ComponentRenderProps): JSX.Element;

interface AlertProps {
  title: string;
  message?: string;
  type?: "info" | "success" | "warning" | "error";
  className?: string[];
}

/**
 * Progress - Progress bar
 * File: components/demo/progress.tsx
 */
export function Progress({ element }: ComponentRenderProps): JSX.Element;

interface ProgressProps {
  value: number;  // 0-100
  max?: number;   // Default: 100
  label?: string;
  className?: string[];
}

/**
 * Rating - Star rating display
 * File: components/demo/rating.tsx
 */
export function Rating({ element }: ComponentRenderProps): JSX.Element;

interface RatingProps {
  value: number;
  max?: number;   // Default: 5
  label?: string;
  className?: string[];
}
```

### Chart Components

Data visualization components.

```typescript { .api }
/**
 * BarGraph - Vertical bar chart
 * File: components/demo/bar-graph.tsx
 */
export function BarGraph({ element }: ComponentRenderProps): JSX.Element;

interface BarGraphProps {
  title?: string;
  data: Array<{ label: string; value: number }>;
  className?: string[];
}

// Visual representation:
// - Vertical bars
// - Labels below each bar
// - Height proportional to value
// - Maximum value determines scale

/**
 * LineGraph - Line chart
 * File: components/demo/line-graph.tsx
 */
export function LineGraph({ element }: ComponentRenderProps): JSX.Element;

interface LineGraphProps {
  title?: string;
  data: Array<{ label: string; value: number }>;
  className?: string[];
}

// Visual representation:
// - Connected line chart
// - Dots at each data point
// - Labels below points
// - Maximum value determines scale
```

### Fallback Component

Handles unknown component types.

```typescript { .api }
/**
 * Fallback - Unknown component type handler
 * File: components/demo/fallback.tsx
 */
export function Fallback({ element }: ComponentRenderProps): JSX.Element;

// Displays error message:
// "Unknown component: {type}"
// Useful for debugging when AI generates invalid type

interface FallbackProps {
  // No specific props - uses element.type for error message
}
```

## Utilities

### Component Types

Type definitions for components:

```typescript { .api }
/**
 * Component type definitions
 * File: components/demo/types.ts
 */

export interface ComponentRenderProps {
  element: {
    key: string;
    type: string;
    props: Record<string, any>;
    children?: string[];
  };
  children?: React.ReactNode;
  onAction?: (action: any) => void;
}

export type ComponentType = React.ComponentType<ComponentRenderProps>;

export type ComponentRegistry = Record<string, ComponentType>;
```

### Interactive State Hook

Hook for managing interactive component state:

```typescript { .api }
/**
 * Interactive state hook
 * File: components/demo/utils.ts
 */

export function useInteractiveState(): void;

// Purpose:
// - Initializes state for Select components
// - Enables dropdowns to maintain selected value
// - Called once in Demo component

// Usage:
import { useInteractiveState } from "./demo/utils";

function Demo() {
  useInteractiveState();
  // ... rest of component
}
```

## Component Registry Export

All components are exported as a registry:

```typescript { .api }
/**
 * Complete component registry
 * File: components/demo/index.ts
 */

export const demoRegistry: ComponentRegistry = {
  Alert,
  Avatar,
  Badge,
  BarGraph,
  Button,
  Card,
  Checkbox,
  Divider,
  Form,
  Grid,
  Heading,
  Image,
  Input,
  LineGraph,
  Link,
  Progress,
  Radio,
  Rating,
  Select,
  Stack,
  Switch,
  Text,
  Textarea,
};

export const fallbackComponent = Fallback;
```

## Styling Patterns

All components follow consistent styling patterns:

```typescript { .api }
/**
 * Common styling patterns
 */
interface StylingPatterns {
  tailwind: {
    usage: "Extensive Tailwind utility classes";
    customization: "className prop merges custom classes";
    merge: "Uses clsx or cn utility for class merging";
  };

  responsiveness: {
    approach: "Mobile-first responsive design";
    breakpoints: ["sm", "md", "lg"];
    grid: "Grid component uses responsive column classes";
  };

  theming: {
    colors: "Theme-aware color variables";
    modes: ["light", "dark"];
    variables: [
      "text-foreground",
      "text-muted-foreground",
      "bg-background",
      "bg-muted",
      "border-border"
    ];
  };

  spacing: {
    gap: {
      sm: "gap-2 (0.5rem / 8px)";
      md: "gap-4 (1rem / 16px)";
      lg: "gap-6 (1.5rem / 24px)";
    };
    padding: "Consistent p-3 to p-6 patterns";
    margins: "Minimal margins, prefer gap in containers";
  };
}
```

## Implementation Patterns

Common patterns across components:

```typescript { .api }
/**
 * Implementation patterns
 */
interface ImplementationPatterns {
  props: {
    extraction: "const { prop1, prop2, className } = element.props";
    defaults: "Use || operator or optional chaining for defaults";
    validation: "Minimal validation, trust json-render catalog";
  };

  classNames: {
    base: "Base classes defined per component";
    custom: "element.props.className array joined with space";
    merge: "Base + custom using clsx or cn utility";
  };

  children: {
    layout: "Card, Stack, Grid render props.children";
    leaf: "Input, Button, Text do not render children";
  };

  state: {
    client: 'Most components are "use client" for interactivity';
    server: "Typography components could be server-rendered";
  };

  actions: {
    button: "Calls window.__demoAction for demo purposes";
    production: "Real apps would use onAction prop";
  };
}
```

## Usage Example

Using the demo registry with json-render:

```typescript
import { Renderer, JSONUIProvider } from "@json-render/react";
import { demoRegistry, fallbackComponent } from "@/components/demo";
import type { UITree } from "@json-render/core";

function App() {
  const tree: UITree = {
    root: "card",
    elements: {
      card: {
        key: "card",
        type: "Card",
        props: { title: "Hello World" },
        children: ["heading"],
      },
      heading: {
        key: "heading",
        type: "Heading",
        props: { text: "Welcome!", level: 2 },
      },
    },
  };

  return (
    <JSONUIProvider registry={demoRegistry}>
      <Renderer
        tree={tree}
        registry={demoRegistry}
        fallback={fallbackComponent}
      />
    </JSONUIProvider>
  );
}
```

## Component Count Summary

```typescript { .api }
/**
 * Component count by category
 */
const COMPONENT_COUNT = {
  layout: 4,        // Card, Stack, Grid, Divider
  formInputs: 7,    // Input, Textarea, Select, Checkbox, Radio, Switch, Form
  actions: 2,       // Button, Link
  typography: 2,    // Heading, Text
  dataDisplay: 6,   // Image, Avatar, Badge, Alert, Progress, Rating
  charts: 2,        // BarGraph, LineGraph
  total: 23,        // 22 + Fallback
};
```
