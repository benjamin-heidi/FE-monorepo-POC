# Command Cheat Sheet

Quick reference for common monorepo commands.

## 📦 Installation

```bash
# Install all dependencies
pnpm install

# Install in specific package
pnpm --filter <package> install

# Add dependency to specific package
pnpm --filter @heidi/design-system add react-icons
pnpm --filter scribe add axios
pnpm --filter plugin add lodash

# Add dev dependency
pnpm --filter @heidi/design-system add -D typescript

# Remove dependency
pnpm --filter scribe remove axios
```

## 🚀 Development

```bash
# Start all apps in parallel
pnpm dev

# Start specific app
pnpm --filter scribe dev              # Next.js app
pnpm --filter plugin dev              # Plugin app

# Start design system in watch mode
pnpm --filter @heidi/design-system dev

# Start multiple specific packages
pnpm --filter scribe --filter plugin dev
```

## 🏗️ Building

```bash
# Build everything
pnpm build

# Build specific package
pnpm --filter @heidi/design-system build
pnpm --filter scribe build
pnpm --filter plugin build

# Build in topological order (dependencies first)
pnpm -r build
```

## 🧪 Testing

```bash
# Run all tests
pnpm test

# Test specific package
pnpm --filter scribe test
pnpm --filter plugin test

# Run tests in watch mode
pnpm --filter scribe test -- --watch

# E2E tests
pnpm --filter scribe playwright:test
pnpm --filter plugin playwright
```

## 🎨 Linting

```bash
# Lint all packages
pnpm lint

# Lint with auto-fix
pnpm lint:fix

# Lint specific package
pnpm --filter scribe lint
pnpm --filter scribe lint:fix

# Check formatting without fixing
pnpm --filter scribe lint:check
```

## 🔄 Versioning with Changesets

```bash
# Create a changeset
pnpm changeset

# Apply version bumps
pnpm version-packages

# Publish packages
pnpm publish-packages

# Check changeset status
pnpm changeset status
```

## 📊 Information

```bash
# List all workspace packages
pnpm list -r --depth=0

# Show dependency tree
pnpm why <package-name>

# Show outdated dependencies
pnpm outdated -r

# Show package info
pnpm --filter scribe list --depth=0
```

## 🔍 Filtering

```bash
# Filter by package name
pnpm --filter scribe <command>
pnpm --filter @heidi/design-system <command>

# Filter by directory pattern
pnpm --filter './apps/*' <command>
pnpm --filter './packages/*' <command>

# Filter multiple packages
pnpm --filter scribe --filter plugin <command>

# Exclude packages
pnpm --filter '!scribe' <command>
```

## 🔧 Updating

```bash
# Update all dependencies
pnpm -r up

# Update specific package everywhere
pnpm -r up react

# Update to latest
pnpm -r up --latest

# Interactive update
pnpm -r up -i
```

## 🧹 Cleaning

```bash
# Remove all node_modules
rm -rf node_modules apps/*/node_modules packages/*/node_modules

# Remove build artifacts
rm -rf apps/*/dist apps/*/.next packages/*/dist

# Clean pnpm store
pnpm store prune

# Full clean and reinstall
rm -rf node_modules apps/*/node_modules packages/*/node_modules pnpm-lock.yaml
pnpm install
```

## 🎯 Running Scripts

```bash
# Run script in all packages
pnpm -r <script>

# Run script in parallel
pnpm -r --parallel <script>

# Run script sequentially
pnpm -r --workspace-concurrency=1 <script>

# Run custom script
pnpm --filter scribe <custom-script>
```

## 📝 Workspace-Specific

```bash
# Add workspace dependency
pnpm --filter scribe add @heidi/design-system@workspace:*

# Link local packages
pnpm install

# Check workspace configuration
cat pnpm-workspace.yaml
```

## 🔐 Environment & Config

```bash
# Use specific node version (if using nvm)
nvm use

# Check pnpm config
pnpm config list

# Set registry
pnpm config set registry https://registry.npmjs.org/
```

## 🐛 Debugging

```bash
# Verbose output
pnpm --filter scribe build --reporter=default

# Show recursive dependencies
pnpm list -r --depth=1

# Check for duplicate dependencies
pnpm dedupe --check

# Verify pnpm-lock.yaml
pnpm install --frozen-lockfile
```

## 🚢 Production

```bash
# Install production dependencies only
pnpm install --prod

# Build for production
NODE_ENV=production pnpm build

# Prune dev dependencies
pnpm prune --prod
```

## 📦 Design System Specific

```bash
# Watch mode (rebuilds on changes)
pnpm --filter @heidi/design-system dev

# One-time build
pnpm --filter @heidi/design-system build

# Test imports
pnpm --filter @heidi/design-system test

# Publish to npm
pnpm --filter @heidi/design-system publish
```

## 🏃 App-Specific

### scribe-fe-v2 (Next.js)
```bash
pnpm --filter scribe dev              # Development server
pnpm --filter scribe dev:https        # HTTPS dev server
pnpm --filter scribe build            # Production build
pnpm --filter scribe start            # Start production server
pnpm --filter scribe test             # Run tests
pnpm --filter scribe playwright:test  # E2E tests
pnpm --filter scribe tauri:dev        # Tauri desktop dev
pnpm --filter scribe storybook        # Start Storybook
```

### scribe-js-plugin (Webpack)
```bash
pnpm --filter plugin dev                    # Development server
pnpm --filter plugin dev:staging            # Staging environment
pnpm --filter plugin dev:local-backend      # Local backend
pnpm --filter plugin build                  # Production build
pnpm --filter plugin build:staging          # Staging build
pnpm --filter plugin test                   # Run tests
pnpm --filter plugin playwright             # E2E tests
```

## 💡 Tips

### Use aliases in your shell
```bash
# Add to ~/.zshrc or ~/.bashrc
alias pf='pnpm --filter'
alias pfb='pnpm --filter @heidi/design-system build'
alias pfd='pnpm --filter @heidi/design-system dev'
alias pfs='pnpm --filter scribe'
alias pfp='pnpm --filter plugin'

# Usage:
pfs dev
pfp build
pfd
```

### Run commands from app directory
```bash
# You can still cd into app directories
cd apps/scribe-fe-v2
pnpm dev    # Works the same
pnpm build  # Works the same
```

### Parallel execution
```bash
# Run builds in parallel (faster)
pnpm -r --parallel build

# But be careful with resource-intensive tasks
# Use concurrency limit if needed
pnpm -r --workspace-concurrency=2 build
```

### Check what will be published
```bash
pnpm --filter @heidi/design-system publish --dry-run
```

## 🔗 Quick Links

- [pnpm CLI](https://pnpm.io/cli/add)
- [pnpm Filtering](https://pnpm.io/filtering)
- [Changesets CLI](https://github.com/changesets/changesets/blob/main/packages/cli/README.md)

