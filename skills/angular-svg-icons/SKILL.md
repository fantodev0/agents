---
name: angular-svg-icons
description: Extract or add SVG icons in Angular apps using a typed icon registry and reusable standalone icon component; use for refactoring inline SVGs out of Angular templates or standardizing Tailwind/SSR-safe icon rendering.
---

# Angular SVG Icons

Use this pattern when Angular page templates contain repeated or bulky inline `<svg>` markup, or when adding app-owned icons without introducing an icon library.

## Recommended shape

- Keep SVG data in a shared registry, for example `src/app/icons/icon.registry.ts`.
- Export an `IconName` union from the registry keys so template usages are type-checked.
- Store structured icon definitions (`viewBox`, default `fill`, and `path` attributes), not raw HTML strings.
- Render icons through a small standalone component such as `app-icon`.
- Avoid `[innerHTML]` for SVG injection; Angular bindings over structured data are safer and SSR-friendly.

## Component behavior

- Accept `name: IconName` as a required input.
- Default icons to decorative: `aria-hidden="true"` when no label is supplied.
- Optionally accept an accessible label and set `role="img"`/`aria-label` on the host when the icon itself conveys meaning.
- Put sizing/color classes on the component host (`class="size-6 text-primary"`) and render the internal `<svg>` as `class="block size-full"` so Tailwind utilities inherit cleanly.
- Use `currentColor` for strokes/fills whenever possible so text color utilities control icon color.

## Refactor workflow

1. Inventory inline SVGs with `rg -n "<svg|</svg>" src/app --glob '*.html'`.
2. Group duplicate path data and give each icon a stable semantic name (`search`, `upload`, `alert-triangle`, app logo, etc.).
3. Create or update the registry and component.
4. Replace inline SVG blocks with compact usages like:

   ```html
   <app-icon name="search" class="size-4 shrink-0 text-primary" />
   ```

5. Import the standalone icon component in every standalone component whose template uses it.
6. Preserve existing visual classes and accessibility semantics; most icons beside visible text remain decorative.
7. Run formatting, Angular linting, and build/type checking.

## Validation checklist

- No page template inline SVGs remain except inside the shared icon component.
- Duplicate SVG path data is defined once in the registry.
- Existing Tailwind sizes/colors still apply from the `<app-icon>` host.
- Decorative icons are hidden from assistive tech; meaningful icon-only controls still have visible text, `aria-label`, or an icon label.
- SSR builds do not rely on browser-only APIs.
