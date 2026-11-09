---
name: angular-project-structure
description: Guides Angular standalone/SSR project structure using core, shared, and pages. Use when generating, moving, or reviewing Angular files; deciding where to place route-level components, page-local subviews, services, guards, interceptors, i18n/data/types, reusable UI, providers, or SSR/prerender wiring.
license: MIT
---

# Angular Project Structure

Use this skill to place Angular code in the right folder and preserve the local architecture. The reference project is an Angular 21 standalone + SSR/prerender app for the Afroschool marketing, onboarding, and founder dashboard flows.

## Root wiring files are not feature folders

Keep application-wide bootstrap, routing, providers, and server concerns in the top-level app/server files:

- `src/app/app.ts`, `src/app/app.html`, `src/app/app.css`: root shell and global layout orchestration.
- `src/app/app.routes.ts`: the client route table. Route pages are lazy standalone components loaded with `loadComponent()`. Route guards are attached here.
- `src/app/app.config.ts`: app-wide providers such as `provideRouter`, hydration, HTTP/interceptors, and Lucide icon registration.
- `src/app/app.config.server.ts`: server-side provider merge/configuration.
- `src/app/app.routes.server.ts`: SSR/prerender route rendering configuration.
- `src/server.ts`: Express SSR entry point and `/api` proxy. Do not treat it as an Angular feature folder.

Do not put provider registration, route definitions, SSR route config, or Express proxy logic inside `core`, `shared`, or `pages` unless the existing app explicitly does so.

## Three-tier app hierarchy

### `src/app/core/` — non-UI application logic

Use `core` for logic that is app-wide, UI-agnostic, or tied to backend/domain behavior:

- `core/services/`: injectable business/API/state services such as auth, modal state, translation, lead submission, payment/configuration summaries, onboarding polling, and account draft state.
- `core/guards/`: functional router guards such as `dashboard-auth.guard.ts`.
- `core/interceptors/`: functional HTTP interceptors such as `auth.interceptor.ts`, registered from `app.config.ts`.
- `core/types/`: domain DTOs, API contracts, payload types, constants, and pure helper functions tied to those contracts.
- `core/data/`: static/domain datasets and lookup helpers such as locations, FAQ content, subscription pricing, and localized content exports.
- `core/i18n/`: translation schema and language dictionaries such as `en`, `fr`, and `index` exports.

`core` code should not own reusable visual components or page templates.

### `src/app/shared/` — reusable UI and template utilities

Use `shared` for UI primitives and utilities reused across multiple pages or flows:

- `shared/components/`: reusable cross-page components such as `header`, `footer`, `hero`, `auth-modal`, `language-selector`, `image-with-fallback`, and dashboard layout pieces.
- `shared/components/<component>/<component>.ts|html`: preferred local component-folder pattern with concise file names.
- Nested reusable component families are allowed when scoped to a shared widget, for example `shared/components/auth-modal/login`, `signup`, `forgot-password`, and `reset-password`.
- `shared/directives/`: reusable DOM behavior such as reveal animations.
- `shared/pipes/`: reusable template transforms such as `TranslatePipe`.

Promote a component to `shared/components` only when it is reused by multiple pages or clearly acts as an app-wide UI primitive.

### `src/app/pages/` — route-level screens and page-local flows

Use `pages` for components that correspond to routes:

- Create one folder per route or route flow, such as `home`, `admin`, `parent`, `initialize`, `configuration`, `dashboard`, `confirmation`, `reset-password`, and informational/legal pages.
- Route pages are lazy-loaded from `app.routes.ts` and commonly named `*PageComponent` where the surrounding code does so.
- Pages orchestrate shared UI, forms, route state, and core services. They should not become the permanent home for app-wide business logic.

## Page-local subviews and page-only code

When a component is only used by one route flow, keep it inside that page folder instead of moving it to `shared`.

Use `pages/<route>/views/<view>/<view>.ts|html` for multi-step or page-specific subviews. This app uses that pattern for flows such as:

- `pages/initialize/views/personal-info`
- `pages/initialize/views/school-info`
- `pages/initialize/views/subscription`
- `pages/initialize/views/configuration`
- `pages/initialize/views/payment`
- `pages/configuration/views/contact`
- `pages/configuration/views/payment`
- `pages/configuration/views/setup`
- `pages/configuration/views/ready`

Keep page-local view models, small page-only types, and helper data beside the page when they only support that page, for example `pages/initialize/initialize-view-models.ts`. Move them to `core/types`, `core/data`, or `core/services` only when they become domain-wide or are reused by other pages.

## Placement decision rules

- API calls, auth/session/onboarding/payment state, persistence, DTO mapping -> `core/services` plus `core/types`.
- Static data, lookup tables, pricing, localized domain content -> `core/data` or `core/i18n`.
- Router access rules -> `core/guards`, attached in `app.routes.ts`.
- HTTP request/response mutation -> `core/interceptors`, registered in `app.config.ts`.
- Cross-page layout, reusable widgets, or visual controls -> `shared/components`.
- Reusable DOM behavior -> `shared/directives`.
- Reusable template transforms -> `shared/pipes`.
- Route screens -> `pages/<route>` with a lazy `app.routes.ts` entry.
- Route-specific subcomponents -> `pages/<route>/views/...` until reused elsewhere.
- SSR/prerender behavior -> `app.config.server.ts`, `app.routes.server.ts`, and `src/server.ts`, not feature folders.

## Local Angular conventions to preserve

- Use standalone components with `imports: [...]`; do not introduce NgModule feature folders.
- Prefer external templates (`templateUrl`) and concise file names such as `home.ts` and `header.ts` rather than `*.component.ts`, unless the surrounding folder already uses another convention.
- Preserve existing naming quirks unless doing an intentional cleanup, for example component folders/files with legacy casing.
- Prefer `ChangeDetectionStrategy.OnPush` for components.
- Prefer `inject()` for dependency injection.
- Prefer Angular signals/computed for component and service state.
- Use `input()` and `output()` for component inputs/outputs where appropriate.
- Keep route definitions in `app.routes.ts` and protect onboarding/dashboard routes with `core` guards.
- Register global providers, interceptors, and icon sets in `app.config.ts`, not in page components.
- Keep TypeScript and Angular templates strict-friendly.
- Guard browser-only APIs (`window`, `document`, `localStorage`, `IntersectionObserver`, timers) for SSR/prerender with `isPlatformBrowser`, `typeof`, `afterNextRender`, or equivalent patterns.

## Review and output expectations

When using this skill:

- State the intended file paths for new Angular code before or while implementing.
- Explain whether each new file is app-wide (`core`/`shared`) or page-local (`pages/<route>`) and why.
- Mention required route/provider/SSR registration changes in `app.routes.ts`, `app.config.ts`, `app.config.server.ts`, `app.routes.server.ts`, or `src/server.ts` when applicable.
- Prefer tests beside the implementation using the existing `*.spec.ts` style when adding meaningful logic.
- Keep pages thin: page components orchestrate; reusable business logic and API integration belong in `core`.
