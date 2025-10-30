# Package Publishing Guide

## Overview

This monorepo uses **pnpm workspaces** with the **workspace protocol** for internal packages. By default, packages are NOT published to npm—they're used internally within the monorepo only.

## Internal Packages (Current Setup - Recommended)

### How It Works

Packages in `packages/` are consumed by apps in `apps/` using the workspace protocol:

```json
{
  "dependencies": {
    "@heidi/design-system": "workspace:*"
  }
}
```

### Benefits

- ✅ **Instant updates** - Changes to packages are immediately available to all apps
- ✅ **No publishing required** - Works entirely within the monorepo
- ✅ **Type safety** - TypeScript works across package boundaries
- ✅ **Single source of truth** - One version of each package
- ✅ **Simplified workflow** - No version bumping for internal changes

### Development Workflow

```bash
# Start package in watch mode
pnpm dev:design-system

# Start app (in another terminal)
pnpm dev:web

# Edit packages/design-system/src/components/Button.tsx
# → App automatically reloads with changes
```

### How Deployment Works

**During deployment:**

1. CI/CD runs `pnpm install` at monorepo root
2. pnpm resolves workspace dependencies via symlinks
3. Build tools (Next.js, Webpack) bundle package code into app
4. Deployed app contains all package code (no external dependencies)

**Example (Vercel deploying scribe-web):**

```bash
# Vercel does:
pnpm install                    # Installs all deps + links packages
cd apps/scribe-web
pnpm build                      # Builds app (includes @heidi/design-system)
# → .next/ folder contains bundled design-system code
```

### When Apps Get Updates

Apps get package updates in two ways:

1. **Development:** Instant via watch mode + hot reload
2. **Deployment:** During build step (fresh build includes latest package code)

---

## Publishing Packages to npm (Optional)

If you need to publish packages to npm (for external consumption, other repos, or versioning):

### 1. Configure Package for Publishing

Your `packages/design-system/package.json` is already set up:

```json
{
  "name": "@heidi/design-system",
  "version": "0.1.0",
  "private": false,              // ✅ Can be published
  "main": "./dist/index.cjs",
  "module": "./dist/index.js",
  "types": "./dist/index.d.ts",
  "files": ["dist"]              // ✅ Only ships built files
}
```

### 2. Using Changesets (Already Configured)

You already have `@changesets/cli` installed. Use it for versioning:

```bash
# Create a changeset (describes what changed)
pnpm changeset

# Example interaction:
# ? Which packages would you like to include? 
#   → Select: @heidi/design-system
# ? What kind of change? (major/minor/patch)
#   → Select: minor
# ? Please enter a summary
#   → "Added new Button variants"

# This creates a file in .changeset/ describing the change
```

### 3. Version and Publish

```bash
# Update package versions based on changesets
pnpm changeset version

# This will:
# - Update package.json versions
# - Update CHANGELOG.md
# - Delete processed changesets

# Publish to npm
pnpm publish-packages

# Or publish manually
cd packages/design-system
pnpm publish --access public
```

### 4. Apps Consuming Published Packages

If you publish to npm, apps can use either:

**Option A: Keep using workspace protocol (recommended in monorepo)**
```json
{
  "dependencies": {
    "@heidi/design-system": "workspace:*"
  }
}
```
When published, pnpm converts this to the actual version automatically.

**Option B: Use specific versions**
```json
{
  "dependencies": {
    "@heidi/design-system": "^0.2.0"
  }
}
```
But this defeats the purpose of the monorepo for internal apps.

---

## Recommended Strategy for Your Monorepo

### For Internal Packages (Design System, Shared Utils)

**Don't publish to npm.** Use workspace protocol:

```json
{
  "dependencies": {
    "@heidi/design-system": "workspace:*"
  }
}
```

**Advantages:**
- Atomic changes across apps and packages in single PR
- No version management overhead
- Instant updates during development
- Deploy apps directly with latest package code

**Update workflow:**
1. Edit package code
2. Apps automatically pick up changes (dev mode)
3. Deploy apps → they include latest package code

### For External Packages (Optional)

If you create a package that needs to be:
- Used outside the monorepo
- Versioned independently
- Published to npm registry

Then use changesets + publishing workflow.

---

## How Vercel and AWS Get Package Updates

### Vercel (scribe-web)

**During each deployment:**

```yaml
# Vercel automatically runs:
pnpm install          # Links workspace packages
pnpm run build        # Builds app with latest package code

# Result: Deployed app includes current package code
```

**To deploy with package changes:**
1. Edit `packages/design-system/src/Button.tsx`
2. Commit and push
3. Vercel detects changes and rebuilds
4. New deployment includes updated Button

### AWS CodeBuild (scribe-widget)

**During each build:**

```yaml
# buildspec.yml runs:
pnpm install --frozen-lockfile
cd apps/scribe-plugins/plugin
pnpm run build        # Webpack bundles with latest package code

# Result: Widget bundle includes current package code
```

**To deploy with package changes:**
1. Edit `packages/design-system/src/Button.tsx`
2. Commit and push
3. CodeBuild detects changes and rebuilds widget
4. New bundle includes updated Button

---

## Path-Based Build Triggers

Configure CI/CD to rebuild apps when packages change:

### Vercel
In project settings or `vercel.json`:

```json
{
  "ignoreCommand": "bash -c '[ -z $(git diff HEAD^ HEAD -- apps/scribe-web packages/) ]'"
}
```

Triggers rebuild if changes in:
- `apps/scribe-web/`
- `packages/` (any package)

### AWS CodeBuild

In webhook/trigger settings:
- **Include paths:** `apps/scribe-plugins/**`, `packages/**`
- **Exclude paths:** `apps/scribe-web/**`

This ensures widget rebuilds when design-system changes.

---

## Common Questions

### Q: Do I need to publish packages for apps to use them?
**A:** No! Workspace protocol provides instant access without publishing.

### Q: How do apps get package updates during deployment?
**A:** Fresh `pnpm install` + build includes latest package code automatically.

### Q: Should I version my internal packages?
**A:** Not necessary. Only version if you plan to publish to npm for external use.

### Q: What if I want to track package versions internally?
**A:** You can still use changesets to generate CHANGELOGs without publishing:
```bash
pnpm changeset
pnpm changeset version  # Updates versions + changelog
# Don't run publish
```

### Q: How do I share packages across separate repositories?
**A:** Then you MUST publish to npm (or private registry). The monorepo approach only works within a single repo.

---

## Summary

### Current Setup (Recommended)

```
┌─────────────────┐
│ packages/       │
│ design-system   │──┐
└─────────────────┘  │
                     │ workspace:*
                     │ (symlink in dev)
                     │ (bundled in prod)
        ┌────────────┴────────────┐
        ▼                         ▼
┌──────────────┐          ┌──────────────┐
│ apps/        │          │ apps/        │
│ scribe-web   │          │ scribe-       │
│              │          │ plugins      │
│ (Vercel)     │          │ (AWS)        │
└──────────────┘          └──────────────┘
```

**No publishing needed. Apps include package code during build.**

### Publishing Setup (If Needed)

```
┌─────────────────┐
│ packages/       │
│ design-system   │
└────────┬────────┘
         │
         │ pnpm publish
         ▼
    ┌─────────┐
    │   npm   │
    │ registry│
    └─────────┘
         │
         │ install (external consumers)
         ▼
  External projects
```

**Only needed for external consumption or separate repos.**

