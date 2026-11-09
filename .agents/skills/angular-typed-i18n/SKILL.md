---
name: angular-typed-i18n
description: Implement or maintain a lightweight Angular runtime i18n system using typed TypeScript dictionaries, route/page-specific translation files, a default-locale-derived TranslationSchema, signal-based language state, a translate pipe, and optional language selector. Use when an Angular app needs runtime language switching without Angular compile-time i18n.
license: MIT
---

# Angular Typed Runtime i18n

Use this pattern for runtime language switching with TypeScript dictionaries and Angular signals.

## Core pattern

- Translations live in `core/i18n/<locale>/` as plain TypeScript objects.
- The default locale is the source of truth for the translation shape.
- `TranslationSchema` is derived from the default locale with literal values widened to `string`, `number`, and `boolean`.
- Every other locale uses `as const satisfies TranslationSchema` to catch missing or invalid keys at build time.
- `TranslationService` stores the current language as a signal, exposes the active dictionary as a computed signal, persists the language safely, and resolves dot-path keys.
- `TranslatePipe` is used for simple string keys; direct typed access is used for structured content.

## Suggested structure

```text
src/app/core/i18n/
├── index.ts
├── schema.ts
├── en/
│   ├── index.ts
│   ├── common.ts
│   └── routes/
│       ├── home.ts
│       ├── admin.ts
│       └── initialize.ts
└── fr/
    ├── index.ts
    ├── common.ts
    └── routes/
        ├── home.ts
        ├── admin.ts
        └── initialize.ts
src/app/core/services/translation.service.ts
src/app/shared/pipes/translate.pipe.ts
src/app/shared/components/language-selector/   # optional
```

## Route-level files

Each application route should have its own translation file in every locale. This keeps page copy close to the route that owns it, prevents `common.ts` from becoming a catch-all dictionary, and makes schema errors easier to trace.

Examples:

- `/` or the home page route → `routes/home.ts`
- `/admin` → `routes/admin.ts`
- `/initialize` → `routes/initialize.ts`
- `/reset-password` → `routes/reset-password.ts`

Keep only route-specific copy in route files. Shared labels, buttons, validation messages, auth copy, and navigation text should live in shared files such as `common.ts`, `forms.ts`, `auth.ts`, or `navigation.ts`.

Expose route dictionaries under a `routes` key so typed access and pipe keys stay predictable: `t().routes.home.hero.title` and `routes.home.hero.title`.

## Dictionary and schema

Shared translation objects are plain TypeScript objects:

```ts
// core/i18n/en/common.ts
export const common = {
  actions: {
    save: 'Save',
    cancel: 'Cancel',
    continue: 'Continue',
  },
  form: {
    emailLabel: 'Email address',
    emailPlaceholder: 'you@example.com',
    required: 'This field is required.',
  },
} as const;
```

Route translation objects can contain nested objects and arrays for page content:

```ts
// core/i18n/en/routes/home.ts
export const home = {
  meta: {
    title: 'Afroschool — Modern school management',
    description: 'Manage admissions, payments, classrooms, and parent communication in one place.',
  },
  hero: {
    eyebrow: 'Built for African schools',
    title: 'Run your school with less paperwork',
    description: 'Afroschool helps founders digitize operations without losing the human touch.',
    cta: {
      primary: 'Start onboarding',
      secondary: 'Book a demo',
    },
  },
  stats: [
    { value: '25k+', label: 'students supported' },
    { value: '300+', label: 'schools onboarded' },
  ],
  faq: {
    title: 'Frequently asked questions',
    items: [
      {
        question: 'Can I switch languages at runtime?',
        answer: 'Yes. The active dictionary is signal-driven and updates the UI immediately.',
      },
    ],
  },
} as const;
```

The default locale root imports shared files and every route file:

```ts
// core/i18n/en/index.ts
import { common } from './common';
import { home } from './routes/home';
// import { admin } from './routes/admin';
// import { initialize } from './routes/initialize';

export const EN = {
  common,
  routes: {
    home,
    // admin,
    // initialize,
  },
} as const;
```

Derive the schema from the default locale:

```ts
// core/i18n/schema.ts
import { EN } from './en';

type WidenLiteral<T> = T extends string
  ? string
  : T extends number
    ? number
    : T extends boolean
      ? boolean
      : T extends readonly (infer Item)[]
        ? readonly WidenLiteral<Item>[]
        : T extends object
          ? { readonly [Key in keyof T]: WidenLiteral<T[Key]> }
          : T;

export type TranslationSchema = WidenLiteral<typeof EN>;
```

Non-default locale files can validate either at the locale root or per route file. Per-file validation gives faster, more local errors:

```ts
// core/i18n/fr/routes/home.ts
import type { TranslationSchema } from '../../schema';

type HomeTranslations = TranslationSchema['routes']['home'];

export const home = {
  meta: {
    title: 'Afroschool — Gestion scolaire moderne',
    description: 'Gérez les admissions, les paiements, les classes et la communication parentale au même endroit.',
  },
  hero: {
    eyebrow: 'Conçu pour les écoles africaines',
    title: 'Gérez votre école avec moins de paperasse',
    description: 'Afroschool aide les fondateurs à numériser leurs opérations sans perdre le contact humain.',
    cta: {
      primary: 'Commencer l’onboarding',
      secondary: 'Réserver une démo',
    },
  },
  stats: [
    { value: '25k+', label: 'élèves accompagnés' },
    { value: '300+', label: 'écoles intégrées' },
  ],
  faq: {
    title: 'Questions fréquentes',
    items: [
      {
        question: 'Puis-je changer de langue à l’exécution ?',
        answer: 'Oui. Le dictionnaire actif est piloté par des signaux et met l’interface à jour immédiatement.',
      },
    ],
  },
} as const satisfies HomeTranslations;
```

```ts
// core/i18n/fr/index.ts
import type { TranslationSchema } from '../schema';
import { common } from './common';
import { home } from './routes/home';

export const FR = {
  common,
  routes: {
    home,
  },
} as const satisfies TranslationSchema;
```

```ts
// core/i18n/index.ts
import { EN } from './en';
import { FR } from './fr';
import type { TranslationSchema } from './schema';

export type { TranslationSchema } from './schema';
export type Language = 'en' | 'fr';

export const translations: Readonly<Record<Language, TranslationSchema>> = {
  en: EN,
  fr: FR,
};
```

## Service responsibilities

`TranslationService` should:

- define `availableLanguages`
- expose `currentLanguage = signal<Language>(...)`
- expose `language = currentLanguage.asReadonly()`
- expose `t = computed(() => translations[currentLanguage()])`
- persist language in `localStorage`, guarded with `typeof window !== 'undefined'`
- provide `setLanguage(language: Language)`
- provide `getTranslation(key: string): string`
- resolve dot paths such as `common.actions.save` or `routes.home.hero.title`; array indexes may be supported as numeric path segments
- return the key itself when no string translation exists

Canonical shape:

```ts
@Injectable({ providedIn: 'root' })
export class TranslationService {
  readonly availableLanguages = ['en', 'fr'] as const;
  readonly currentLanguage = signal<Language>(this.getStoredLanguage());
  readonly language = this.currentLanguage.asReadonly();
  readonly t = computed(() => translations[this.currentLanguage()]);

  setLanguage(language: Language): void { /* validate and set */ }
  getTranslation(key: string): string { /* dot-path lookup; fallback to key */ }
  private getStoredLanguage(): Language { /* SSR-safe localStorage read */ }
  private setStoredLanguage(language: Language): void { /* SSR-safe localStorage write */ }
}

export { TranslationService as LanguageService };
```

Keep the `LanguageService` alias only if it improves readability or preserves existing imports.

## Pipe

```ts
@Pipe({ name: 'translate', pure: false })
export class TranslatePipe implements PipeTransform {
  private readonly translationService = inject(TranslationService);

  transform(key: string): string {
    return this.translationService.getTranslation(key);
  }
}
```

Use it for simple template strings:

```html
<button>{{ 'common.actions.save' | translate }}</button>
<input [placeholder]="'common.form.emailPlaceholder' | translate" />
<h1>{{ 'routes.home.hero.title' | translate }}</h1>
```

For Angular versions where standalone is not the default, add `standalone: true`.

## Structured content

For translated arrays or larger nested objects, prefer typed access over many string keys. This is especially useful inside route components because each route has a typed translation object:

```ts
type HomeContent = TranslationSchema['routes']['home'];
readonly home = computed<HomeContent>(() => this.translationService.t().routes.home);
```

```html
<section>
  <p>{{ home().hero.eyebrow }}</p>
  <h1>{{ home().hero.title }}</h1>

  @for (stat of home().stats; track stat.label) {
    <article>
      <strong>{{ stat.value }}</strong>
      <span>{{ stat.label }}</span>
    </article>
  }
</section>
```

Shared sections can use the same pattern:

```ts
type CommonContent = TranslationSchema['common'];
readonly common = computed<CommonContent>(() => this.translationService.t().common);
```

## Language selector

A selector component is optional. It should read `language()`, call `setLanguage()`, close after selection, and use accessible button/menu semantics. Keep the language list typed with `Language`.

## Workflow

Adding keys:

1. Add shared keys to the default locale shared file first, or add page copy to that route's default-locale file first.
2. Mirror the keys in the same file path for all other locales.
3. When adding a route, create one route translation file per locale and export it from each locale root.
4. Export new shared sections from each locale root.
5. Run the TypeScript build to catch schema mismatches.

Adding a language:

1. Create the locale folder with matching sections.
2. Export the locale root with `as const satisfies TranslationSchema`.
3. Add the code to `Language`, `translations`, `availableLanguages`, and the selector UI.

## Notes

- This is runtime i18n, not Angular compile-time localization.
- Prefer semantic keys over sentence-based keys.
- Keep every route's copy in its own route file; do not let `common.ts` become a page-content dumping ground.
- Missing keys returning themselves makes untranslated strings visible during development.
- Keep browser APIs guarded for SSR/prerender compatibility.
