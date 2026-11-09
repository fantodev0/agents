# Tailwind v4 Utilities Reference

## Replaced Utilities

Tailwind v4 removed deprecated utilities. Always use the replacement option.

### Opacity Utilities

| Deprecated | Replacement | Example |
|------------|-------------|---------|
| `bg-opacity-*` | `bg-{color}/*` | `bg-black/50` (50% opacity) |
| `text-opacity-*` | `text-{color}/*` | `text-blue-600/75` |
| `border-opacity-*` | `border-{color}/*` | `border-gray-300/60` |
| `divide-opacity-*` | `divide-{color}/*` | `divide-gray-200/50` |
| `ring-opacity-*` | `ring-{color}/*` | `ring-blue-500/30` |
| `placeholder-opacity-*` | `placeholder-{color}/*` | `placeholder-gray-400/50` |

**Important**: Opacity values are still numeric (0-100), not percentages.

```html
<!-- ❌ OLD (Tailwind v3) -->
<div class="bg-blue-500 bg-opacity-50">Content</div>
<p class="text-gray-900 text-opacity-75">Text</p>

<!-- ✅ NEW (Tailwind v4) -->
<div class="bg-blue-500/50">Content</div>
<p class="text-gray-900/75">Text</p>
```

### Flex Utilities

| Deprecated | Replacement |
|------------|-------------|
| `flex-shrink-*` | `shrink-*` |
| `flex-grow-*` | `grow-*` |

```html
<!-- ❌ OLD -->
<div class="flex-shrink-0 flex-grow">Content</div>

<!-- ✅ NEW -->
<div class="shrink-0 grow">Content</div>
```

### Text and Decoration Utilities

| Deprecated | Replacement |
|------------|-------------|
| `overflow-ellipsis` | `text-ellipsis` |
| `decoration-slice` | `box-decoration-slice` |
| `decoration-clone` | `box-decoration-clone` |

```html
<!-- ❌ OLD -->
<p class="overflow-ellipsis decoration-slice">Text</p>

<!-- ✅ NEW -->
<p class="text-ellipsis box-decoration-slice">Text</p>
```

## Color System

### Using Colors with Opacity

Tailwind v4 uses a slash notation for opacity:

```html
<!-- Background colors -->
<div class="bg-blue-500/50">50% opacity</div>
<div class="bg-red-600/25">25% opacity</div>
<div class="bg-green-700/100">Full opacity</div>

<!-- Text colors -->
<p class="text-gray-900/80">80% opacity</p>
<h1 class="text-blue-600/60">60% opacity</h1>

<!-- Border colors -->
<div class="border border-gray-300/50">Subtle border</div>

<!-- Ring colors -->
<input class="ring-2 ring-blue-500/30">
```

### Full Color Palette

```html
<!-- Slate/Gray/Zinc/Neutral/Stone -->
<div class="bg-slate-50">...</div>
<div class="bg-gray-100">...</div>
<div class="bg-zinc-200">...</div>
<div class="bg-neutral-300">...</div>
<div class="bg-stone-400">...</div>

<!-- Brand colors -->
<div class="bg-red-500">...</div>
<div class="bg-orange-500">...</div>
<div class="bg-amber-500">...</div>
<div class="bg-yellow-500">...</div>
<div class="bg-lime-500">...</div>
<div class="bg-green-500">...</div>
<div class="bg-emerald-500">...</div>
<div class="bg-teal-500">...</div>
<div class="bg-cyan-500">...</div>
<div class="bg-sky-500">...</div>
<div class="bg-blue-500">...</div>
<div class="bg-indigo-500">...</div>
<div class="bg-violet-500">...</div>
<div class="bg-purple-500">...</div>
<div class="bg-fuchsia-500">...</div>
<div class="bg-pink-500">...</div>
<div class="bg-rose-500">...</div>

<!-- Shades: 50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950 -->
```

## Layout Utilities

### Container

```html
<!-- Centered container with padding -->
<div class="container mx-auto px-4">Content</div>

<!-- Full-width container -->
<div class="container">Content</div>
```

### Display

```html
<div class="block">Block</div>
<div class="inline-block">Inline Block</div>
<div class="inline">Inline</div>
<div class="flex">Flex</div>
<div class="inline-flex">Inline Flex</div>
<div class="grid">Grid</div>
<div class="inline-grid">Inline Grid</div>
<div class="hidden">Hidden</div>
```

### Position

```html
<div class="static">Static</div>
<div class="fixed">Fixed</div>
<div class="absolute">Absolute</div>
<div class="relative">Relative</div>
<div class="sticky">Sticky</div>
```

## Flexbox & Grid

### Flexbox

```html
<!-- Flex direction -->
<div class="flex flex-row">Row</div>
<div class="flex flex-col">Column</div>
<div class="flex flex-row-reverse">Reverse row</div>
<div class="flex flex-col-reverse">Reverse column</div>

<!-- Justify content -->
<div class="flex justify-start">Start</div>
<div class="flex justify-center">Center</div>
<div class="flex justify-end">End</div>
<div class="flex justify-between">Between</div>
<div class="flex justify-around">Around</div>
<div class="flex justify-evenly">Evenly</div>

<!-- Align items -->
<div class="flex items-start">Start</div>
<div class="flex items-center">Center</div>
<div class="flex items-end">End</div>
<div class="flex items-baseline">Baseline</div>
<div class="flex items-stretch">Stretch</div>

<!-- Flex wrap -->
<div class="flex flex-wrap">Wrap</div>
<div class="flex flex-nowrap">No wrap</div>

<!-- Gap -->
<div class="flex gap-4">Gap 1rem</div>
<div class="flex gap-x-4 gap-y-2">Different gaps</div>
```

### Grid

```html
<!-- Grid columns -->
<div class="grid grid-cols-1">1 column</div>
<div class="grid grid-cols-2">2 columns</div>
<div class="grid grid-cols-3">3 columns</div>
<div class="grid grid-cols-4">4 columns</div>
<div class="grid grid-cols-12">12 columns</div>

<!-- Grid rows -->
<div class="grid grid-rows-3">3 rows</div>

<!-- Gap -->
<div class="grid gap-4">Gap 1rem</div>
<div class="grid gap-x-4 gap-y-2">Different gaps</div>

<!-- Column span -->
<div class="col-span-2">Spans 2 columns</div>
<div class="col-span-full">Full width</div>

<!-- Row span -->
<div class="row-span-2">Spans 2 rows</div>
```

## Spacing

### Padding & Margin

```html
<!-- Padding -->
<div class="p-4">All sides</div>
<div class="px-4">Horizontal</div>
<div class="py-4">Vertical</div>
<div class="pt-4">Top</div>
<div class="pr-4">Right</div>
<div class="pb-4">Bottom</div>
<div class="pl-4">Left</div>

<!-- Margin -->
<div class="m-4">All sides</div>
<div class="mx-auto">Center horizontally</div>
<div class="my-4">Vertical</div>
<div class="-mt-4">Negative margin</div>

<!-- Scale: 0, 0.5, 1, 1.5, 2, 2.5, 3, 3.5, 4, 5, 6, 7, 8, 9, 10, 11, 12, 14, 16, 20, 24, 28, 32, 36, 40, 44, 48, 52, 56, 60, 64, 72, 80, 96 -->
```

### Gap

```html
<!-- Use gap for spacing in flex/grid -->
<div class="flex gap-4">Items with gap</div>
<div class="grid gap-6">Grid with gap</div>
<div class="flex gap-x-4 gap-y-2">Different x/y gaps</div>
```

## Typography

### Font Size

```html
<p class="text-xs">Extra small</p>
<p class="text-sm">Small</p>
<p class="text-base">Base</p>
<p class="text-lg">Large</p>
<p class="text-xl">Extra large</p>
<p class="text-2xl">2X large</p>
<p class="text-3xl">3X large</p>
<p class="text-4xl">4X large</p>
<p class="text-5xl">5X large</p>
<p class="text-6xl">6X large</p>
<p class="text-7xl">7X large</p>
<p class="text-8xl">8X large</p>
<p class="text-9xl">9X large</p>
```

### Font Weight

```html
<p class="font-thin">Thin (100)</p>
<p class="font-extralight">Extra Light (200)</p>
<p class="font-light">Light (300)</p>
<p class="font-normal">Normal (400)</p>
<p class="font-medium">Medium (500)</p>
<p class="font-semibold">Semibold (600)</p>
<p class="font-bold">Bold (700)</p>
<p class="font-extrabold">Extra Bold (800)</p>
<p class="font-black">Black (900)</p>
```

### Text Utilities

```html
<!-- Alignment -->
<p class="text-left">Left aligned</p>
<p class="text-center">Center aligned</p>
<p class="text-right">Right aligned</p>
<p class="text-justify">Justified</p>

<!-- Transform -->
<p class="uppercase">UPPERCASE</p>
<p class="lowercase">lowercase</p>
<p class="capitalize">Capitalize Each Word</p>

<!-- Decoration -->
<p class="underline">Underlined</p>
<p class="line-through">Line through</p>
<p class="no-underline">No underline</p>

<!-- Overflow -->
<p class="truncate">Truncates with ellipsis</p>
<p class="text-ellipsis overflow-hidden">Also truncates</p>
<p class="text-clip">Clips text</p>

<!-- Line height -->
<p class="leading-none">Line height 1</p>
<p class="leading-tight">Tight</p>
<p class="leading-normal">Normal</p>
<p class="leading-relaxed">Relaxed</p>
<p class="leading-loose">Loose</p>
```

## Borders & Shadows

### Borders

```html
<!-- Border width -->
<div class="border">1px border</div>
<div class="border-2">2px border</div>
<div class="border-4">4px border</div>
<div class="border-8">8px border</div>

<!-- Border sides -->
<div class="border-t">Top border</div>
<div class="border-r">Right border</div>
<div class="border-b">Bottom border</div>
<div class="border-l">Left border</div>

<!-- Border color -->
<div class="border border-gray-300">Gray border</div>
<div class="border border-blue-500/50">Blue border with opacity</div>

<!-- Border radius -->
<div class="rounded">Rounded corners</div>
<div class="rounded-lg">Large rounded</div>
<div class="rounded-full">Fully rounded</div>
<div class="rounded-t-lg">Top rounded</div>
```

### Shadows

```html
<div class="shadow-sm">Small shadow</div>
<div class="shadow">Default shadow</div>
<div class="shadow-md">Medium shadow</div>
<div class="shadow-lg">Large shadow</div>
<div class="shadow-xl">Extra large shadow</div>
<div class="shadow-2xl">2X large shadow</div>
<div class="shadow-none">No shadow</div>
```

## Interactive States

### Hover, Focus, Active

```html
<!-- Hover -->
<button class="bg-blue-500 hover:bg-blue-600">Hover me</button>
<a class="text-blue-600 hover:text-blue-800 hover:underline">Link</a>

<!-- Focus -->
<input class="border focus:border-blue-500 focus:ring-2 focus:ring-blue-500">

<!-- Active -->
<button class="bg-blue-500 active:bg-blue-700">Click me</button>

<!-- Disabled -->
<button class="disabled:opacity-50 disabled:cursor-not-allowed">Button</button>

<!-- Combination -->
<button class="bg-blue-500 hover:bg-blue-600 focus:ring-2 focus:ring-blue-500 active:bg-blue-700">
  Interactive button
</button>
```

## Responsive Design

```html
<!-- Mobile first approach -->
<div class="text-sm md:text-base lg:text-lg xl:text-xl">
  Responsive text
</div>

<!-- Breakpoints: sm (640px), md (768px), lg (1024px), xl (1280px), 2xl (1536px) -->

<!-- Show/hide at breakpoints -->
<div class="hidden md:block">Hidden on mobile, visible on md+</div>
<div class="block md:hidden">Visible on mobile, hidden on md+</div>

<!-- Responsive grid -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
  Items
</div>
```

## Best Practices

1. **Use gap for spacing** - Prefer `gap-*` over margins in flex/grid layouts
2. **Mobile-first responsive design** - Start with mobile, add larger breakpoints
3. **Use semantic color names** - `bg-blue-500` is clearer than `bg-primary`
4. **Leverage opacity variants** - `bg-black/50` instead of separate opacity classes
5. **Use `text-ellipsis`** - Not `overflow-ellipsis` (deprecated)
6. **Use `shrink-*` and `grow-*`** - Not `flex-shrink-*` or `flex-grow-*` (deprecated)
