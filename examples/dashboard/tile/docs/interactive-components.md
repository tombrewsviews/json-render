# Interactive Components

Interactive components handle user input and actions. They support two-way data binding, validation, and action triggering through the ActionProvider.

## Capabilities

### Button Component

Clickable button that triggers actions through the ActionProvider.

```typescript { .api }
/**
 * Button - Clickable button with action handling
 * Location: components/ui/button.tsx
 */
import { Button } from "./components/ui";

interface ButtonProps {
  label: string; // Button text
  variant?: "primary" | "secondary" | "danger" | "ghost" | null; // Button style, default: "primary"
  action: { name: string }; // Action to trigger on click
  disabled?: boolean | null; // Whether button is disabled, default: false
}

// Available actions (defined in catalog):
// - export_report: Export the current dashboard to PDF
// - refresh_data: Refresh all metrics and charts
// - view_details: View detailed information
// - apply_filter: Apply the current filter settings
```

**Usage Examples:**

```typescript
import { Button } from "./components/ui";

// Primary button (default variant)
<Button label="Export Report" action={{ name: "export_report" }} />

// Secondary button
<Button
  label="Cancel"
  variant="secondary"
  action={{ name: "cancel" }}
/>

// Danger button
<Button
  label="Delete"
  variant="danger"
  action={{ name: "delete_item" }}
/>

// Ghost button (minimal styling)
<Button
  label="View Details"
  variant="ghost"
  action={{ name: "view_details" }}
/>

// Disabled button
<Button
  label="Save"
  action={{ name: "save" }}
  disabled={true}
/>
```

**Button Variants:**
- `primary`: Blue background, white text (default)
- `secondary`: Gray background, dark text
- `danger`: Red background, white text
- `ghost`: Transparent background, colored text

**Action Handling:**

```typescript
// Actions are handled through ActionProvider
import { ActionProvider } from "@json-render/react";

const ACTION_HANDLERS = {
  export_report: () => {
    console.log("Exporting report...");
    // Export logic
  },
  refresh_data: () => {
    console.log("Refreshing data...");
    // Refresh logic
  },
  view_details: (params) => {
    console.log("Viewing details:", params);
    // View details logic
  },
};

<ActionProvider handlers={ACTION_HANDLERS}>
  <Button label="Export" action={{ name: "export_report" }} />
</ActionProvider>;
```

### TextField Component

Text input field with two-way data binding and validation support.

```typescript { .api }
/**
 * TextField - Text input with validation
 * Location: components/ui/text-field.tsx
 */
import { TextField } from "./components/ui";

interface TextFieldProps {
  label: string; // Field label
  valuePath: string; // JSON pointer for two-way binding (e.g., "/form/username")
  placeholder?: string | null; // Placeholder text
  type?: string | null; // Input type: "text" | "email" | "password" | "number" | "tel" | "url", default: "text"
  checks?: Array<{
    // Validation rules
    fn: string; // Validation function name
    message: string; // Error message
  }> | null;
  validateOn?: "change" | "blur" | "submit" | null; // When to validate, default: "blur"
}
```

**Usage Examples:**

```typescript
import { TextField } from "./components/ui";

// Basic text input
<TextField
  label="Username"
  valuePath="/form/username"
  placeholder="Enter username"
/>

// Email input with validation
<TextField
  label="Email Address"
  valuePath="/form/email"
  type="email"
  placeholder="you@example.com"
  checks={[
    { fn: "required", message: "Email is required" },
    { fn: "email", message: "Invalid email format" }
  ]}
  validateOn="blur"
/>

// Password input
<TextField
  label="Password"
  valuePath="/form/password"
  type="password"
  placeholder="Enter password"
  checks={[
    { fn: "required", message: "Password is required" },
    { fn: "minLength:8", message: "Password must be at least 8 characters" }
  ]}
/>

// Number input
<TextField
  label="Age"
  valuePath="/form/age"
  type="number"
  placeholder="Enter your age"
/>

// Validate on change
<TextField
  label="Search"
  valuePath="/form/search"
  placeholder="Search..."
  validateOn="change"
/>
```

**Input Types:**
- `text`: Plain text input (default)
- `email`: Email input with browser validation
- `password`: Password input (masked)
- `number`: Numeric input
- `tel`: Telephone number
- `url`: URL input

**Validation:**

The `checks` array defines validation rules:

```typescript
checks={[
  // Required field
  { fn: "required", message: "This field is required" },

  // Minimum length
  { fn: "minLength:8", message: "Must be at least 8 characters" },

  // Maximum length
  { fn: "maxLength:50", message: "Must be less than 50 characters" },

  // Email format
  { fn: "email", message: "Invalid email format" },

  // Pattern match
  { fn: "pattern:^[a-zA-Z]+$", message: "Only letters allowed" }
]}
```

**Validation Timing:**
- `change`: Validate on every keystroke
- `blur`: Validate when field loses focus (default)
- `submit`: Validate only on form submission

### Select Component

Dropdown select input with two-way data binding.

```typescript { .api }
/**
 * Select - Dropdown select input
 * Location: components/ui/select.tsx
 */
import { Select } from "./components/ui";

interface SelectProps {
  label: string; // Field label
  valuePath: string; // JSON pointer for two-way binding (e.g., "/form/region")
  options: Array<{
    // Dropdown options
    value: string; // Option value (stored in data)
    label: string; // Option display text
  }>;
  placeholder?: string | null; // Placeholder text when no selection
}
```

**Usage Examples:**

```typescript
import { Select } from "./components/ui";

// Region selector
<Select
  label="Region"
  valuePath="/form/region"
  options={[
    { value: "all", label: "All Regions" },
    { value: "north", label: "North" },
    { value: "south", label: "South" },
    { value: "east", label: "East" },
    { value: "west", label: "West" }
  ]}
  placeholder="Select a region"
/>

// Date range selector
<Select
  label="Date Range"
  valuePath="/form/dateRange"
  options={[
    { value: "7d", label: "Last 7 days" },
    { value: "30d", label: "Last 30 days" },
    { value: "90d", label: "Last 90 days" },
    { value: "1y", label: "Last year" }
  ]}
/>

// Status filter
<Select
  label="Status"
  valuePath="/form/status"
  options={[
    { value: "all", label: "All Statuses" },
    { value: "active", label: "Active" },
    { value: "pending", label: "Pending" },
    { value: "completed", label: "Completed" }
  ]}
  placeholder="Filter by status"
/>
```

**Data Binding:**

```typescript
// Initial data
const data = {
  form: {
    region: "north", // Pre-selected value
  },
};

// Select component updates data.form.region when user changes selection
<Select
  label="Region"
  valuePath="/form/region"
  options={[
    { value: "north", label: "North" },
    { value: "south", label: "South" },
  ]}
/>;

// After user selects "south":
// data.form.region = "south"
```

### DatePicker Component

Date picker input with two-way data binding.

```typescript { .api }
/**
 * DatePicker - Date picker input
 * Location: components/ui/date-picker.tsx
 */
import { DatePicker } from "./components/ui";

interface DatePickerProps {
  label: string; // Field label
  valuePath: string; // JSON pointer for two-way binding (e.g., "/form/startDate")
  placeholder?: string | null; // Placeholder text
}
```

**Usage Examples:**

```typescript
import { DatePicker } from "./components/ui";

// Basic date picker
<DatePicker
  label="Start Date"
  valuePath="/form/startDate"
  placeholder="Select start date"
/>

// End date picker
<DatePicker
  label="End Date"
  valuePath="/form/endDate"
  placeholder="Select end date"
/>

// Birth date picker
<DatePicker
  label="Date of Birth"
  valuePath="/form/birthDate"
/>
```

**Data Format:**

```typescript
// DatePicker stores dates as ISO 8601 strings
const data = {
  form: {
    startDate: "2024-01-15", // YYYY-MM-DD format
  },
};
```

## Two-Way Data Binding

Interactive components use `valuePath` to bind to data via JSON pointers:

```typescript { .api }
import { DataProvider } from "@json-render/react";

// Initial data
const initialData = {
  form: {
    username: "",
    email: "",
    region: "all",
    startDate: null,
  },
};

// Data provider enables two-way binding
<DataProvider initialData={initialData}>
  <TextField label="Username" valuePath="/form/username" />
  <TextField label="Email" valuePath="/form/email" type="email" />
  <Select
    label="Region"
    valuePath="/form/region"
    options={[
      { value: "all", label: "All" },
      { value: "north", label: "North" },
    ]}
  />
  <DatePicker label="Start Date" valuePath="/form/startDate" />
</DataProvider>;
```

**Data Flow:**

1. Component reads initial value from data via `valuePath`
2. User interacts with component (types, selects, picks date)
3. Component updates data at `valuePath` via `set(path, value)`
4. Other components bound to same path automatically re-render

## Form Patterns

### Login Form

```typescript
<Card title="Login" description="Enter your credentials">
  <Stack direction="vertical" gap="16px">
    <TextField
      label="Email"
      valuePath="/form/email"
      type="email"
      placeholder="you@example.com"
      checks={[
        { fn: "required", message: "Email is required" },
        { fn: "email", message: "Invalid email" }
      ]}
    />
    <TextField
      label="Password"
      valuePath="/form/password"
      type="password"
      placeholder="Enter password"
      checks={[
        { fn: "required", message: "Password is required" }
      ]}
    />
    <Stack direction="horizontal" gap="8px" align="end">
      <Button
        label="Cancel"
        variant="secondary"
        action={{ name: "cancel" }}
      />
      <Button
        label="Login"
        variant="primary"
        action={{ name: "login" }}
      />
    </Stack>
  </Stack>
</Card>
```

### Filter Form

```typescript
<Card title="Filters" description="Customize your dashboard view">
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
    <DatePicker
      label="Custom Start Date"
      valuePath="/form/customStartDate"
      placeholder="Optional custom date"
    />
    <Button
      label="Apply Filters"
      variant="primary"
      action={{ name: "apply_filter" }}
    />
  </Stack>
</Card>
```

### Registration Form

```typescript
<Card title="Create Account">
  <Stack direction="vertical" gap="16px">
    <TextField
      label="Full Name"
      valuePath="/form/name"
      placeholder="John Doe"
      checks={[
        { fn: "required", message: "Name is required" }
      ]}
    />
    <TextField
      label="Email"
      valuePath="/form/email"
      type="email"
      placeholder="you@example.com"
      checks={[
        { fn: "required", message: "Email is required" },
        { fn: "email", message: "Invalid email" }
      ]}
    />
    <TextField
      label="Password"
      valuePath="/form/password"
      type="password"
      checks={[
        { fn: "required", message: "Password is required" },
        { fn: "minLength:8", message: "At least 8 characters" }
      ]}
    />
    <TextField
      label="Phone"
      valuePath="/form/phone"
      type="tel"
      placeholder="(555) 555-5555"
    />
    <Button
      label="Create Account"
      variant="primary"
      action={{ name: "register" }}
    />
  </Stack>
</Card>
```

## Action Handling

Actions are defined in the catalog and handled through ActionProvider:

```typescript { .api }
import { ActionProvider } from "@json-render/react";

// Define action handlers
const ACTION_HANDLERS = {
  export_report: () => {
    // Export report as PDF
    console.log("Exporting report...");
  },
  refresh_data: () => {
    // Fetch fresh data from API
    console.log("Refreshing data...");
  },
  view_details: (params?: any) => {
    // Navigate to details page
    console.log("Viewing details:", params);
  },
  apply_filter: () => {
    // Apply current filter settings
    console.log("Applying filters...");
  },
  // Custom actions
  save_settings: () => {
    console.log("Saving settings...");
  },
  cancel: () => {
    console.log("Cancelled");
  },
};

// Provide handlers to components
<ActionProvider handlers={ACTION_HANDLERS}>
  <Button label="Export" action={{ name: "export_report" }} />
  <Button label="Refresh" action={{ name: "refresh_data" }} />
  <Button label="Save" action={{ name: "save_settings" }} />
</ActionProvider>;
```

## Validation Patterns

### Field Validation

```typescript
// Required field
<TextField
  label="Username"
  valuePath="/form/username"
  checks={[
    { fn: "required", message: "Username is required" }
  ]}
/>

// Email validation
<TextField
  label="Email"
  valuePath="/form/email"
  type="email"
  checks={[
    { fn: "required", message: "Email is required" },
    { fn: "email", message: "Invalid email format" }
  ]}
/>

// Length validation
<TextField
  label="Password"
  valuePath="/form/password"
  type="password"
  checks={[
    { fn: "required", message: "Password is required" },
    { fn: "minLength:8", message: "At least 8 characters" },
    { fn: "maxLength:50", message: "Maximum 50 characters" }
  ]}
/>

// Pattern validation
<TextField
  label="Username"
  valuePath="/form/username"
  checks={[
    { fn: "pattern:^[a-zA-Z0-9_]+$", message: "Only letters, numbers, and underscores" }
  ]}
/>
```

### Validation Timing

```typescript
// Validate on blur (default) - best for user experience
<TextField
  label="Email"
  valuePath="/form/email"
  validateOn="blur"
  checks={[...]}
/>

// Validate on change - immediate feedback
<TextField
  label="Username"
  valuePath="/form/username"
  validateOn="change"
  checks={[...]}
/>

// Validate on submit - least intrusive
<TextField
  label="Notes"
  valuePath="/form/notes"
  validateOn="submit"
  checks={[...]}
/>
```
