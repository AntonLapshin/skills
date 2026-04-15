# SKILL: High-Level UI Stories with Mocked Side Effects

## Context

Use this SKILL when creating high-level Storybook stories that showcase complex UI behavior. Wrap components with ContextSideEffects.Provider to mock side effects and demonstrate edge cases.

## Requirements

- Create stories for composite components (molecules, organisms, pages)
- Use `ContextSideEffects.Provider` to inject mock implementations
- Showcase different states: loading, success, error, empty
- Simulate edge cases through mock configuration
- Stories should demonstrate real user flows without actual API calls

## Examples

### Recipe List with Different States

```tsx
// components/organisms/RecipeList/RecipeList.stories.tsx
import { ContextSideEffectsProvider } from '../../../contexts/ContextSideEffects';
import { RecipeList } from './RecipeList';

// Mock: Loading state
const getRecipesLoading = async () => {
  return new Promise(() => {}); // Never resolves
};

export const Loading = () => {
  return (
    <ContextSideEffectsProvider value={{ getRecipes: getRecipesLoading }}>
      <RecipeList />
    </ContextSideEffectsProvider>
  );
};

// Mock: Success with data
const getRecipesSuccess = async () => {
  return [
    { id: '1', name: 'Pasta Carbonara', category: 'italian', prepTime: 20 },
    { id: '2', name: 'Chicken Curry', category: 'indian', prepTime: 45 },
    { id: '3', name: 'Caesar Salad', category: 'american', prepTime: 15 },
  ];
};

export const WithData = () => {
  return (
    <ContextSideEffectsProvider value={{ getRecipes: getRecipesSuccess }}>
      <RecipeList />
    </ContextSideEffectsProvider>
  );
};

// Mock: Empty state
const getRecipesEmpty = async () => {
  return [];
};

export const Empty = () => {
  return (
    <ContextSideEffectsProvider value={{ getRecipes: getRecipesEmpty }}>
      <RecipeList />
    </ContextSideEffectsProvider>
  );
};

// Mock: Error state
const getRecipesError = async () => {
  throw new Error('Failed to fetch recipes');
};

export const Error = () => {
  return (
    <ContextSideEffectsProvider value={{ getRecipes: getRecipesError }}>
      <RecipeList />
    </ContextSideEffectsProvider>
  );
};
```

### Complex Page Story with Multiple Side Effects

```tsx
// pages/RecipeDashboard/RecipeDashboard.stories.tsx
import { ContextSideEffectsProvider } from '../../contexts/ContextSideEffects';
import { RecipeDashboard } from './RecipeDashboard';

// Mock factory for different scenarios
const createMocks = (scenario: 'new-user' | 'power-user' | 'error') => {
  if (scenario === 'new-user') {
    return {
      getRecipes: async () => [],
      getFavorites: async () => [],
      getRecommendations: async () => [
        { id: 'rec1', name: 'Easy Starter Recipe' },
      ],
    };
  }
  
  if (scenario === 'power-user') {
    return {
      getRecipes: async () => Array.from({ length: 50 }, (_, i) => ({
        id: `r${i}`,
        name: `Recipe ${i}`,
      })),
      getFavorites: async () => [
        { id: 'f1', name: 'Family Favorite' },
        { id: 'f2', name: 'Weeknight Special' },
      ],
      getRecommendations: async () => [
        { id: 'rec1', name: 'Because you liked Pasta' },
        { id: 'rec2', name: 'Trending now' },
      ],
    };
  }
  
  return {
    getRecipes: async () => { throw new Error('API Error'); },
    getFavorites: async () => [],
    getRecommendations: async () => [],
  };
};

export const NewUser = () => {
  return (
    <ContextSideEffectsProvider value={createMocks('new-user')}>
      <RecipeDashboard />
    </ContextSideEffectsProvider>
  );
};

export const PowerUser = () => {
  return (
    <ContextSideEffectsProvider value={createMocks('power-user')}>
      <RecipeDashboard />
    </ContextSideEffectsProvider>
  );
};

export const ApiError = () => {
  return (
    <ContextSideEffectsProvider value={createMocks('error')}>
      <RecipeDashboard />
    </ContextSideEffectsProvider>
  );
};
```

### Mock with Delay for Realistic Loading

```tsx
// Simulate network delay
const getRecipesWithDelay = async (delay = 1000) => {
  await new Promise(resolve => setTimeout(resolve, delay));
  return [
    { id: '1', name: 'Slow Cooked Stew', prepTime: 180 },
    { id: '2', name: 'Quick Salad', prepTime: 5 },
  ];
};

export const WithNetworkDelay = () => {
  return (
    <ContextSideEffectsProvider value={{ 
      getRecipes: () => getRecipesWithDelay(2000) 
    }}>
      <RecipeList />
    </ContextSideEffectsProvider>
  );
};
```

## Critical Rules

- **All high-level stories must wrap content with ContextSideEffectsProvider directly**
- Create mocks that represent real scenarios: loading, success, empty, error
- Use inline mock functions in stories for scenario-specific behavior
- Demonstrate edge cases through mock data (empty arrays, long lists, errors)
- Reuse the same mock implementations from `.mock.ts` files when possible
- Stories should work without any real API or backend running
