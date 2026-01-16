# json-render Documentation and Playground Application

A Next.js web application that serves as the official documentation and interactive playground for the json-render library. This application demonstrates how to build guardrailed AI-generated UIs with predictable, constrained output.

## Package Information

- **Application Name**: web
- **Package Type**: Next.js Application
- **Language**: TypeScript
- **Framework**: Next.js 16.1.1 (App Router)
- **React Version**: 19.2.3
- **Package Manager**: pnpm
- **Repository**: github:tombrewsviews/json-render (monorepo - apps/web)
- **Private**: Yes (not published to npm)

## Core Imports

This is a web application, not a library. It does not export code for import. Instead, it provides:

- **Web Interface**: Accessible via browser at deployed URL
- **API Endpoint**: `POST /api/generate` for programmatic UI generation
- **Demo Components**: Reference implementations in `components/demo/` (not exported)

For developers working within the monorepo:

```typescript
// Accessing demo registry (internal use only, not exported)
import { demoRegistry, fallbackComponent } from "@/components/demo";

// Using supporting components (internal use only, not exported)
import { Demo } from "@/components/demo";
import { CodeBlock } from "@/components/code-block";
```

**Note**: This application is marked `"private": true` and does not publish to npm.

## Basic Usage

### Accessing the Application

**Local Development:**

```bash
# Navigate to the application
cd apps/web

# Start development server
pnpm dev

# Access in browser
# http://localhost:3000
```

**Using the Interactive Demo:**

1. Open the homepage (`/`)
2. Watch the simulation mode auto-play an example
3. Enter your own prompt (140 characters max)
4. View real-time AI-generated UI

**Using the API Endpoint:**

```bash
# POST request to generate UI from prompt
curl -X POST http://localhost:3000/api/generate \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Create a login form"}'

# Response: Streaming JSONL patches
{"op":"set","path":"/root","value":"card"}
{"op":"add","path":"/elements/card","value":{...}}
```

**Browsing Documentation:**

- Navigate to `/docs` for complete documentation
- Browse Getting Started, Core Concepts, Guides, and API Reference sections

## Installation & Setup

This application is part of a monorepo and depends on workspace packages `@json-render/core` and `@json-render/react`.

### Prerequisites

```bash
# Requires Node.js and pnpm
pnpm --version
```

### Install Dependencies

```bash
# From monorepo root
pnpm install
```

### Development Server

```bash
# From apps/web directory or monorepo root
pnpm --filter web dev

# Or directly
cd apps/web && pnpm dev
```

The development server runs on `http://localhost:3000` with Turbopack enabled.

### Production Build

```bash
# Build the application
pnpm --filter web build

# Start production server
pnpm --filter web start
```

### Scripts { .api }

```typescript
{
  "dev": "next dev --turbopack --port 3000",      // Development server
  "build": "next build",                          // Production build
  "start": "next start",                          // Production server
  "lint": "eslint --max-warnings 0",             // Linting
  "check-types": "next typegen && tsc --noEmit"  // Type checking
}
```

## Application Architecture

This is a Next.js App Router application with the following structure:

- **Documentation Portal**: Comprehensive guides and API references
- **Interactive Demo**: Live playground with AI-powered UI generation
- **API Endpoint**: Server-side route for UI generation via Claude AI
- **Demo Components**: 22 example components showcasing json-render integration

## Capabilities

### Web Routes

The application provides public web pages for documentation and demonstration.

[Web Routes Documentation](./routes.md)

### API Endpoint

Server-side API for AI-powered UI generation using Claude Opus 4.5.

[API Endpoint Documentation](./api-endpoint.md)

### Interactive Demo

Client-side interactive component for demonstrating json-render capabilities.

[Interactive Demo Documentation](./interactive-demo.md)

### Demo Components

Registry of 22 demonstration components implementing json-render interface.

[Demo Components Documentation](./demo-components.md)

### Configuration

Application configuration including dependencies, themes, and build settings.

[Configuration Documentation](./configuration.md)

## Environment Variables

The application uses Vercel AI SDK which may require API keys:

```bash
# For AI generation endpoint
ANTHROPIC_API_KEY=your_api_key_here  # Required for /api/generate endpoint
```

## Key Dependencies

```json { .api }
{
  "@json-render/core": "workspace:*",
  "@json-render/react": "workspace:*",
  "next": "16.1.1",
  "react": "19.2.3",
  "ai": "^6.0.33",
  "zod": "^3.24.0",
  "next-themes": "^0.4.6",
  "tailwindcss": "^4.1.18"
}
```

## Path Aliases

```typescript { .api }
{
  "@/*": "./*"  // Maps to project root
}
```

## Port Configuration

- **Development**: 3000 (configurable via `--port` flag)
- **Production**: Default Next.js port (3000)

## Monorepo Context

This application is part of the json-render monorepo:

```
json-render/
├── packages/
│   ├── core/        → @json-render/core (consumed by this app)
│   └── react/       → @json-render/react (consumed by this app)
├── apps/
│   └── web/         → This application
└── examples/
    └── dashboard/   → Example dashboard app
```

The application consumes but does not export any library functionality.
