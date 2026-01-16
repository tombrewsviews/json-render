# Order Dashboard Configuration

Create a configuration system for an order management dashboard that supports conditional visibility, user actions with confirmation dialogs, field validation, and data binding.

## Capabilities

### Complex Visibility Conditions

Implement visibility rules using compound logic:

- When user has auth state "signedIn" AND data path `/user/role` equals "admin" AND data path `/order/amount` is greater than 500, component is visible [@test](../test/visibility-admin.test.ts)
- When data path `/order/refunded` is false AND data path `/order/paymentStatus` equals "completed", component is visible [@test](../test/visibility-refund.test.ts)
- When data path `/order/userId` equals data path `/currentUser/id` OR data path `/user/role` equals "admin", component is visible [@test](../test/visibility-owner.test.ts)

### Actions with Confirmation and Callbacks

Define actions that require confirmation and handle success/error states:

- Action with confirmation shows dialog with title "Confirm Refund" and message interpolating data path `/order/amount` [@test](../test/action-confirm.test.ts)
- On action success, update data path `/order/status` to "refunded" and set data path `/ui/successMessage` to "Refund processed" [@test](../test/action-success.test.ts)
- On action error, set data path `/ui/errorMessage` to the error message from the caught exception [@test](../test/action-error.test.ts)

### Conditional Field Validation

Configure form field validation that depends on context:

- Field at data path `/order/notes` requires validation check for "required" with message "Notes are required" [@test](../test/validation-required.test.ts)
- Field at data path `/order/notes` requires validation check for minimum length of 10 characters with message "Notes must be at least 10 characters" [@test](../test/validation-minlength.test.ts)
- Validation is only enabled when data path `/order/status` equals "pending" [@test](../test/validation-conditional.test.ts)

### Data Path Binding

Bind component properties to data model paths:

- Component property resolves value from data path `/order/amount` formatted as currency [@test](../test/binding-currency.test.ts)
- Component property resolves value from data path `/order/status` [@test](../test/binding-status.test.ts)
- Component property resolves value from data path `/customer/name` [@test](../test/binding-name.test.ts)

## Implementation

[@generates](./src/dashboard.ts)

## API

```typescript { #api }
/**
 * Creates the catalog defining available components and actions
 */
export function createOrderDashboardCatalog(): Catalog;

/**
 * Configures the component registry for rendering
 */
export function createComponentRegistry(): ComponentRegistry;

/**
 * Sets up action handlers for dashboard interactions
 */
export function createActionHandlers(): ActionHandlers;
```

## Dependencies { .dependencies }

### @json-render/core { .dependency }

Provides core functionality for catalog creation, visibility conditions, action definitions, and validation rules.

### @json-render/react { .dependency }

Provides React components and hooks for rendering dynamic UIs with data providers and action handlers.

### zod { .dependency }

Provides schema validation support for defining component prop types.
