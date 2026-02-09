# Claude Development Guide - Portfolio App

## Project Overview
Personal portfolio built with:
- **NuxtJS 4.3.1** (Vue 3.5.27)
- **Bun** package manager
- **Vite** bundler
- **Custom CSS** (no Tailwind/libraries)
- **Storybook** for component library

## Key Context
- User: Product designer, familiar with dev concepts but not expert
- Prefers clear explanations without complex examples
- Environment: MacBook Pro M1
- Location: `~/Desktop/portfolio-project/portfolio-app/`

## Development Commands
```bash
bun run dev          # Dev server (localhost:3000)
bun run storybook    # Storybook (localhost:6006)
bun run build        # Production build
bun run generate     # Static generation
```

## Architecture Rules
1. **Custom CSS only** - No Tailwind, Bootstrap, or CSS frameworks
2. **Component library** - Build reusable components documented in Storybook
3. **File structure**:
   - Components: `/components/` with `.vue` files
   - Stories: `/components/**/*.stories.ts` (TypeScript)
   - Pages: `/pages/`
   - Assets: `/assets/` - CSS global, variables, images, fonts (traités par Vite)
   - Public: `/public/` - Fichiers statiques servis tels quels (robots.txt, favicon)

## Development Workflow
1. Create component in `/components/`
2. Write custom CSS (scoped or in `/assets/`)
3. Create Storybook story alongside component
4. Test in Storybook before integrating

## Assets Directory (`/assets/`)
**Rôle** : Fichiers traités par Vite (optimisation, minification, hash)

**Structure recommandée** :
```
assets/
├── styles/
│   ├── global.css      # Styles globaux (importé dans nuxt.config)
│   ├── variables.css   # Variables CSS custom properties
│   └── reset.css       # Reset/normalize CSS
├── images/             # Images optimisées par Vite
└── fonts/              # Polices web
```

**Usage** :
- CSS global : Déclaré dans `nuxt.config.ts` → `css: ['~/assets/styles/global.css']`
- Images : `<img src="~/assets/images/logo.svg">` (Vite génère URL optimisée)
- Dans CSS : `background: url('~/assets/images/bg.jpg')`

**vs /public/** : `/public/` pour fichiers statiques non traités (robots.txt, favicon)

## Storybook Setup
- Config: `.storybook/main.ts` and `.storybook/preview.ts`
- Stories pattern: `components/**/*.stories.ts` (TypeScript only)
- Run with: `bun run storybook`

## Important Notes
- Use TypeScript (tsconfig references Nuxt's generated configs)
- Nuxt auto-imports components and composables
- `.nuxt/` is auto-generated, don't edit directly
- Use `<NuxtPage />` for routing in app.vue

## When Making Changes
1. Explain what you're doing and why
2. Keep examples simple and practical
3. Prioritize custom CSS solutions
4. Document components in Storybook
5. Respect the "no framework CSS" constraint