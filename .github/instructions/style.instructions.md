---
description: 'Tailwind CSS v4 styling patterns and dark theme guidelines'
applyTo: '**/*.{astro,css}'
---

# Tailwind CSS Instructions

## Tailwind CSS v4 Configuration

This project uses Tailwind CSS v4.1.14 via the `@tailwindcss/vite` plugin.

### Global CSS Setup

- Import Tailwind in `global.css`: `@import "tailwindcss";`
- No separate `tailwind.config.js` file is used
- Configuration is handled through the Vite plugin

## TypeScript Formatting and Style

The project enforces consistent TypeScript formatting through **ESLint** (`npm run lint`). All TypeScript and Astro files must pass linting before commit.

### TypeScript Conventions

- **Explicit types**: All function parameters and return values must have explicit type annotations
  - ✅ `function getAllGames(db: Database): Promise<Game[]>`
  - ❌ `function getAllGames(db) { }`
- **Exported functions**: Must have JSDoc/TSDoc comment blocks (see [`comments.instructions.md`](comments.instructions.md))
- **Naming**:
  - Use camelCase for variables and functions
  - Use PascalCase for types, interfaces, and classes
  - Use UPPER_SNAKE_CASE for constants
- **Imports**:
  - Group imports: external packages first, then relative imports
  - Use explicit named imports, not `import *` where possible
  - Remove unused imports (ESLint enforces this)
- **Spacing**:
  - 2 spaces for indentation (enforced by Prettier via ESLint)
  - Consistent spacing around operators and control structures
- **Comments**:
  - Comment *why*, not *what* (see [`comments.instructions.md`](comments.instructions.md))
  - Every exported function needs a JSDoc block
  - Remove comments that merely restate code

### ESLint Configuration

ESLint rules enforce style consistency. Key enforced rules:

- `@typescript-eslint/explicit-function-return-types` — Functions must have explicit return types
- `@typescript-eslint/explicit-member-accessibility` — Class members must have explicit accessibility (public/private)
- `no-unused-vars` / `@typescript-eslint/no-unused-vars` — Unused variables and imports are disallowed
- `@typescript-eslint/naming-convention` — Enforce camelCase, PascalCase, and UPPER_SNAKE_CASE conventions
- `no-var` — Use `const` and `let`, never `var`

Run `npm run lint` to check compliance. Many issues can be auto-fixed with `npm run lint -- --fix`.

## Dark Theme Styling

ALL UI components MUST use dark theme colors:

### Color Palette

- Background colors: `bg-slate-800`, `bg-slate-900`, `bg-slate-950`
- Text colors: `text-slate-100`, `text-slate-200`, `text-slate-300`
- Border colors: `border-slate-700`, `border-slate-600`
- Accent colors for hover/focus states

### Common Patterns

- Cards and containers: `bg-slate-800 rounded-xl p-6 shadow-lg`
- Hover effects: `hover:bg-slate-700 transition-colors duration-200`
- Borders: `border border-slate-700`
- Gradients for visual interest: `bg-gradient-to-br from-slate-800 to-slate-900`
- Backdrop effects: `backdrop-blur-sm bg-slate-900/50`

### Responsive Design

- Use responsive prefixes: `sm:`, `md:`, `lg:`, `xl:`
- Mobile-first approach
- Ensure readability on all screen sizes

## Utility Classes

- Prefer utility classes over custom CSS when possible
- Use semantic grouping: layout, spacing, colors, typography
- Keep utility combinations readable and maintainable

## Modern UI Patterns

- Rounded corners: `rounded-lg`, `rounded-xl`, `rounded-2xl`
- Smooth transitions: `transition-all duration-200 ease-in-out`
- Shadows for depth: `shadow-md`, `shadow-lg`, `shadow-xl`
- Focus states for accessibility: `focus:ring-2 focus:ring-blue-500`
