---
name: angular-best-practices
description: Enforces modern Angular best practices (v20+), including Signals, Standalone Components, and the inject() function. Use this skill when writing, refactoring, or reviewing Angular component code, templates, or services, especially to ensure accessibility (a11y) and strict state management conventions are followed.
license: MIT
---

# Angular Professional Development

This skill enforces high-performance and accessible Angular code standards.

## TypeScript Best Practices

- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid the `any` type; use `unknown` when type is uncertain

## Angular Best Practices

- Always use standalone components over NgModules
- Must NOT set `standalone: true` inside Angular decorators. It's the default in Angular v20+.
- Use signals for state management
- Implement lazy loading for feature routes
- Do NOT use the `@HostBinding` and `@HostListener` decorators. Put host bindings inside the `host` object of the `@Component` or `@Directive` decorator instead
- Use `NgOptimizedImage` for all static images.
  - `NgOptimizedImage` does not work for inline base64 images.

## Accessibility Requirements

- It MUST pass all AXE checks.
- It MUST follow all WCAG AA minimums, including focus management, color contrast, and ARIA attributes.

### Components

- Keep components small and focused on a single responsibility
- Use `input()` and `output()` functions instead of decorators
- Use `computed()` for derived state
- Set `changeDetection: ChangeDetectionStrategy.OnPush` in `@Component` decorator
- Prefer Reactive forms instead of Template-driven ones
- Do NOT use `ngClass`, use `class` bindings instead
- Do NOT use `ngStyle`, use `style` bindings instead
- When using external templates/styles, use paths relative to the component TS file.

## State Management

- Use signals for local component state
- Use `computed()` for derived state
- Keep state transformations pure and predictable
- Do NOT use `mutate` on signals, use `update` or `set` instead

## Templates

- Keep templates simple and avoid complex logic
- Use native control flow (`@if`, `@for`, `@switch`) instead of `*ngIf`, `*ngFor`, `*ngSwitch`
- Use the async pipe to handle observables
- Do not assume globals like (`new Date()`) are available.
- Do not write arrow functions in templates (they are not supported).

## Services

- Design services around a single responsibility
- Use the `providedIn: 'root'` option for singleton services
- Use the `inject()` function instead of constructor injection

## File Naming

Enforce kebab-case naming for files (e.g., `user-profile.ts` for an user-profile component) and match Angular's conventions for file suffixes (e.g., `.ts`, `.service.ts`, etc.).

**Angular and TypeScript Best Practices**

- **Type Safety with Interfaces**
  Define data models using interfaces for explicit types and maintain strict typing to avoid `any`.

- **Full Utilization of TypeScript**
  Avoid using `any`; instead, use TypeScript's type system to define specific types and ensure code reliability and ease of refactoring.

- **Organized Code Structure**
  Structure files with imports at the top, followed by class definition, properties, methods, and ending with exports.

- **Optional Chaining & Nullish Coalescing**
  Leverage optional chaining (`?.`) and nullish coalescing (`??`) to prevent null/undefined errors elegantly.

- **Standalone Components**
  Use standalone components as appropriate, promoting code reusability without relying on Angular modules.

- **Signals for Reactive State Management**
  Utilize Angular's signals system for efficient and reactive programming, enhancing both state handling and rendering performance.

- **Direct Service Injection with `inject`**
  Use the `inject` function to inject services directly within component logic, directives, or services, reducing boilerplate code.

**File Structure and Naming Conventions**

- **Component Files**: `*.ts`
- **Template Files**: `*.html`
- **Service Files**: `*.service.ts`
- **Module Files**: `*.module.ts`
- **Directive Files**: `*.directive.ts`
- **Pipe Files**: `*.pipe.ts`
- **Test Files**: `*.spec.ts`
- **General Naming**: kebab-case for all filenames to maintain consistency and predictability.

- Dont put `component` in the name of a component file as well as template and stylesheet files.
  For example: for an `user-profile` component, name the component file `user-profile.ts`, the template file `user-profile.html`, and the stylesheet file `user-profile.css`.

- Place each component in its own folder, with the component file and other related files (e.g., `*.html`, `*.css`, `*.spec.ts`) placed alongside it.
- Since we use tailwindcss avoid using component stylesheets (when possible) and instead use tailwind classes directly in the template file.

  **Security Best Practices**

- Prevent XSS by relying on Angular's built-in sanitization and avoiding `innerHTML`.
- Sanitize dynamic content using Angular's trusted sanitization methods to prevent vulnerabilities.

**Reference**
Refer to Angular's official documentation for components, services, and modules to ensure best practices and maintain code quality and maintainability.
