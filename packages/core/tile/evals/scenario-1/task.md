# User Dashboard Component System

Build a user dashboard component system that conditionally renders UI elements based on user state.

## Requirements

Implement a component catalog and dashboard configuration with these elements:

1. An admin panel that is only visible to signed-in users who have EITHER:
   - The role "admin", OR
   - A permission level of 3 or higher

2. A premium features section that is only visible when the user's subscription tier is greater than or equal to a dynamically configured required tier value

3. A delete account button with an action that:
   - Retrieves the user ID from the data model at path `/user/id`
   - Includes a confirmation dialog that displays the username in the message using path interpolation
   - On successful execution, triggers navigation to "/logout"
   - On error, saves the error message to the data model at path `/ui/error`

4. An email input field with validation that:
   - Checks if the email is required and valid
   - Is only enabled when the data model value at `/ui/changeEmail` is true

## Implementation

[@generates](./src/dashboard.ts)

## API

```typescript { #api }
import { z } from 'zod';
import { Catalog } from '@json-render/core';

/**
 * Create and export a catalog with the following components:
 * - AdminPanel: accepts a title (string)
 * - PremiumSection: accepts a features array of strings
 * - Button: accepts a label (string) and an action object
 * - EmailInput: accepts a value (string) and validation config
 */
export const dashboardCatalog: Catalog<any>;

/**
 * Create a dashboard tree with the following structure:
 * - Root element is AdminPanel with visibility based on auth and permissions
 * - PremiumSection with visibility based on subscription tier comparison
 * - Button for delete account with proper action configuration
 * - EmailInput with conditional validation
 *
 * The dashboard should use a data model with this structure:
 * {
 *   user: { id: string, name: string, role: string, permissions: number, subscriptionTier: number },
 *   config: { premiumTierRequired: number },
 *   ui: { changeEmail: boolean, email: string }
 * }
 */
export const dashboardTree: any;
```

## Test Cases

- Admin panel visibility evaluates to true when user is signed in with role "admin" [@test](./src/dashboard.test.ts)
- Admin panel visibility evaluates to true when user is signed in with permissions of 3 [@test](./src/dashboard.test.ts)
- Admin panel visibility evaluates to false when user is not signed in [@test](./src/dashboard.test.ts)
- Premium section visibility evaluates to true when user tier equals required tier [@test](./src/dashboard.test.ts)
- Premium section visibility evaluates to false when user tier is below required tier [@test](./src/dashboard.test.ts)
- Delete button action resolves user ID correctly from data model path [@test](./src/dashboard.test.ts)
- Delete button confirmation message includes the username [@test](./src/dashboard.test.ts)
- Email validation returns valid when changeEmail is false (validation disabled) [@test](./src/dashboard.test.ts)
- Email validation returns invalid for empty email when changeEmail is true [@test](./src/dashboard.test.ts)

## Dependencies { .dependencies }

### @json-render/core { .dependency }

Provides component catalog and UI tree functionality with visibility conditions, actions, and validation.

### zod { .dependency }

Provides schema validation support.
