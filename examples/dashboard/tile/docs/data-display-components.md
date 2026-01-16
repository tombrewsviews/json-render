# Data Display Components

Data display components render metrics, charts, tables, and lists from data bound via JSON pointer paths. They support various formatting options and are optimized for dashboard visualizations.

## Capabilities

### Metric Component

Displays a single metric value with optional formatting and trend indicator.

```typescript { .api }
/**
 * Metric - Single metric display with trend
 * Location: components/ui/metric.tsx
 */
import { Metric } from "./components/ui";

interface MetricProps {
  label: string; // Metric label (e.g., "Total Revenue")
  valuePath: string; // JSON pointer to value (e.g., "/analytics/revenue")
  format?: "number" | "currency" | "percent" | null; // Value format, default: "number"
  trend?: "up" | "down" | null; // Trend direction indicator
  trendValue?: string | null; // Trend percentage (e.g., "12%")
}
```

**Usage Examples:**

```typescript
import { Metric } from "./components/ui";

// Currency metric with upward trend
<Metric
  label="Total Revenue"
  valuePath="/analytics/revenue"
  format="currency"
  trend="up"
  trendValue="12%"
/>

// Number metric
<Metric
  label="Total Customers"
  valuePath="/analytics/customers"
  format="number"
/>

// Percentage metric with downward trend
<Metric
  label="Conversion Rate"
  valuePath="/analytics/conversionRate"
  format="percent"
  trend="down"
  trendValue="3%"
/>

// Basic metric (no format)
<Metric
  label="Active Sessions"
  valuePath="/analytics/sessions"
/>
```

**Formatting Behavior:**
- `number`: Formats with thousand separators (e.g., 1,234)
- `currency`: Formats as USD currency (e.g., $1,234.56)
- `percent`: Appends % symbol (e.g., 12%)
- `null`: Displays raw value

### Chart Component

Displays bar chart visualization from array data.

```typescript { .api }
/**
 * Chart - Bar chart visualization
 * Location: components/ui/chart.tsx
 */
import { Chart } from "./components/ui";

interface ChartProps {
  type: "bar" | "line" | "pie" | "area"; // Chart type (currently only 'bar' is implemented)
  dataPath: string; // JSON pointer to array data (e.g., "/analytics/salesByRegion")
  title?: string | null; // Chart title
  height?: number | null; // Chart height in pixels, default: 300
}

// Expected data format for bar chart:
// Array<{ label: string, value: number }>
```

**Usage Examples:**

```typescript
import { Chart } from "./components/ui";

// Bar chart with title
<Chart
  type="bar"
  dataPath="/analytics/salesByRegion"
  title="Sales by Region"
  height={350}
/>

// Bar chart without title
<Chart
  type="bar"
  dataPath="/analytics/monthlySales"
  height={250}
/>

// Data format example
const data = {
  analytics: {
    salesByRegion: [
      { label: "North", value: 45000 },
      { label: "South", value: 32000 },
      { label: "East", value: 38000 },
      { label: "West", value: 41000 }
    ]
  }
};
```

**Chart Behavior:**
- Type: Currently only `bar` type is fully implemented
- Data: Expects array of objects with `label` and `value` properties
- Height: Configurable, default is 300px
- Responsive: Adapts to container width
- Colors: Uses consistent color scheme

### Table Component

Displays tabular data with configurable columns and formatting.

```typescript { .api }
/**
 * Table - Tabular data display
 * Location: components/ui/table.tsx
 * Catalog Schema: Does NOT include title prop
 * Implementation: Component supports title prop (not in catalog)
 */
import { Table } from "./components/ui";

// Catalog-defined interface (what AI can generate)
interface TableProps {
  dataPath: string; // JSON pointer to array data (e.g., "/analytics/recentTransactions")
  columns: Array<{
    // Column definitions
    key: string; // Property key in data objects
    label: string; // Column header label
    format?: "text" | "currency" | "date" | "badge" | null; // Value format, default: "text"
  }>;
}

// Note: The component implementation also supports title?: string | null
// but this is NOT in the catalog, so AI cannot generate it
```

**Usage Examples:**

```typescript
import { Table } from "./components/ui";

// Table with multiple column formats (catalog-supported)
<Table
  dataPath="/analytics/recentTransactions"
  columns={[
    { key: "id", label: "ID", format: "text" },
    { key: "customer", label: "Customer", format: "text" },
    { key: "amount", label: "Amount", format: "currency" },
    { key: "status", label: "Status", format: "badge" },
    { key: "date", label: "Date", format: "date" }
  ]}
/>

// Simple table (catalog-supported)
<Table
  dataPath="/users"
  columns={[
    { key: "name", label: "Name" },
    { key: "email", label: "Email" },
    { key: "role", label: "Role", format: "badge" }
  ]}
/>

// Table with title (implementation supports, but NOT in catalog)
// AI generation cannot use this - only manual code can
<Table
  title="Recent Transactions"
  dataPath="/analytics/recentTransactions"
  columns={[...]}
/>

// Data format example
const data = {
  analytics: {
    recentTransactions: [
      {
        id: "TXN-001",
        customer: "Alice Johnson",
        amount: 1250.50,
        status: "completed",
        date: "2024-01-15"
      },
      {
        id: "TXN-002",
        customer: "Bob Smith",
        amount: 750.00,
        status: "pending",
        date: "2024-01-16"
      }
    ]
  }
};
```

**Column Format Behavior:**
- `text`: Displays value as-is
- `currency`: Formats as USD currency ($1,234.56)
- `date`: Formats date string (Jan 15, 2024)
- `badge`: Displays value as a colored badge
- `null`: Displays value as text

### List Component

Renders a list from array data with template-based children.

```typescript { .api }
/**
 * List - List renderer for array data
 * Location: components/ui/list.tsx
 */
import { List } from "./components/ui";

interface ListProps {
  dataPath: string; // JSON pointer to array data (e.g., "/items")
  emptyMessage?: string | null; // Message when list is empty
  children?: React.ReactNode; // Template for each list item
}
```

**Usage Examples:**

```typescript
import { List, Card, Text, Badge } from "./components/ui";

// List with template children
<List dataPath="/analytics/recentTransactions" emptyMessage="No transactions found">
  <Card>
    <Stack direction="horizontal" gap="8px">
      <Text content={{ path: "customer" }} variant="body" />
      <Badge text={{ path: "status" }} />
    </Stack>
  </Card>
</List>

// List of items
<List dataPath="/notifications">
  <Stack direction="vertical" gap="4px">
    <Text content={{ path: "title" }} variant="label" />
    <Text content={{ path: "message" }} variant="caption" />
  </Stack>
</List>

// Empty list
<List dataPath="/emptyArray" emptyMessage="Nothing to display">
  <Card>
    <Text content={{ path: "name" }} />
  </Card>
</List>
```

**List Behavior:**
- Maps over array data at `dataPath`
- Renders children template for each item
- Children can use `{ path: "property" }` to reference item properties
- Shows `emptyMessage` when array is empty
- Each item gets a unique key automatically

## Data Binding Patterns

### JSON Pointer Paths

All data display components use JSON pointer paths (RFC 6901) to reference data:

```typescript { .api }
// JSON pointer format: "/" + path segments joined by "/"

// Root level
valuePath: "/revenue"  // data.revenue

// Nested object
valuePath: "/analytics/revenue"  // data.analytics.revenue

// Array element
valuePath: "/items/0"  // data.items[0]

// Nested in array
valuePath: "/items/0/name"  // data.items[0].name
```

**Example Data Structure:**

```typescript
const data = {
  analytics: {
    revenue: 125000,
    customers: 1200,
    salesByRegion: [
      { label: "North", value: 45000 },
      { label: "South", value: 32000 },
    ],
    recentTransactions: [
      {
        id: "TXN-001",
        customer: "Alice",
        amount: 1250.5,
        status: "completed",
      },
    ],
  },
};

// Accessing values:
// /analytics/revenue → 125000
// /analytics/salesByRegion → [{ label: "North", value: 45000 }, ...]
// /analytics/recentTransactions/0/customer → "Alice"
```

### Using getByPath

Components internally use `getByPath` from `@json-render/core`:

```typescript { .api }
import { getByPath } from "@json-render/core";

// Get value from nested object
const value = getByPath(data, "/analytics/revenue"); // 125000

// Get nested array
const sales = getByPath(data, "/analytics/salesByRegion"); // [...]

// Get array element
const firstTx = getByPath(data, "/analytics/recentTransactions/0"); // { id: "TXN-001", ... }
```

## Dashboard Patterns

### Metrics Grid

Common pattern for displaying multiple metrics:

```typescript
<Grid columns={3} gap="16px">
  <Card>
    <Metric
      label="Total Revenue"
      valuePath="/analytics/revenue"
      format="currency"
      trend="up"
      trendValue="12%"
    />
  </Card>
  <Card>
    <Metric
      label="Total Customers"
      valuePath="/analytics/customers"
      format="number"
      trend="up"
      trendValue="8%"
    />
  </Card>
  <Card>
    <Metric
      label="Total Orders"
      valuePath="/analytics/orders"
      format="number"
      trend="down"
      trendValue="3%"
    />
  </Card>
</Grid>
```

### Chart with Data

Displaying regional sales data:

```typescript
<Card title="Sales Performance" description="Regional breakdown">
  <Chart
    type="bar"
    dataPath="/analytics/salesByRegion"
    height={350}
  />
</Card>
```

### Transaction Table

Displaying recent transactions:

```typescript
<Card title="Recent Transactions" description="Last 10 transactions">
  <Table
    dataPath="/analytics/recentTransactions"
    columns={[
      { key: "id", label: "Transaction ID", format: "text" },
      { key: "customer", label: "Customer", format: "text" },
      { key: "amount", label: "Amount", format: "currency" },
      { key: "status", label: "Status", format: "badge" },
      { key: "date", label: "Date", format: "date" },
    ]}
  />
</Card>
```

### List of Notifications

Using List with template:

```typescript
<Card title="Recent Activity">
  <List dataPath="/notifications" emptyMessage="No recent activity">
    <Stack direction="horizontal" gap="12px">
      <Badge text={{ path: "type" }} />
      <Stack direction="vertical" gap="4px">
        <Text content={{ path: "message" }} variant="body" />
        <Text content={{ path: "timestamp" }} variant="caption" />
      </Stack>
    </Stack>
  </List>
</Card>
```

## Format Options

### Metric Formats

```typescript
// Number format: 1,234
<Metric label="Count" valuePath="/count" format="number" />

// Currency format: $1,234.56
<Metric label="Revenue" valuePath="/revenue" format="currency" />

// Percent format: 12%
<Metric label="Growth" valuePath="/growth" format="percent" />
```

### Table Column Formats

```typescript
columns={[
  // Text: displays as-is
  { key: "name", label: "Name", format: "text" },

  // Currency: $1,234.56
  { key: "amount", label: "Amount", format: "currency" },

  // Date: Jan 15, 2024
  { key: "date", label: "Date", format: "date" },

  // Badge: colored badge based on value
  { key: "status", label: "Status", format: "badge" }
]}
```

## Responsive Behavior

All data display components are responsive:

- **Metric**: Adjusts font size for small screens
- **Chart**: Scales to container width, maintains aspect ratio
- **Table**: Scrollable on small screens, full width on large screens
- **List**: Stacks items vertically, adjusts spacing

## Empty States

### Table Empty State

Tables automatically handle empty data:

```typescript
// When dataPath points to empty array, table shows "No data" message
<Table
  title="Transactions"
  dataPath="/emptyArray"
  columns={[...]}
/>
```

### List Empty State

Lists use custom empty message:

```typescript
<List dataPath="/emptyArray" emptyMessage="No items to display">
  {/* template */}
</List>
```

### Chart Empty State

Charts handle empty data gracefully:

```typescript
// When dataPath points to empty array, chart shows empty state
<Chart type="bar" dataPath="/emptyArray" title="No Data Available" />
```
