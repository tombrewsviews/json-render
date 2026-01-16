# Interactive Demo

Client-side React component providing an interactive demonstration of json-render capabilities.

## Capabilities

### Demo Component

Main interactive component for the home page.

```typescript { .api }
/**
 * Interactive demo component
 * File: components/demo.tsx
 * Type: Client-side React component
 */

import { Renderer, useUIStream, JSONUIProvider } from "@json-render/react";
import type { UITree } from "@json-render/core";

export function Demo(): JSX.Element;
```

### Component Modes

The demo operates in two distinct modes:

```typescript { .api }
/**
 * Demo modes
 */
type DemoMode = "simulation" | "interactive";

interface SimulationMode {
  behavior: "Auto-plays predefined example on page load";
  example: "Create a contact form with name, email, and message";
  phases: ["typing", "streaming", "complete"];
  transitions: "Automatically transitions to interactive mode when complete";
}

interface InteractiveMode {
  behavior: "User enters custom prompts";
  apiEndpoint: "/api/generate";
  maxPromptLength: 140;  // Characters
  streaming: true;
  rendering: "Progressive";
}
```

### Simulation Mode Features

Auto-play demonstration with animations:

```typescript { .api }
/**
 * Simulation mode behavior
 */
interface SimulationMode {
  typingPhase: {
    effect: "Character-by-character typing animation";
    speed: "20ms per character";
    prompt: "Create a contact form with name, email, and message";
    transition: "Waits 500ms then moves to streaming phase";
  };

  streamingPhase: {
    effect: "Progressive JSONL patch simulation";
    stages: 5;  // Pre-defined stages
    interval: "600ms between stages";
    patches: [
      { tree: "Empty card created", stream: 'Set root' },
      { tree: "Card with name input", stream: 'Add name input' },
      { tree: "Card with name and email", stream: 'Add email input' },
      { tree: "Card with name, email, message", stream: 'Add textarea' },
      { tree: "Complete form with submit", stream: 'Add submit button' }
    ];
    transition: "Waits 500ms then moves to interactive mode";
  };

  visualization: {
    tabbedDisplay: ["json", "stream", "code"];
    liveRendering: "Right panel shows progressive UI updates";
    stopButton: "User can interrupt and switch to interactive mode";
  };
}
```

### Interactive Mode Features

User-controlled generation with real API calls:

```typescript { .api }
/**
 * Interactive mode behavior
 */
interface InteractiveMode {
  promptInput: {
    type: "text";
    placeholder: "Describe what you want to build...";
    maxLength: 140;
    disabled: "When isStreaming is true";
    submission: "Enter key or submit button";
  };

  apiIntegration: {
    hook: "useUIStream from @json-render/react";
    endpoint: "/api/generate";
    method: "POST";
    errorHandling: "onError callback logs to console";
  };

  streaming: {
    progressive: true;
    visualization: "Live stream lines in 'stream' tab";
    rendering: "Progressive UI updates as patches arrive";
    stopButton: "User can stop generation mid-stream";
  };

  state: {
    tree: "UITree from useUIStream hook";
    isStreaming: "Boolean streaming state";
    send: "Function to submit prompt";
    clear: "Function to reset state";
  };
}
```

### Tabbed Display

Three-tab interface showing different views:

```typescript { .api }
/**
 * Tabbed display interface
 */
type TabType = "stream" | "json" | "code";

interface TabbedDisplay {
  stream: {
    content: "JSONL patch operations";
    format: "One line per patch";
    highlighting: "Syntax highlighted via CodeBlock component";
    loading: "Shows loading dots when streaming";
  };

  json: {
    content: "Current UITree structure";
    format: "Pretty-printed JSON";
    highlighting: "Syntax highlighted via CodeBlock component";
    updates: "Updates as patches arrive";
  };

  code: {
    content: "Example integration code";
    format: "TypeScript/React code";
    highlighting: "Syntax highlighted via CodeBlock component";
    static: "Does not change during demo";
  };
}
```

**Example Code Tab Content:**

```typescript { .api }
const EXAMPLE_CODE = `
import { Renderer, useUIStream } from '@json-render/react';
import { registry } from './registry';

function App() {
  const { tree, isStreaming, send } = useUIStream({
    api: '/api/generate',
  });

  return (
    <Renderer
      tree={tree}
      registry={registry}
      loading={isStreaming}
    />
  );
}
`;
```

### Rendering Panel

Live preview of generated UI:

```typescript { .api }
/**
 * Rendering panel features
 */
interface RenderingPanel {
  container: {
    dimensions: "Fixed height (h-96 / 384px)";
    scrolling: "overflow-auto";
    border: "border border-border rounded";
    background: "bg-background";
  };

  content: {
    provider: "JSONUIProvider wraps Renderer";
    renderer: "Renderer from @json-render/react";
    registry: "demoRegistry with 22 components";
    fallback: "fallbackComponent for unknown types";
    loading: "Loading state during streaming";
  };

  fullscreen: {
    toggle: "Maximize button in top-right";
    modal: "Fixed full-screen overlay";
    close: "X button to exit fullscreen";
  };

  empty: {
    message: 'isStreaming ? "generating..." : "waiting..."';
    styling: "text-muted-foreground/50";
  };
}
```

### Action Handling

Demo buttons trigger toast notifications:

```typescript { .api }
/**
 * Demo action handler
 */
interface DemoActionHandler {
  global: {
    window.__demoAction: (text: string) => void;
    purpose: "Expose action handler to registry components";
    lifecycle: "Created on mount, cleaned up on unmount";
  };

  behavior: {
    trigger: "Button component calls window.__demoAction(actionText)";
    result: "Toast notification via sonner library";
  };
}

// Usage in Button component
function handleClick() {
  const actionText = props.actionText || props.label;
  window.__demoAction?.(actionText);
}
```

### State Management

Demo uses React hooks for state:

```typescript { .api }
/**
 * Demo component state
 */
interface DemoState {
  // Mode and phase
  mode: "simulation" | "interactive";
  phase: "typing" | "streaming" | "complete";

  // Prompt state
  typedPrompt: string;     // For simulation typing animation
  userPrompt: string;      // For interactive user input

  // Streaming state
  stageIndex: number;      // Current simulation stage
  streamLines: string[];   // Accumulated stream lines

  // UI state
  activeTab: "stream" | "json" | "code";
  simulationTree: UITree | null;  // Simulation tree state
  isFullscreen: boolean;

  // API state (from useUIStream hook)
  tree: UITree;            // Current UI tree
  isStreaming: boolean;    // Streaming status
  send: (prompt: string) => Promise<void>;
  clear: () => void;
}
```

### Refs and Effects

Component uses refs and effects:

```typescript { .api }
/**
 * Demo refs and effects
 */
interface DemoRefsAndEffects {
  refs: {
    inputRef: "HTMLInputElement for focus management";
  };

  effects: {
    typingEffect: {
      dependencies: ["mode", "phase"];
      behavior: "Animates typing in simulation mode";
      interval: "20ms per character";
      cleanup: "Clears interval on unmount";
    };

    streamingEffect: {
      dependencies: ["mode", "phase"];
      behavior: "Simulates JSONL streaming in simulation mode";
      interval: "600ms between stages";
      cleanup: "Clears interval on unmount";
    };

    streamTrackingEffect: {
      dependencies: ["mode", "apiTree", "streamLines"];
      behavior: "Tracks real API stream lines in interactive mode";
      updates: "Appends new stream lines";
    };

    actionHandlerEffect: {
      dependencies: [];
      behavior: "Exposes window.__demoAction on mount";
      cleanup: "Deletes window.__demoAction on unmount";
    };
  };
}
```

### Integration with json-render

Demo demonstrates json-render library usage:

```typescript { .api }
/**
 * json-render integration
 */
import { Renderer, useUIStream, JSONUIProvider } from "@json-render/react";
import type { UITree } from "@json-render/core";

interface JsonRenderIntegration {
  hooks: {
    useUIStream: {
      config: {
        api: "/api/generate";
        onError: (err: Error) => void;
      };
      returns: {
        tree: UITree;
        isStreaming: boolean;
        send: (prompt: string) => Promise<void>;
        clear: () => void;
      };
    };
  };

  components: {
    JSONUIProvider: {
      props: {
        registry: "ComponentRegistry";
      };
      purpose: "Provides component context";
    };

    Renderer: {
      props: {
        tree: UITree;
        registry: "ComponentRegistry";
        loading: boolean;
        fallback: "ComponentType for unknown types";
      };
      purpose: "Renders UI tree";
    };
  };

  types: {
    UITree: {
      root: "string | null";  // Root element key
      elements: "Record<string, Element>";
    };
  };
}
```

### Demo Component Registry

Uses internal demo registry:

```typescript { .api }
/**
 * Demo component registry
 */
import { demoRegistry, fallbackComponent } from "./demo/index";

interface DemoRegistryUsage {
  registry: {
    source: "components/demo/index.ts";
    components: 22;
    type: "ComponentRegistry";
  };

  fallback: {
    source: "components/demo/fallback.tsx";
    purpose: "Handles unknown component types";
    display: "Shows error message with component type";
  };
}
```

### Interactive State Hook

Demo components use interactive state:

```typescript { .api }
/**
 * Interactive state hook for Select components
 */
import { useInteractiveState } from "./demo/utils";

interface InteractiveStateHook {
  purpose: "Initialize interactive state for Select dropdowns";
  usage: "Called once in Demo component";
  effect: "Enables Select components to maintain selected value";
}

// In Demo component
useInteractiveState();
```

### Accessibility Features

Demo includes accessibility features:

```typescript { .api }
/**
 * Accessibility features
 */
interface AccessibilityFeatures {
  buttons: {
    ariaLabel: "Submit, Stop, Maximize, Close buttons have aria-label";
    keyboard: "Enter key submits prompt";
  };

  focus: {
    management: "inputRef focuses on click or mode switch";
    visible: "Focus styles via Tailwind";
  };

  semantics: {
    form: "Proper form element for prompt submission";
    headings: "Semantic heading structure";
  };
}
```

### Styling and Theming

Demo respects application theme:

```typescript { .api }
/**
 * Styling and theming
 */
interface StylingAndTheming {
  theme: {
    provider: "ThemeProvider wraps app";
    modes: ["light", "dark"];
    toggle: "ThemeToggle component in header";
  };

  tailwind: {
    classes: "Extensive Tailwind utility classes";
    responsiveness: "Mobile-first responsive design";
    colors: "Theme-aware color variables";
  };

  animations: {
    fadeIn: "animate-in fade-in duration-200";
    pulse: "animate-pulse for typing cursor";
    loadingDots: "Staggered animation-delay on dots";
  };
}
```

### Example Prompts

Demo suggests example prompts:

```typescript { .api }
/**
 * Example prompts shown to user
 */
const EXAMPLE_PROMPTS = [
  "Create a login form",
  "Build a feedback form with rating"
];

// Displayed below prompt input:
// "Try: 'Create a login form' or 'Build a feedback form with rating'"
```

### Toast Notifications

Uses Sonner for toast notifications:

```typescript { .api }
/**
 * Toast notifications
 */
import { toast } from "sonner";
import { Toaster } from "./ui/sonner";

interface ToastIntegration {
  component: {
    Toaster: {
      position: "bottom-right";
      rendered: "In render panel";
    };
  };

  trigger: {
    action: "Button clicks in demo components";
    handler: "window.__demoAction(text)";
    display: "toast(text)";
  };
}
```
