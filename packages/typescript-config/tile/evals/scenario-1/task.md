# TypeScript Configuration Manager

## Overview

You need to create a TypeScript project configuration system that properly extends shared TypeScript configurations and provides project-specific compiler settings for different types of applications in a monorepo.

## Requirements

You are building configurations for three different projects in a monorepo:

1. **Backend API Service** - A Node.js backend service
2. **Next.js Web Application** - A Next.js frontend application
3. **React Component Library** - A reusable React component library

Each project needs its own `tsconfig.json` file that:

- Extends the appropriate shared configuration from the provided package
- Adds project-specific compiler options
- Uses the correct paths for source and output directories

### Project 1: Backend API Service

Create `tsconfig.backend.json` with these requirements:

- Must extend the base configuration from the shared package
- Output directory: `./dist`
- Root directory: `./src`
- Must include type definitions for Node.js

### Project 2: Next.js Web Application

Create `tsconfig.next.json` with these requirements:

- Must extend the Next.js-specific configuration from the shared package
- Must include the `./src` directory in the compilation
- Must exclude `node_modules` from compilation

### Project 3: React Component Library

Create `tsconfig.library.json` with these requirements:

- Must extend the React library configuration from the shared package
- Output directory: `./lib`
- Root directory: `./src`
- Must generate declaration files and declaration maps

## Test Cases { .test }

### Test 1: Backend Configuration Extends Correctly { .test }

**File:** `tsconfig.backend.json`

The backend configuration must properly extend the base configuration. When parsed, it should inherit all base options.

```typescript
// verify-backend.test.ts
import * as fs from 'fs';
import * as path from 'path';

const config = JSON.parse(fs.readFileSync('tsconfig.backend.json', 'utf-8'));

// Should extend base configuration
if (!config.extends || !config.extends.includes('base')) {
  throw new Error('Backend config must extend base configuration');
}

// Should have correct output directory
if (config.compilerOptions?.outDir !== './dist') {
  throw new Error('Backend config must have outDir set to ./dist');
}

console.log('Backend configuration is valid');
```

### Test 2: Next.js Configuration Uses Correct Preset { .test }

**File:** `tsconfig.next.json`

The Next.js configuration must extend the Next.js-specific preset.

```typescript
// verify-nextjs.test.ts
import * as fs from 'fs';

const config = JSON.parse(fs.readFileSync('tsconfig.next.json', 'utf-8'));

// Should extend nextjs configuration
if (!config.extends || !config.extends.includes('nextjs')) {
  throw new Error('Next.js config must extend nextjs configuration');
}

// Should include src directory
if (!config.include || !config.include.includes('./src')) {
  throw new Error('Next.js config must include ./src directory');
}

console.log('Next.js configuration is valid');
```

### Test 3: Library Configuration Generates Declarations { .test }

**File:** `tsconfig.library.json`

The library configuration must extend the React library preset and ensure declarations are generated.

```typescript
// verify-library.test.ts
import * as fs from 'fs';

const config = JSON.parse(fs.readFileSync('tsconfig.library.json', 'utf-8'));

// Should extend react-library configuration
if (!config.extends || !config.extends.includes('react-library')) {
  throw new Error('Library config must extend react-library configuration');
}

// Should have correct output directory
if (config.compilerOptions?.outDir !== './lib') {
  throw new Error('Library config must have outDir set to ./lib');
}

console.log('Library configuration is valid');
```

## Dependencies { .dependencies }

### @repo/typescript-config { .dependency }

Provides shared TypeScript configuration presets including base, Next.js, and React library configurations.

## Deliverables

1. `tsconfig.backend.json` - Backend API service configuration
2. `tsconfig.next.json` - Next.js application configuration
3. `tsconfig.library.json` - React component library configuration

All three configuration files must be valid JSON and properly extend the appropriate shared configurations from `@repo/typescript-config`.
