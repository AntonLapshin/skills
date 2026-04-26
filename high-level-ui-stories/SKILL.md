---
name: creating-ui-stories
description: Create Storybook stories for organisms and pages with mocked side effects using ContextSideEffectsProvider. Use when writing stories that need to demonstrate loading, success, error, and empty states without real API calls.
---

# High-Level UI Stories with Mocked Side Effects

## Requirements

- Create stories for molecules, organisms, pages
- Showcase states: loading, success, error, empty
- Demonstrate real user flows without API calls

## Example: Component States

```tsx
// RecipeList.stories.tsx
import { ContextSideEffectsProvider } from '../../../contexts/ContextSideEffects';
import { RecipeList } from './RecipeList';

export const Loading = () => (
  <ContextSideEffectsProvider value={{ getRecipes: () => new Promise(() => {}) }}>
    <RecipeList />
  </ContextSideEffectsProvider>
);

export const WithData = () => (
  <ContextSideEffectsProvider value={{ getRecipes: async () => [
    { id: '1', name: 'Pasta Carbonara', category: 'italian', prepTime: 20 },
    { id: '2', name: 'Chicken Curry', category: 'indian', prepTime: 45 },
  ]}}>
    <RecipeList />
  </ContextSideEffectsProvider>
);

export const Empty = () => (
  <ContextSideEffectsProvider value={{ getRecipes: async () => [] }}>
    <RecipeList />
  </ContextSideEffectsProvider>
);

export const Error = () => (
  <ContextSideEffectsProvider value={{ getRecipes: async () => { throw new Error('fail'); } }}>
    <RecipeList />
  </ContextSideEffectsProvider>
);
```

## Example: Multiple Side Effects

```tsx
// RecipeDashboard.stories.tsx
const createMocks = (scenario: 'new' | 'power' | 'error') => {
  if (scenario === 'new') return { getRecipes: async () => [], getFavorites: async () => [] };
  if (scenario === 'power') return { getRecipes: async () => Array(50).fill({ id: '1', name: 'Recipe' }) };
  return { getRecipes: async () => { throw new Error('API Error'); } };
};

export const NewUser = () => (
  <ContextSideEffectsProvider value={createMocks('new')}>
    <RecipeDashboard />
  </ContextSideEffectsProvider>
);
```

## Example: Network Delay

```tsx
const withDelay = (delay = 1000) => async () => {
  await new Promise(r => setTimeout(r, delay));
  return mockData;
};

export const SlowNetwork = () => (
  <ContextSideEffectsProvider value={{ getRecipes: withDelay(2000) }}>
    <RecipeList />
  </ContextSideEffectsProvider>
);
```

## Critical Rules

- Wrap with `ContextSideEffectsProvider` directly in every high-level story
- Create mocks for: loading (never-resolves), success, empty, error
- Use inline mock functions for scenario-specific behavior
- Stories must work without real API/backend
