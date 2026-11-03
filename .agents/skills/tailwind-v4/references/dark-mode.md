# Tailwind v4 Dark Mode Reference

## Enabling Dark Mode

Dark mode in Tailwind v4 uses the `dark:` variant to apply styles when dark mode is active.

## Configuration

Dark mode can be configured in your CSS or project configuration:

### Method 1: Class-based (Recommended)

```css
@import "tailwindcss";

@theme {
  --color-scheme: light dark;
}
```

Then toggle with a class on the `<html>` or `<body>` element:

```html
<html class="dark">
  <!-- Dark mode is active -->
</html>
```

### Method 2: Media Query (System Preference)

```css
@import "tailwindcss";

@media (prefers-color-scheme: dark) {
  /* Dark mode styles automatically applied based on system preference */
}
```

## Basic Dark Mode Usage

### Background Colors

```html
<div class="bg-white dark:bg-gray-900">
  Content with light/dark background
</div>

<div class="bg-gray-100 dark:bg-gray-800">
  Subtle background
</div>
```

### Text Colors

```html
<h1 class="text-gray-900 dark:text-white">
  Heading
</h1>

<p class="text-gray-700 dark:text-gray-300">
  Body text
</p>

<span class="text-gray-500 dark:text-gray-400">
  Subtle text
</span>
```

### Border Colors

```html
<div class="border border-gray-300 dark:border-gray-700">
  Container with border
</div>

<input class="border border-gray-200 dark:border-gray-600">
```

## Common Dark Mode Patterns

### Card Component

```html
<div class="bg-white dark:bg-gray-800 rounded-lg shadow-lg p-6 border border-gray-200 dark:border-gray-700">
  <h2 class="text-2xl font-bold text-gray-900 dark:text-white mb-4">
    Card Title
  </h2>
  <p class="text-gray-600 dark:text-gray-300">
    Card content goes here with appropriate dark mode styling.
  </p>
</div>
```

### Navigation Bar

```html
<nav class="bg-white dark:bg-gray-900 border-b border-gray-200 dark:border-gray-800">
  <div class="container mx-auto px-4 py-3">
    <div class="flex items-center justify-between">
      <a href="/" class="text-xl font-bold text-gray-900 dark:text-white">
        Logo
      </a>
      <div class="flex gap-6">
        <a href="/about" class="text-gray-700 dark:text-gray-300 hover:text-blue-600 dark:hover:text-blue-400">
          About
        </a>
        <a href="/contact" class="text-gray-700 dark:text-gray-300 hover:text-blue-600 dark:hover:text-blue-400">
          Contact
        </a>
      </div>
    </div>
  </div>
</nav>
```

### Button Component

```html
<!-- Primary button -->
<button class="bg-blue-600 hover:bg-blue-700 dark:bg-blue-500 dark:hover:bg-blue-600 text-white px-4 py-2 rounded">
  Primary Button
</button>

<!-- Secondary button -->
<button class="bg-gray-200 hover:bg-gray-300 dark:bg-gray-700 dark:hover:bg-gray-600 text-gray-900 dark:text-white px-4 py-2 rounded">
  Secondary Button
</button>

<!-- Outline button -->
<button class="border-2 border-blue-600 dark:border-blue-400 text-blue-600 dark:text-blue-400 hover:bg-blue-50 dark:hover:bg-blue-900/20 px-4 py-2 rounded">
  Outline Button
</button>
```

### Form Inputs

```html
<input 
  type="text" 
  placeholder="Enter text..." 
  class="w-full px-4 py-2 rounded border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-800 text-gray-900 dark:text-white placeholder-gray-400 dark:placeholder-gray-500 focus:ring-2 focus:ring-blue-500 dark:focus:ring-blue-400 focus:border-transparent"
>

<select class="w-full px-4 py-2 rounded border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-800 text-gray-900 dark:text-white">
  <option>Option 1</option>
  <option>Option 2</option>
</select>

<textarea 
  placeholder="Enter message..." 
  class="w-full px-4 py-2 rounded border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-800 text-gray-900 dark:text-white placeholder-gray-400 dark:placeholder-gray-500"
></textarea>
```

### Table

```html
<table class="w-full">
  <thead class="bg-gray-100 dark:bg-gray-800">
    <tr>
      <th class="px-4 py-2 text-left text-gray-900 dark:text-white">Name</th>
      <th class="px-4 py-2 text-left text-gray-900 dark:text-white">Email</th>
      <th class="px-4 py-2 text-left text-gray-900 dark:text-white">Role</th>
    </tr>
  </thead>
  <tbody class="bg-white dark:bg-gray-900">
    <tr class="border-b border-gray-200 dark:border-gray-800">
      <td class="px-4 py-2 text-gray-900 dark:text-white">John Doe</td>
      <td class="px-4 py-2 text-gray-600 dark:text-gray-400">john@example.com</td>
      <td class="px-4 py-2 text-gray-600 dark:text-gray-400">Admin</td>
    </tr>
  </tbody>
</table>
```

### Alert/Notification

```html
<!-- Info alert -->
<div class="bg-blue-100 dark:bg-blue-900/20 border-l-4 border-blue-500 dark:border-blue-400 p-4">
  <p class="text-blue-900 dark:text-blue-200">
    This is an informational message.
  </p>
</div>

<!-- Success alert -->
<div class="bg-green-100 dark:bg-green-900/20 border-l-4 border-green-500 dark:border-green-400 p-4">
  <p class="text-green-900 dark:text-green-200">
    Operation completed successfully!
  </p>
</div>

<!-- Warning alert -->
<div class="bg-yellow-100 dark:bg-yellow-900/20 border-l-4 border-yellow-500 dark:border-yellow-400 p-4">
  <p class="text-yellow-900 dark:text-yellow-200">
    Please review this warning.
  </p>
</div>

<!-- Error alert -->
<div class="bg-red-100 dark:bg-red-900/20 border-l-4 border-red-500 dark:border-red-400 p-4">
  <p class="text-red-900 dark:text-red-200">
    An error occurred!
  </p>
</div>
```

## Dark Mode with Opacity

Use the slash notation for opacity in dark mode:

```html
<div class="bg-white/95 dark:bg-gray-900/95">
  Semi-transparent background
</div>

<div class="text-gray-900/80 dark:text-white/80">
  Text with opacity
</div>

<div class="border border-gray-200/50 dark:border-gray-700/50">
  Subtle border
</div>
```

## Dark Mode with Hover States

Combine dark mode with interactive states:

```html
<button class="bg-blue-600 hover:bg-blue-700 dark:bg-blue-500 dark:hover:bg-blue-600 text-white px-4 py-2 rounded">
  Button with hover states
</button>

<a class="text-blue-600 hover:text-blue-800 dark:text-blue-400 dark:hover:text-blue-300 hover:underline">
  Link with hover states
</a>

<div class="bg-white hover:bg-gray-50 dark:bg-gray-800 dark:hover:bg-gray-700 p-4 rounded transition-colors">
  Hoverable card
</div>
```

## Dark Mode Toggle Implementation

### JavaScript Toggle Example

```html
<button 
  id="theme-toggle"
  class="p-2 rounded bg-gray-200 dark:bg-gray-700 text-gray-800 dark:text-gray-200"
  aria-label="Toggle dark mode"
>
  <!-- Sun icon (visible in dark mode) -->
  <svg class="w-6 h-6 hidden dark:block" fill="currentColor" viewBox="0 0 20 20">
    <path d="M10 2a1 1 0 011 1v1a1 1 0 11-2 0V3a1 1 0 011-1zm4 8a4 4 0 11-8 0 4 4 0 018 0zm-.464 4.95l.707.707a1 1 0 001.414-1.414l-.707-.707a1 1 0 00-1.414 1.414zm2.12-10.607a1 1 0 010 1.414l-.706.707a1 1 0 11-1.414-1.414l.707-.707a1 1 0 011.414 0zM17 11a1 1 0 100-2h-1a1 1 0 100 2h1zm-7 4a1 1 0 011 1v1a1 1 0 11-2 0v-1a1 1 0 011-1zM5.05 6.464A1 1 0 106.465 5.05l-.708-.707a1 1 0 00-1.414 1.414l.707.707zm1.414 8.486l-.707.707a1 1 0 01-1.414-1.414l.707-.707a1 1 0 011.414 1.414zM4 11a1 1 0 100-2H3a1 1 0 000 2h1z"/>
  </svg>
  <!-- Moon icon (visible in light mode) -->
  <svg class="w-6 h-6 block dark:hidden" fill="currentColor" viewBox="0 0 20 20">
    <path d="M17.293 13.293A8 8 0 016.707 2.707a8.001 8.001 0 1010.586 10.586z"/>
  </svg>
</button>

<script>
  const toggle = document.getElementById('theme-toggle');
  const html = document.documentElement;
  
  // Check for saved theme preference or default to system preference
  const currentTheme = localStorage.getItem('theme') || 
    (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
  
  if (currentTheme === 'dark') {
    html.classList.add('dark');
  }
  
  toggle.addEventListener('click', () => {
    html.classList.toggle('dark');
    const theme = html.classList.contains('dark') ? 'dark' : 'light';
    localStorage.setItem('theme', theme);
  });
</script>
```

## Common Color Schemes

### Recommended Dark Mode Colors

```html
<!-- Backgrounds -->
<div class="bg-white dark:bg-gray-900">Primary background</div>
<div class="bg-gray-50 dark:bg-gray-800">Secondary background</div>
<div class="bg-gray-100 dark:bg-gray-700">Tertiary background</div>

<!-- Text -->
<span class="text-gray-900 dark:text-white">Primary text</span>
<span class="text-gray-700 dark:text-gray-200">Secondary text</span>
<span class="text-gray-600 dark:text-gray-300">Tertiary text</span>
<span class="text-gray-500 dark:text-gray-400">Muted text</span>

<!-- Borders -->
<div class="border-gray-200 dark:border-gray-800">Primary border</div>
<div class="border-gray-300 dark:border-gray-700">Secondary border</div>
```

## Best Practices

1. **Always provide dark mode styles** if the project supports dark mode
2. **Use consistent color scales** - e.g., `gray-900` in light mode, `white` in dark mode
3. **Test contrast ratios** - ensure text is readable in both modes
4. **Consider hover states** - apply dark mode to all interactive states
5. **Use semantic colors** - maintain meaning across light/dark (e.g., red for errors)
6. **Leverage opacity** - use `bg-black/10 dark:bg-white/10` for subtle overlays
7. **Persist user preference** - save theme choice in localStorage
8. **Respect system preference** - default to `prefers-color-scheme` when no saved preference
9. **Smooth transitions** - consider adding `transition-colors` to elements
10. **Test both modes** - verify all components work in both light and dark modes
