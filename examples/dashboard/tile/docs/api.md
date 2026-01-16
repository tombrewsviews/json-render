# AI Generation API

The AI generation API endpoint streams AI-generated UI components using Claude Opus 4.5. It accepts natural language prompts and context data, then streams back JSONL (JSON Lines) patches that progressively build a UI tree.

## Capabilities

### POST /api/generate

Endpoint for streaming AI-generated UI component definitions.

```typescript { .api }
// API Route: POST /api/generate
// Location: app/api/generate/route.ts

// Request body
interface GenerateRequest {
  prompt: string; // Natural language prompt describing desired UI
  context?: {
    // Optional context for generation
    data: any; // Current application data for context-aware generation
  };
}

// Response: Streaming text response (Content-Type: text/plain)
// Format: JSONL (JSON Lines) - each line is a JSON patch operation

interface JSONLPatch {
  op: "set" | "add"; // Operation type
  path: string; // JSON pointer path in UI tree
  value: any; // Value to set/add
}

// Example patches:
// {"op":"set","path":"/root","value":"card-1"}
// {"op":"add","path":"/elements/card-1","value":{"key":"card-1","type":"Card","props":{"title":"Revenue"},"children":["metric-1"]}}
// {"op":"add","path":"/elements/metric-1","value":{"key":"metric-1","type":"Metric","props":{"label":"Total","valuePath":"/analytics/revenue","format":"currency"}}}
```

**Usage Example:**

```typescript
// Making a request to the generation endpoint
const response = await fetch("/api/generate", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    prompt: "Show revenue metrics with a trend indicator",
    context: {
      data: {
        analytics: {
          revenue: 125000,
          growth: 12.5,
        },
      },
    },
  }),
});

// Process streaming response
const reader = response.body.getReader();
const decoder = new TextDecoder();

while (true) {
  const { done, value } = await reader.read();
  if (done) break;

  const chunk = decoder.decode(value);
  const lines = chunk.split("\n").filter((line) => line.trim());

  for (const line of lines) {
    const patch = JSON.parse(line);
    // Apply patch to UI tree
    console.log(patch); // { op: 'add', path: '/elements/...', value: {...} }
  }
}
```

### Route Configuration

```typescript { .api }
// Exported from app/api/generate/route.ts

// Maximum execution time (30 seconds)
export const maxDuration = 30;

// Route handler
export async function POST(req: Request): Promise<Response>;
```

**Implementation Details:**

```typescript
import { streamText } from "ai";
import { componentList } from "@/lib/catalog";

export const maxDuration = 30;

export async function POST(req: Request) {
  const { prompt, context } = await req.json();

  // Build full prompt with context
  let fullPrompt = prompt;
  if (context?.data) {
    fullPrompt += `\n\nAVAILABLE DATA:\n${JSON.stringify(context.data, null, 2)}`;
  }

  // Stream UI generation using Claude Opus 4.5
  const result = streamText({
    model: 'anthropic/claude-opus-4.5',
    system: SYSTEM_PROMPT, // Includes component catalog and format rules
    prompt: fullPrompt,
    temperature: 0.7,
  });

  return result.toTextStreamResponse();
}
```

### Using with useUIStream Hook

The endpoint is typically consumed using the `useUIStream` hook from `@json-render/react`:

```typescript { .api }
import { useUIStream } from "@json-render/react";

interface UseUIStreamOptions {
  api: string; // API endpoint URL
  onError?: (error: Error) => void; // Error callback
}

interface UseUIStreamReturn {
  tree: UITree | null; // Current UI tree state
  isStreaming: boolean; // Whether streaming is in progress
  error: Error | null; // Error if any
  send: (prompt: string, context?: any) => Promise<void>; // Send prompt
  clear: () => void; // Clear current tree
}

function useUIStream(options: UseUIStreamOptions): UseUIStreamReturn;
```

**Usage Example:**

```typescript
import { useUIStream } from "@json-render/react";
import { useState } from "react";

function DashboardGenerator() {
  const [prompt, setPrompt] = useState("");

  const { tree, isStreaming, error, send, clear } = useUIStream({
    api: "/api/generate",
    onError: (err) => console.error("Generation error:", err),
  });

  const handleGenerate = async () => {
    await send(prompt, {
      data: {
        analytics: { revenue: 50000, customers: 1200 },
      },
    });
  };

  return (
    <div>
      <input value={prompt} onChange={(e) => setPrompt(e.target.value)} />
      <button onClick={handleGenerate} disabled={isStreaming}>
        {isStreaming ? "Generating..." : "Generate Dashboard"}
      </button>
      <button onClick={clear}>Clear</button>

      {error && <div>Error: {error.message}</div>}

      <Renderer tree={tree} registry={componentRegistry} loading={isStreaming} />
    </div>
  );
}
```

## UI Tree Structure

The patches build up a UI tree structure:

```typescript { .api }
interface UITree {
  root: string; // Key of root element
  elements: Record<string, UIElement>; // Map of element key to element
}

interface UIElement {
  key: string; // Unique element identifier
  type: string; // Component type (e.g., 'Card', 'Metric')
  props: Record<string, any>; // Component props
  children?: string[]; // Array of child element keys
}
```

**Example UI Tree:**

```json
{
  "root": "card-1",
  "elements": {
    "card-1": {
      "key": "card-1",
      "type": "Card",
      "props": {
        "title": "Analytics Dashboard",
        "description": "Real-time metrics"
      },
      "children": ["grid-1"]
    },
    "grid-1": {
      "key": "grid-1",
      "type": "Grid",
      "props": { "columns": 2 },
      "children": ["metric-1", "metric-2"]
    },
    "metric-1": {
      "key": "metric-1",
      "type": "Metric",
      "props": {
        "label": "Revenue",
        "valuePath": "/analytics/revenue",
        "format": "currency"
      }
    },
    "metric-2": {
      "key": "metric-2",
      "type": "Metric",
      "props": {
        "label": "Customers",
        "valuePath": "/analytics/customers",
        "format": "number"
      }
    }
  }
}
```

## Patch Operations

### Set Operation

Sets a value at a specific path in the UI tree.

```typescript { .api }
interface SetPatch {
  op: "set";
  path: string; // JSON pointer path
  value: any; // New value
}

// Example: Set root element
{
  "op": "set",
  "path": "/root",
  "value": "card-1"
}
```

### Add Operation

Adds a new element to the UI tree.

```typescript { .api }
interface AddPatch {
  op: "add";
  path: string; // JSON pointer path (typically /elements/{key})
  value: UIElement; // Element to add
}

// Example: Add a Card element
{
  "op": "add",
  "path": "/elements/card-1",
  "value": {
    "key": "card-1",
    "type": "Card",
    "props": { "title": "Dashboard" },
    "children": ["metric-1"]
  }
}
```

## Error Handling

```typescript { .api }
// Common errors

// 1. Missing API key
// HTTP 500: AI_GATEWAY_API_KEY environment variable not set

// 2. Invalid request body
// HTTP 400: Missing required field 'prompt'

// 3. AI generation timeout
// HTTP 504: Request exceeded maxDuration (30 seconds)

// 4. AI generation error
// HTTP 500: Error from AI provider
```

**Error Handling Example:**

```typescript
const { error, send } = useUIStream({
  api: "/api/generate",
  onError: (err) => {
    if (err.message.includes("API key")) {
      console.error("API key not configured");
    } else if (err.message.includes("timeout")) {
      console.error("Generation took too long");
    } else {
      console.error("Generation failed:", err);
    }
  },
});

// Or check error state directly
if (error) {
  return <div>Error: {error.message}</div>;
}
```

## Environment Configuration

```typescript { .api }
// Required environment variable for AI SDK gateway
AI_GATEWAY_API_KEY: string;

// Set in .env or .env.local:
// AI_GATEWAY_API_KEY=your_anthropic_or_gateway_api_key
```

**Note:** The endpoint uses the Vercel AI SDK which reads the API key from the environment automatically when using provider-prefixed model names like `'anthropic/claude-opus-4.5'`.

## AI Model Configuration

The endpoint uses Claude Opus 4.5 via the Vercel AI SDK:

```typescript { .api }
// Model configuration in streamText call
{
  model: 'anthropic/claude-opus-4.5', // Provider/model string format
  system: SYSTEM_PROMPT,                // System prompt with catalog + rules
  temperature: 0.7,                      // Creativity level (0-1)
  // maxDuration is set at route level (30 seconds)
}
```

**System Prompt:** The endpoint includes a comprehensive system prompt that:
- Lists all 16 available components from the catalog
- Provides detailed component schemas with prop examples
- Explains data binding patterns (valuePath, dataPath)
- Specifies JSONL patch output format with examples
- Includes a complete example of generating a revenue dashboard

## Generation Context

The optional `context` parameter provides data to the AI for context-aware generation:

```typescript { .api }
// Context structure
interface GenerationContext {
  data: any; // Current application state
}

// Example: Provide analytics data
const context = {
  data: {
    analytics: {
      revenue: 125000,
      growth: 12.5,
      customers: 1200,
      orders: 450,
    },
  },
};

await send("Show revenue with trend", context);
```

The AI can reference this data when generating components:
- Creates `valuePath` props pointing to actual data
- Suggests appropriate formats based on data types
- Includes relevant metrics based on available data
