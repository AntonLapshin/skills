---
name: managing-side-effects
description: Manage side effects through a context pattern enabling easy mocking in tests and Storybook. Use when implementing API calls or external integrations that need to be mocked for testing or story isolation.
---

# Side Effects Context Pattern

## Pattern Steps

1. **Contract**: Method that throws `new Error('Not implemented')`
2. **Mock**: `.mock.ts` file with realistic data
3. **Production**: `.prod.ts` file with real implementation
4. **Context**: `ContextSideEffects` with production as default
5. **Usage**: `useContext(ContextSideEffects)` in components

## Example

### Contract
```ts
// sideEffects/getRecipes.ts
export interface GetRecipesParams { category?: string; limit?: number; }
export const getRecipes = async (params?: GetRecipesParams): Promise<Recipe[]> => {
  throw new Error('Not implemented');
};
```

### Mock
```ts
// sideEffects/getRecipes.mock.ts
export const getRecipesMock = async (params?: GetRecipesParams): Promise<Recipe[]> => {
  const recipes = [{ id: '1', name: 'Pasta' }, { id: '2', name: 'Curry' }];
  if (params?.category) return recipes.filter(r => r.category === params.category);
  if (params?.limit) return recipes.slice(0, params.limit);
  return recipes;
};
```

### Context
```tsx
// contexts/ContextSideEffects.tsx
import { getRecipes as getRecipesProd } from '../sideEffects/getRecipes.prod';

export interface SideEffects { getRecipes: typeof getRecipes; }
const defaults: SideEffects = { getRecipes: getRecipesProd };
export const ContextSideEffects = createContext<SideEffects>(defaults);

export const ContextSideEffectsProvider = ({ children, value = {} }: { children: ReactNode; value?: Partial<SideEffects> }) => (
  <ContextSideEffects.Provider value={{ ...defaults, ...value }}>{children}</ContextSideEffects.Provider>
);
```

### Component Usage
```tsx
// RecipeList.tsx
export const RecipeList = () => {
  const { getRecipes } = useContext(ContextSideEffects);
  const [recipes, setRecipes] = useState<Recipe[]>([]);

  useEffect(() => { getRecipes().then(setRecipes); }, [getRecipes]);
  return <ul>{recipes.map(r => <li key={r.id}>{r.name}</li>)}</ul>;
};
```

### Test Mocking
```tsx
// RecipeList.test.tsx
import { getRecipesMock } from '../sideEffects/getRecipes.mock';

render(
  <ContextSideEffectsProvider value={{ getRecipes: getRecipesMock }}>
    <RecipeList />
  </ContextSideEffectsProvider>
);
```

## Critical Rules

- Every side effect: contract (throw) + mock + useContext usage
- Production implementation is default in context
- Never call side effects directly - always through context
- Mocks must simulate realistic data and edge cases
