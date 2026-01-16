# Typography and Status Components

Typography and status components provide text display, headings, badges, alerts, dividers, and empty state placeholders for dashboard UIs.

## Capabilities

### Heading Component

Section headings with configurable levels (h1-h4).

```typescript { .api }
/**
 * Heading - Section headings
 * Location: components/ui/heading.tsx
 */
import { Heading } from "./components/ui";

interface HeadingProps {
  text: string; // Heading text
  level?: "h1" | "h2" | "h3" | "h4" | null; // Heading level, default: "h2"
}
```

**Usage Examples:**

```typescript
import { Heading } from "./components/ui";

// H1 heading (largest)
<Heading text="Dashboard Overview" level="h1" />

// H2 heading (default)
<Heading text="Revenue Metrics" level="h2" />
<Heading text="Sales Performance" /> // Default is h2

// H3 heading
<Heading text="Regional Breakdown" level="h3" />

// H4 heading (smallest)
<Heading text="Quick Stats" level="h4" />
```

**Heading Sizes:**
- `h1`: Largest, used for page titles
- `h2`: Section headings (default)
- `h3`: Subsection headings
- `h4`: Smallest, used for minor sections

### Text Component

Text paragraphs and labels with style variants and colors.

```typescript { .api }
/**
 * Text - Text paragraphs with variants and colors
 * Location: components/ui/text.tsx
 */
import { Text } from "./components/ui";

interface TextProps {
  content: string; // Text content
  variant?: "body" | "caption" | "label" | null; // Text style/size (default: "body")
  color?: "default" | "muted" | "success" | "warning" | "danger" | null; // Text color (default: "default")
}
```

**Usage Examples:**

```typescript
import { Text } from "./components/ui";

// Body text (default)
<Text content="This is a paragraph of body text." variant="body" />
<Text content="Another paragraph." /> // Default is body

// Caption text (smaller)
<Text content="Last updated 5 minutes ago" variant="caption" />

// Label text
<Text content="Dashboard Settings" variant="label" />

// Colored text (using color prop)
<Text content="Operation successful" color="success" />
<Text content="Warning: High usage detected" color="warning" />
<Text content="Error: Failed to load data" color="danger" />
<Text content="Additional information" color="muted" />

// Combining variant and color
<Text content="Small success message" variant="caption" color="success" />
```

**Text Variants (variant prop):**
- `body`: Regular text size (default)
- `caption`: Smaller text for captions/footnotes
- `label`: Text for labels

**Text Colors (color prop):**
- `default`: Default text color
- `muted`: Gray/muted text
- `success`: Green text for success messages
- `warning`: Orange/yellow text for warnings
- `danger`: Red text for errors

### Badge Component

Small status indicator badge with color variants.

```typescript { .api }
/**
 * Badge - Status badge
 * Location: components/ui/badge.tsx
 */
import { Badge } from "./components/ui";

interface BadgeProps {
  text: string; // Badge text
  variant?: "default" | "success" | "warning" | "danger" | "info" | null; // Badge color, default: "default"
}
```

**Usage Examples:**

```typescript
import { Badge } from "./components/ui";

// Default badge (gray)
<Badge text="New" variant="default" />
<Badge text="Draft" /> // Default variant

// Success badge (green)
<Badge text="Active" variant="success" />
<Badge text="Completed" variant="success" />

// Warning badge (orange/yellow)
<Badge text="Pending" variant="warning" />
<Badge text="In Progress" variant="warning" />

// Danger badge (red)
<Badge text="Failed" variant="danger" />
<Badge text="Inactive" variant="danger" />

// Info badge (blue)
<Badge text="Info" variant="info" />
<Badge text="Beta" variant="info" />

```

**Badge Colors:**
- `default`: Gray background
- `success`: Green background
- `warning`: Orange/yellow background
- `danger`: Red background
- `info`: Blue background

// Example data:
const data = {
  status: "completed",
  user: {
    role: "admin",
  },
};

// Using data paths:
<Badge text={{ path: "/status" }} /> // Displays "completed"
<Badge text={{ path: "/user/role" }} /> // Displays "admin"
```

### Alert Component

Alert/notification banner with type variants.

```typescript { .api }
/**
 * Alert - Alert/notification banner
 * Location: components/ui/alert.tsx
 */
import { Alert } from "./components/ui";

interface AlertProps {
  type: "info" | "success" | "warning" | "error"; // Alert type (required)
  title: string; // Alert title (required)
  message?: string | null; // Optional alert message
  dismissible?: boolean | null; // Whether alert can be dismissed
}
```

**Usage Examples:**

```typescript
import { Alert } from "./components/ui";

// Info alert (blue)
<Alert type="info" title="Information" message="Your dashboard has been updated." />
<Alert type="info" title="New Features" message="Check out the latest updates!" />

// Success alert (green)
<Alert type="success" title="Success" message="Settings saved successfully!" />

// Warning alert (orange/yellow)
<Alert type="warning" title="Warning" message="Your session will expire in 5 minutes." />

// Error alert (red)
<Alert type="error" title="Error" message="Failed to load data. Please try again." />

// Alert with dismissible option
<Alert type="info" title="Tip" message="You can customize your dashboard layout." dismissible={true} />

// Alert without message (title only)
<Alert type="success" title="Operation completed" />
```

**Alert Types:**
- `info`: Blue background, informational
- `success`: Green background, success messages
- `warning`: Orange/yellow background, warnings
- `error`: Red background, error messages

**Props:**
- `type`: Required, defines the alert style
- `title`: Required, main alert heading
- `message`: Optional, additional details
- `dismissible`: Optional, enables close button

### Divider Component

Visual divider line for separating content.

```typescript { .api }
/**
 * Divider - Visual divider
 * Location: components/ui/divider.tsx
 * Catalog Schema: Only defines `label` prop
 * Implementation: Also supports `orientation` prop (not in catalog)
 */
import { Divider } from "./components/ui";

// Catalog-defined interface (what AI can generate)
interface DividerProps {
  label?: string | null; // Optional label text for the divider
}

// Note: The component implementation also supports orientation?: "vertical"
// but this is NOT in the catalog, so AI cannot generate it
```

**Usage Examples:**

```typescript
import { Divider } from "./components/ui";

// Divider with label (catalog-supported)
<Divider label="Section Break" />

// Plain divider (catalog-supported)
<Divider />

// Vertical divider (implementation supports, but NOT in catalog)
// AI generation cannot use this - only manual code can
<Divider orientation="vertical" />
```

**Usage Patterns:**

```typescript
// Horizontal divider between sections
<Stack direction="vertical" gap="24px">
  <Card title="Section 1">
    {/* content */}
  </Card>
  <Divider />
  <Card title="Section 2">
    {/* content */}
  </Card>
</Stack>

// Vertical divider between inline elements
<Stack direction="horizontal" gap="16px">
  <Text content="Option 1" />
  <Divider orientation="vertical" />
  <Text content="Option 2" />
  <Divider orientation="vertical" />
  <Text content="Option 3" />
</Stack>
```

### Empty Component

Empty state placeholder for when no data is available.

```typescript { .api }
/**
 * Empty - Empty state placeholder
 * Location: components/ui/empty.tsx
 */
import { Empty } from "./components/ui";

interface EmptyProps {
  title: string; // Empty state title
  description?: string | null; // Optional description text
}
```

**Usage Examples:**

```typescript
import { Empty } from "./components/ui";

// Basic empty state
<Empty title="No data available" />

// Empty state with description
<Empty
  title="No transactions found"
  description="There are no transactions to display for the selected period."
/>

// Empty state in card
<Card title="Recent Activity">
  <Empty
    title="No recent activity"
    description="Check back later for updates."
  />
</Card>
```

## Common Patterns

### Page Header

```typescript
<Stack direction="vertical" gap="8px">
  <Heading text="Analytics Dashboard" level="h1" />
  <Text content="Real-time business metrics and insights" variant="muted" />
</Stack>
```

### Section Header

```typescript
<Stack direction="vertical" gap="4px">
  <Heading text="Revenue Metrics" level="h2" />
  <Text content="Track your revenue performance" variant="caption" />
</Stack>
```

### Status Display

```typescript
<Stack direction="horizontal" gap="8px" align="center">
  <Text content="Status:" variant="label" />
  <Badge text="Active" variant="success" />
</Stack>
```

### Alert Messages

```typescript
<Stack direction="vertical" gap="16px">
  <Alert message="Data refreshed successfully" variant="success" />
  <Alert message="Some metrics may be delayed" variant="warning" />
</Stack>
```

### Table with Status Badges

```typescript
<Table
  title="Users"
  dataPath="/users"
  columns={[
    { key: "name", label: "Name", format: "text" },
    { key: "email", label: "Email", format: "text" },
    { key: "status", label: "Status", format: "badge" }, // Badge in table
  ]}
/>
```

### Card with Multiple Text Variants

```typescript
<Card>
  <Stack direction="vertical" gap="12px">
    <Heading text="System Status" level="h3" />
    <Text content="All systems operational" variant="success" />
    <Divider />
    <Text content="Last checked: 2 minutes ago" variant="caption" />
  </Stack>
</Card>
```

### Empty State in List

```typescript
<Card title="Notifications">
  <List dataPath="/notifications" emptyMessage="No notifications">
    <Stack direction="horizontal" gap="8px">
      <Badge text={{ path: "type" }} />
      <Text content={{ path: "message" }} />
    </Stack>
  </List>
</Card>
```

### Metric with Status

```typescript
<Card>
  <Stack direction="vertical" gap="8px">
    <Stack direction="horizontal" gap="8px" align="center">
      <Text content="API Status" variant="label" />
      <Badge text="Online" variant="success" />
    </Stack>
    <Metric
      label="Response Time"
      valuePath="/metrics/responseTime"
      format="number"
    />
    <Text content="Average over last 5 minutes" variant="caption" />
  </Stack>
</Card>
```

### Error Display

```typescript
<Card>
  <Stack direction="vertical" gap="16px">
    <Alert message="Failed to load dashboard data" variant="error" />
    <Empty
      title="Unable to display content"
      description="Please check your connection and try again."
    />
    <Stack direction="horizontal" gap="8px">
      <Button
        label="Retry"
        variant="primary"
        action={{ name: "refresh_data" }}
      />
      <Button
        label="Go Back"
        variant="secondary"
        action={{ name: "go_back" }}
      />
    </Stack>
  </Stack>
</Card>
```

### Multi-Level Content

```typescript
<Stack direction="vertical" gap="24px">
  <Heading text="Dashboard" level="h1" />

  <Card>
    <Stack direction="vertical" gap="16px">
      <Heading text="Performance Metrics" level="h2" />
      <Text content="Key performance indicators for your business" variant="muted" />

      <Divider />

      <Stack direction="vertical" gap="12px">
        <Heading text="Revenue" level="h3" />
        <Metric
          label="Total Revenue"
          valuePath="/analytics/revenue"
          format="currency"
          trend="up"
          trendValue="12%"
        />
        <Text content="Compared to last month" variant="caption" />
      </Stack>
    </Stack>
  </Card>
</Stack>
```

### Status List

```typescript
<Card title="System Services">
  <Stack direction="vertical" gap="12px">
    <Stack direction="horizontal" gap="8px" align="center">
      <Text content="API Gateway" variant="label" />
      <Badge text="Running" variant="success" />
    </Stack>
    <Stack direction="horizontal" gap="8px" align="center">
      <Text content="Database" variant="label" />
      <Badge text="Running" variant="success" />
    </Stack>
    <Stack direction="horizontal" gap="8px" align="center">
      <Text content="Cache Server" variant="label" />
      <Badge text="Maintenance" variant="warning" />
    </Stack>
  </Stack>
</Card>
```

### Conditional Alerts

```typescript
<Stack direction="vertical" gap="16px">
  {/* Show different alerts based on data */}
  <Alert
    message={{ path: "/system/successMessage" }}
    variant="success"
  />
  <Alert
    message={{ path: "/system/warningMessage" }}
    variant="warning"
  />
  <Alert
    message={{ path: "/system/errorMessage" }}
    variant="error"
  />
</Stack>
```

## Accessibility

All typography and status components follow accessibility best practices:

- **Headings**: Use semantic HTML heading tags (h1-h4)
- **Text**: Proper contrast ratios for all color variants
- **Badges**: Color is not the only indicator (text labels included)
- **Alerts**: Use appropriate ARIA roles for screen readers
- **Empty States**: Descriptive text for screen readers

## Responsive Behavior

- **Headings**: Font sizes scale down on smaller screens
- **Text**: Line length and spacing adjust for readability
- **Badges**: Maintain minimum size for touch targets
- **Alerts**: Full width on mobile, stack alert content
- **Dividers**: Maintain visibility at all screen sizes
- **Empty States**: Center-aligned, responsive padding
