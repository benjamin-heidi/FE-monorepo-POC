# Monorepo Architecture

Visual representation of the Heidi monorepo structure and relationships.

## Directory Structure

```
monorepo-poc/                                    [ROOT WORKSPACE]
│
├── 📄 pnpm-workspace.yaml                       Workspace definition
├── 📄 package.json                              Root package with unified scripts
├── 📄 tsconfig.base.json                        Shared TypeScript config
├── 📄 .npmrc                                    pnpm configuration
├── 📄 .gitignore                                Git ignore patterns
│
├── 📁 .changeset/                               Version management
│   ├── config.json
│   └── README.md
│
├── 📁 scripts/                                  Utility scripts
│   └── verify-setup.sh                          Setup verification
│
├── 📁 packages/                                 Shared packages (publishable)
│   └── 📦 design-system/                        @heidi/design-system
│       ├── package.json                         • name: @heidi/design-system
│       ├── tsconfig.json                        • version: 0.1.0
│       ├── README.md                            • private: false
│       └── src/                                 • exports: ESM + CJS
│           ├── index.ts
│           └── components/
│               ├── Button.tsx
│               └── Card.tsx
│
└── 📁 apps/                                     Applications (private)
    │
    ├── 📱 scribe-fe-v2/                         Next.js + Tauri Desktop App
    │   ├── package.json                         • name: scribe
    │   ├── next.config.js                       • depends on: @heidi/design-system
    │   ├── tailwind.config.ts                   • tech: Next.js 14, React 18, Tauri
    │   ├── tsconfig.json                        • private: true
    │   ├── src/
    │   ├── src-tauri/
    │   ├── public/
    │   └── ...
    │
    └── 📱 scribe-js-plugin/                     Webpack Plugin/Widget
        └── plugin/
            ├── package.json                     • name: plugin
            ├── webpack.config.cjs               • depends on: @heidi/design-system
            ├── tsconfig.json                    • tech: Webpack 5, React 18
            ├── src/                             • private: true
            ├── widget/
            └── ...
```

## Dependency Graph

```
┌─────────────────────────────────────────────────────────────┐
│                      Root Workspace                         │
│                   (@heidi/monorepo)                         │
└─────────────────────────────────────────────────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│   scribe     │    │    plugin    │    │design-system │
│  (Next.js)   │    │  (Webpack)   │    │  (Library)   │
│              │    │              │    │              │
│  Private ✓   │    │  Private ✓   │    │  Public ✓    │
└──────────────┘    └──────────────┘    └──────────────┘
       │                   │                   
       │                   │                   
       └────────┬──────────┘                   
                │                              
                │ depends on (workspace:*)     
                │                              
                ▼                              
        ┌──────────────┐                      
        │design-system │                      
        │              │                      
        │ @heidi/      │                      
        │ design-system│                      
        └──────────────┘                      
                │
                │ peer dependencies
                │
                ▼
        ┌──────────────┐
        │    React     │
        │  React DOM   │
        │              │
        │  (v18)       │
        └──────────────┘
```

## Package Relationships

```
┌────────────────────────────────────────────────────────────────┐
│                    @heidi/design-system                        │
│  ┌──────────────────────────────────────────────────────┐     │
│  │  Components                                          │     │
│  │  • Button (variants, sizes)                          │     │
│  │  • Card (Header, Title, Content)                     │     │
│  │  • ...more components                                │     │
│  └──────────────────────────────────────────────────────┘     │
│                           │                                    │
│                           │ exports                            │
│                           ▼                                    │
│  ┌──────────────────────────────────────────────────────┐     │
│  │  Output                                              │     │
│  │  • dist/index.js      (ESM)                          │     │
│  │  • dist/index.cjs     (CommonJS)                     │     │
│  │  • dist/index.d.ts    (TypeScript declarations)      │     │
│  └──────────────────────────────────────────────────────┘     │
└────────────────────────────────────────────────────────────────┘
                           │
                           │ imports
            ┌──────────────┴──────────────┐
            │                             │
            ▼                             ▼
┌─────────────────────┐       ┌─────────────────────┐
│  scribe-fe-v2       │       │ scribe-js-plugin    │
│  ┌───────────────┐  │       │  ┌───────────────┐  │
│  │  Next.js App  │  │       │  │  Widget/Plugin│  │
│  │               │  │       │  │               │  │
│  │  Uses:        │  │       │  │  Uses:        │  │
│  │  • Button     │  │       │  │  • Button     │  │
│  │  • Card       │  │       │  │  • Card       │  │
│  └───────────────┘  │       │  └───────────────┘  │
│                     │       │                     │
│  Tech:              │       │  Tech:              │
│  • Next.js 14       │       │  • Webpack 5        │
│  • Tauri            │       │  • React 18         │
│  • TailwindCSS      │       │  • TailwindCSS      │
└─────────────────────┘       └─────────────────────┘
```

## Build Flow

```
1. Install Dependencies
   ┌─────────────────────┐
   │  pnpm install       │
   └──────────┬──────────┘
              │
              ▼
   ┌─────────────────────┐
   │  Install all:       │
   │  • Root deps        │
   │  • design-system    │
   │  • scribe           │
   │  • plugin           │
   └──────────┬──────────┘
              │
              ▼

2. Build Design System (Required First)
   ┌─────────────────────┐
   │  tsup               │
   │  src/index.ts       │
   └──────────┬──────────┘
              │
              ▼
   ┌─────────────────────┐
   │  Output:            │
   │  • dist/index.js    │
   │  • dist/index.cjs   │
   │  • dist/index.d.ts  │
   └──────────┬──────────┘
              │
              ▼

3. Build Apps (Can run in parallel)
   ┌──────────┬──────────┐
   │          │          │
   ▼          ▼          ▼
┌─────┐  ┌─────┐  ┌─────┐
│Next │  │Webpack│  │Other│
│Build│  │ Build │  │ ... │
└─────┘  └───────┘  └─────┘
```

## Development Workflow

```
Terminal 1:                Terminal 2:              Terminal 3:
┌──────────────┐          ┌──────────────┐         ┌──────────────┐
│              │          │              │         │              │
│  pnpm --filter│         │  pnpm --filter│        │  pnpm --filter│
│  @heidi/     │          │  scribe dev  │         │  plugin dev  │
│  design-system│         │              │         │              │
│  dev         │          │  Runs on     │         │  Runs on     │
│              │          │  :3000       │         │  :8080       │
│  (Watch mode)│          │              │         │              │
│              │          │              │         │              │
└──────────────┘          └──────────────┘         └──────────────┘
       │                         │                        │
       │  rebuilds on change     │                        │
       └────────────┬────────────┴────────────────────────┘
                    │
                    │  Changes reflected immediately
                    │  via workspace: protocol
                    ▼
            ┌──────────────┐
            │  Hot reload  │
            │  in both apps│
            └──────────────┘
```

## Versioning Flow (Changesets)

```
1. Make Changes
   ┌─────────────────────────────┐
   │ Edit design-system/src/     │
   │ components/Button.tsx       │
   └──────────────┬──────────────┘
                  │
                  ▼
2. Create Changeset
   ┌─────────────────────────────┐
   │  pnpm changeset             │
   └──────────────┬──────────────┘
                  │
                  ▼
   ┌─────────────────────────────┐
   │ Select: @heidi/design-system│
   │ Type: minor                 │
   │ Summary: Add size prop to   │
   │          Button component   │
   └──────────────┬──────────────┘
                  │
                  ▼
   ┌─────────────────────────────┐
   │ .changeset/random-slug.md   │
   │ created                     │
   └──────────────┬──────────────┘
                  │
                  ▼
3. Version (when ready)
   ┌─────────────────────────────┐
   │  pnpm version-packages      │
   └──────────────┬──────────────┘
                  │
                  ▼
   ┌─────────────────────────────┐
   │ Updates:                    │
   │ • package.json (0.1.0→0.2.0)│
   │ • CHANGELOG.md              │
   │ • Removes changeset file    │
   └──────────────┬──────────────┘
                  │
                  ▼
4. Publish
   ┌─────────────────────────────┐
   │  pnpm publish-packages      │
   └──────────────┬──────────────┘
                  │
                  ▼
   ┌─────────────────────────────┐
   │  Published to npm registry  │
   └─────────────────────────────┘
```

## File Resolution

```
When app imports:
import { Button } from "@heidi/design-system"

1. pnpm checks package.json
   ┌─────────────────────────────┐
   │ "@heidi/design-system":     │
   │ "workspace:*"               │
   └──────────────┬──────────────┘
                  │
                  ▼
2. Resolves to local package
   ┌─────────────────────────────┐
   │ packages/design-system/     │
   └──────────────┬──────────────┘
                  │
                  ▼
3. Checks exports in package.json
   ┌─────────────────────────────┐
   │ "exports": {                │
   │   ".": {                    │
   │     "import": "./dist/...   │
   │     "require": "./dist/...  │
   │   }                         │
   │ }                           │
   └──────────────┬──────────────┘
                  │
                  ▼
4. Loads built file
   ┌─────────────────────────────┐
   │ dist/index.js               │
   └─────────────────────────────┘
```

## TypeScript Compilation

```
tsconfig hierarchy:

Root:
┌─────────────────────────────┐
│  tsconfig.base.json         │
│  • Target: ES2022           │
│  • Module: ESNext           │
│  • Shared base config       │
└──────────────┬──────────────┘
               │ extends
        ┌──────┴──────┬──────────────┐
        │             │              │
        ▼             ▼              ▼
┌──────────┐  ┌──────────┐  ┌──────────┐
│design-   │  │scribe    │  │plugin    │
│system    │  │          │  │          │
│          │  │ (optional)  │ (optional) │
│tsconfig  │  │extends   │  │extends   │
│extends ✓ │  │base      │  │base      │
└──────────┘  └──────────┘  └──────────┘
```

## Deployment Targets

```
┌─────────────────────────────────────────────────────────┐
│                    Production Build                     │
└───────────────────────┬─────────────────────────────────┘
                        │
        ┌───────────────┼───────────────┐
        │               │               │
        ▼               ▼               ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ Design System│ │   scribe     │ │   plugin     │
└──────┬───────┘ └──────┬───────┘ └──────┬───────┘
       │                │                │
       ▼                ▼                ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ npm registry │ │ Vercel/AWS   │ │ CDN/Hosting  │
│ (published)  │ │ (Next.js)    │ │ (bundle)     │
│              │ │              │ │              │
│ Public ✓     │ │ + Tauri      │ │              │
└──────────────┘ │ Desktop      │ └──────────────┘
                 └──────────────┘
```

## Key Patterns

### 1. Workspace Protocol
```
"@heidi/design-system": "workspace:*"
     │                       │      └─── Any version (latest local)
     │                       └────────── Workspace-local package
     └────────────────────────────────── Package name
```

### 2. Peer Dependencies
```
design-system declares React as peer dep
    ↓
scribe and plugin provide React
    ↓
Single React instance (no duplicates!)
```

### 3. Shared Config
```
Base config defines common settings
    ↓
Individual packages extend and override
    ↓
Consistency + Flexibility
```

## Performance Considerations

```
┌─────────────────────────────────────┐
│  Parallel Installation              │
│  pnpm uses hard links              │
│  → Faster, less disk space         │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  Incremental Builds                │
│  Only rebuild what changed         │
│  → Watch mode for design system    │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  Selective Filtering               │
│  Build only needed packages        │
│  → --filter flag                   │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  Tree Shaking                      │
│  Only bundle imported components   │
│  → ESM exports                     │
└─────────────────────────────────────┘
```

## Security Model

```
┌─────────────────────────────────────┐
│  Private Apps                       │
│  • scribe      (private: true)     │
│  • plugin      (private: true)     │
│  → Never published to npm          │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  Public Package                    │
│  • design-system (private: false)  │
│  → Can be published to npm         │
│  → Requires authentication         │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  Changesets Ignore List            │
│  • scribe (won't version)          │
│  • plugin (won't version)          │
│  → Only design-system versioned    │
└─────────────────────────────────────┘
```

