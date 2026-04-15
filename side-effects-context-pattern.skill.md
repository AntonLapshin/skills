# SKILL: Side Effects Context Pattern

## Context

Use this SKILL for all side effects (API calls, external integrations, non-deterministic operations). Implement the ContextSideEffects pattern to enable mocking in tests and Storybook.

## Requirements

1. Create a method with `throw new Error('Not implemented')` as the contract
2. Create a mock implementation in a `.mock.ts` file
3. Create a `ContextSideEffects` context that provides the production implementation by default
4. Create a custom hook that consumes the context for each side effect
5. Override implementations via Context provider for tests and stories

## Examples

### Side Effect Contract

```ts
// sideEffects/getRecipes.ts
import { Recipe } from '../types';

export interface GetRecipesParams {
  category?: string;
  limit?: number;
}

export const getRecipes = async (params?: GetRecipesParams): Promise<Recipe[]> => {
  throw new Error('Not implemented');
};
```

### Mock Implementation

```ts
// sideEffects/getRecipes.mock.ts
import { getRecipes, GetRecipesParams } from './getRecipes';
import { Recipe } from '../types';

export const getRecipesMock = async (params?: GetRecipesParams): Promise<Recipe[]> => {
  const mockRecipes: Recipe[] = [
    { id: '1', name: 'Pasta Carbonara', category: 'italian', prepTime: 20 },
    { id: '2', name: 'Chicken Curry', category: 'indian', prepTime: 45 },
    { id: '3', name: 'Caesar Salad', category: 'american', prepTime: 15 },
  ];

  let result = mockRecipes;
  
  if (params?.category) {
    result = result.filter(r => r.category === params.category);
  }
  
  if (params?.limit) {
    result = result.slice(0, params.limit);
  }

  return result;
};
```

### Context Definition

```tsx
// contexts/ContextSideEffects.tsx
import { createContext, ReactNode, useContext } from 'react';
import { getRecipes } from '../sideEffects/getRecipes';

// Import all production implementations
import { getRecipes as getRecipesProd } from '../sideEffects/getRecipes.prod';

export interface SideEffects {
  getRecipes: typeof getRecipes;
}

const defaultSideEffects: SideEffects = {
  getRecipes: getRecipesProd,
};

export const ContextSideEffects = createContext<SideEffects>(defaultSideEffects);

export interface ContextSideEffectsProviderProps {
  children: ReactNode;
  value?: Partial<SideEffects>;
}

export const ContextSideEffectsProvider = ({ children, value = {} }: ContextSideEffectsProviderProps) => {
  const mergedValue = { ...defaultSideEffects, ...value };
  return (
    <ContextSideEffects.Provider value={mergedValue}>
      {children}
    </ContextSideEffects.Provider>
  );
};
```

### Using in Components

```tsx
// components/RecipeList.tsx
import { useEffect, useState, useContext } from 'react';
import { ContextSideEffects } from '../contexts/ContextSideEffects';
import { Recipe } from '../types';

export const RecipeList = () => {
  const { getRecipes } = useContext(ContextSideEffects);
  const [recipes, setRecipes] = useState<Recipe[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    getRecipes({ limit: 10 })
      .then(setRecipes)
      .finally(() => setLoading(false));
  }, [getRecipes]);

  if (loading) return <div>Loading...</div>;

  return (
    <ul>
      {recipes.map(recipe => (
        <li key={recipe.id}>{recipe.name}</li>
      ))}
    </ul>
  );
};
```

### Mocking in Tests

```tsx
// RecipeList.test.tsx
import { render, screen, waitFor } from '@testing-library/react';
import { ContextSideEffectsProvider } from '../contexts/ContextSideEffects';
import { getRecipesMock } from '../sideEffects/getRecipes.mock';
import { RecipeList } from './RecipeList';

describe('RecipeList', () => {
  it('renders recipes from mock', async () => {
    render(
      <ContextSideEffectsProvider value={{ getRecipes: getRecipesMock }}>
        <RecipeList />
      </ContextSideEffectsProvider>
    );

    await waitFor(() => {
      expect(screen.getByText('Pasta Carbonara')).toBeInTheDocument();
    });
  });
});
```

## Critical Rules

- **Every side effect must have**: contract (throw), mock, and useContext usage
- **Production implementation** is the default in ContextSideEffects
- **Never call side effects directly** - always use `useContext(ContextSideEffects)`
- Mock files must simulate realistic data and edge cases
- Context allows swapping implementations without code changes
