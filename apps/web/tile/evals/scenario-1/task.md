# User Settings Form with Conditional Actions

Build a settings form component that dynamically shows/hides sections and validates user input based on selected preferences.

## Requirements

### Form Structure

Create a form that collects the following user settings:

1. **Account Type Selection** - A field to select between "personal" or "business" account types
2. **Email Address** - A required email input field
3. **Notification Preferences** (only visible for personal accounts):
   - A toggle for email notifications
   - A toggle for SMS notifications
4. **Business Information** (only visible for business accounts):
   - Company name (required when visible)
   - Tax ID (required when visible, must be exactly 9 digits)
5. **Password Change Section** (only visible when user is signed in):
   - New password field (required when visible, minimum 8 characters)
   - Confirm password field (must match the new password)
6. **Save Button** - Saves the settings
7. **Delete Account Button** - Deletes the user account (only visible for business accounts)

### Behavior Requirements

**Visibility Rules:**
- Notification preferences should only appear when account type is "personal"
- Business information should only appear when account type is "business"
- Password change section should only appear when the user is authenticated
- Delete account button should only appear for business accounts

**Validation Requirements:**
- Email must be a valid email address
- When visible, company name cannot be empty
- When visible, tax ID must contain exactly 9 numeric characters
- When visible, new password must be at least 8 characters
- Confirm password must match the new password field
- All validation should occur on blur (when field loses focus)

**Action Requirements:**
- The save button should trigger a "save_settings" action with all form data as parameters
- Before saving, show a confirmation dialog with the message: "Save settings for [email]?" where [email] is replaced with the actual email value
- On successful save, update a status message at path "/ui/statusMessage" to "Settings saved successfully"
- On error, update an error message at path "/ui/errorMessage" with the error details
- The delete account button should trigger a "delete_account" action with the email as a parameter
- Before deleting, show a danger-variant confirmation with title "Delete Account" and message "Permanently delete account for [email]? This cannot be undone."
- On successful deletion, navigate to "/goodbye"
- On error, update the error message at path "/ui/errorMessage"

### Data Structure

All form values should be stored under the `/form` path:
- `/form/accountType` - "personal" or "business"
- `/form/email` - email address string
- `/form/emailNotifications` - boolean
- `/form/smsNotifications` - boolean
- `/form/companyName` - company name string
- `/form/taxId` - tax ID string
- `/form/newPassword` - new password string
- `/form/confirmPassword` - confirm password string

Status messages should be stored at:
- `/ui/statusMessage` - success message string
- `/ui/errorMessage` - error message string

### Implementation Notes

You must use the data binding features to connect form fields to the data model paths. All conditional logic should be expressed declaratively, not in imperative code. String interpolation should be used in confirmation messages to reference form values.

## Test Cases

- When account type is "personal", business information fields are not rendered [@test](./test/settings.test.ts)
- When account type is "business", notification preference fields are not rendered [@test](./test/settings.test.ts)
- The confirm password field validates that it matches the new password field [@test](./test/settings.test.ts)
- The save button shows a confirmation dialog before executing the action [@test](./test/settings.test.ts)

## Implementation

[@generates](./src/settings-form.ts)

## Dependencies { .dependencies }

### @json-render/core { .dependency }

Provides types, schemas, visibility logic, actions, and validation for constrained UI generation.

### @json-render/react { .dependency }

Provides React renderer, providers, and hooks for json-render.

### zod { .dependency }

Schema validation library required by @json-render/core.
