# @repo/typescript-config

Shared TypeScript configuration files for the json-render monorepo, providing reusable TypeScript compiler settings with strict type checking and modern JavaScript features.

## Package Information

- **Package Name**: @repo/typescript-config
- **Package Type**: npm
- **Language**: TypeScript (Configuration)
- **Installation**: This package is typically installed as a dev dependency in a monorepo workspace
- **Usage**: Extend the configuration files in your `tsconfig.json`

## Core Usage

The package provides three TypeScript configuration presets that can be extended in a project's `tsconfig.json`:

```json
{
  "extends": "@repo/typescript-config/base.json"
}
```

Or for specific frameworks:

```json
{
  "extends": "@repo/typescript-config/nextjs.json"
}
```

```json
{
  "extends": "@repo/typescript-config/react-library.json"
}
```

## Basic Usage

**For a standard TypeScript project**:

```json
{
  "extends": "@repo/typescript-config/base.json",
  "compilerOptions": {
    "outDir": "./dist"
  },
  "include": ["src/**/*"]
}
```

**For a Next.js application**:

```json
{
  "extends": "@repo/typescript-config/nextjs.json",
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

**For a React library**:

```json
{
  "extends": "@repo/typescript-config/react-library.json",
  "compilerOptions": {
    "outDir": "./dist"
  },
  "include": ["src/**/*"]
}
```

## Capabilities

### Base Configuration

Strict TypeScript configuration targeting ES2022 with NodeNext module resolution, designed for maximum type safety.

```typescript { .api }
// File: base.json
interface BaseConfig {
  $schema: string;
  compilerOptions: {
    declaration: boolean;           // true - Generate .d.ts declaration files
    declarationMap: boolean;        // true - Generate sourcemaps for declarations
    esModuleInterop: boolean;       // true - Emit additional JS for CommonJS/ES module interop
    incremental: boolean;           // false - Disable incremental compilation
    isolatedModules: boolean;       // true - Ensure each file can be safely transpiled
    lib: string[];                  // ["es2022", "DOM", "DOM.Iterable"]
    module: string;                 // "NodeNext"
    moduleDetection: string;        // "force"
    moduleResolution: string;       // "NodeNext"
    noUncheckedIndexedAccess: boolean; // true - Strict index access checking
    resolveJsonModule: boolean;     // true - Allow importing JSON files
    skipLibCheck: boolean;          // true - Skip type checking of declaration files
    strict: boolean;                // true - Enable all strict type-checking options
    target: string;                 // "ES2022"
  };
}
```

**Key Features**:
- Targets ES2022 with modern JavaScript features
- NodeNext module system for native Node.js ESM support
- Strict type checking with `noUncheckedIndexedAccess` for extra safety
- Declaration file generation enabled
- DOM libraries included for browser API support

**Usage**:

```json
{
  "extends": "@repo/typescript-config/base.json"
}
```

### Next.js Configuration

Next.js-specific TypeScript configuration extending the base configuration with bundler-style module resolution and JSX preservation.

```typescript { .api }
// File: nextjs.json
interface NextJsConfig {
  $schema: string;
  extends: string;              // "./base.json"
  compilerOptions: {
    plugins: Array<{ name: string }>; // [{ "name": "next" }]
    module: string;             // "ESNext" - Overrides base
    moduleResolution: string;   // "Bundler" - Overrides base
    allowJs: boolean;           // true - Allow JavaScript files
    jsx: string;                // "preserve" - Preserve JSX for Next.js
    noEmit: boolean;            // true - Don't emit (Next.js handles compilation)
  };
}
```

**Key Features**:
- Extends base configuration
- Bundler module resolution for Next.js compatibility
- JSX preservation (Next.js transforms JSX)
- Next.js TypeScript plugin support
- No emit (Next.js handles compilation)
- JavaScript file support

**Usage**:

```json
{
  "extends": "@repo/typescript-config/nextjs.json"
}
```

### React Library Configuration

React library TypeScript configuration extending the base with React 17+ JSX transform.

```typescript { .api }
// File: react-library.json
interface ReactLibraryConfig {
  $schema: string;
  extends: string;          // "./base.json"
  compilerOptions: {
    jsx: string;            // "react-jsx" - Use React 17+ JSX transform
  };
}
```

**Key Features**:
- Extends base configuration
- React 17+ JSX transform (no need to import React)
- Inherits all base strict type checking
- Declaration file generation enabled

**Usage**:

```json
{
  "extends": "@repo/typescript-config/react-library.json"
}
```

## Configuration Hierarchy

The configurations follow this inheritance structure:

```
base.json (foundation)
├── nextjs.json (extends base)
└── react-library.json (extends base)
```

Both `nextjs.json` and `react-library.json` inherit all settings from `base.json` and can override or add specific options.

## TypeScript Compiler Options Reference

All configurations support standard TypeScript compiler options. Common options you may want to override or add:

```typescript { .api }
interface CompilerOptionsOverrides {
  outDir?: string;           // Output directory for compiled files
  rootDir?: string;          // Root directory of source files
  baseUrl?: string;          // Base directory for module resolution
  paths?: Record<string, string[]>; // Path mapping for module resolution
  types?: string[];          // Type declaration files to include
  include?: string[];        // Files to include in compilation
  exclude?: string[];        // Files to exclude from compilation
}
```

**Example with custom options**:

```json
{
  "extends": "@repo/typescript-config/base.json",
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

## Types

### Configuration File Schema

All configuration files follow the TypeScript configuration JSON schema:

```typescript { .api }
interface TSConfig {
  $schema?: string;           // JSON schema reference
  extends?: string | string[]; // Path(s) to base configuration(s)
  compilerOptions?: CompilerOptions; // TypeScript compiler options
  include?: string[];         // Glob patterns for files to include
  exclude?: string[];         // Glob patterns for files to exclude
  files?: string[];           // Explicit file list
  references?: Array<{ path: string }>; // Project references
}

interface CompilerOptions {
  // Type Checking
  strict?: boolean;
  noUncheckedIndexedAccess?: boolean;

  // Modules
  module?: string;
  moduleResolution?: string;
  moduleDetection?: string;
  resolveJsonModule?: boolean;

  // Emit
  declaration?: boolean;
  declarationMap?: boolean;
  noEmit?: boolean;
  outDir?: string;

  // JavaScript Support
  allowJs?: boolean;

  // Interop Constraints
  esModuleInterop?: boolean;
  isolatedModules?: boolean;

  // Language and Environment
  target?: string;
  lib?: string[];
  jsx?: string;

  // Completeness
  skipLibCheck?: boolean;

  // Build Performance
  incremental?: boolean;

  // Advanced
  baseUrl?: string;
  paths?: Record<string, string[]>;
  plugins?: Array<{ name: string; [key: string]: any }>;
  types?: string[];
}
```

## Common Usage Patterns

### Pattern 1: Standard Library Package

```json
{
  "extends": "@repo/typescript-config/base.json",
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src/**/*"],
  "exclude": ["**/*.test.ts", "**/*.spec.ts"]
}
```

### Pattern 2: Next.js Application

```json
{
  "extends": "@repo/typescript-config/nextjs.json",
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

### Pattern 3: React Component Library

```json
{
  "extends": "@repo/typescript-config/react-library.json",
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src",
    "declarationDir": "./dist/types"
  },
  "include": ["src/**/*"],
  "exclude": ["src/**/*.test.tsx", "src/**/*.stories.tsx"]
}
```

### Pattern 4: Monorepo Package with Project References

```json
{
  "extends": "@repo/typescript-config/base.json",
  "compilerOptions": {
    "composite": true,
    "outDir": "./dist"
  },
  "include": ["src/**/*"],
  "references": [
    { "path": "../shared-types" },
    { "path": "../utils" }
  ]
}
```

## Notes

- All configurations enforce strict type checking for maximum type safety
- The `noUncheckedIndexedAccess` option adds extra safety for array/object index access
- Declaration files and sourcemaps are generated by default in base configuration
- Next.js configuration disables emit since Next.js handles compilation
- React library configuration uses the modern React 17+ JSX transform
