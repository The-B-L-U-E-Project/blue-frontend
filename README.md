# BLUE Frontend

Full-stack React application built on TanStack Start, shadcn/ui, and Tailwind CSS.

For architectural decisions, design principles, and coding conventions, refer to [frontend-stack-guidelines.md](./frontend-stack-guidelines.md).

To add components to your app, run the following command:
---

## Prerequisites

- **Node.js** 20+
- **pnpm** (required — do not use npm or yarn)

Install pnpm if you don't have it:

```bash
npm install -g pnpm
```

---

## Getting started

```bash
pnpm install
```

---

## Development

```bash
pnpm dev
```

Starts the Vite dev server at [http://localhost:3000](http://localhost:3000) with HMR enabled.

---

## Build

```bash
pnpm build
```

Outputs a production build via Vite + Nitro. To preview the production build locally:

## Using components
```bash
pnpm preview
```

---

## Other scripts

| Command | Description |
|---|---|
| `pnpm typecheck` | Run TypeScript type checking without emitting files |
| `pnpm lint` | Lint with ESLint |
| `pnpm format` | Format all `.ts`, `.tsx`, `.js`, `.jsx` files with Prettier |
| `pnpm test` | Run unit tests with Vitest |

---

## Stack overview

| Concern | Library |
|---|---|
| Framework | [TanStack Start](https://tanstack.com/start) (Vite + Nitro) |
| Routing | [TanStack Router](https://tanstack.com/router) (file-based, type-safe) |
| Language | TypeScript (strict mode) |
| Styling | Tailwind CSS v4 |
| UI components | shadcn/ui (Radix primitives) |
| Icons | Lucide React |
| Testing | Vitest + React Testing Library |

See [frontend-stack-guidelines.md](./frontend-stack-guidelines.md) for the full rationale behind each choice and the conventions the team follows.

---

## Adding shadcn/ui components

```bash
npx shadcn@latest add button
```

Components are copied into `src/components/ui/` and can be customized freely.

```tsx
import { Button } from "@/components/ui/button";
```

---

## Project structure

```
src/
  routes/       # File-based routes (TanStack Router)
  components/
    ui/         # shadcn/ui primitives
  lib/          # Shared utilities and helpers
  api/          # API client and Zod schemas
```
