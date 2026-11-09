---
name: tailwind-v4
description: Expert guidance for styling with Tailwind CSS v4, including modern utility classes, dark mode implementation, responsive design patterns, and component composition. Use this skill when styling HTML, creating UI components, implementing dark mode, or working with Tailwind CSS v4 in any project.
---

# Tailwind CSS v4 Styling Skill

This skill provides comprehensive guidance for building beautiful, responsive, and accessible user interfaces with Tailwind CSS v4 using modern utility classes and best practices.

## Purpose

Provide expert-level Tailwind CSS v4 guidance covering:
- Modern v4 utility classes and deprecation updates
- Dark mode implementation and patterns
- Responsive design with mobile-first approach
- Common component patterns and layouts
- Spacing, typography, and color systems
- Interactive states and transitions
- Best practices for maintainable styling

## When to Use

Use this skill when:
- Styling HTML elements or components
- Creating UI components or layouts
- Implementing or updating dark mode
- Working with responsive designs
- Converting from Tailwind v3 to v4
- Building forms, cards, navigation, or other UI patterns
- Applying consistent spacing and typography
- Using Tailwind CSS v4 in any project

## Critical v4 Updates

### Import Changes

Tailwind v4 uses CSS `@import` instead of `@tailwind` directives:

```css
/* ❌ OLD (v3) */
@tailwind base;
@tailwind components;
@tailwind utilities;

/* ✅ NEW (v4) */
@import "tailwindcss";
```

### Deprecated Utilities

Tailwind v4 removed deprecated utilities. Always use replacements:

| Deprecated | Replacement | Example |
|------------|-------------|---------|
| `bg-opacity-*` | `bg-{color}/*` | `bg-black/50` |
| `text-opacity-*` | `text-{color}/*` | `text-blue-600/75` |
| `border-opacity-*` | `border-{color}/*` | `border-gray-300/60` |
| `flex-shrink-*` | `shrink-*` | `shrink-0` |
| `flex-grow-*` | `grow-*` | `grow` |
| `overflow-ellipsis` | `text-ellipsis` | `text-ellipsis` |
| `decoration-slice` | `box-decoration-slice` | `box-decoration-slice` |
| `decoration-clone` | `box-decoration-clone` | `box-decoration-clone` |

**Note**: Opacity values remain numeric (0-100), not percentages.

```html
<!-- ✅ CORRECT v4 syntax -->
<div class="bg-blue-500/50 text-gray-900/80 border-gray-300/60">
  Content with opacity
</div>

<!-- ❌ WRONG (v3 syntax) -->
<div class="bg-blue-500 bg-opacity-50 text-gray-900 text-opacity-80">
  Don't use this
</div>
```

Read `references/utilities.md` for complete utility class reference.

## Core Principles

### 1. Use Gap for Spacing in Flex/Grid

Always prefer `gap-*` utilities over margins for spacing items in flex or grid layouts:

```html
<!-- ✅ CORRECT - Use gap -->
<div class="flex gap-8">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>

<!-- ❌ WRONG - Don't use margins -->
<div class="flex">
  <div class="mr-8">Item 1</div>
  <div class="mr-8">Item 2</div>
  <div>Item 3</div>
</div>
```

### 2. Mobile-First Responsive Design

Start with mobile styles, then add larger breakpoints:

```html
<!-- Mobile first - text is small by default, larger on bigger screens -->
<h1 class="text-2xl md:text-4xl lg:text-5xl xl:text-6xl">
  Responsive Heading
</h1>

<!-- Responsive grid -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
  <div>Item</div>
  <div>Item</div>
  <div>Item</div>
</div>
```

Breakpoints: `sm` (640px), `md` (768px), `lg` (1024px), `xl` (1280px), `2xl` (1536px)

### 3. Always Support Dark Mode

If the project supports dark mode, apply dark mode variants to all components:

```html
<div class="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
  <!-- Background and text adapt to theme -->
  <h2 class="text-2xl font-bold text-gray-900 dark:text-white mb-4">
    Title
  </h2>
  <p class="text-gray-600 dark:text-gray-300">
    Content that works in both themes
  </p>
</div>
```

Read `references/dark-mode.md` for comprehensive dark mode patterns.

### 4. Think Through Class Order

Group utilities logically for readability:

```html
<!-- Good: Layout → Spacing → Typography → Colors → States -->
<button class="flex items-center gap-2 px-4 py-2 rounded text-lg font-semibold bg-blue-600 text-white hover:bg-blue-700 focus:ring-2 focus:ring-blue-500 transition-colors">
  Click Me
</button>
```

### 5. Remove Redundant Classes

Don't duplicate inherited properties:

```html
<!-- ❌ WRONG - Redundant classes -->
<div class="flex flex-row">Content</div>

<!-- ✅ CORRECT - flex-row is default -->
<div class="flex">Content</div>
```

## Common Patterns

### Card Component

```html
<div class="bg-white dark:bg-gray-800 rounded-lg shadow-lg p-6 border border-gray-200 dark:border-gray-700">
  <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-2">
    Card Title
  </h3>
  <p class="text-gray-600 dark:text-gray-300">
    Card content with proper dark mode support.
  </p>
</div>
```

### Button Variants

```html
<!-- Primary -->
<button class="bg-blue-600 hover:bg-blue-700 dark:bg-blue-500 dark:hover:bg-blue-600 text-white px-4 py-2 rounded font-medium transition-colors">
  Primary
</button>

<!-- Secondary -->
<button class="bg-gray-200 hover:bg-gray-300 dark:bg-gray-700 dark:hover:bg-gray-600 text-gray-900 dark:text-white px-4 py-2 rounded font-medium transition-colors">
  Secondary
</button>

<!-- Outline -->
<button class="border-2 border-blue-600 dark:border-blue-400 text-blue-600 dark:text-blue-400 hover:bg-blue-50 dark:hover:bg-blue-900/20 px-4 py-2 rounded font-medium transition-colors">
  Outline
</button>
```

### Form Input

```html
<input 
  type="text" 
  placeholder="Enter text..." 
  class="w-full px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-800 text-gray-900 dark:text-white placeholder-gray-400 dark:placeholder-gray-500 focus:ring-2 focus:ring-blue-500 dark:focus:ring-blue-400 focus:border-transparent transition-colors"
>
```

### Navigation Bar

```html
<nav class="bg-white dark:bg-gray-900 border-b border-gray-200 dark:border-gray-800 shadow-sm">
  <div class="container mx-auto px-4 py-3">
    <div class="flex items-center justify-between">
      <a href="/" class="text-xl font-bold text-gray-900 dark:text-white">
        Logo
      </a>
      <div class="flex gap-6">
        <a href="/about" class="text-gray-700 dark:text-gray-300 hover:text-blue-600 dark:hover:text-blue-400 transition-colors">
          About
        </a>
        <a href="/contact" class="text-gray-700 dark:text-gray-300 hover:text-blue-600 dark:hover:text-blue-400 transition-colors">
          Contact
        </a>
      </div>
    </div>
  </div>
</nav>
```

Read `references/patterns.md` for comprehensive component patterns.

## Reference Files

This skill includes detailed reference files:

- **`references/utilities.md`** - Complete v4 utility class reference, deprecated utilities, color system, layout, typography, borders, shadows
- **`references/dark-mode.md`** - Dark mode implementation, color schemes, toggle patterns, all component variations
- **`references/patterns.md`** - Layout patterns, card components, buttons, forms, navigation, modals

Read the appropriate reference file(s) when working on specific styling tasks.

## Working with Existing Projects

### Check Existing Conventions

Before writing new styles, check sibling files for patterns:

```html
<!-- If project uses this pattern for buttons: -->
<button class="btn-primary">Click</button>

<!-- Follow the same pattern instead of: -->
<button class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded">
  Click
</button>
```

### Extract Repeated Patterns

When seeing the same utilities repeated, suggest creating a component:

```html
<!-- If this appears multiple times: -->
<button class="bg-blue-600 hover:bg-blue-700 dark:bg-blue-500 dark:hover:bg-blue-600 text-white px-4 py-2 rounded font-medium transition-colors">
  Button
</button>

<!-- Suggest extracting to a reusable component -->
```

## Interactive States

Always include hover, focus, and active states:

```html
<button class="
  bg-blue-600 
  hover:bg-blue-700 
  active:bg-blue-800 
  focus:ring-2 
  focus:ring-blue-500 
  focus:ring-offset-2
  dark:bg-blue-500 
  dark:hover:bg-blue-600 
  dark:active:bg-blue-700
  dark:focus:ring-blue-400
  text-white 
  px-4 py-2 
  rounded 
  transition-colors
  disabled:opacity-50 
  disabled:cursor-not-allowed
">
  Interactive Button
</button>
```

## Color Usage Best Practices

### Recommended Dark Mode Colors

```html
<!-- Backgrounds -->
<div class="bg-white dark:bg-gray-900">Primary</div>
<div class="bg-gray-50 dark:bg-gray-800">Secondary</div>
<div class="bg-gray-100 dark:bg-gray-700">Tertiary</div>

<!-- Text -->
<p class="text-gray-900 dark:text-white">Primary text</p>
<p class="text-gray-700 dark:text-gray-200">Secondary text</p>
<p class="text-gray-600 dark:text-gray-300">Tertiary text</p>
<p class="text-gray-500 dark:text-gray-400">Muted text</p>

<!-- Borders -->
<div class="border-gray-200 dark:border-gray-800">Primary</div>
<div class="border-gray-300 dark:border-gray-700">Secondary</div>
```

## Best Practices Summary

1. ✅ Use `@import "tailwindcss"` instead of `@tailwind` directives
2. ✅ Use v4 utility replacements (e.g., `bg-blue-500/50` not `bg-opacity-50`)
3. ✅ Use `gap-*` for spacing in flex/grid layouts
4. ✅ Start mobile-first, add breakpoints progressively
5. ✅ Always include dark mode variants if project supports it
6. ✅ Group utilities logically (layout → spacing → typography → colors → states)
7. ✅ Remove redundant classes
8. ✅ Include hover, focus, active, and disabled states
9. ✅ Add transitions for smooth interactions
10. ✅ Check existing project conventions before writing new styles
11. ✅ Extract repeated patterns into reusable components
12. ✅ Use semantic color scales consistently
13. ✅ Test both light and dark modes
14. ✅ Consider accessibility (contrast ratios, focus states)

## Common Tasks

### Styling a New Component

1. Start with layout utilities (flex, grid, positioning)
2. Add spacing (padding, margin, gap)
3. Apply typography (text size, weight, color)
4. Add colors (background, text, border)
5. Include dark mode variants for all colors
6. Add interactive states (hover, focus, active)
7. Add transitions for smooth interactions
8. Test in both light and dark modes
9. Verify responsive behavior at all breakpoints

### Converting from v3 to v4

1. Replace `@tailwind` directives with `@import "tailwindcss"`
2. Update opacity utilities to slash notation (`bg-blue-500/50`)
3. Replace `flex-shrink-*` with `shrink-*`
4. Replace `flex-grow-*` with `grow-*`
5. Replace `overflow-ellipsis` with `text-ellipsis`
6. Replace decoration utilities with box-decoration variants
7. Test all components to ensure styles still work

This skill ensures Tailwind CSS v4 applications have consistent, modern, accessible styling that works beautifully in both light and dark modes across all devices.
