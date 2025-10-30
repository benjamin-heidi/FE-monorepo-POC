# Quick Start Guide

This guide will help you get up and running with the Heidi monorepo.

## Step 1: Install Dependencies

From the root of the monorepo:

```bash
pnpm install
```

This single command installs dependencies for:
- ✅ Root workspace
- ✅ @heidi/design-system package
- ✅ scribe-fe-v2 app
- ✅ scribe-js-plugin app

## Step 2: Build the Design System

Before running the apps, build the design system once:

```bash
pnpm --filter @heidi/design-system build
```

Or run it in watch mode (recommended for development):

```bash
pnpm --filter @heidi/design-system dev
```

## Step 3: Run Your Apps

### Option A: Run all apps in parallel

```bash
pnpm dev
```

This starts:
- scribe-fe-v2 on `http://localhost:3000`
- scribe-js-plugin on `http://localhost:8080` (or configured port)

### Option B: Run apps individually

**Next.js App (scribe-fe-v2):**
```bash
pnpm --filter scribe dev
```

**Plugin App (scribe-js-plugin):**
```bash
pnpm --filter plugin dev
```

## Step 4: Using the Design System

### In scribe-fe-v2 (Next.js)

Create a new component or page:

```typescript
// apps/scribe-fe-v2/src/components/Example.tsx
import { Button, Card, CardHeader, CardTitle, CardContent } from "@heidi/design-system";

export default function Example() {
  return (
    <Card className="max-w-md">
      <CardHeader>
        <CardTitle>Welcome to Heidi</CardTitle>
      </CardHeader>
      <CardContent>
        <p className="mb-4">This uses the shared design system!</p>
        <div className="flex gap-2">
          <Button variant="primary">Primary</Button>
          <Button variant="secondary">Secondary</Button>
          <Button variant="outline">Outline</Button>
        </div>
      </CardContent>
    </Card>
  );
}
```

### In scribe-js-plugin

```typescript
// apps/scribe-js-plugin/plugin/src/components/Example.tsx
import { Button, Card, CardHeader, CardTitle, CardContent } from "@heidi/design-system";

export const Example = () => {
  return (
    <Card>
      <CardHeader>
        <CardTitle>Plugin Example</CardTitle>
      </CardHeader>
      <CardContent>
        <Button variant="primary" onClick={() => alert("Clicked!")}>
          Click Me
        </Button>
      </CardContent>
    </Card>
  );
};
```

## Step 5: Making Changes to the Design System

1. **Make your changes:**
   ```bash
   # Edit files in packages/design-system/src/
   ```

2. **Create a changeset:**
   ```bash
   pnpm changeset
   ```
   
   Follow the prompts:
   - Select `@heidi/design-system`
   - Choose version bump type (major/minor/patch)
   - Write a description of changes

3. **Your changes are automatically reflected:**
   - If running `pnpm --filter @heidi/design-system dev`, apps see changes immediately
   - Otherwise, rebuild: `pnpm --filter @heidi/design-system build`

## Common Development Tasks

### Add a new shared component

```bash
# 1. Create the component
touch packages/design-system/src/components/NewComponent.tsx

# 2. Export it
# Add to packages/design-system/src/index.ts:
# export * from "./components/NewComponent";

# 3. Rebuild
pnpm --filter @heidi/design-system build
```

### Add a dependency to the design system

```bash
pnpm --filter @heidi/design-system add <package-name>
```

### Add a dependency to an app

```bash
# For scribe-fe-v2:
pnpm --filter scribe add <package-name>

# For scribe-js-plugin:
pnpm --filter plugin add <package-name>
```

### Run tests

```bash
# All tests
pnpm test

# Specific app
pnpm --filter scribe test
pnpm --filter plugin test
```

### Lint and format

```bash
# Lint all
pnpm lint

# Lint with auto-fix
pnpm lint:fix

# Specific app
pnpm --filter scribe lint:fix
```

### Build for production

```bash
# Build everything
pnpm build

# Build specific app
pnpm --filter scribe build
pnpm --filter plugin build
```

## Troubleshooting

### "Module not found: @heidi/design-system"

**Solution:** Build the design system first:
```bash
pnpm --filter @heidi/design-system build
```

### Stale design system changes

**Solution:** Rebuild the design system or run it in watch mode:
```bash
pnpm --filter @heidi/design-system dev
```

### Type errors from design system

**Solution:** Ensure TypeScript declarations are generated:
```bash
pnpm --filter @heidi/design-system build
```

### pnpm install failures

**Solution:** Clear cache and reinstall:
```bash
rm -rf node_modules apps/*/node_modules packages/*/node_modules
pnpm store prune
pnpm install
```

## Next Steps

1. ✅ Read the main [README.md](./README.md) for full documentation
2. ✅ Explore the design system at `packages/design-system/`
3. ✅ Check out existing apps at `apps/`
4. ✅ Learn about [Changesets](https://github.com/changesets/changesets) for versioning

## Helpful Commands Reference

```bash
# Install all dependencies
pnpm install

# Run all apps in dev mode
pnpm dev

# Build everything
pnpm build

# Run tests everywhere
pnpm test

# Lint everything
pnpm lint

# Create a changeset
pnpm changeset

# Apply version bumps
pnpm version-packages

# Filter commands by package
pnpm --filter <package-name> <script>

# Examples:
pnpm --filter @heidi/design-system dev
pnpm --filter scribe test
pnpm --filter plugin build

# Run in all packages
pnpm -r <script>

# Run in parallel
pnpm -r --parallel <script>

# List all packages
pnpm list -r --depth=0
```

Happy coding! 🚀

