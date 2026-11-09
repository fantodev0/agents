# Tailwind v4 Patterns & Components Reference

## Layout Patterns

### Centered Container

```html
<div class="container mx-auto px-4">
  <h1>Centered content with padding</h1>
</div>
```

### Full-Width Section with Centered Content

```html
<section class="bg-gray-100 dark:bg-gray-900 py-12">
  <div class="container mx-auto px-4">
    <h2>Section Title</h2>
  </div>
</section>
```

### Sticky Header

```html
<header class="sticky top-0 z-50 bg-white dark:bg-gray-900 border-b border-gray-200 dark:border-gray-800 shadow-sm">
  <nav class="container mx-auto px-4 py-4">
    <div class="flex items-center justify-between">
      <div class="text-xl font-bold">Logo</div>
      <div class="flex gap-6">
        <a href="#">Home</a>
        <a href="#">About</a>
        <a href="#">Contact</a>
      </div>
    </div>
  </nav>
</header>
```

### Sidebar Layout

```html
<div class="flex min-h-screen">
  <!-- Sidebar -->
  <aside class="w-64 bg-gray-100 dark:bg-gray-900 border-r border-gray-200 dark:border-gray-800 p-6">
    <nav class="flex flex-col gap-2">
      <a href="#" class="px-4 py-2 rounded hover:bg-gray-200 dark:hover:bg-gray-800">Dashboard</a>
      <a href="#" class="px-4 py-2 rounded hover:bg-gray-200 dark:hover:bg-gray-800">Settings</a>
    </nav>
  </aside>
  
  <!-- Main content -->
  <main class="flex-1 p-8">
    <h1>Content</h1>
  </main>
</div>
```

### Responsive Grid Gallery

```html
<div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
  <div class="aspect-square bg-gray-200 dark:bg-gray-700 rounded-lg"></div>
  <div class="aspect-square bg-gray-200 dark:bg-gray-700 rounded-lg"></div>
  <div class="aspect-square bg-gray-200 dark:bg-gray-700 rounded-lg"></div>
  <div class="aspect-square bg-gray-200 dark:bg-gray-700 rounded-lg"></div>
</div>
```

## Card Patterns

### Basic Card

```html
<div class="bg-white dark:bg-gray-800 rounded-lg shadow-lg p-6">
  <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-2">Card Title</h3>
  <p class="text-gray-600 dark:text-gray-300">Card content goes here.</p>
</div>
```

### Card with Image

```html
<div class="bg-white dark:bg-gray-800 rounded-lg shadow-lg overflow-hidden">
  <img src="image.jpg" alt="Card image" class="w-full h-48 object-cover">
  <div class="p-6">
    <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-2">Card Title</h3>
    <p class="text-gray-600 dark:text-gray-300 mb-4">Card description.</p>
    <button class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded">
      Learn More
    </button>
  </div>
</div>
```

### Hoverable Card

```html
<div class="bg-white dark:bg-gray-800 rounded-lg shadow-lg p-6 transition-all hover:shadow-xl hover:scale-105">
  <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-2">Interactive Card</h3>
  <p class="text-gray-600 dark:text-gray-300">Hover to see the effect.</p>
</div>
```

### Card with Header and Footer

```html
<div class="bg-white dark:bg-gray-800 rounded-lg shadow-lg overflow-hidden">
  <div class="bg-blue-600 dark:bg-blue-700 px-6 py-4">
    <h3 class="text-xl font-semibold text-white">Card Header</h3>
  </div>
  <div class="p-6">
    <p class="text-gray-600 dark:text-gray-300">Card content.</p>
  </div>
  <div class="bg-gray-50 dark:bg-gray-700 px-6 py-4 border-t border-gray-200 dark:border-gray-600">
    <button class="text-blue-600 dark:text-blue-400 hover:underline">Action</button>
  </div>
</div>
```

## Button Patterns

### Button Variants

```html
<!-- Primary -->
<button class="bg-blue-600 hover:bg-blue-700 active:bg-blue-800 text-white px-4 py-2 rounded font-medium transition-colors">
  Primary
</button>

<!-- Secondary -->
<button class="bg-gray-200 hover:bg-gray-300 active:bg-gray-400 dark:bg-gray-700 dark:hover:bg-gray-600 dark:active:bg-gray-500 text-gray-900 dark:text-white px-4 py-2 rounded font-medium transition-colors">
  Secondary
</button>

<!-- Outline -->
<button class="border-2 border-blue-600 dark:border-blue-400 text-blue-600 dark:text-blue-400 hover:bg-blue-50 dark:hover:bg-blue-900/20 px-4 py-2 rounded font-medium transition-colors">
  Outline
</button>

<!-- Ghost -->
<button class="text-blue-600 dark:text-blue-400 hover:bg-blue-50 dark:hover:bg-blue-900/20 px-4 py-2 rounded font-medium transition-colors">
  Ghost
</button>

<!-- Danger -->
<button class="bg-red-600 hover:bg-red-700 active:bg-red-800 text-white px-4 py-2 rounded font-medium transition-colors">
  Danger
</button>

<!-- Disabled -->
<button class="bg-gray-300 dark:bg-gray-600 text-gray-500 dark:text-gray-400 px-4 py-2 rounded font-medium cursor-not-allowed" disabled>
  Disabled
</button>
```

### Button Sizes

```html
<button class="bg-blue-600 hover:bg-blue-700 text-white px-2 py-1 rounded text-sm">Small</button>
<button class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded">Medium</button>
<button class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-3 rounded text-lg">Large</button>
```

### Button Group

```html
<div class="inline-flex rounded-lg border border-gray-300 dark:border-gray-600 overflow-hidden">
  <button class="px-4 py-2 bg-white dark:bg-gray-800 hover:bg-gray-50 dark:hover:bg-gray-700 border-r border-gray-300 dark:border-gray-600">
    Left
  </button>
  <button class="px-4 py-2 bg-white dark:bg-gray-800 hover:bg-gray-50 dark:hover:bg-gray-700 border-r border-gray-300 dark:border-gray-600">
    Middle
  </button>
  <button class="px-4 py-2 bg-white dark:bg-gray-800 hover:bg-gray-50 dark:hover:bg-gray-700">
    Right
  </button>
</div>
```

## Form Patterns

### Text Input

```html
<div class="mb-4">
  <label for="name" class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
    Name
  </label>
  <input 
    type="text" 
    id="name"
    class="w-full px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-800 text-gray-900 dark:text-white placeholder-gray-400 dark:placeholder-gray-500 focus:ring-2 focus:ring-blue-500 dark:focus:ring-blue-400 focus:border-transparent transition-colors"
    placeholder="Enter your name"
  >
</div>
```

### Select Input

```html
<div class="mb-4">
  <label for="country" class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
    Country
  </label>
  <select 
    id="country"
    class="w-full px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-800 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 dark:focus:ring-blue-400 focus:border-transparent transition-colors"
  >
    <option>United States</option>
    <option>Canada</option>
    <option>Mexico</option>
  </select>
</div>
```

### Checkbox and Radio

```html
<!-- Checkbox -->
<label class="flex items-center gap-2 cursor-pointer">
  <input 
    type="checkbox" 
    class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-400 dark:bg-gray-700 dark:border-gray-600"
  >
  <span class="text-gray-900 dark:text-white">Accept terms</span>
</label>

<!-- Radio -->
<label class="flex items-center gap-2 cursor-pointer">
  <input 
    type="radio" 
    name="option"
    class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 focus:ring-blue-500 dark:focus:ring-blue-400 dark:bg-gray-700 dark:border-gray-600"
  >
  <span class="text-gray-900 dark:text-white">Option 1</span>
</label>
```

### Textarea

```html
<div class="mb-4">
  <label for="message" class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-2">
    Message
  </label>
  <textarea 
    id="message"
    rows="4"
    class="w-full px-4 py-2 rounded-lg border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-800 text-gray-900 dark:text-white placeholder-gray-400 dark:placeholder-gray-500 focus:ring-2 focus:ring-blue-500 dark:focus:ring-blue-400 focus:border-transparent transition-colors resize-none"
    placeholder="Enter your message"
  ></textarea>
</div>
```

### Form with Validation States

```html
<!-- Success state -->
<div class="mb-4">
  <label class="block text-sm font-medium text-green-700 dark:text-green-300 mb-2">
    Email (Valid)
  </label>
  <input 
    type="email"
    class="w-full px-4 py-2 rounded-lg border-2 border-green-500 dark:border-green-400 bg-white dark:bg-gray-800 text-gray-900 dark:text-white focus:ring-2 focus:ring-green-500"
  >
  <p class="mt-1 text-sm text-green-600 dark:text-green-400">Looks good!</p>
</div>

<!-- Error state -->
<div class="mb-4">
  <label class="block text-sm font-medium text-red-700 dark:text-red-300 mb-2">
    Password (Error)
  </label>
  <input 
    type="password"
    class="w-full px-4 py-2 rounded-lg border-2 border-red-500 dark:border-red-400 bg-white dark:bg-gray-800 text-gray-900 dark:text-white focus:ring-2 focus:ring-red-500"
  >
  <p class="mt-1 text-sm text-red-600 dark:text-red-400">Password is required</p>
</div>
```

## Navigation Patterns

### Horizontal Navigation

```html
<nav class="flex items-center gap-6">
  <a href="#" class="text-blue-600 dark:text-blue-400 font-medium">Home</a>
  <a href="#" class="text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-white">About</a>
  <a href="#" class="text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-white">Services</a>
  <a href="#" class="text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-white">Contact</a>
</nav>
```

### Vertical Navigation

```html
<nav class="flex flex-col gap-2">
  <a href="#" class="px-4 py-2 rounded bg-blue-600 dark:bg-blue-700 text-white">Dashboard</a>
  <a href="#" class="px-4 py-2 rounded hover:bg-gray-100 dark:hover:bg-gray-800 text-gray-700 dark:text-gray-300">Projects</a>
  <a href="#" class="px-4 py-2 rounded hover:bg-gray-100 dark:hover:bg-gray-800 text-gray-700 dark:text-gray-300">Team</a>
  <a href="#" class="px-4 py-2 rounded hover:bg-gray-100 dark:hover:bg-gray-800 text-gray-700 dark:text-gray-300">Settings</a>
</nav>
```

### Tabs

```html
<div>
  <div class="border-b border-gray-200 dark:border-gray-700">
    <nav class="flex gap-8">
      <button class="pb-4 border-b-2 border-blue-600 dark:border-blue-400 text-blue-600 dark:text-blue-400 font-medium">
        Tab 1
      </button>
      <button class="pb-4 border-b-2 border-transparent text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-white hover:border-gray-300 dark:hover:border-gray-600">
        Tab 2
      </button>
      <button class="pb-4 border-b-2 border-transparent text-gray-600 dark:text-gray-400 hover:text-gray-900 dark:hover:text-white hover:border-gray-300 dark:hover:border-gray-600">
        Tab 3
      </button>
    </nav>
  </div>
  <div class="pt-4">
    <p class="text-gray-700 dark:text-gray-300">Tab content goes here</p>
  </div>
</div>
```

### Breadcrumbs

```html
<nav class="flex items-center gap-2 text-sm">
  <a href="#" class="text-blue-600 dark:text-blue-400 hover:underline">Home</a>
  <span class="text-gray-400 dark:text-gray-600">/</span>
  <a href="#" class="text-blue-600 dark:text-blue-400 hover:underline">Products</a>
  <span class="text-gray-400 dark:text-gray-600">/</span>
  <span class="text-gray-600 dark:text-gray-400">Current Page</span>
</nav>
```

## Modal Pattern

```html
<!-- Backdrop -->
<div class="fixed inset-0 bg-black/50 flex items-center justify-center p-4">
  <!-- Modal -->
  <div class="bg-white dark:bg-gray-800 rounded-lg shadow-xl max-w-md w-full p-6">
    <div class="flex items-center justify-between mb-4">
      <h2 class="text-xl font-semibold text-gray-900 dark:text-white">Modal Title</h2>
      <button class="text-gray-400 hover:text-gray-600 dark:hover:text-gray-200">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
        </svg>
      </button>
    </div>
    <p class="text-gray-600 dark:text-gray-300 mb-6">Modal content goes here.</p>
    <div class="flex gap-3 justify-end">
      <button class="px-4 py-2 rounded bg-gray-200 dark:bg-gray-700 text-gray-900 dark:text-white hover:bg-gray-300 dark:hover:bg-gray-600">
        Cancel
      </button>
      <button class="px-4 py-2 rounded bg-blue-600 hover:bg-blue-700 text-white">
        Confirm
      </button>
    </div>
  </div>
</div>
```

## Best Practices

1. **Use gap for spacing** - Prefer `gap-*` over margins between flex/grid items
2. **Maintain consistency** - Reuse component patterns across the application
3. **Extract repeated patterns** - Create reusable components for common patterns
4. **Think through class order** - Group related utilities (layout → spacing → typography → colors)
5. **Remove redundant classes** - Don't duplicate inherited properties
6. **Use transition classes** - Add smooth transitions for interactive elements
7. **Support dark mode** - Include dark mode variants for all components
8. **Consider accessibility** - Use proper semantic HTML and ARIA attributes
9. **Make components responsive** - Use responsive utilities for different breakpoints
10. **Keep it simple** - Don't over-engineer - use Tailwind's defaults when possible
