# API Endpoint

Server-side API route for AI-powered UI generation using Claude Opus 4.5.

## Capabilities

### UI Generation Endpoint

Generates UI components from natural language prompts using AI, constrained to a predefined component catalog.

```typescript { .api }
/**
 * POST /api/generate
 *
 * Generates JSONL UI patches from a natural language prompt
 * using Claude Opus 4.5 with guardrails.
 */

// File: app/api/generate/route.ts
// Runtime: Edge/Serverless
// Max Duration: 30 seconds

export const maxDuration = 30;

interface GenerateRequest {
  prompt: string;  // User prompt (max 140 characters)
}

interface GenerateResponse {
  // Streaming text response in JSONL format
  // Content-Type: text/plain; charset=utf-8
  // Each line is a JSON patch operation
}
```

**Usage Example:**

```typescript
// Client-side request
const response = await fetch('/api/generate', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    prompt: 'Create a login form with email and password'
  }),
});

// Response is a streaming text response
const reader = response.body?.getReader();
const decoder = new TextDecoder();

while (true) {
  const { done, value } = await reader.read();
  if (done) break;

  const text = decoder.decode(value);
  // Process JSONL lines
  const lines = text.split('\n').filter(line => line.trim());
  for (const line of lines) {
    const patch = JSON.parse(line);
    // Apply patch to UI tree
  }
}
```

### Request Processing

The endpoint processes requests with the following flow:

```typescript { .api }
/**
 * Request processing pipeline
 */
interface RequestProcessing {
  input: {
    prompt: string;
    maxLength: 140;  // Characters
  };
  sanitization: {
    method: "String(prompt || '').slice(0, 140)";
    purpose: "Prevent excessive prompts";
  };
  aiModel: {
    provider: "anthropic";
    model: "claude-opus-4.5";
    temperature: 0.7;
  };
  systemPrompt: {
    purpose: "Define available components and output format";
    components: 22;
    format: "JSONL (JSON Lines)";
  };
  output: {
    type: "streaming";
    format: "text/plain";
    encoding: "utf-8";
  };
}
```

### System Prompt Components

The endpoint constrains AI output to 22 predefined components:

```typescript { .api }
/**
 * Available components defined in system prompt
 */
interface AvailableComponents {
  layout: {
    Card: {
      props: {
        title?: string;
        description?: string;
        maxWidth?: "sm" | "md" | "lg" | "full";
        centered?: boolean;
      };
      hasChildren: true;
      purpose: "Container card for content sections";
    };
    Stack: {
      props: {
        direction?: "horizontal" | "vertical";
        gap?: "sm" | "md" | "lg";
      };
      hasChildren: true;
      purpose: "Flex container";
    };
    Grid: {
      props: {
        columns?: 2 | 3 | 4;
        gap?: "sm" | "md" | "lg";
      };
      hasChildren: true;
      purpose: "Grid layout (mobile-first)";
    };
    Divider: {
      props: {};
      hasChildren: false;
      purpose: "Horizontal separator line";
    };
  };

  formInputs: {
    Input: {
      props: {
        label: string;
        name: string;
        type?: "text" | "email" | "password" | "number";
        placeholder?: string;
      };
      hasChildren: false;
    };
    Textarea: {
      props: {
        label: string;
        name: string;
        placeholder?: string;
        rows?: number;
      };
      hasChildren: false;
    };
    Select: {
      props: {
        label: string;
        name: string;
        options: string[];
        placeholder?: string;
      };
      hasChildren: false;
    };
    Checkbox: {
      props: {
        label: string;
        name: string;
        checked?: boolean;
      };
      hasChildren: false;
    };
    Radio: {
      props: {
        label: string;
        name: string;
        options: string[];
      };
      hasChildren: false;
    };
    Switch: {
      props: {
        label: string;
        name: string;
        checked?: boolean;
      };
      hasChildren: false;
    };
  };

  actions: {
    Button: {
      props: {
        label: string;
        variant?: "primary" | "secondary" | "danger";
        actionText?: string;  // Shown in toast on click
      };
      hasChildren: false;
    };
    Link: {
      props: {
        label: string;
        href: string;
      };
      hasChildren: false;
    };
  };

  typography: {
    Heading: {
      props: {
        text: string;
        level?: 1 | 2 | 3 | 4;  // h1-h4
      };
      hasChildren: false;
    };
    Text: {
      props: {
        content: string;
        variant?: "body" | "caption" | "muted";
      };
      hasChildren: false;
    };
  };

  dataDisplay: {
    Image: {
      props: {
        src: string;
        alt: string;
        width?: number;
        height?: number;
      };
      hasChildren: false;
    };
    Avatar: {
      props: {
        src?: string;
        name: string;  // For fallback initials
        size?: "sm" | "md" | "lg";
      };
      hasChildren: false;
    };
    Badge: {
      props: {
        text: string;
        variant?: "default" | "success" | "warning" | "danger";
      };
      hasChildren: false;
    };
    Alert: {
      props: {
        title: string;
        message?: string;
        type?: "info" | "success" | "warning" | "error";
      };
      hasChildren: false;
    };
    Progress: {
      props: {
        value: number;  // 0-100
        max?: number;
        label?: string;
      };
      hasChildren: false;
    };
    Rating: {
      props: {
        value: number;
        max?: number;
        label?: string;
      };
      hasChildren: false;
    };
  };

  charts: {
    BarGraph: {
      props: {
        title?: string;
        data: Array<{ label: string; value: number }>;
      };
      hasChildren: false;
    };
    LineGraph: {
      props: {
        title?: string;
        data: Array<{ label: string; value: number }>;
      };
      hasChildren: false;
    };
  };
}

/**
 * All components support className for custom Tailwind styling
 */
interface UniversalProps {
  className?: string[];  // Array of Tailwind classes
}
```

### JSONL Output Format

The endpoint returns JSONL (JSON Lines) with patch operations:

```typescript { .api }
/**
 * JSONL patch operations
 */
type JSONLPatch =
  | {
      op: "set";
      path: "/root";
      value: string;  // Root element key
    }
  | {
      op: "add";
      path: `/elements/${string}`;  // /elements/{key}
      value: {
        key: string;
        type: string;  // Component type name
        props: Record<string, unknown>;
        children?: string[];  // Array of child element keys
      };
    };
```

**Example Output:**

```json
{"op":"set","path":"/root","value":"card"}
{"op":"add","path":"/elements/card","value":{"key":"card","type":"Card","props":{"title":"Contact Us","maxWidth":"md"},"children":["name","email","message","submit"]}}
{"op":"add","path":"/elements/name","value":{"key":"name","type":"Input","props":{"label":"Name","name":"name"}}}
{"op":"add","path":"/elements/email","value":{"key":"email","type":"Input","props":{"label":"Email","name":"email","type":"email"}}}
{"op":"add","path":"/elements/message","value":{"key":"message","type":"Textarea","props":{"label":"Message","name":"message","rows":4}}}
{"op":"add","path":"/elements/submit","value":{"key":"submit","type":"Button","props":{"label":"Send Message","variant":"primary"}}}
```

### System Prompt Rules

The system prompt enforces strict rules:

```typescript { .api }
/**
 * System prompt rules enforced by AI
 */
interface SystemPromptRules {
  outputFormat: {
    rule1: "First line sets /root to root element key";
    rule2: "Add elements with /elements/{key}";
    rule3: "Children array contains string keys, not objects";
    rule4: "Parent first, then children";
    rule5: "Each element needs: key, type, props";
    rule6: "Use className for custom Tailwind styling when needed";
  };

  forbiddenClasses: [
    "min-h-screen",
    "h-screen",
    "min-h-full",
    "h-full",
    "min-h-dvh",
    "h-dvh",
    "bg-gray-50",
    "bg-slate-50"
  ];

  responsiveDesign: {
    principle: "Mobile-first";
    gridPattern: "columns:1 prop, add className for larger screens";
    example: 'className:["sm:grid-cols-2","md:grid-cols-3"]';
  };

  layoutRules: {
    pageHeaders: "Use Stack with Heading directly, NOT inside Card";
    forms: "Card should be root element for login/signup/contact forms";
    horizontalStacks: "Use className:['flex-wrap'] to prevent overflow";
  };
}
```

### Integration with Vercel AI SDK

The endpoint uses Vercel AI SDK for streaming:

```typescript { .api }
/**
 * Vercel AI SDK integration
 */
import { streamText } from 'ai';

async function POST(req: Request) {
  const { prompt } = await req.json();

  const sanitizedPrompt = String(prompt || '').slice(0, 140);

  const result = streamText({
    model: 'anthropic/claude-opus-4.5',
    system: SYSTEM_PROMPT,  // Contains component definitions
    prompt: sanitizedPrompt,
    temperature: 0.7,
  });

  return result.toTextStreamResponse();
}
```

### Error Handling

The endpoint includes basic error handling:

```typescript { .api }
/**
 * Error scenarios
 */
interface ErrorHandling {
  emptyPrompt: {
    handling: "Converted to empty string";
    result: "AI receives empty prompt";
  };
  longPrompt: {
    handling: "Truncated to 140 characters";
    result: "Trimmed prompt sent to AI";
  };
  aiError: {
    handling: "Handled by Vercel AI SDK";
    result: "Stream terminates with error";
  };
  timeout: {
    threshold: "30 seconds";
    handling: "Serverless function timeout";
  };
}
```

### Environment Requirements

```typescript { .api }
/**
 * Required environment variables
 */
interface EnvironmentVariables {
  ANTHROPIC_API_KEY: {
    required: true;
    purpose: "Authenticate with Anthropic API for Claude access";
    source: "Vercel AI SDK automatically reads this";
  };
}
```

**Setup:**

```bash
# .env.local
ANTHROPIC_API_KEY=your_api_key_here
```

### Rate Limiting

No built-in rate limiting is implemented. Consider adding:

- Per-IP rate limiting
- Per-session rate limiting
- Request queuing for high traffic

### Security Considerations

```typescript { .api }
/**
 * Security measures
 */
interface SecurityMeasures {
  promptSanitization: "Max 140 characters";
  modelConstraints: "System prompt limits output to 22 components";
  outputValidation: "json-render validates against catalog";
  noUserContent: "Generated UI cannot execute arbitrary code";
}
```

### Performance Characteristics

```typescript { .api }
/**
 * Performance metrics
 */
interface PerformanceMetrics {
  coldStart: "Edge function cold start ~100-500ms";
  firstToken: "Claude Opus typically ~500-1000ms";
  streaming: "Progressive JSONL patches ~50-200ms between patches";
  totalDuration: "Typically 2-5 seconds for simple UIs";
  maxDuration: "30 seconds (enforced)";
}
```
