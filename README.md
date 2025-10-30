# Heidi Health Monorepo

This is a proof-of-concept monorepo containing Heidi's frontend applications and shared packages using pnpm workspaces.

## 📦 Structure

```
monorepo-poc/
├── apps/
│   ├── scribe-fe-v2/          # Next.js web app with Tauri desktop
│   └── scribe-js-plugin/      # Webpack-based plugin/widget
├── packages/
│   └── design-system/         # Shared design system components
├── pnpm-workspace.yaml
├── package.json
├── tsconfig.base.json
└── .changeset/
```

## 🚀 Getting Started

### Prerequisites

- Node.js 20.9.0 (check `.nvmrc` in each app)
- pnpm 10.11.0

### Installation

Install all dependencies across the monorepo:

```bash
pnpm install
```

This will install dependencies for:
- Root workspace
- Design system package
- Both applications

### Development

#### Run all apps in parallel

```bash
pnpm dev
```

#### Run specific app

```bash
# Next.js app (scribe-fe-v2)
pnpm --filter scribe dev

# Plugin (scribe-js-plugin)
pnpm --filter plugin dev
```

#### Run design system in watch mode

```bash
pnpm --filter @heidi/design-system dev
```

### Building

#### Build everything

```bash
pnpm build
```

#### Build specific package/app

```bash
pnpm --filter @heidi/design-system build
pnpm --filter scribe build
pnpm --filter plugin build
```

## 📚 Packages

### @heidi/design-system

Shared component library used across all applications.

**Location:** `packages/design-system/`

**Components:**
- Button (with variants: primary, secondary, outline, ghost)
- Card (with Header, Title, Content sub-components)

**Usage:**
```typescript
import { Button, Card } from "@heidi/design-system";
```

## 🏗️ Applications

### scribe-fe-v2

**Location:** `apps/scribe-fe-v2/`

**Tech Stack:**
- Next.js 14
- React 18
- Tauri (desktop app)
- TailwindCSS

**Key Scripts:**
```bash
pnpm --filter scribe dev              # Development server
pnpm --filter scribe dev:https        # HTTPS development
pnpm --filter scribe build            # Production build
pnpm --filter scribe tauri:dev        # Tauri development
pnpm --filter scribe test             # Run tests
pnpm --filter scribe playwright:test  # E2E tests
```

### scribe-js-plugin

**Location:** `apps/scribe-js-plugin/plugin/`

**Tech Stack:**
- React 18
- Webpack 5
- TypeScript

**Key Scripts:**
```bash
pnpm --filter plugin dev                    # Development server
pnpm --filter plugin dev:staging            # Staging environment
pnpm --filter plugin build                  # Production build
pnpm --filter plugin build:staging          # Staging build
pnpm --filter plugin test                   # Run tests
pnpm --filter plugin playwright             # E2E tests
```

## 🔄 Versioning & Publishing

This monorepo uses [Changesets](https://github.com/changesets/changesets) for version management.

### Creating a changeset

When you make changes to the design system:

```bash
pnpm changeset
```

Follow the prompts to:
1. Select the package(s) that changed
2. Choose the bump type (major, minor, patch)
3. Write a summary of the changes

### Versioning packages

To bump versions and update CHANGELOGs:

```bash
pnpm version-packages
```

### Publishing

To publish packages (design system only, apps are private):

```bash
pnpm publish-packages
```

## 🧪 Testing

### Run all tests

```bash
pnpm test
```

### Run tests for specific app

```bash
pnpm --filter scribe test
pnpm --filter plugin test
```

## 🎨 Linting

### Lint all packages

```bash
pnpm lint
```

### Lint with auto-fix

```bash
pnpm lint:fix
```

## 📝 Workspace Commands Cheat Sheet

```bash
# Install dependencies
pnpm install

# Add dependency to specific package
pnpm --filter @heidi/design-system add react-icons
pnpm --filter scribe add axios

# Remove dependency
pnpm --filter @heidi/design-system remove react-icons

# Update dependencies
pnpm -r up                           # Update all packages
pnpm --filter @heidi/design-system up # Update specific package

# List workspace packages
pnpm list -r --depth=0

# Run script in all packages
pnpm -r <script-name>

# Run script in parallel
pnpm -r --parallel <script-name>

# Filter by directory pattern
pnpm --filter './apps/*' dev
pnpm --filter './packages/*' build
```

## 🔧 Configuration Files

- **`pnpm-workspace.yaml`**: Defines workspace packages
- **`package.json`**: Root package with workspace scripts
- **`.npmrc`**: pnpm configuration
- **`tsconfig.base.json`**: Shared TypeScript configuration
- **`.changeset/config.json`**: Changesets configuration

## 🏗️ Architecture Decisions

1. **Workspace Protocol**: Apps use `workspace:*` to depend on the design system, ensuring local development uses local packages
2. **Peer Dependencies**: Design system uses peer dependencies for React to avoid duplicate React copies
3. **Independent Versioning**: Each package can be versioned independently (not using fixed/locked versions)
4. **Private Apps**: App packages are marked as private and won't be published
5. **Shared Config**: Base TypeScript config is shared via `tsconfig.base.json`

## 📖 Additional Resources

- [pnpm Workspaces Documentation](https://pnpm.io/workspaces)
- [Changesets Documentation](https://github.com/changesets/changesets)
- [TypeScript Project References](https://www.typescriptlang.org/docs/handbook/project-references.html)

## 🤝 Contributing

When adding new shared components:

1. Add them to `packages/design-system/src/components/`
2. Export from `packages/design-system/src/index.ts`
3. Create a changeset: `pnpm changeset`
4. Test in both apps before publishing

## 📄 License

See individual package licenses.

