<!--
CHUNK: 14
TITLE: Frontend (conditional - generate only when UI exists)
PROJECT: [Project Name]
VERSION: [X.X]
PART OF: LLD - [Project Name]
NOTE: This chunk is OMITTED when the LLD scope has no UI surface. Do not stub it.
-->

# 17. Frontend

> **Conventions per CLAUDE.md:**
> - Angular 17+, standalone components only (no NgModules).
> - `inject()` over constructor DI.
> - Signals for component state; NgRx SignalStore for shared/complex state; RxJS for streams only.
> - OnPush change detection by default.
> - Tailwind + PrimeNG (PrimeNG first, custom only when PrimeNG cannot do it).
> - Strict TypeScript, no `any`.
> - Routing via standalone APIs (`provideRouter`, `loadComponent`), no `RouterModule`.

## 17.1 Module / Component Tree

```text
[app-root]
  |-- [feature-shell]
  |     |-- [feature-list]
  |     |     |-- [feature-list-item]
  |     |-- [feature-detail]
  |-- [shared]
        |-- [components]
        |-- [services]
```

## 17.2 State Management Boundaries

| Boundary | Mechanism | Examples |
|----------|-----------|----------|
| Component-local state | Signal | Form values, toggle states, derived view |
| Feature-shared state | NgRx SignalStore | Selected tenant, current user, feature data cache |
| Streams | RxJS | HTTP responses, WebSocket subscriptions, debounced inputs |

## 17.3 Routing

| Route | Component | Guards | Lazy-loaded? |
|-------|-----------|--------|--------------|
| `/foo` | `FooListComponent` | `authGuard` | Yes (`loadComponent`) |
| `/foo/:id` | `FooDetailComponent` | `authGuard`, `tenantGuard` | Yes |

## 17.4 PrimeNG Components Used

| Component | Used in | Notes |
|-----------|---------|-------|
| `<p-table>` | `FooListComponent` | Server-side pagination, sortable, sticky header, bulk actions, CSV export, persistent per-user prefs |
| `<p-dialog>` | `FooDetailComponent` | Destructive actions use typed-name confirmation (CLAUDE.md UX rule) |

## 17.5 Theming

| Concern | Choice |
|---------|--------|
| Design tokens | `[token file path]` |
| Tenant theming | Brand color, logo, product name from tenant config at runtime |
| Dark mode | [Yes / No / Tenant-controlled] |

## 17.6 i18n

- **Library:** `@angular/localize` (or `ngx-translate`).
- **String policy:** no string concatenation. All strings via i18n keys.
- **RTL support:** logical CSS properties only (`margin-inline-start`, not `margin-left`); full Arabic support.
- **Locale formatting:** dates, numbers, currency formatted via tenant locale (not browser locale, per CLAUDE.md).

## 17.7 Accessibility (WCAG 2.1 AA)

| Concern | Approach |
|---------|----------|
| Semantic HTML | Default; no `<div>` for buttons / links |
| Keyboard navigation | Every interactive element reachable via keyboard |
| Focus indicators | Visible at all times |
| Contrast | 4.5:1 minimum |
| Forms | Validate on blur; errors explain how to fix |
| Empty / loading / error states | First-class — never expose stack traces |

## 17.8 Form Conventions

- Validate on blur.
- Errors explain how to fix.
- One convention for required vs optional, applied uniformly.
- Destructive actions: typed-name or two-step confirm (no generic "Are you sure?").

## 17.9 Component Architecture

- Presentational vs Container split.
- Logic in services / stores, not templates.
- Strict TypeScript everywhere.

<!-- MASTER: lld-master.md | PREV: 13-testing.md | NEXT: 15-open-questions.md -->
