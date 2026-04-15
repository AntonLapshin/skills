# SKILL: Unit Testing with Pure Functions

## Context

Use this SKILL when writing unit tests for frontend logic. The guiding principle is: extract any testable logic into pure functions and cover them with unit tests.

## Requirements

- Extract business logic, data transformations, and calculations into pure functions
- Keep components thin - they should orchestrate, not contain heavy logic
- Unit tests must be fast, deterministic, and have no side effects
- Test file naming: `<functionName>.test.ts` alongside the implementation

## Examples

### Extracting Logic from Component

**Before (hard to test):**
```tsx
// RecipeCard.tsx - BAD: Logic mixed with UI
export const RecipeCard = ({ recipe }: { recipe: Recipe }) => {
  const caloriesPerServing = recipe.totalCalories / recipe.servings;
  const difficultyLabel = recipe.prepTime < 15 ? 'Easy' : recipe.prepTime < 30 ? 'Medium' : 'Hard';
  const isVegetarian = recipe.ingredients.every(i => !i.containsMeat);
  
  return (
    <div>
      <h3>{recipe.name}</h3>
      <p>{caloriesPerServing} cal/serving</p>
      <span>{difficultyLabel}</span>
      {isVegetarian && <span>Vegetarian</span>}
    </div>
  );
};
```

**After (testable):**
```tsx
// recipeUtils.ts
export const calculateCaloriesPerServing = (
  totalCalories: number,
  servings: number
): number => {
  if (servings <= 0) throw new Error('Servings must be positive');
  return totalCalories / servings;
};

export const getDifficultyLabel = (prepTime: number): string => {
  if (prepTime < 15) return 'Easy';
  if (prepTime < 30) return 'Medium';
  return 'Hard';
};

export const checkIsVegetarian = (ingredients: Ingredient[]): boolean => {
  return ingredients.every(i => !i.containsMeat);
};

// recipeUtils.test.ts
import { describe, it, expect } from 'vitest';
import { calculateCaloriesPerServing, getDifficultyLabel, checkIsVegetarian } from './recipeUtils';

describe('calculateCaloriesPerServing', () => {
  it('calculates correctly for valid inputs', () => {
    expect(calculateCaloriesPerServing(500, 2)).toBe(250);
    expect(calculateCaloriesPerServing(600, 3)).toBe(200);
  });

  it('throws error for invalid servings', () => {
    expect(() => calculateCaloriesPerServing(500, 0)).toThrow();
    expect(() => calculateCaloriesPerServing(500, -1)).toThrow();
  });
});

describe('getDifficultyLabel', () => {
  it('returns Easy for prep time under 15 minutes', () => {
    expect(getDifficultyLabel(10)).toBe('Easy');
    expect(getDifficultyLabel(14)).toBe('Easy');
  });

  it('returns Medium for prep time 15-29 minutes', () => {
    expect(getDifficultyLabel(15)).toBe('Medium');
    expect(getDifficultyLabel(29)).toBe('Medium');
  });

  it('returns Hard for prep time 30+ minutes', () => {
    expect(getDifficultyLabel(30)).toBe('Hard');
    expect(getDifficultyLabel(60)).toBe('Hard');
  });
});

// RecipeCard.tsx - CLEAN: Component only orchestrates
import { calculateCaloriesPerServing, getDifficultyLabel, checkIsVegetarian } from './recipeUtils';

export const RecipeCard = ({ recipe }: { recipe: Recipe }) => {
  const caloriesPerServing = calculateCaloriesPerServing(recipe.totalCalories, recipe.servings);
  const difficultyLabel = getDifficultyLabel(recipe.prepTime);
  const isVegetarian = checkIsVegetarian(recipe.ingredients);
  
  return (
    <div>
      <h3>{recipe.name}</h3>
      <p>{caloriesPerServing} cal/serving</p>
      <span>{difficultyLabel}</span>
      {isVegetarian && <span>Vegetarian</span>}
    </div>
  );
};
```

## Critical Rules

- **Any logic that can be extracted as a pure function MUST be extracted**
- Pure functions: same input → same output, no side effects, no external dependencies
- Components should focus on rendering and event delegation
- Aim for 100% coverage on pure utility functions
