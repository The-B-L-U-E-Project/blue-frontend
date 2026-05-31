
# Frontend Stack & Design Guidelines
This document defines the frontend architecture, technology choices, and design principles for the BLUE project. It serves as the source of truth for engineers and designers joining the team and as a reference during design and code review.

---
## 1. User Experience Principles
These principles apply to every screen, every flow, and every interaction. When in doubt, default to them.

### Clarity over cleverness
The user should never wonder what something does or what just happened. Labels say what they do, buttons describe the action, empty states explain what to do next. If a UI element needs a tooltip to be understood, it probably needs better wording, but still, add a tooltop.
### One primary action per screen
Every screen should have one obvious next step. Secondary actions exist, but they should visually recede. A page with five buttons of equal weight has no primary action and the user has to think.
### Reduce, then reduce again
Cluttered interfaces hide important things behind noise. Before adding an element, ask whether removing something else would serve the user better. 
### Progressive disclosure
Show what users need at the moment they need it. Advanced options, filters, settings, and rarely-used controls should be tucked behind a clear entry point rather than competing for attention by default.
### Consistency builds trust
The same action should look the same, sit in the same place, and behave the same way across the system. Inconsistency forces users to re-learn the interface on every screen and erodes confidence.
### Feedback is non-negotiable
Every user action must produce a visible response within 100ms: a hover state, a loading indicator, a confirmation, an error. Silence makes users wonder if their click registered, and they'll click again.
### Forgive mistakes
Destructive actions require confirmation. Deletes should be reversible (undo, trash, soft delete) wherever possible. Form errors appear next to the field, in plain language, with a path to fix the problem.
### Performance is UX
A fast interface feels considered; a slow one feels broken. Target Largest Contentful Paint under 2.5s, Interaction to Next Paint under 200ms, and Cumulative Layout Shift under 0.1. Optimize images, prefetch on hover, paginate or virtualize long lists.
### Accessibility is the floor, not the ceiling
Every interactive element must be keyboard-navigable, screen-reader-accessible, and meet WCAG 2.2 AA contrast ratios. 
### Mobile is not a smaller desktop
Touch targets are at least 44×44px. Hover states have tap equivalents. Critical actions stay within thumb reach. Tests should eventually be done on real devices.

---
## 2. UI Principles
UX defines *how it works*. UI defines *how it looks*. The two are inseparable, but these principles apply specifically to visual decisions.
### Visual hierarchy through restraint
Size, weight, color, and spacing all communicate importance. Use the minimum contrast needed to establish hierarchy. For example, if everything is bold, nothing is bold.
### Whitespace is a design element
Generous spacing around content makes interfaces feel easy to use. Cramped layouts feel rash and stressful. Default to more space than feels necessary, then tighten only where density is genuinely required.
### Typography does most of the work
A strong type system, typically two or three sizes for body, a small set of heading scales, and one or two weights, handles the majority of visual hierarchy. Reach for color or decoration only when type alone isn't enough.
### Color with intention
Maintain a restrained palette: a neutral foundation, one or two accent colors, and semantic colors for success, warning, error, and info. 
### Components, not pages
Build a library of small, composable components and assemble pages from them. This produces visual consistency for free and makes the product feel like one product, not a collection of screens shipped by different teams.
### Match motion to meaning
Animations should clarify state changes, not perform. Most transitions should sit between 150–300ms with eased curves. Respect `prefers-reduced-motion`.
### Don't decorate, communicate
Every visual element should earn its place by helping the user understand or accomplish something. Decorative flourishes, gratuitous gradients, and "design for design's sake" add cognitive load without adding value.

---
## 3. Frontend Technology Stack
The stack is built around the TanStack ecosystem, with selected best-in-class libraries for concerns TanStack doesn't cover.
### Framework and routing

**TanStack Start**: full-stack React framework powered by Vite. Handles SSR, static prerendering, server functions, and the build pipeline. Chosen for its Vite-based developer experience and tight integration with the rest of the TanStack ecosystem.

**TanStack Router**: file-based, type-safe routing (included with Start). Provides end-to-end type safety from URL parameters to loader data, route-level data loading with built-in caching, and automatic preloading on hover. Routes are the unit of organization for the app.

### Language and validation

**TypeScript** in strict mode. All code is typed. No `any` in committed code without a comment justifying it.

**Zod** for runtime validation at every external boundary — API responses, form inputs, URL params, environment variables. Zod schemas double as TypeScript types via `z.infer`, so validation and types stay in sync.

### Data layer

**TanStack Query** for server state — caching, deduplication, background refetching, optimistic updates, and request retries. All API calls go through Query. Never `fetch` directly from a component.

**TanStack DB** *(optional, evaluate per feature)* — adds persistence, offline support, and reactive collections on top of Query. Use when the app needs to work offline or sync across tabs. Skip for simple read-mostly features.
### State management

**Zustand** for client state that needs to be shared across components but isn't server data, UI state, modal management, multi-step flow state. Small, simple, and stays out of the way.

**URL state via TanStack Router search params** for anything that should be shareable or survive a refresh: filters, pagination, selected tabs, search queries. The URL is the best state store you already have.
### UI components and styling

**shadcn/ui** as the component foundation. Components are copied into the codebase rather than installed as a dependency, which means they can be customized freely without fighting a library's API. Built on Radix primitives, so accessibility is handled at the primitive level.
**Tailwind CSS** for styling. All component styling uses Tailwind utilities. Custom CSS is reserved for things Tailwind can't express cleanly (complex animations, third-party integration styles).

**Design tokens layer** sitting underneath shadcn, colors, spacing scale, typography, radiuses, shadows, defined as CSS variables and consumed by Tailwind. This ensures visual consistency as the component library grows and makes theming (dark mode, white-labeling) trivial.

**Lucide** for icons. Single icon library, consistent visual weight across the product.

### Forms

**TanStack Form** for form state, validation, and submission. Type-safe end-to-end, integrates naturally with Zod schemas, and stays in the same ecosystem as the rest of the stack. Pairs with shadcn/ui form primitives.

### Images

**Cloudflare Images** (or R2 + Image Resizing) for storage, transformation, and delivery. Optimized formats (AVIF, WebP) negotiated automatically per client. Edge-delivered globally.

**Unpic React** as the image component. Provides the `next/image`-style API (responsive `srcset`, lazy loading, blur placeholders, layout shift prevention) and works with any image CDN. Never use a raw `<img>` for content images.

### Virtualization and large datasets

**TanStack Virtual** for any list, grid, or table over ~100 items. Renders only what's visible, keeping the DOM small and scrolling smooth.

**TanStack Table** when displaying tabular data with sorting, filtering, or column manipulation. Headless — pairs with shadcn table primitives for styling.

### Animation

**Motion** (formerly Framer Motion) for animations that require orchestration, gestures, or layout transitions. For simple transitions, prefer Tailwind's built-in transition utilities — don't reach for a library when CSS will do.

### Testing

**Vitest** for unit tests. Fast, Vite-native, Jest-compatible API.

**React Testing Library** for component tests. Test behavior from the user's perspective, not implementation details.

**Playwright** for end-to-end tests. Cover critical user flows: signup, authentication, primary actions, payment, anything user-facing that would be catastrophic if broken.

**Storybook** for component development and documentation. Every reusable component has a story. Doubles as a living design system reference.

### Observability

**Sentry** for error tracking and performance monitoring in production. Source maps uploaded on build. Errors triaged weekly.

**PostHog** for product analytics and session replay. Lets product and design see how users actually use the product, not just how we think they do.

### Tooling

**pnpm** as the package manager. Faster, stricter, better at monorepo scenarios than npm or yarn.

---
## 4. Conventions

A few conventions that keep code consistent and predictable across the team.

### Project structure
Routes live in `src/routes/`. Reusable components live in `src/components/` (with subdirectories for `ui/` shadcn primitives and feature-specific component folders). Shared logic lives in `src/lib/`. API client and Zod schemas live in `src/api/`.

### Component boundaries
A component file exports one component. If it grows past ~200 lines, split it. Co-locate small helper components next to their parent in the same file when they're not reused.

### Naming
Components in PascalCase. Hooks prefixed with `use`. Files match their default export. Boolean variables and props are named affirmatively (`isOpen`, not `isClosed`; `hasError`, not `noError`).

### Imports
Absolute imports from `src/` via TypeScript path aliases. Group imports: third-party first, internal modules second, relative imports last, types last within each group.

### Comments
Code explains *what*. Comments explain *why*. If a comment is restating the code, delete it. If a section needs a comment to be understood, see if it can be rewritten to not need one.
