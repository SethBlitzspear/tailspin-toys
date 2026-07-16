---
description: 'Comment and documentation philosophy for code clarity and intent'
---

# Commenting and Documentation Standards

Clear, intentional comments and documentation keep the codebase readable and enable Copilot to generate correct changes. This file documents a unified convention for comments and documentation across the project.

## Core Philosophy: Comment *Why*, Not *What*

**Golden Rule**: Comments should explain the *reasoning* or *intent* behind code, not restate what the code already says.

### ❌ Don't: Restating Code

```ts
// Increment the counter
counter++;

// Loop through all games
games.forEach((game) => {
  // Add the game to the array
  results.push(game);
});
```

### ✅ Do: Explaining Intent

```ts
// Increment counter to track the number of attempts for exponential backoff
counter++;

// Collect games in insertion order; sorting happens later in the pipeline
games.forEach((game) => {
  results.push(game);
});
```

### Key Indicators You Don't Need a Comment

- The code is self-documenting (clear variable/function names)
- The comment just repeats the code
- The code is doing the obvious next step in an algorithm
- A better variable name or refactoring would eliminate the need

### When to Comment

- **Non-obvious decisions**: Why this algorithm over another? Why disable a feature?
- **Workarounds**: "Work around browser X not supporting Y; see issue #123"
- **Subtle edge cases**: "Order by title ensures deterministic static builds"
- **Performance rationale**: "Cache this value; the calculation is O(n²)"
- **Intent across multiple lines**: "Ensure all games are sorted so static builds are reproducible"

## TypeScript/JavaScript: TSDoc and JSDoc

Every **exported function** in `db/` and `src/lib/` must have a **JSDoc/TSDoc comment** block describing purpose, parameters, and return value.

### Format

```ts
/**
 * Fetches all games from the database, sorted alphabetically by title.
 * 
 * @param db - The database client (injectable for testing with in-memory instances)
 * @returns Array of games in title-order; empty array if no games exist
 */
export async function getAllGames(db: Database): Promise<Game[]> {
  const rows = await db.select().from(games).orderBy(asc(games.title));
  return rows.map(transform);
}
```

### Rules

- **Every exported function** needs a JSDoc/TSDoc block
- **Purpose**: First sentence describes what the function does
- **Parameters**: Document each parameter with `@param name - description`
  - For the `db` argument: Always note that it's injectable and why (enables testing)
  - Include type and any constraints or side effects
- **Return value**: Document with `@returns description`
  - Describe what you get back, including edge cases (empty array, null for not found, etc.)
- **Side effects**: If the function modifies data, include a note about mutations or database writes
- **Examples (optional)**: For complex functions, include a brief usage example

### Helpers with Injectable `db`

The data layer uses an injectable `db` argument so helpers work with both real and in-memory databases in tests. Always document this pattern:

```ts
/**
 * Looks up a single game by ID.
 * 
 * @param db - Database client (injectable for testing)
 * @param id - Game ID to fetch
 * @returns The game object, or null if not found
 */
export async function getGameById(db: Database, id: number): Promise<Game | null> {
  // …
}
```

### Pure Functions and Transforms

Even pure functions (no side effects) should be documented, especially if they perform non-obvious logic:

```ts
/**
 * Deterministically derives a star rating (3.0–5.0) from a game title using stable hash.
 * Used at seed time so static builds remain reproducible.
 * 
 * @param title - Game title to hash
 * @returns Rating between 3.0 and 5.0, inclusive
 */
export function ratingFromTitle(title: string): number {
  // …
}
```

## Astro Components: Props Documentation

Every **reusable `.astro` component** must document its `Props` interface so the component API is self-explanatory.

### Format

```astro
---
/**
 * Displays a game card with title, cover image, and action buttons.
 * 
 * @prop game - The game object (contains title, cover, publisher, category)
 * @prop featured - If true, renders with an accent border and larger text
 */
interface Props {
  game: Game;
  featured?: boolean;
}

const { game, featured } = Astro.props;
---

<article class={featured ? 'featured-card' : 'card'}>
  {/* … */}
</article>
```

### Rules

- Add a comment block **above** the `Props` interface
- Start with a short description of what the component does
- Document each prop with `@prop name - description`
  - Include type and any constraints
  - For optional props, mention defaults or when they're useful
- Link to or reference related components or utilities if helpful
- Keep it concise — the TypeScript types already define structure; focus on *why* each prop exists

### Page-Only Components

Pages (`src/pages/`) don't need documented `Props` (they're never called with props), but **reusable components** (`src/components/`) do. If a component will ever be imported elsewhere, document it.

## Keeping Comments Current

Treat outdated comments as bugs. When you touch code that has a related comment:

- ✅ **Update the comment** if the code logic changed
- ✅ **Delete the comment** if it's no longer relevant
- ✅ **Verify accuracy** if the comment still applies

If you see an outdated comment in untouched code, fix it in a separate commit with a message like "Fix: update outdated comment in X" so reviewers know it's maintenance, not a logic change.

## Inline Comments (Rare)

Inline comments (on the same line or immediately before a line) are often a sign that variable names or function names could be clearer. Prefer refactoring over inline comments:

### Before (not ideal)

```ts
// Don't use Math.random() — must be deterministic for static builds
const rating = ratingFromTitle(title);
```

### After (better)

```ts
// The function name makes it clear why we're not using Math.random()
const rating = ratingFromTitle(title);
```

If an inline comment is truly necessary (workarounds, browser quirks), keep it brief and explain the *why*:

```ts
// Work around Chromium not supporting the `capture` option; remove once fixed
element.addEventListener('click', handler, false);
```

## ESLint and Automation

ESLint rules may enforce comment style where helpful (e.g., JSDoc requirement on exports). See the ESLint configuration for enforcement rules. Run `npm run lint` to check compliance.

## Summary Checklist

- [ ] Comments explain *why* a decision was made, not *what* the code does
- [ ] No comments that merely restate code
- [ ] Every exported function in `db/` and `src/lib/` has a JSDoc/TSDoc block
- [ ] Each JSDoc documents purpose, parameters, return value, and side effects (if any)
- [ ] The `db` parameter is always documented as injectable
- [ ] Every reusable `.astro` component documents its `Props` interface
- [ ] Comments are updated whenever related code changes
- [ ] Outdated comments are deleted or corrected, not left stale
