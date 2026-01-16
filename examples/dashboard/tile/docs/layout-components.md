# Layout Components

Layout components provide container and structural elements for organizing dashboard content. They support children and enable responsive, flexible layouts.

## Capabilities

### Card Component

Container component with optional title, description, and configurable padding.

```typescript { .api }
/**
 * Card - Container with optional header
 * Location: components/ui/card.tsx
 */
import { Card } from "./components/ui";

interface CardProps {
  title?: string | null; // Card title displayed at top
  description?: string | null; // Subtitle text below title
  padding?: string | null; // Custom padding (e.g., '16px', '1rem')
  children?: React.ReactNode; // Card content
}
```

**Usage Examples:**

```typescript
import { Card, Metric } from "./components/ui";

// Card with title and description
<Card title="Revenue Overview" description="Last 30 days">
  <Metric label="Total Revenue" valuePath="/analytics/revenue" format="currency" />
</Card>

// Card with custom padding
<Card title="Dashboard" padding="24px">
  {/* content */}
</Card>

// Card without header
<Card>
  {/* content */}
</Card>
```

### Grid Component

Grid layout component with configurable columns and gap spacing.

```typescript { .api }
/**
 * Grid - Responsive grid layout
 * Location: components/ui/grid.tsx
 */
import { Grid } from "./components/ui";

interface GridProps {
  columns?: number | null; // Number of columns (1-4), default varies by screen size
  gap?: string | null; // Gap between grid items (e.g., '16px', '1rem')
  children?: React.ReactNode; // Grid items
}
```

**Usage Examples:**

```typescript
import { Grid, Card, Metric } from "./components/ui";

// 2-column grid
<Grid columns={2} gap="16px">
  <Card title="Revenue">
    <Metric label="Total" valuePath="/analytics/revenue" format="currency" />
  </Card>
  <Card title="Growth">
    <Metric label="Growth Rate" valuePath="/analytics/growth" format="percent" />
  </Card>
</Grid>

// 3-column grid with custom gap
<Grid columns={3} gap="24px">
  <Metric label="Revenue" valuePath="/analytics/revenue" format="currency" />
  <Metric label="Customers" valuePath="/analytics/customers" format="number" />
  <Metric label="Orders" valuePath="/analytics/orders" format="number" />
</Grid>

// Auto columns (responsive)
<Grid>
  {/* content */}
</Grid>
```

**Grid Behavior:**
- Default columns: Responsive based on screen size
- Column range: 1-4 columns
- Items wrap to next row automatically
- Equal column widths
- Configurable gap spacing

### Stack Component

Flex container for horizontal or vertical stacking with alignment control.

```typescript { .api }
/**
 * Stack - Flex container for stacking elements
 * Location: components/ui/stack.tsx
 */
import { Stack } from "./components/ui";

interface StackProps {
  direction?: "horizontal" | "vertical" | null; // Stack direction, default: 'vertical'
  gap?: string | null; // Gap between items (e.g., '8px', '0.5rem')
  align?: "start" | "center" | "end" | null; // Cross-axis alignment, default: 'start'
  children?: React.ReactNode; // Stack items
}
```

**Usage Examples:**

```typescript
import { Stack, Button, Text } from "./components/ui";

// Vertical stack (default)
<Stack direction="vertical" gap="16px">
  <Text content="Dashboard Actions" variant="label" />
  <Button label="Export Report" action={{ name: "export_report" }} />
  <Button label="Refresh Data" action={{ name: "refresh_data" }} />
</Stack>

// Horizontal stack with center alignment
<Stack direction="horizontal" gap="8px" align="center">
  <Text content="Status:" variant="label" />
  <Badge text="Active" variant="success" />
</Stack>

// Horizontal button group
<Stack direction="horizontal" gap="12px" align="end">
  <Button label="Cancel" variant="secondary" action={{ name: "cancel" }} />
  <Button label="Save" variant="primary" action={{ name: "save" }} />
</Stack>
```

**Stack Behavior:**
- Default direction: `vertical`
- Default alignment: `start`
- Items do not wrap
- Flexible sizing based on content
- Configurable gap spacing

## Layout Patterns

### Nested Layouts

Layout components can be nested to create complex structures:

```typescript
// Dashboard with nested Grid and Cards
<Grid columns={2} gap="24px">
  <Card title="Metrics">
    <Stack direction="vertical" gap="16px">
      <Metric label="Revenue" valuePath="/analytics/revenue" format="currency" />
      <Metric label="Growth" valuePath="/analytics/growth" format="percent" />
    </Stack>
  </Card>

  <Card title="Quick Actions">
    <Stack direction="horizontal" gap="8px">
      <Button label="Export" action={{ name: "export_report" }} />
      <Button label="Refresh" action={{ name: "refresh_data" }} />
    </Stack>
  </Card>
</Grid>
```

### Responsive Layout

Using Grid with different column counts for responsive design:

```typescript
// 3-column grid for metrics
<Grid columns={3} gap="16px">
  <Card>
    <Metric label="Revenue" valuePath="/analytics/revenue" format="currency" />
  </Card>
  <Card>
    <Metric label="Customers" valuePath="/analytics/customers" format="number" />
  </Card>
  <Card>
    <Metric label="Orders" valuePath="/analytics/orders" format="number" />
  </Card>
</Grid>
```

### Card Headers

Cards with titles and descriptions create clear section boundaries:

```typescript
<Card title="Sales Performance" description="Regional breakdown for Q1 2024">
  <Chart
    type="bar"
    dataPath="/analytics/salesByRegion"
    title="Sales by Region"
    height={300}
  />
</Card>
```

### Button Groups

Stack component creates aligned button groups:

```typescript
// Horizontal button toolbar
<Stack direction="horizontal" gap="8px">
  <Button label="Export PDF" action={{ name: "export_report" }} variant="primary" />
  <Button label="Refresh" action={{ name: "refresh_data" }} variant="secondary" />
  <Button
    label="View Details"
    action={{ name: "view_details" }}
    variant="secondary"
  />
</Stack>
```

## Styling Considerations

### Padding

Card component accepts custom padding values:

```typescript
// Default padding (16px)
<Card title="Dashboard">{/* content */}</Card>

// Custom padding
<Card title="Dashboard" padding="24px">{/* content */}</Card>
<Card title="Dashboard" padding="32px 16px">{/* content */}</Card>

// No padding
<Card title="Dashboard" padding="0">{/* content */}</Card>
```

### Gap Spacing

Grid and Stack components accept custom gap values:

```typescript
// Small gap
<Grid columns={3} gap="8px">{/* items */}</Grid>

// Medium gap (default-ish)
<Grid columns={2} gap="16px">{/* items */}</Grid>

// Large gap
<Grid columns={2} gap="24px">{/* items */}</Grid>

// Using rem units
<Stack direction="vertical" gap="1.5rem">{/* items */}</Stack>
```

### Alignment

Stack component provides cross-axis alignment:

```typescript
// Start alignment (default)
<Stack direction="horizontal" gap="8px" align="start">
  {/* aligned to top in horizontal stack */}
</Stack>

// Center alignment
<Stack direction="horizontal" gap="8px" align="center">
  {/* vertically centered in horizontal stack */}
</Stack>

// End alignment
<Stack direction="horizontal" gap="8px" align="end">
  {/* aligned to bottom in horizontal stack */}
</Stack>
```

## Common Layout Combinations

### Dashboard Grid with Cards

```typescript
<Grid columns={2} gap="24px">
  <Card title="Revenue Metrics">
    <Stack direction="vertical" gap="12px">
      <Metric label="Total Revenue" valuePath="/analytics/revenue" format="currency" />
      <Metric label="Growth Rate" valuePath="/analytics/growth" format="percent" />
    </Stack>
  </Card>

  <Card title="Customer Metrics">
    <Stack direction="vertical" gap="12px">
      <Metric label="Total Customers" valuePath="/analytics/customers" format="number" />
      <Metric label="Active Orders" valuePath="/analytics/orders" format="number" />
    </Stack>
  </Card>
</Grid>
```

### Card with Header and Actions

```typescript
<Card title="Dashboard Settings" description="Configure your dashboard view">
  <Stack direction="vertical" gap="16px">
    <Select
      label="Date Range"
      valuePath="/form/dateRange"
      options={[
        { value: "7d", label: "Last 7 days" },
        { value: "30d", label: "Last 30 days" },
      ]}
    />
    <Select
      label="Region"
      valuePath="/form/region"
      options={[
        { value: "all", label: "All Regions" },
        { value: "north", label: "North" },
      ]}
    />
    <Stack direction="horizontal" gap="8px" align="end">
      <Button label="Reset" variant="secondary" action={{ name: "reset" }} />
      <Button label="Apply" variant="primary" action={{ name: "apply_filter" }} />
    </Stack>
  </Stack>
</Card>
```

### Full Dashboard Layout

```typescript
<Stack direction="vertical" gap="24px">
  <Card title="Analytics Dashboard" description="Real-time business metrics">
    <Grid columns={3} gap="16px">
      <Metric label="Revenue" valuePath="/analytics/revenue" format="currency" trend="up" trendValue="12%" />
      <Metric label="Customers" valuePath="/analytics/customers" format="number" trend="up" trendValue="8%" />
      <Metric label="Orders" valuePath="/analytics/orders" format="number" trend="down" trendValue="3%" />
    </Grid>
  </Card>

  <Grid columns={2} gap="24px">
    <Card title="Sales by Region">
      <Chart
        type="bar"
        dataPath="/analytics/salesByRegion"
        height={300}
      />
    </Card>

    <Card title="Recent Transactions">
      <Table
        dataPath="/analytics/recentTransactions"
        columns={[
          { key: "customer", label: "Customer", format: "text" },
          { key: "amount", label: "Amount", format: "currency" },
          { key: "status", label: "Status", format: "badge" },
        ]}
      />
    </Card>
  </Grid>
</Stack>
```
