---
name: testing-pure-functions
description: Write unit tests by extracting logic into pure functions for fast, deterministic testing. Use when creating testable utility functions that have no side effects and produce consistent outputs for given inputs.
---

# Unit Testing with Pure Functions

## Requirements

- Extract logic into pure functions
- Keep components thin (orchestration only)
- Tests: fast, deterministic, no side effects
- Test file: `<functionName>.test.ts` alongside implementation

## Example: Before & After

**Before (hard to test):**
```tsx
// RecipeCard.tsx - Logic mixed with UI
export const RecipeCard = ({ recipe }: { recipe: Recipe }) => {
  const calories = recipe.totalCalories / recipe.servings;
  const difficulty = recipe.prepTime < 15 ? 'Easy' : recipe.prepTime < 30 ? 'Medium' : 'Hard';
  return <div>{calories} cal/serving - {difficulty}</div>;
};
```

**After (testable):**
```tsx
// recipeUtils.ts
export const calcCalories = (total: number, servings: number): number => {
  if (servings <= 0) throw new Error('Servings must be positive');
  return total / servings;
};

export const getDifficulty = (prepTime: number): string => {
  if (prepTime < 15) return 'Easy';
  if (prepTime < 30) return 'Medium';
  return 'Hard';
};

// recipeUtils.test.ts
import { describe, it, expect } from 'vitest';
import { calcCalories, getDifficulty } from './recipeUtils';

describe('calcCalories', () => {
  it('calculates correctly', () => {
    expect(calcCalories(500, 2)).toBe(250);
    expect(calcCalories(600, 3)).toBe(200);
  });

  it('throws on invalid servings', () => {
    expect(() => calcCalories(500, 0)).toThrow();
  });
});

describe('getDifficulty', () => {
  it('returns Easy for < 15 min', () => {
    expect(getDifficulty(10)).toBe('Easy');
  });
  it('returns Medium for 15-29 min', () => {
    expect(getDifficulty(20)).toBe('Medium');
  });
  it('returns Hard for 30+ min', () => {
    expect(getDifficulty(30)).toBe('Hard');
  });
});

// RecipeCard.tsx - Component orchestrates
import { calcCalories, getDifficulty } from './recipeUtils';

export const RecipeCard = ({ recipe }: { recipe: Recipe }) => (
  <div>
    {calcCalories(recipe.totalCalories, recipe.servings)} cal/serving - {getDifficulty(recipe.prepTime)}
  </div>
);
```

## Critical Rules

- Any extractable logic MUST be a pure function
- Pure functions: same input → same output, no side effects
- Components focus on rendering and event delegation
- 100% coverage on pure utility functions
