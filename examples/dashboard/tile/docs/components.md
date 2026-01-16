# Component Registry

The component registry is the central mapping of component names to React component implementations. It enables dynamic component instantiation from JSON UI definitions.

## Capabilities

### Component Registry Object

The default export from `./components/ui/index.ts` that maps component names to implementations.

```typescript { .api }
import componentRegistry from "./components/ui";

// Registry type
const componentRegistry: Record<string, React.ComponentType<any>>;

// Registry contains mappings for all 17 components:
// {
//   Alert: AlertComponent,
//   Badge: BadgeComponent,
//   Button: ButtonComponent,
//   Card: CardComponent,
//   Chart: ChartComponent,
//   DatePicker: DatePickerComponent,
//   Divider: DividerComponent,
//   Empty: EmptyComponent,
//   Grid: GridComponent,
//   Heading: HeadingComponent,
//   List: ListComponent,
//   Metric: MetricComponent,
//   Select: SelectComponent,
//   Stack: StackComponent,
//   Table: TableComponent,
//   Text: TextComponent,
//   TextField: TextFieldComponent
// }
```

### Individual Component Imports

All components can also be imported individually as named exports.

```typescript { .api }
import {
  Alert,
  Badge,
  Button,
  Card,
  Chart,
  DatePicker,
  Divider,
  Empty,
  Grid,
  Heading,
  List,
  Metric,
  Select,
  Stack,
  Table,
  Text,
  TextField,
} from "./components/ui";
```

**Usage Example:**

```typescript
import { Card, Metric } from "./components/ui";

function Dashboard() {
  return (
    <Card title="Revenue">
      <Metric
        label="Total Revenue"
        valuePath="/analytics/revenue"
        format="currency"
      />
    </Card>
  );
}
```

### Component Categories

The 17 components are organized into five categories:

**Layout Components (3):**
- `Card` - Container with optional title and description
- `Grid` - Grid layout with configurable columns
- `Stack` - Flex container for stacking

**Data Display Components (4):**
- `Metric` - Single metric with trend indicator
- `Chart` - Bar chart visualization
- `Table` - Tabular data display
- `List` - List renderer for arrays

**Interactive Components (4):**
- `Button` - Clickable button with actions
- `TextField` - Text input with validation (exists in registry but NOT in catalog, cannot be AI-generated)
- `Select` - Dropdown select
- `DatePicker` - Date picker input

**Typography Components (2):**
- `Heading` - Section headings (h1-h4)
- `Text` - Text paragraphs

**Status Components (4):**
- `Badge` - Status badge
- `Alert` - Alert banner
- `Divider` - Visual divider
- `Empty` - Empty state placeholder

### Using the Registry with Renderer

The component registry is typically used with the `Renderer` component from `@json-render/react`:

```typescript { .api }
import { Renderer } from "@json-render/react";
import componentRegistry from "./components/ui";

interface RendererProps {
  tree: UITree | null;
  registry: Record<string, React.ComponentType<any>>;
  loading?: boolean;
}
```

**Usage Example:**

```typescript
import { Renderer } from "@json-render/react";
import componentRegistry from "./components/ui";

function App({ uiTree }) {
  return <Renderer tree={uiTree} registry={componentRegistry} />;
}
```

The Renderer:
1. Takes a UI tree (JSON structure)
2. Uses the registry to resolve component types
3. Instantiates components with their props
4. Renders the complete UI hierarchy

## Component File Locations

All component implementations are located in the `components/ui/` directory:

```typescript { .api }
// File structure
components/ui/
├── index.ts              // Registry export
├── alert.tsx             // Alert component
├── badge.tsx             // Badge component
├── button.tsx            // Button component
├── card.tsx              // Card component
├── chart.tsx             // Chart component
├── date-picker.tsx       // DatePicker component
├── divider.tsx           // Divider component
├── empty.tsx             // Empty component
├── grid.tsx              // Grid component
├── heading.tsx           // Heading component
├── list.tsx              // List component
├── metric.tsx            // Metric component
├── select.tsx            // Select component
├── stack.tsx             // Stack component
├── table.tsx             // Table component
├── text.tsx              // Text component
└── text-field.tsx        // TextField component
```
