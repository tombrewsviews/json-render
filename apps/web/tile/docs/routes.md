# Web Routes

All public-facing web pages in the application.

## Capabilities

### Home Page

Landing page with interactive demo and feature showcase.

```typescript { .api }
// Route: /
// File: app/page.tsx
// Type: Static page with client interactivity

interface HomePageSections {
  hero: {
    heading: "Predictable. Guardrailed. Fast.";
    description: string;
    demo: InteractiveDemo;
    installation: string;
  };
  howItWorks: {
    steps: [
      { title: "Define Your Catalog"; description: string },
      { title: "Users Prompt"; description: string },
      { title: "Render Instantly"; description: string }
    ];
  };
  codeExamples: {
    catalogDefinition: CodeBlock;
    jsonOutput: CodeBlock;
  };
  features: Array<{ title: string; description: string }>;
  callToAction: {
    installCommand: string;
    docsLink: "/docs";
  };
}
```

### Documentation Index

Introduction and overview of documentation.

```typescript { .api }
// Route: /docs
// File: app/docs/page.tsx
// Layout: app/docs/layout.tsx (includes sidebar navigation)
```

### Getting Started Routes

Installation and quick start documentation.

```typescript { .api }
// Installation Guide
// Route: /docs/installation
// File: app/docs/installation/page.tsx

// Quick Start Guide
// Route: /docs/quick-start
// File: app/docs/quick-start/page.tsx
```

### Core Concepts Routes

Fundamental concepts documentation.

```typescript { .api }
// Catalog Documentation
// Route: /docs/catalog
// File: app/docs/catalog/page.tsx
// Content: How to define component catalogs with Zod schemas

// Components Documentation
// Route: /docs/components
// File: app/docs/components/page.tsx
// Content: How to register and implement React components

// Data Binding Documentation
// Route: /docs/data-binding
// File: app/docs/data-binding/page.tsx
// Content: JSON Pointer paths and data binding patterns

// Actions Documentation
// Route: /docs/actions
// File: app/docs/actions/page.tsx
// Content: Named actions and action handling

// Visibility Documentation
// Route: /docs/visibility
// File: app/docs/visibility/page.tsx
// Content: Conditional rendering based on data/auth

// Validation Documentation
// Route: /docs/validation
// File: app/docs/validation/page.tsx
// Content: Built-in and custom validation functions
```

### Guides Routes

Integration and usage guides.

```typescript { .api }
// AI SDK Integration Guide
// Route: /docs/ai-sdk
// File: app/docs/ai-sdk/page.tsx
// Content: How to integrate with Vercel AI SDK

// Streaming Guide
// Route: /docs/streaming
// File: app/docs/streaming/page.tsx
// Content: Progressive rendering with streaming responses
```

### API Reference Routes

Detailed API documentation for json-render packages.

```typescript { .api }
// @json-render/core API Reference
// Route: /docs/api/core
// File: app/docs/api/core/page.tsx
// Content: Complete API for core package

// @json-render/react API Reference
// Route: /docs/api/react
// File: app/docs/api/react/page.tsx
// Content: Complete API for React package
```

### Playground Route

Instructions for running local playground.

```typescript { .api }
// Route: /playground
// File: app/playground/page.tsx
// Type: Static instructional page

interface PlaygroundPage {
  instructions: {
    howToRun: string;
    examplePrompts: string[];
    githubLink: string;
  };
  note: "Browser-based playground coming soon";
}
```

## Route Structure

All documentation routes use a nested layout with sidebar navigation:

```typescript { .api }
// Documentation Layout
// File: app/docs/layout.tsx
// Features:
// - Sidebar navigation
// - Documentation-specific styling
// - Nested under root layout
```

## Root Layout

The root layout wraps all pages:

```typescript { .api }
// File: app/layout.tsx

interface RootLayoutFeatures {
  fonts: {
    sans: "Geist Sans";
    mono: "Geist Mono";
  };
  metadata: {
    title: string;
    description: "json-render | AI-powered UI from JSON with guardrails";
  };
  components: {
    header: Header;      // Site header with navigation
    footer: Footer;      // Site footer
    themeProvider: ThemeProvider;  // Light/dark mode
  };
  globalStyles: "app/globals.css";  // Tailwind CSS
}
```

## Navigation Structure

The documentation sidebar organizes routes into sections:

```typescript { .api }
interface NavigationStructure {
  sections: {
    "Getting Started": ["/docs/installation", "/docs/quick-start"];
    "Core Concepts": [
      "/docs/catalog",
      "/docs/components",
      "/docs/data-binding",
      "/docs/actions",
      "/docs/visibility",
      "/docs/validation"
    ];
    "Guides": ["/docs/ai-sdk", "/docs/streaming"];
    "API Reference": ["/docs/api/core", "/docs/api/react"];
  };
}
```

## Mobile Responsiveness

All routes implement mobile-first responsive design:

- Single column on mobile
- Multi-column layouts on larger screens
- Responsive navigation
- Touch-friendly interactive elements
