# Configuration

Application configuration including dependencies, build settings, and development tools.

## Capabilities

### Package Configuration

Core package.json configuration:

```json { .api }
{
  "name": "web",
  "version": "0.1.0",
  "type": "module",
  "private": true,
  "license": "Apache-2.0"
}
```

### Scripts

Available npm scripts:

```json { .api }
{
  "scripts": {
    "dev": "next dev --turbopack --port 3000",
    "build": "next build",
    "start": "next start",
    "lint": "eslint --max-warnings 0",
    "check-types": "next typegen && tsc --noEmit"
  }
}
```

**Script Descriptions:**

```typescript { .api }
interface Scripts {
  dev: {
    command: "next dev --turbopack --port 3000";
    purpose: "Start development server";
    features: ["Turbopack bundler", "Hot reload", "Port 3000"];
    usage: "pnpm dev";
  };

  build: {
    command: "next build";
    purpose: "Build production application";
    output: ".next directory";
    optimizations: ["Minification", "Code splitting", "Static generation"];
    usage: "pnpm build";
  };

  start: {
    command: "next start";
    purpose: "Start production server";
    requires: "Must run 'build' first";
    port: "Default 3000 (configurable)";
    usage: "pnpm start";
  };

  lint: {
    command: "eslint --max-warnings 0";
    purpose: "Check code quality";
    strictness: "Zero warnings allowed";
    config: "eslint.config.js";
    usage: "pnpm lint";
  };

  "check-types": {
    command: "next typegen && tsc --noEmit";
    purpose: "Type checking";
    steps: ["Generate Next.js types", "Run TypeScript compiler"];
    config: "tsconfig.json";
    usage: "pnpm check-types";
  };
}
```

### Dependencies

Production dependencies:

```json { .api }
{
  "dependencies": {
    "@ai-sdk/gateway": "^3.0.13",
    "@json-render/core": "workspace:*",
    "@json-render/react": "workspace:*",
    "@radix-ui/react-slot": "^1.2.4",
    "@radix-ui/react-tabs": "^1.1.13",
    "ai": "^6.0.33",
    "class-variance-authority": "^0.7.1",
    "clsx": "^2.1.1",
    "lucide-react": "^0.562.0",
    "next": "16.1.1",
    "next-themes": "^0.4.6",
    "react": "19.2.3",
    "react-dom": "19.2.3",
    "shiki": "^3.21.0",
    "sonner": "^2.0.7",
    "tailwind-merge": "^3.4.0",
    "zod": "^3.24.0"
  }
}
```

**Dependency Purposes:**

```typescript { .api }
interface DependencyPurposes {
  workspace: {
    "@json-render/core": "Core json-render types and utilities";
    "@json-render/react": "React renderer and hooks for json-render";
  };

  framework: {
    next: "Next.js 16.1.1 - React framework with App Router";
    react: "React 19.2.3 - UI library";
    "react-dom": "React 19.2.3 - DOM rendering";
  };

  ai: {
    ai: "Vercel AI SDK for streaming AI responses";
    "@ai-sdk/gateway": "AI SDK gateway for model routing";
  };

  ui: {
    "@radix-ui/react-slot": "Slot component primitive";
    "@radix-ui/react-tabs": "Tab component primitive";
    "lucide-react": "Icon library";
    "class-variance-authority": "Variant-based className utility";
    clsx: "Conditional className utility";
    "tailwind-merge": "Tailwind class merging utility";
  };

  features: {
    "next-themes": "Dark/light theme management";
    shiki: "Syntax highlighting for code blocks";
    sonner: "Toast notifications";
    zod: "Schema validation";
  };
}
```

### Development Dependencies

Development and build tools:

```json { .api }
{
  "devDependencies": {
    "@repo/eslint-config": "workspace:*",
    "@repo/typescript-config": "workspace:*",
    "@tailwindcss/postcss": "^4.1.18",
    "@types/node": "^22.15.3",
    "@types/react": "19.2.3",
    "@types/react-dom": "19.2.3",
    "eslint": "^9.39.1",
    "postcss": "^8.5.6",
    "tailwindcss": "^4.1.18",
    "tw-animate-css": "^1.4.0",
    "typescript": "5.9.2"
  }
}
```

**DevDependency Purposes:**

```typescript { .api }
interface DevDependencyPurposes {
  monorepo: {
    "@repo/eslint-config": "Shared ESLint configuration";
    "@repo/typescript-config": "Shared TypeScript configuration";
  };

  styling: {
    tailwindcss: "Tailwind CSS 4.1.18 - Utility-first CSS";
    "@tailwindcss/postcss": "PostCSS plugin for Tailwind";
    postcss: "CSS transformation tool";
    "tw-animate-css": "Animation utilities for Tailwind";
  };

  typescript: {
    typescript: "TypeScript 5.9.2 compiler";
    "@types/node": "Node.js type definitions";
    "@types/react": "React 19 type definitions";
    "@types/react-dom": "React DOM 19 type definitions";
  };

  linting: {
    eslint: "ESLint 9.39.1 - Code linting";
  };
}
```

### TypeScript Configuration

TypeScript compiler options:

```json { .api }
{
  "extends": "@repo/typescript-config/nextjs.json",
  "compilerOptions": {
    "paths": {
      "@/*": ["./*"]
    }
  },
  "include": [
    "next-env.d.ts",
    "**/*.ts",
    "**/*.tsx",
    ".next/types/**/*.ts"
  ],
  "exclude": ["node_modules"]
}
```

**Configuration Details:**

```typescript { .api }
interface TypeScriptConfig {
  extends: {
    source: "@repo/typescript-config/nextjs.json";
    purpose: "Shared monorepo TypeScript config for Next.js";
  };

  compilerOptions: {
    paths: {
      "@/*": {
        maps: "Project root";
        example: '@/components/demo → ./components/demo';
      };
    };
  };

  include: [
    "next-env.d.ts",      // Next.js environment types
    "**/*.ts",            // All TypeScript files
    "**/*.tsx",           // All TypeScript React files
    ".next/types/**/*.ts" // Generated Next.js types
  ];

  exclude: ["node_modules"];
}
```

### ESLint Configuration

Code quality and linting:

```javascript { .api }
// File: eslint.config.js

import baseConfig from "@repo/eslint-config/base.js";

export default [
  ...baseConfig,
  {
    // Project-specific overrides
  },
];
```

**ESLint Details:**

```typescript { .api }
interface ESLintConfig {
  extends: {
    source: "@repo/eslint-config/base.js";
    purpose: "Shared monorepo ESLint rules";
  };

  enforcement: {
    script: "eslint --max-warnings 0";
    strictness: "Zero warnings policy";
  };

  features: [
    "TypeScript support",
    "React best practices",
    "Next.js specific rules",
    "Import sorting",
    "Accessibility checks"
  ];
}
```

### Next.js Configuration

Next.js framework configuration:

```javascript { .api }
// File: next.config.js

/** @type {import('next').NextConfig} */
const nextConfig = {
  // Configuration options
};

export default nextConfig;
```

**Configuration Options:**

```typescript { .api }
interface NextConfig {
  // Default Next.js 16 configuration
  // May include:
  // - Custom webpack configuration
  // - Image optimization settings
  // - Environment variable handling
  // - Build output configuration
  // - Internationalization settings
}
```

### PostCSS Configuration

CSS processing configuration:

```javascript { .api }
// File: postcss.config.mjs

export default {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
```

**PostCSS Details:**

```typescript { .api }
interface PostCSSConfig {
  plugins: {
    "@tailwindcss/postcss": {
      purpose: "Process Tailwind CSS directives";
      version: "4.1.18";
      features: ["JIT compilation", "Automatic purging", "Custom directives"];
    };
  };
}
```

### Tailwind CSS Configuration

Styling framework configuration:

```json { .api }
// File: tailwind.config.js (implicit via @tailwindcss/postcss)

{
  "content": [
    "./app/**/*.{js,ts,jsx,tsx,mdx}",
    "./components/**/*.{js,ts,jsx,tsx,mdx}"
  ],
  "theme": {
    "extend": {
      // Custom theme extensions
    }
  },
  "plugins": []
}
```

**Tailwind Details:**

```typescript { .api }
interface TailwindConfig {
  version: "4.1.18";

  content: {
    paths: ["./app/**/*.{js,ts,jsx,tsx,mdx}", "./components/**/*.{js,ts,jsx,tsx,mdx}"];
    purpose: "Files to scan for Tailwind classes";
  };

  theme: {
    colors: "CSS variables for light/dark mode";
    fonts: {
      sans: "Geist Sans";
      mono: "Geist Mono";
    };
    extend: "Custom theme extensions";
  };

  features: [
    "JIT (Just-In-Time) compilation",
    "Dark mode support",
    "Responsive utilities",
    "Custom color palette",
    "Animation utilities"
  ];
}
```

### shadcn/ui Configuration

UI component library configuration:

```json { .api }
// File: components.json

{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "default",
  "rsc": true,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "app/globals.css",
    "baseColor": "slate",
    "cssVariables": true
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils"
  }
}
```

**shadcn/ui Details:**

```typescript { .api }
interface ShadcnConfig {
  style: "default";
  rsc: true;  // React Server Components
  tsx: true;  // TypeScript

  tailwind: {
    config: "tailwind.config.js";
    css: "app/globals.css";
    baseColor: "slate";
    cssVariables: true;  // Use CSS variables for theming
  };

  aliases: {
    components: "@/components";
    utils: "@/lib/utils";
  };

  components: [
    "button",
    "card",
    "badge",
    "tabs",
    "sonner"
  ];
}
```

### Global Styles

Global CSS configuration:

```css { .api }
/* File: app/globals.css */

@import "tailwindcss";

/* CSS variable definitions for theming */
:root {
  /* Light mode colors */
  --background: ...;
  --foreground: ...;
  --muted: ...;
  --border: ...;
  /* etc. */
}

.dark {
  /* Dark mode colors */
  --background: ...;
  --foreground: ...;
  --muted: ...;
  --border: ...;
  /* etc. */
}
```

**Global Style Details:**

```typescript { .api }
interface GlobalStyles {
  tailwind: {
    import: "@import 'tailwindcss'";
    purpose: "Import Tailwind base, components, utilities";
  };

  cssVariables: {
    light: "Defined in :root";
    dark: "Defined in .dark selector";
    usage: "var(--background), var(--foreground), etc.";
  };

  theming: {
    provider: "next-themes";
    modes: ["light", "dark"];
    default: "system";
    storage: "localStorage";
  };
}
```

### Environment Variables

Required and optional environment variables:

```typescript { .api }
/**
 * Environment variables
 */
interface EnvironmentVariables {
  required: {
    ANTHROPIC_API_KEY: {
      purpose: "Authenticate with Anthropic API for Claude";
      usage: "API endpoint /api/generate";
      provider: "Anthropic";
      docs: "https://docs.anthropic.com/";
    };
  };

  optional: {
    NODE_ENV: {
      values: ["development", "production", "test"];
      purpose: "Environment mode";
      default: "development";
    };

    PORT: {
      purpose: "Server port";
      default: 3000;
      override: "Command line --port flag";
    };
  };
}
```

**Environment File:**

```bash
# .env.local
ANTHROPIC_API_KEY=your_api_key_here
```

### Build Output

Build artifacts and structure:

```typescript { .api }
interface BuildOutput {
  directory: ".next";

  structure: {
    cache: ".next/cache - Build cache";
    server: ".next/server - Server bundles";
    static: ".next/static - Static assets";
    types: ".next/types - Generated types";
  };

  artifacts: {
    pages: "Server-side rendered pages";
    api: "API route handlers";
    static: "Static files and assets";
    chunks: "JavaScript bundles";
  };

  optimization: {
    minification: "JavaScript and CSS minification";
    splitting: "Code splitting for optimal loading";
    treeshaking: "Dead code elimination";
    compression: "Gzip/Brotli compression";
  };
}
```

### Monorepo Integration

Workspace configuration:

```typescript { .api }
interface MonorepoIntegration {
  packageManager: "pnpm";

  workspaces: {
    packages: ["packages/*", "apps/*", "examples/*"];
  };

  dependencies: {
    "@json-render/core": "workspace:*";
    "@json-render/react": "workspace:*";
    "@repo/eslint-config": "workspace:*";
    "@repo/typescript-config": "workspace:*";
  };

  benefits: [
    "Shared dependencies across projects",
    "Consistent configuration",
    "Unified build process",
    "Cross-package development"
  ];
}
```
