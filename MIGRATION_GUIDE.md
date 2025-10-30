# Migration Guide: Moving to Monorepo

This document explains what changed when we moved to a monorepo structure and how it affects your workflow.

## What Changed?

### Directory Structure

**Before:**
```
heidi/
├── scribe-fe-v2/
│   ├── package.json
│   ├── src/
│   └── ...
└── scribe-js-plugin/
    ├── plugin/
    │   ├── package.json
    │   ├── src/
    │   └── ...
    └── ...
```

**After:**
```
monorepo-poc/
├── apps/
│   ├── scribe-fe-v2/          # Moved here
│   │   ├── package.json       # ✨ Added @heidi/design-system dependency
│   │   ├── src/
│   │   └── ...
│   └── scribe-js-plugin/      # Moved here
│       └── plugin/
│           ├── package.json   # ✨ Added @heidi/design-system dependency
│           ├── src/
│           └── ...
├── packages/
│   └── design-system/         # ✨ NEW: Shared components
│       ├── package.json
│       ├── src/
│       │   ├── index.ts
│       │   └── components/
│       └── tsconfig.json
├── pnpm-workspace.yaml        # ✨ NEW: Workspace definition
├── package.json               # ✨ NEW: Root package
├── tsconfig.base.json         # ✨ NEW: Shared TS config
├── .npmrc                     # ✨ NEW: pnpm config
└── .changeset/                # ✨ NEW: Version management
```

## Breaking Changes

### None! 🎉

All existing code continues to work as-is. The monorepo structure is purely organizational.

## New Features

### 1. Shared Design System

You can now import shared components:

```typescript
import { Button, Card } from "@heidi/design-system";
```

### 2. Unified Commands

Run commands across all packages from the root:

```bash
# Before: cd into each repo and run commands
cd scribe-fe-v2 && pnpm dev
cd scribe-js-plugin/plugin && pnpm dev

# After: run from root with filters
pnpm --filter scribe dev
pnpm --filter plugin dev

# Or run all at once
pnpm dev
```

### 3. Shared Dependencies

React and React DOM are now managed as peer dependencies in the design system, ensuring no duplicate React copies.

## Workflow Changes

### Installing Dependencies

**Before:**
```bash
cd scribe-fe-v2
pnpm install

cd ../scribe-js-plugin/plugin
pnpm install
```

**After:**
```bash
# From monorepo root
pnpm install  # Installs everything
```

### Running Development Servers

**Before:**
```bash
# Terminal 1
cd scribe-fe-v2
pnpm dev

# Terminal 2
cd scribe-js-plugin/plugin
pnpm dev
```

**After:**
```bash
# Option 1: All at once
pnpm dev

# Option 2: Individual apps
pnpm --filter scribe dev
pnpm --filter plugin dev
```

### Adding Dependencies

**Before:**
```bash
cd scribe-fe-v2
pnpm add axios
```

**After:**
```bash
# From anywhere in monorepo
pnpm --filter scribe add axios
pnpm --filter plugin add axios

# Or cd into the app and use pnpm add as before
```

### Running Tests

**Before:**
```bash
cd scribe-fe-v2
pnpm test
```

**After:**
```bash
# Option 1: From root
pnpm --filter scribe test

# Option 2: Test all packages
pnpm test

# Option 3: Still works from within app directory
cd apps/scribe-fe-v2
pnpm test
```

### Building

**Before:**
```bash
cd scribe-fe-v2
pnpm build
```

**After:**
```bash
# From root
pnpm --filter scribe build

# Build everything (includes design system)
pnpm build
```

## File Path Updates

### Importing from Design System

When using the design system, no relative paths needed:

```typescript
// ✅ Correct
import { Button } from "@heidi/design-system";

// ❌ Don't do this
import { Button } from "../../packages/design-system/src/components/Button";
```

### Existing Internal Imports

All existing imports continue to work:

```typescript
// Still works exactly the same
import { MyComponent } from "@/components/MyComponent";
import { useAuth } from "@/hooks/useAuth";
```

## Configuration Files

### package.json Changes

Each app's `package.json` now includes:

```json
{
  "dependencies": {
    "@heidi/design-system": "workspace:*",
    // ... other dependencies
  }
}
```

The `workspace:*` protocol tells pnpm to use the local package during development.

### TypeScript Configuration

Apps can now optionally extend the base config:

```json
{
  "extends": "../../tsconfig.base.json",
  // ... app-specific overrides
}
```

**Note:** This is optional. Existing tsconfig.json files continue to work.

## Environment Variables

### No Changes Required

Environment variables work exactly as before:
- `.env` files stay in the app directories
- No changes to how they're accessed in code

## CI/CD Considerations

### Building in CI

Update your build commands:

```yaml
# Before
- run: pnpm install
- run: pnpm build

# After (if building from root)
- run: pnpm install
- run: pnpm --filter scribe build
- run: pnpm --filter plugin build

# Or build everything
- run: pnpm build
```

### Caching

Consider caching at the root level:

```yaml
- uses: actions/cache@v3
  with:
    path: |
      node_modules
      apps/*/node_modules
      packages/*/node_modules
    key: ${{ runner.os }}-pnpm-${{ hashFiles('**/pnpm-lock.yaml') }}
```

## Git Workflow

### Commits

No changes to git workflow. Commit as usual:

```bash
git add .
git commit -m "feat: add new feature"
```

### .gitignore

The root `.gitignore` handles common patterns. App-specific `.gitignore` files are still respected.

## Common Questions

### Q: Do I need to rebuild the design system after changes?

**A:** Only if not running in watch mode.

```bash
# Option 1: One-time build
pnpm --filter @heidi/design-system build

# Option 2: Watch mode (recommended during development)
pnpm --filter @heidi/design-system dev
```

### Q: Can I still work in individual app directories?

**A:** Yes! All existing commands work:

```bash
cd apps/scribe-fe-v2
pnpm dev      # Still works
pnpm test     # Still works
pnpm build    # Still works
```

### Q: What if I don't want to use the design system?

**A:** No problem! It's an optional dependency. You can:
- Not import it
- Remove it from package.json
- Continue using existing components

### Q: How do I share code between apps?

**A:** Create a new package:

```bash
# 1. Create package directory
mkdir packages/shared-utils

# 2. Add package.json
# 3. Add to both apps' dependencies:
{
  "dependencies": {
    "@heidi/shared-utils": "workspace:*"
  }
}
```

### Q: Does this affect bundle size?

**A:** No! Tree-shaking works the same:
- Only imported components are bundled
- Webpack/Next.js optimizations work as before
- The `workspace:*` protocol only affects development

### Q: Can I still use the old clone-and-install workflow?

**A:** Yes, but it's simpler now:

```bash
# Before: Clone and install each repo
git clone <scribe-fe-v2-url>
git clone <scribe-js-plugin-url>
cd scribe-fe-v2 && pnpm install
cd ../scribe-js-plugin/plugin && pnpm install

# After: Clone once and install once
git clone <monorepo-url>
cd monorepo-poc
pnpm install
```

## Rollback Plan

If you need to revert to the old structure:

```bash
# Move apps back to root level
mv apps/scribe-fe-v2 ../
mv apps/scribe-js-plugin ../

# Remove monorepo-specific files
rm -rf packages/ .changeset/
rm pnpm-workspace.yaml tsconfig.base.json

# Remove design system from app package.json files
# (manually edit package.json to remove @heidi/design-system)

# Reinstall in each app
cd ../scribe-fe-v2
pnpm install

cd ../scribe-js-plugin/plugin
pnpm install
```

## Benefits Summary

✅ **Single install command** for all projects  
✅ **Shared components** via design system  
✅ **Consistent tooling** across projects  
✅ **Easier dependency management**  
✅ **Better code sharing**  
✅ **Atomic commits** across related changes  
✅ **Simplified onboarding** for new developers  

## Need Help?

- See [README.md](./README.md) for full documentation
- See [QUICK_START.md](./QUICK_START.md) for getting started
- Check [pnpm workspace docs](https://pnpm.io/workspaces)

