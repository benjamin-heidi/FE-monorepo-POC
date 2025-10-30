# @heidi/design-system

Shared design system for Heidi Health applications.

## Installation

This package is part of the Heidi monorepo and is consumed via workspace protocol.

```json
{
  "dependencies": {
    "@heidi/design-system": "workspace:*"
  }
}
```

## Usage

### Import Components

```typescript
import { Button, Card } from "@heidi/design-system";

function App() {
  return (
    <Card>
      <Button variant="primary">Click me</Button>
    </Card>
  );
}
```

### Tailwind Configuration

This design system uses Tailwind CSS classes. You **must** include the design system source files in your consuming app's Tailwind configuration:

```typescript
// tailwind.config.ts
export default {
  content: [
    './src/**/*.{ts,tsx}',
    // Add this line to scan design-system components
    '../../packages/design-system/src/**/*.{ts,tsx}', // For scribe-web
    // OR
    '../../../packages/design-system/src/**/*.{ts,tsx}', // For scribe-plugins
  ],
  // ... rest of config
}
```

## Components

- **Button**: A versatile button component with multiple variants (primary, secondary, outline, ghost)
- **Card**: A card container with header, title, and content sub-components

## Development

### Local Development with Hot Reload

When developing components, run the design-system in watch mode alongside your consuming app:

```bash
# From monorepo root - runs both design-system and scribe-web
pnpm dev:web-with-ds

# OR for scribe-plugins
pnpm dev:widget-with-ds

# OR run design-system watch mode separately
pnpm dev:design-system
```

This enables:
- ⚡ Instant rebuilds (~50ms) when you edit components
- 🔄 Hot reload in your consuming app
- 📘 Live TypeScript type updates

### Building

```bash
# Build for production
pnpm --filter @heidi/design-system build

# Or from within the package directory
pnpm build
```

